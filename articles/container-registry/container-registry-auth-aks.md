---
title: "Autenticar com o Registro de Contêiner do Azure do Serviço de Contêiner do Azure"
description: "Saiba como fornecer acesso a imagens em seu registro de contêiner particular do Serviço de Contêiner do Azure usando uma entidade de serviço do Azure Active Directory."
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 1/12/2018
ms.author: nepeters
ms.openlocfilehash: d6f6688011ddebe2b486bb6ae00f1f3e095a931d
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2018
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-service"></a>Autenticar com o Registro de Contêiner do Azure do Serviço de Contêiner do Azure

Quando você estiver usando o ACR (Registro de Contêiner do Azure) com o AKS (Serviço de Contêiner do Azure), um mecanismo de autenticação precisará ser estabelecido. Este documento detalha as configurações recomendadas para a autenticação entre esses dois serviços do Azure.

## <a name="grant-aks-access-to-acr"></a>Conceder acesso do AKS ao ACR

Quando um cluster do AKS é criado, uma entidade de serviço também é criada para gerenciar o operabilidade do cluster com recursos do Azure. Essa entidade de serviço também pode ser usada para autenticação com um registro ACR. Para fazer isso, uma atribuição de função precisa ser criada para conceder o acesso de leitura da entidade de serviço para o recurso ACR. 

O exemplo a seguir pode ser usado para concluir essa operação.

```bash
#!/bin/bash

AKS_RESOURCE_GROUP=myAKSResourceGroup
AKS_CLUSTER_NAME=myAKSCluster
ACR_RESOURCE_GROUP=myACRResourceGroup
ACR_NAME=myACRRegistry

# Get the id of the service principal configured for AKS
CLIENT_ID=$(az aks show --resource-group $AKS_RESOURCE_GROUP --name $AKS_CLUSTER_NAME --query "servicePrincipalProfile.clientId" --output tsv)

# Get the ACR registry resource id
ACR_ID=$(az acr show --name $ACR_NAME --resource-group $ACR_RESOURCE_GROUP --query "id" --output tsv)

# Create role assignment
az role assignment create --assignee $CLIENT_ID --role Reader --scope $ACR_ID
```

## <a name="access-with-kubernetes-secret"></a>Acesso com segredo do Kubernetes

Em alguns casos, a entidade de serviço que está sendo usada pelo AKS não pode ser limitada ao registro do ACR. Nesses casos, você pode criar uma entidade de serviço exclusiva e definir o escopo apenas para o registro do ACR.

O script a seguir pode ser usado para criar a entidade de serviço. 

```bash
#!/bin/bash

ACR_NAME=myacrinstance
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate the ACR login server and resource id. 
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create a contributor role assignment with a scope of the ACR resource. 
SP_PASSWD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME --role Reader --scopes $ACR_REGISTRY_ID --query password --output tsv)

# Get the service principle client id.
CLIENT_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output used when creating Kubernetes secret.
echo "Service principal ID: $CLIENT_ID"
echo "Service principal password: $SP_PASSWD"
```

Agora, as credenciais da entidade de serviço podem ser armazenadas em um [segredo de pull de imagem][image-pull-secret] do Kubernetes e referidas ao executar contêineres em um cluster do AKS. 

O comando a seguir cria o segredo do Kubernetes. Substitua o nome do servidor pelo servidor de logon do ACR, o nome de usuário pela ID da entidade de serviço e a senha pela senha da entidade de serviço.

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> 
```

O segredo do Kubernetes pode ser usado em uma implantação de pod usando o parâmetro `ImagePullSecrets`. 

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: acr-auth-example
spec:
  template:
    metadata:
      labels:
        app: acr-auth-example
    spec:
      containers:
      - name: acr-auth-example
        image: myacrregistry.azurecr.io/acr-auth-example
      imagePullSecrets:
      - name: acr-auth
```

<!-- LINKS - external -->
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[image-pull-secret]: https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets
