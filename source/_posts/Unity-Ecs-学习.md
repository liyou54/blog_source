---
title: Unity Ecs 学习
date: 2023-01-21 11:25:16
tags: Unity
---

# 安装
com.unity.entities

# JobsType

```
IJobParallelForTransform
Ijob
IJobParallelFor
IjobFor
IJobBurstSchedulable
IJobParallelForDefer
IJobParallelForBatch
IJobParallelForFilter
...

Job调度：
Run 主线程立即执行
Schedule 单个工作线程或者主线程执行，顺序执行
ScheduleParallel 多个工作线程执行 数据访问产生冲突

```