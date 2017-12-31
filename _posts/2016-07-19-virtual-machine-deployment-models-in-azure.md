---
layout: post
title:  "Virtual Machine deployment models in Azure"
date:   2016-07-19 04:26:00 +0530
categories: 
- Azure
tags:
- ARM
- ASM
- Azure
- Classic
- Cloud
- Resource Manager
- Virtual Machines
---

If you have been using virtual machines in Azure for some time now, then you'll be familiar that there are basically 2 models to deploy virtual machines in Azure.
1. Azure classic deployment model
2. Azure Resource Manager deployment model

##### Azure classic deployment model  
* This is the legacy way of deploying virtual machines.  
* It is also known as the **Azure Service Management model (ASM)**.
* There is a **cloud service** which serves as a **container** for hosting virtual machines.
* Azure classic portal is mainly used for deploying VMs using the Azure classic deployment model.
* Please check the following [link](https://azure.microsoft.com/en-in/documentation/articles/resource-manager-deployment-model/) for more details on the design and architectural considerations of the classic deployment model.
* For automation and monitoring Azure provides the following options for the classic deployment model
  * Service Management (ASM) REST APIs
  * Azure Powershell
  * Azure CLI commands 

##### Azure Resource Manager deployment model  
* This is the new and recommended way to deploy Virtual Machines.
* It is also known as the **Azure Resource Manager model (ARM)**.
* One of the main features of this model is the concept of **Resource Group**.
* You can deploy, manage, and monitor all of the services for your solution as a group, rather than handling these services individually.
* The Resource Group helps to apply and configure **Role based access (RBAC)** at a granular level in the deployment hierarchy. This I believe is the main advantage of this model.
* Please check the following [link](https://azure.microsoft.com/en-in/documentation/articles/resource-manager-deployment-model/) for more details on the design and architectural considerations of the Resource Manager deployment model.
* For automation and monitoring Azure provides the following options for the classic deployment model
  * Resource Manager (ARM) REST APIs
  * Azure Powershell
  * Azure CLI commands 

With this background in the next article I'll explain how we can use the **Azure REST APIs** for automation purposes for both the **ASM** and the **ARM** deployment models.

