+++
title = "Kubernetes Connectivity Unreachable?"
date = "2025-02-19T23:41:09+07:00"
author = "Dery R Ahaddienata"
authorTwitter = "deryrahman" #do not include @
cover = ""
tags = ["tech", "kubernetes"]
keywords = ["kubernetes", "networking"]
description = "Recently, I tried to setup the k8s cluster in my local. Unlike common setup where we usually use minikube, kindD, or k3s, this time I play around using vm directly. This exercise allow me to have a better understanding on how k8s manages the containers."
showFullContent = false
readingTime = false
+++

Recently, I tried to setup the k8s cluster in my local. Unlike common setup where we usually use minikube, kindD, or k3s, this time I play around using vm directly. This exercise allow me to have a better understanding on how k8s manages the containers.

I use lima vm to create a vm in my mac. Previously I have VirtualBox and VMWare as a candidate, but I felt like it's more resource consuming. Yet, I found lima vm as an alternative that I feel it's more light weight. I tried to spin up 2 VMs, one for control-plane and other for worker. I use user-v2 and vzNAT as a network. Former for vm to vm communication and later for vm to host communication (yes, I need this for exposing LoadBalancer service). So, with this setup, 1 node has 2 network interface. Then, this is where connectivity issue araises..

---

# kube-proxy crashed on worker node

When adding a worker node with 2 interfaces, kube-proxy always crash due to the hostname `lima-vm-control-plane.internal` can't resolved. So we need to debug, why it's not resolved:
- Most likely the problem lies on dns resolution. Turns out when I run debug container on worker node and try to `nslookup` that hostname, it's not resolved

**Root cause:**
- In that container, `/etc/resolv.conf` is wrongly ordered:
    ```
    nameserver 192.168.106.1
    nameserver fe80::184a:53ff:fe41:e165%3
    nameserver 192.168.104.2
    ```
- It first lookup on 192.168.106.1 which is it's vzNAT and it's not accessible through worker

**Fix:**
- First, we should find, how resolv.conf is assined to the container. Turns out it's handled by kubelet, which is can be seen in the kubelet configuration, there's a section to specify in which the configuration must be used (resolvConf). By default, it's `/run/systemd/resolve/resolv.conf`
- Change the order of `/run/systemd/resolve/resolv.conf`, so that `nameserver 192.168.104.2` is on the top. Then recraate the pod

---

# CNI plugin in worker node fail

When I setup the worker node, the cni plugin fails. In the log, I see that the kubernetes IP is unreachable from worker node. When 10.96.0.1 in unreachable, we need know in which layer the network is unreachable. 
- Use curl first, if it's unreachable, then go down to the lower level
- In networking layer, we can use `netcat` , use `ip addr`, `ip route` to know where the ip should be routed, if it's still unreachable check using `iptables` , in my case, the problem shown when I see `iptables`:
- the `nat` table somehow configured to the wrong IP. eventho the IP is belong to the correct node that contains controlplane, but this IP is unreachable from worker node.
-  I realized that my setting is 1 node has 2 interfaces at begining (when I bootstrap the cluster).
	```
	controlplane
	eth0 192.168.104.1/24
	lima1 192.168.106.12/24
	
	worker
	eth0  192.168.104.8/24
	lima1 192.168.106.15/24 
	``` 
- lima1 interface is an interface that connect the node to my local. I use vzNAT. And through this interface, the IP between node is unreachable

**Root cause:**
- The iptables that I have in worker node translate the destination of `10.96.0.1` to `192.168.106.12` that's why it's always fail to connect to api-server in controlplane

**Fix:**
- Long term fix:
	- Hypothetically when bootstrap the kubernetes node, we need to pass the advertise ip for controlplane that can be reached by other nodes. In my case, I bootstrap the cluster without advertise ip
- Quick fix:
	- Route the ip `192.168.106.12` directly to `192.168.104.1` through eth0
	- `ip route add 192.168.106.12 via 192.168.104.1 dev eth0`
---

# CoreDNS in worker node crashed
Truns out the ip forward is not enabled in worker node.

**Fix:**
```sh
# sysctl params required by setup, params persist across reboots

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.ipv4.ip_forward = 1
EOF

# Apply sysctl params without reboot
sudo sysctl --system
```
---

There will be many networking issue comes. Knowing how networking works really save me. There're some book that I found helpful during my debugging process:
- Networking for Systems Administrators - Michael Lucas
- Linux iptables Pocket Reference - Gregor N. Purdy
