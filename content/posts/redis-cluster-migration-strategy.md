---
title : "Redis Cluster Migration Strategy"
date : "2021-07-17T23:16:48+07:00"
author : "Dery R Ahaddienata"
authorTwitter : "deryrahman" #do not include @
cover : ""
tags : ["tech", "storage", "redis"]
keywords : ["redis", "migration", "cluster", "strategy"]
description : "Last week I have a chance to perform Redis cluster migration. Our Redis cluster contains 6 high memory VMs. At peak, it has 800 request/second. In this article I would like to explain how we migrate Redis cluster with zero downtime as well as lesson learned I've gathered."
showFullContent : false
toc: true
---

Last week I have a chance to perform Redis cluster migration. Our Redis cluster contains 6 high memory VMs. At peak, it has 800 request/second. In this article I would like to explain how we migrate Redis cluster with zero downtime as well as lesson learned I've gathered.

## Problem Statement
As Ubuntu 16.04 no longer support LTS, we need to upgrade some obsolete VMs to latest version (20.04). One of the obsolete VMs are our Redis cluster. Most of the services rely on our service that use this Redis cluster as in-memory storage. Upgrading the OS inside the node is not the option since it require complex reconfiguration as well as inevitable downtime.

We're using Redis version 5.0.9, with GCP VM n2-highmem-4. The cluster contains 3 master nodes and 3 slave nodes. The keys are stored in memory that filled up 16GB across 3 partitions.

## Goal
Upgrade the Ubuntu version (16.04) of all Redis cluster nodes to 20.14 with zero downtime.

## Approach

First we spin up new GCP VM with Ubuntu 20.04 installed. Then install and configure the Redis so that this new Redis are ready to join to the existing cluster. Then add this node to the cluster as a slave of partition 1. Wait for this slave to sync the data with the master. Repeat it for the partition 2 and 3. Then perform manual failover so the new node will be promoted as a new master. Finally, detach all the old Redis nodes from cluster. It seems like quite easy. Yes it does. But in practice, we have a challenge. The challenge will be explained in the [Lesson Learned](#lesson-learned) section.

Put image here (TBD)

Here're some steps in details:

1. Spin up new GCP VM. I won't explain how to do that since it's out of scope from our topic.
2. Configure Redis 5.0.9 to meet minimum requirement as a node cluster. All configuration can be seen [here][1].
    {{<code language="conf" title="redis.conf" id="1" isCollapsed="false">}}
    cluster-node-timeout 100
    cluster-enabled yes
    cluster-config-file "auto-nodes.conf"
    cluster-replica-validity-factor 1
    cluster-require-full-coverage no
    aof-use-rdb-preamble yes
    {{</code>}}
3. Add the new node to the existing cluster as a slave. Wait for this new node to perform full sync with master. We have 3 node partitions, make sure this new node will join as a slave of corresponding master.
    ```sh
    redis-cli --cluster add-node <new_redis_box_ip>:6379 <any_old_redis_box_ip>:6379 --cluster-slave --cluster-master-id <master_id>

    ```
4. Repeat step 1-3 for each partitions. The result, there're 2 new slaves for each partition. Total nodes in cluster is 12, 6 old nodes, other 6 are new nodes.
5. [Failover][2] the old master and promote new node as a master for each partitions.
    ```sh
    redis-cli -h <new_redis_box_ip> -p 6379
    # run redis-cli command: CLUSTER FAILOVER
    ```
6. Detach 6 old nodes from the cluster.
    ```sh
    redis-cli --cluster del-node <new_redis_box_ip>:6379 <old_node_id>

    ```

## Lesson Learned

When we do step 3, there's a huge missing read request. After we seek for the root cause it happens because during full sync, ~5GB data transfer from master to new slave, the new slave will fill up the huge data to memory from disk. This activity leads to high CPU usage on our new slave node. Thus, some request that read data from this slave would not be proceeded.

Before that, we also suspect it's because of the fork process during RDB backup on the master. Redis cluster use RDB file to sync with the replica. And yup, fork process on Redis would consume huge CPU. But if this is the cause, it could be the issue for a long time ago, since the sync process between master and slave is always happening.

Second suspect is [due to bandwidth limit][3]. During full sync process, master and slave will transfer the data as fast as possible so that the bandwidth is reach its limit. Hence, it will cause the request blocking. But, after we saw from our monitoring tool, we didn't see any request error when master was transfering data to the new slave.

The request was blocked when new slave fill up the new data. It happened so fast so that the CPU usage was 100%. Some requests that read from this new slave was failing. We still didn't know and the inner process how the Redis fill up memory from zero. We also seek for a solution to hold the Redis read from specific slave, but we didn't see any configuration to configure this. The close possible solution is `cluster-allow-reads-when-down`, but it's for redis 6.0. We didn't have time to explore this deeper.

Thus we planned to do this migration during off hour.

## Conclusion

We're fail to do Redis cluster migration with zero downtime. But, we know for sure that this strategy is worked when data it not big. We performed the Redis cluster migration on integration, and we achieved zero error and zero downtime. Perhaps, if we can block the Redis read on the new slave, it might probably help when the new slave perform a full sync from master node.

[1]: https://redis.io/topics/cluster-tutorial
[2]: https://redis.io/commands/cluster-failover
[3]: https://github.com/redis/redis/issues/4815

**References**
1. https://redis.io/topics/cluster-tutorial
2. https://redis.io/commands/cluster-failover
3. https://github.com/redis/redis/issues/4815
