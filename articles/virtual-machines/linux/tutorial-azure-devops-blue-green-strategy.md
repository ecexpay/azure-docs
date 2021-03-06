---
title: Tutorial - Configure canary deployments for Azure Linux Virtual Machines
description: In this tutorial you will learn how to set up continuous deployment (CD) pipeline that updates a group of Azure virtual machines using the blue-green deployment strategy
author: moala
manager: jpconnock
tags: azure-devops-pipelines

ms.assetid: 
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 4/10/2020
ms.author: moala
ms.custom: devops

#Customer intent: As a developer, I want to learn about CI/CD features in Azure so that I can use devops services like Azure Pipelines to build and deploy my applications automatically.
---

# Tutorial - Configure Blue-Green deployment strategy for Azure Linux Virtual Machines


## IaaS - Configure CI/CD 
Azure Pipelines provides a complete, fully featured set of CI/CD automation tools for deployments to virtual machines. You can configure a continuous delivery pipeline for an Azure VM directly from the Azure portal. This document contains the steps associated with setting up a CI/CD pipeline that uses the blue-green strategy for multi-machine deployments. You can also take a look at other strategies like [rolling](https://aka.ms/AA7jlh8) and [canary](https://aka.ms/AA7jdrz), which are supported out-of-box from Azure portal. 

 
 **Configure CI/CD on Virtual Machines**

Virtual machines can be added as targets to a [deployment group](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) and can be targeted for multi-machine updates. Once deployed, viewing **Deployment History** within a deployment group provides traceability from VM to the pipeline and then to the commit. 
 
  
**Blue-Green Deployments**: A Blue-Green deployment reduces downtime by having an identical standby environment. At any time one of the environments is live. As you prepare for a new release, you complete final stage of testing in the green environment. Once the software is working in the green environment, switch the traffic so that all incoming requests go to the green environment - the blue environment is now idle.
You can configure Blue-Green deployments to your “**virtual machines**” from the Azure portal using the continuous delivery option. 

Here is the step-by-step walkthrough.

1. Sign in to your Azure portal and navigate to a virtual machine 
2. In the VM left pane, go **Continuous delivery**. Then click **Configure**. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. In the configuration panel, click **Azure DevOps Organization** to select an existing account or create one. Then select the project under which you would like to configure the pipeline.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. A deployment group is a logical set of deployment target machines that represent the physical environments; for example, "Dev", "Test", "UAT", and "Production". You can create a new deployment group or select an existing deployment group. 
5. Select the build pipeline that publishes the package to be deployed to the virtual machine. Note that the published package should have a deployment script _deploy.ps1_ or _deploy.sh_ in `deployscripts` folder at the package root. This deployment script will be executed by Azure DevOps pipeline at run time.
6. Select the deployment strategy of your choice. Select **Blue-Green**.
7. Add a "blue" or "green" tag to the VMs that are to be part of Blue-Green deployments. If the VM is for a standby role, then you should tag it as "green", otherwise tag it as "blue".
![AzDevOps_bluegreen_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

8. Click **OK** to configure the continuous delivery pipeline. You will now have a continuous delivery pipeline configured to deploy to the virtual machine.
![AzDevOps_bluegreen_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)


9. Click on  **Edit** release pipeline in Azure DevOps to see the pipeline configuration. The pipeline consists of three phases. The first phase is a deployment group phase and deploys to VMs that are tagged as _green_ (standby VMs). The second phase pauses the pipeline and waits for manual intervention to resume the run. Once a user is satisfied that deployment is stable, they can now redirect the traffic to _green_ VMs and resume the pipeline run which will then swap _blue_ and _green_ tags in the VMs. This makes sure that the VMs that have older application version are tagged as _green_ and are deployed to in the next pipeline run.
![AzDevOps_bluegreen_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)


10. The Execute Deploy Script task will by default execute the deployment script _deploy.ps1_ or _deploy.sh_ in the `deployscripts` folder at the root directory of published package. Make sure that the selected build pipeline publishes this in the root folder of package.
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)




## Other deployment strategies
- [Configure Rolling Deployment Strategy](https://aka.ms/AA7jlh8)
- [Configure Canary Deployment Strategy](https://aka.ms/AA7jdrz)

## Azure DevOps project 
Get started with Azure more easily than ever.
 
With DevOps Projects, start running your application on any Azure service in just three steps: select an application language, a runtime, and an Azure service.
 
[Learn more](https://azure.microsoft.com/features/devops-projects/ ).
 
## Additional resources 
- [Deploy to Azure Virtual Machines using DevOps project](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implement continuous deployment of your app to an Azure Virtual Machine Scale Set](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
