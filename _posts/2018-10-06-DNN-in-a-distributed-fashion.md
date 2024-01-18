---
layout: post
title: DNN in a distributed fashion
description: "This is a summary about work we've done so far. I present the work in IROS 2018."
comments: true
---

**Deep Neural Network** is useful in image recognition, object detection nowadays. DNN usually runs trainings and 
predictions on high performance server. In real environment, IoT (small memory space & low computation power) devices 
actually have more accesses to raw data compared to server. 

### Can IoT devices run DNN predictions directly?
It is **VERY HARD** run DNN prediction on IoT because 
* **Memory Constraint**: DNN has large parameter size (large memory footprint), which is not able to fit into IoT memory.
* **Computation Latency**: Single data inference latency on IoT is high.

Prior work proposed that IoT devices can offload data to server, but it still requires the presence of server. 

### What we do?
In reality, many IoT devices often sit in a shared environment. We propose that instead of running DNN on a single
IoT device, we can use **a cluster of IoT devices** to run DNN predictions in **a distributed fashion**. 

<pre></pre>
<img width="400" src="{{ site.url }}/images/2018-10-06-DNN-in-a-distributed-fashion/figure1.png">
<pre></pre>

Above is a simple explanation for **running DNN in a distributed fashion**. Instead of aggregating 4 layers on a single
device, each layer is allocated on a unique device. One device's output will serve as another device's input,
and data is transferred over internet. This simply builds a system to run DNN in a distributed way. 

### BUT, it is NOT good enough!
Even though we distribute layer to different device, some very heavy layer still presents **memory constraint** and 
**computation latency** challenge to IoT devices. Therefore, in addition to simple DNN distribution, we proposed two 
techniques to solve **memory constraint** and **computation latency** issues.

#### 1st: Data Parallelism
It is hard to reduce **computation latency** because amount of computation done by each layer is fixed. Instead of 
reducing **latency** for each data, our approach focuses on increasing the **throughput** of the system. 

<pre></pre>
<img width="400" src="{{ site.url }}/images/2018-10-06-DNN-in-a-distributed-fashion/figure2.png">
<pre></pre>

In **Data Parallelism** approach, instead of using 1 device for task B, we dedicate 2 (or multiple) devices to run task 
B. Once task A finishes its computation, it sends output to **ANY ONE** of devices which are dedicated to task B.

<pre></pre>In this case, we simply add number of devices dedicated to task B to increase the **throughput** of the system.

#### 2nd: Model Parallelism
We propose **Model Parallelism** to reduce **memory footprint** introduced by a heavy layer. 

<pre></pre>
<img width="400" src="{{ site.url }}/images/2018-10-06-DNN-in-a-distributed-fashion/figure3.png">
<pre></pre>

The difference between **Data Parallelism** and **Model Parallelism** is that, in **Data Parallelism** all devices have
the same parameter, which is the original parameter of task B.

<pre></pre>But in **Model Parallelism**, all devices have unique
parameters, each unique parameter is a portion of original parameter of task B. In **Model Parallelism**, 
the aggregation of parameters on devices dedicated to task B is the original parameter of task B.

<pre></pre>In this case, the **Model Parallelism** helps to reduce each device's **memory footprint**. It brings performance
benefit because with less memory usage, device does not need to go to swap space or hard disk.

#### Thanks for reading. Links to paper can be found under [Publication]({{site.url}}/publications/).