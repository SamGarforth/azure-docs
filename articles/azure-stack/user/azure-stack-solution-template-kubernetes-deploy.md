---
title: Deploy a Kubernetes Cluster to Azure Stack | Microsoft Docs
description: Learn how to deploy a Kubernetes Cluster to Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''

ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: mabrigg
ms.reviewer: waltero

---

# Deploy a Kubernetes cluster to Azure Stack

*Applies to: Azure Stack integrated systems and Azure Stack Development Kit*

> [!note]  
> The Azure Container Services (ACS) Kubernetes on Azure Stack is in private preview. Your Azure Stack operator will need to request access to the Kubernetes Marketplace item needed to perform the instructions in this article.

The following article looks at using an Azure Resource Manager solution template to deploy and provision the resources for Kubernetes in a single, coordinated operation. You will need to collect the required information about your Azure Stack installation, generate the template, and then deploy to your cloud.

## Kubernetes and containers

You can install Azure Container Services (ACS) Kubernetes on Azure Stack. [Kubernetes](https://kubernetes.io) is an open-source system for automating deployment, scaling, and managing of applications in containers. A [container](https://www.docker.com/what-container) is contained in an image, similar to a VM. Unlike a VM the container image is just includes the resources it needs to run an application, such as the code, runtime to execute the code, specific libraries, and settings.

You can use Kubernetes to:

- Develop massively scalable, upgradable, applications that can be deployed in seconds. 
- Simplify the design of your application and improve its reliability by different Helm applications. [Helm](https://github.com/kubernetes/helm) is an open-source packaging tool that helps you install and manage the lifecycle of Kubernetes applications.
- Easily monitor and diagnose the health of your applications with scale and upgrade functionality.

## Prerequisites 

To get started, make sure you have the right permissions and that your Azure Stack is ready.

1. Verify that you can create applications in your Azure Active Directory (Azure AD) tenant. You need these permissions for the Kubernetes deployment.

    For instructions on checking your permissions, see [Check Azure Active Directory permissions](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#check-azure-active-directory-permissions).

2. Generate an SSH public and private key pair to sign in to the Linux VM on Azure Stack. You will need the public key when creating the cluster.

    For instructions on generating a key, see [SSH Key Generation](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation).

3. Check that you have a valid subscription in your Azure Stack tenant portal, and that you have enough public IP addresses available to add new applications.

## Create a service principal in Azure AD

1. Sign in to the global [Azure portal](http://www.poartal.azure.com).
2. Check that you signed in using the Azure AD tenant associated with Azure Stack.
3. Create an Azure AD application.

    a. Select **Azure Active Directory** > **+ App Registrations** > **New Application Registration**.

    b. Enter a **Name** of the application.

    c. Select **Web app / API**

    d. Enter `http://localhost` for the **Sign-on URL**.

    c. Click **Create**

4. Make note of the **Application ID**. You will need the ID when creating the cluster. The ID is referenced as **Service Principal Client ID**.

5. Select **Settings** > **Keys**.

    a. Enter the **Description**.

    b. Select **Never expires** for **Expires**.

    c. Select **Save**. Make note the key string. You will need the key string when creating the cluster. The key is references as the **Service Principal Client Secret**.



## Give the service principal access

Give the service principal access to your subscription so that the principal can create resources.

1.  Sign in to the [Administration portal](https://adminportal.local.azurestack.external).

2. Select **More services** > **User subscriptions** > **+ Add**.

3. Select the subscription that you created.

4. Select **Access control (IAM)** > Select **+ Add**.

5. Select the **Owner** role.

6. Select the application name created for your service principal. You may have to type the name in the search box.

7. Click **Save**.

## Deploy a Kubernetes Cluster

1. Open the [Azure Stack portal](https://portal.local.azurestack.external).

2. Select **+New** > **Compute** > **Kubernetes Cluster**.

    ![Deploy Solution Template](../media/azure-stack-solution-template-kubernetes-cluster-add/azure-stack-kubernetes-cluster-solution-template.png)

3. Select **Parameters** in the Deploy Solution Template.

    ![Deploy Solution Template](../media/azure-stack-solution-template-kubernetes-cluster-add/azure-stack-kubernetes-cluster-solution-template-parameters.png)

2. Enter the **Linux administrative user name**. User name for the Linux Virtual Machines that are part of the Kubernetes cluster and DVM.

3. Enter the **SSH public key** used for authorization to all Linux machines created as part of the Kubernetes cluster and DVM.

4. Enter the **tenant endpoint**. This is the Azure Resource Manager endpoint to connect to create the resource group for the Kubernetes cluster. You will need to get the endpoint from your Azure Stack operator for an integrated system. For the Azure Stack Development Kit (ASDK), you can use `https://management.local.azurestack.external`.

5. Enter the **tenant ID** for the tenant. If you need help finding this value, see [Get tenant ID](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id). 

6. Enter the **master profile DNS prefix** that is unique to the region. This must be a region-unique name, such as `k8s-12345`. Try to chose it same as the resource group name as best practice.

    > [!note]  
    > For each cluster, use a new and unique master profile DNS prefix.

7. Enter the number of agents in the cluster. This value is referred to as the **Agent Pool Profile Count**. There can be from 1 to 32

8. Enter the **service principal application ID** This is used by the Kubernetes Azure cloud provider.

9. Enter the **service principal client secret** that you created when creating service principal application.

10. Enter the **Kubernetes Azure Cloud Provider Version**. This is the version for the Kubernetes Azure provider. Azure Stack releases a custom Kubernetes build for each Azure Stack version.

12. Select **OK**.

### Specify the solution values

1. Select the **Subscription**.

2. Enter the name of a new resource group or select an existing resource group. The resource name needs to be alphanumeric and lowercase.

3. Enter the location of the resource group, such as **local**.

4. Select **Create.**

## Connect to your cluster

You are now ready to connect to your cluster. You will need the **kubectl**, the Kubernetes command-line client. You can find instructions on connecting to and managing the cluster in Azure Container Services documentation.   

For instructions, see [Connect to the cluster](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough#connect-to-the-cluster).

## Next steps

[Add a Kubernetes Cluster to the Marketplace (for the Azure Stack operator)](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes on Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)