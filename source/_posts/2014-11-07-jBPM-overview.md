---
title: jBPM 基础
date: 2014-11-07 09:46:00
categories:
  - 工具
tags: 
  - jbpm
---
了解并部署好jBPM环境是使用的基本，但最重要的是需要了解jBPM的Core Engine API.

## Overview
这里先了解装载流程以及执行流程所需要的API，至于如何定义流程，需要专门去研究BPMN2.0.

为了与一个流程进行交互，需要建立起一个会话。但是创建一个session, 需要首先创建一个knowledge base, 其包含有整个流程的所有定义，这样session通过knowledge base就可以获取一个process的所有信息，从而进行交互。

当你成功创建一个session后，就可以用它来启动流程。每当你启动一个process，对应的就创建了一个流程实例。

例如你准备写一个应用来处理销售订单。为了处理一个订单，你可能需要定义一个或者多个流程，当启动应用时，首先你需要创建一个knowledge base，其包含了这些流程定义；接着需要创建一个基于knowledge base的session；当有一个新订单时，启动一个新的process实例来服务它，由它可以随时了解这个订单的状态。

## knowledge base
多个sessions可以共享一个knowledge base，所以通常knowledge base只在应用启动时创建一次。但是knowledge base 可以被动态的改变，你可以在运行时新增或者删除某些processes.

## session
session是基于knowledge base创建的，用于执行processes，以及与process engine进行交互。可以根据需要创建多个独立的sessions，没有限制。但是大多数情况下在一个应用中我们只创建一个session就够用了。如果你想有多个独立的运行中的unit（例如你想让A客户启动的所有processes与B客户拥有的processes独立开来），或者因为可扩展性的原因，这些情况下可以使用多个sessions。如果你还是不确定，那就干脆只创建一个knowledge base，其包含所有processes的定义；只创建一个session，其用来执行你的所有processes。

综上所述：jBPM API主要用于（1）创建一个包含所有流程定义的knowledge base，然后去（2）创建一个session来启动新的processes实例，调用已存在的processes，注册监听器，等等。
