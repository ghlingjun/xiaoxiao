---
layout: default
title: Human Task 介绍
date: 2014-11-07 09:48:09
tag: [jbpm,task]
---

业务流程中很重要的一个功能是 Human Task 管理。流程中不是所有任务都可以自动的执行，有些任务需要有人来参与执行。

jBPM 流程中用一个特殊的 Human Task 节点来与用户进行交互。流程设计者可以在 Human Task 节点定义用户执行任务相关的属性，例如任务类型，执行者，或者任务相关的数据。

jBPM 也提供也一个 so-called human task 服务以及一个 back-end 服务，来在运行时管理这些 tasks 的生命周期。jBPM 这些实现是基于 WS-HumanTask specification. 这些实现是完全未封装的，意思就是你完全可以根据需要来集成自己的 human task 解决方案。

为了让用户参与进流程中，需要
1. 在流程中加入 human task 节点
2. 集成一个任务管理模块（例如 jBPM 提供的基于 WS-HumanTask 实现的管理）
3. 需要一个 human task 客户端来请求任务列表，处理以及完成指派给用户的任务。

下面我们详细讨论这三点。