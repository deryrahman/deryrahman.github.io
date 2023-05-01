+++
title = "Balancing High Level Low Level Thinking"
date = "2022-01-16T19:08:09+07:00"
author = "Dery R Ahaddienata"
authorTwitter = "deryrahman" #do not include @
cover = ""
tags = ["personal", "tech", "concept"]
keywords = ["high-level", "low-level"]
description = ""
showFullContent = false
readingTime = false
+++

We can't maintain fast thinking and precise thinking at the same time. There's a tradeoff. Let me explain, when you read a sentence, you can grasp the meaning of that sentence. But, you don't really care about the meaning of each word, or in more precise manner, you don't even read each alphabet. How many alphabet `m` you've read up until this point? You don't really care right? Because, our brain has tendency to automate common repeatable things (in this case, the individual word meaning or the alphabet) and only focus on the things that we think it matters (in this case, the meaning of whole sentence).


{{< image src="/static/img/highlevel-lowlevel.png" alt="High Level & low level in language hierarchy" position="center" style="border-radius: 5px;" >}}

Understanding individual word meaning can be seen as a low level, and understanding the whole sentence meaning can be seen as a high level. If we only focus on each individual word meaning, our reading ability will slower than if we focus on whole sentence as it is. But you can't know the meaning of the sentence if you've never understood the individual word itself. You need to be more precise when you learn something new.

Are you able to understand this sentence? 月曜日の朝. If you're not familiar with the Japanese language, you need to learn the alphabet (hiragana, katakana, kanji), and learn the meaning of the word, and then the sentence as a whole. Let me rephrase this into more generalize concept. If we only focus on lower level task, we trade the ability to know detailed things over the speed. But if we focus on high level task before lower level, our understanding of high level is blurred. We need a balance of it. And as a software engineer, it really matters. 

> If we only focus on lower level task, we trade the ability to know detailed things over the speed. But if we focus on high level task before lower level, our understanding of high level is blurred. We need a balance of it.


## Why is it important

Short answer, it's not. But if you care about the efficiency of your time, it's important. It will reduce the unecessary thinking if you see the things in higher level. And it will improve your ability to understand the things under the hood if you see the things in lower level. We know the abstraction. Each level has abstraction of its lower level. We don't care about how the electricity give the chip gate circuit on and off (low level) while we write a program (high level) (unless you're assembly engineer 😄). But knowing how memory works (low level) while we write a program (high level), can be reduce the catastrophic disaster that can be possibly happen in certain point of time.


## How we define the high level and low level

It depends. But rule of thumb is you need to know just enough the low level that you think it matters, and think in high level to boost you speed. If you're a backend engineer, the low level you need to know is how OS works, and the high level thing is the code itself. If you're an assembly engineer, maybe backend's low level can be seen as their high level and how the chip works can be seen as their low level thing.

> Just enough to know the low level concept, but think in high level abstraction.


{{< image src="/static/img/highlevel-lowlevel-softeng.png" alt="High Level & low level in software engineer" position="center" style="border-radius: 5px;" >}}

## Conclusion

We need a right balance between thinking in high level and low level. Sometimes, we need to slowdown the process if we need to understand the detail in deep, sometimes we need to focus only on high level without even care about the lower level (it just happens unconciously). By balancing both dynamically in a certain short of condition, we are able to utilize the advantages from both combination.

> No amount of genius can overcome a preoccupation with detail. - Levy’s Eighth Law
