---
title: Autenticar com o Registro de Contêiner do Azure do Serviço de Kubernetes do Azure
description: Saiba como fornecer acesso a imagens em seu registro de contêiner particular do Serviço de Kubernetes do Azure usando uma entidade de serviço do Azure Active Directory.
services: container-service
author: mmacy
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 08/08/2018
ms.author: marsma
ms.openlocfilehash: c9ade4d61a1b95d5041a13f9436f0d02a7951758
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46981637"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Autenticar com o Registro de Contêiner do Azure do Serviço de Kubernetes do Azure

Quando você estiver usando o ACR (Registro de Contêiner do Azure) com o AKS (Serviço de Kubernetes do Azure), um mecanismo de autenticação precisará ser estabelecido. Este artigo detalha as configurações recomendadas para a autenticação entre esses dois serviços do Azure.

## <a name="grant-aks-access-to-acr"></a>Conceder acesso do AKS ao ACR

Quando você cria um cluster do AKS, o Azure também cria uma entidade de serviço para dar suporte à operabilidade do cluster com outros recursos do Azure. Use essa entidade de serviço gerada automaticamente para autenticação com um registro de ACR. Para fazer isso, você precisará criar uma [atribuição de função](../role-based-access-control/overview.md#role-assignments) do Azure AD que concede acesso à entidade de serviço do cluster para o registro de contêiner.

Use o script a seguir para conceder à entidade de serviço gerada pelo AKS acesso a um registro de contêiner do Azure. Modifique as variáveis `AKS_*` e `ACR_*` de seu ambiente antes de executar o script.

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

Em alguns casos, você não poderá atribuir a função necessária à entidade de serviço do AKS autogerada por meio da concessão de acesso ao ACR. Por exemplo, devido ao modelo de segurança da sua organização, talvez você não tenha permissão suficiente em seu diretório do Azure AD para atribuir uma função à entidade de serviço gerada pelo AKS. Nesse caso, você pode criar uma nova entidade de serviço e conceder a ela acesso ao registro de contêiner usando um segredo de pull de imagem do Kubernetes.

Use o script a seguir para criar uma nova entidade de serviço (você usará as credenciais para o segredo de pull de imagem do Kubernetes). Modifique a variável `ACR_NAME` de seu ambiente antes de executar o script.

```bash
#!/bin/bash

ACR_NAME=myacrinstance
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate the ACR login server and resource id.
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create a 'Reader' role assignment with a scope of the ACR resource.
SP_PASSWD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME --role Reader --scopes $ACR_REGISTRY_ID --query password --output tsv)

# Get the service principal client id.
CLIENT_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output used when creating Kubernetes secret.
echo "Service principal ID: $CLIENT_ID"
echo "Service principal password: $SP_PASSWD"
```

Agora, você pode armazenar as credenciais da entidade de serviço em um [segredo de pull de imagem][image-pull-secret] do Kubernetes, consultado por seu cluster AKS ao executar contêineres.

Use o seguinte comando **kubectl** para criar o segredo do Kubernetes. Substitua `<acr-login-server>` pelo nome totalmente qualificado do registro de contêiner do Azure (está no formato "acrname.azurecr.io"). Substitua `<service-principal-ID>` e `<service-principal-password>` pelos valores obtidos pela execução do script anterior. Substitua `<email-address>` por qualquer endereço de email válido.

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> --docker-email <email-address>
```

Agora você pode usar o segredo do Kubernetes em implantações de pod especificando seu nome (neste caso, "acr-auth") no parâmetro `imagePullSecrets`:

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
