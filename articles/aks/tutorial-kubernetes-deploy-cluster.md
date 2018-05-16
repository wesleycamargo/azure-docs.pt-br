---
title: Tutorial do Kubernetes no Azure – Implantar cluster
description: Tutorial do AKS - Implantar cluster
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/24/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: e4e4e7c3956f396024513a4c48722dfb86e89151
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Tutorial: implantar um cluster do Serviço de Kubernetes do Azure (AKS)

Kubernetes fornece uma plataforma distribuída para aplicativos em contêineres. Com o AKS, o provisionamento de um cluster Kubernetes pronto para produção é simples e rápido. Neste tutorial, a terceira parte de oito, um cluster Kubernetes é implantado no AKS. As etapas concluídas incluem:

> [!div class="checklist"]
> * Implantação de um cluster AKS Kubernetes
> * Instalação da CLI Kubernetes (kubectl)
> * Configuração de kubectl

Nos tutoriais subsequentes, o aplicativo Azure Vote é implantado no cluster, dimensionado e atualizado, e o Log Analytics é configurado para monitorar o cluster Kubernetes.

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, uma imagem de contêiner foi criada e carregada em uma instância do Registro de Contêiner do Azure. Se você ainda não realizou essas etapas e deseja continuar acompanhando, retorne ao [Tutorial 1 – Criar imagens de contêiner][aks-tutorial-prepare-app].

## <a name="enable-aks-preview"></a>Habilitar versão prévia do AKS

Enquanto o AKS está na versão prévia, a criação de novos clusters requer um sinalizador de recurso em sua assinatura. Você pode solicitar esse recurso para qualquer número de assinaturas que deseje usar. Use o comando `az provider register` para registrar o provedor do AKS:

```azurecli
az provider register -n Microsoft.ContainerService
```

Após o registro, você estará pronto para criar um cluster do Kubernetes com o AKS.

## <a name="create-kubernetes-cluster"></a>Criar cluster Kubernetes

O exemplo a seguir cria um cluster denominado `myAKSCluster` em um Grupo de recursos denominado `myResourceGroup`. Este Grupo de recursos foi criado no [tutorial anterior][aks-tutorial-prepare-acr].

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

Após alguns minutos, a implantação é concluída e retorna as informações formatadas em JSON sobre a implantação do AKS.

## <a name="install-the-kubectl-cli"></a>Instalar a CLI kubectl

Para se conectar ao cluster Kubernetes do computador cliente, use [kubectl][kubectl], o cliente de linha de comando do Kubernetes.

Se você estiver usando o Azure CloudShell, o kubectl já estará instalado. Se desejar instalá-lo localmente, execute este comando:

```azurecli
az aks install-cli
```

## <a name="connect-with-kubectl"></a>Conectar-se com kubectl

Para configurar o kubectl e se conectar ao cluster Kubernetes, execute este comando:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a conexão ao seu cluster, execute o comando [kubectl get nodes][kubectl-get].

```azurecli
kubectl get nodes
```

Saída:

```
NAME                          STATUS    AGE       VERSION
k8s-myAKSCluster-36346190-0   Ready     49m       v1.7.9
```

Ao concluir o tutorial, você tem um cluster AKS pronto para cargas de trabalho. Em tutoriais subsequentes, um aplicativo de multicontêiner é implantado nesse cluster, escalado horizontalmente, atualizado e monitorado.

## <a name="configure-acr-authentication"></a>Configurar a autenticação do ACR

A autenticação precisa ser configurada entre o cluster do AKS e o registro ACR. Isso envolve conceder à identidade do AKS os direitos adequados para efetuar pull de imagens do registro ACR.

Primeiro, obtenha a ID da entidade de serviço configurada para o AKS. Atualize o nome do grupo de recursos e o nome de cluster do AKS para corresponder ao seu ambiente.

```azurecli
CLIENT_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId" --output tsv)
```

Obtenha a ID de recurso do registro ACR. Atualize o nome de registro do registro ACR e o grupo de recursos para o grupo de recursos onde se encontra o registro ACR.

```azurecli
ACR_ID=$(az acr show --name <acrName> --resource-group myResourceGroup --query "id" --output tsv)
```

Crie a atribuição de função, que concede o acesso apropriado.

```azurecli
az role assignment create --assignee $CLIENT_ID --role Reader --scope $ACR_ID
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, um cluster Kubernetes foi implantado no AKS. As etapas a seguir foram concluídas:

> [!div class="checklist"]
> * Implantação de um cluster AKS Kubernetes
> * Instalação da CLI Kubernetes (kubectl)
> * Configuração de kubectl

Avance para o próximo tutorial para saber mais sobre como executar o aplicativo no cluster.

> [!div class="nextstepaction"]
> [Implantar o aplicativo no Kubernetes][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
