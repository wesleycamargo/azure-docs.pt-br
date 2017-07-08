---
title: "Guia de início rápido - cluster Kubernetes do Azure para Linux | Microsoft Docs"
description: "Aprenda a criar um cluster Kubernetes para contêineres do Linux no Serviço de Contêiner do Azure rapidamente com a CLI do Azure."
services: container-service
documentationcenter: 
author: anhowe
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: anhowe
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 25043f6bf5e5ab3def8563bd2c096b79706bfec1
ms.contentlocale: pt-br
ms.lasthandoff: 06/20/2017

---

# <a name="deploy-kubernetes-cluster-for-linux-containers"></a>Implantar um cluster Kubernetes para contêineres do Linux

A CLI do Azure é usada para criar e gerenciar recursos do Azure da linha de comando ou em scripts. Este guia detalha o uso da CLI do Azure para implantar um cluster [Kubernetes](https://kubernetes.io/docs/home/) no [Serviço de Contêiner do Azure](container-service-intro.md). Depois que o cluster é implantado, você se conecta a ele com a ferramenta de linha de comando `kubectl` Kubernetes e implanta o primeiro contêiner do Linux.

Este tutorial requer a CLI do Azure, versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar atualizar, confira [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-azure"></a>Fazer logon no Azure 

Faça logon na sua assinatura do Azure com o comando [az login](/cli/azure/#login) e siga as instruções na tela.

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um grupo lógico no qual os recursos do Azure são implantados e gerenciados. 

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-kubernetes-cluster"></a>Criar cluster Kubernetes
Crie um cluster Kubernetes no Serviço de Contêiner do Azure com o comando [az acs create](/cli/azure/acs#create). 

O exemplo a seguir cria um cluster denominado *myK8sCluster* com um nó Linux mestre dois nós de agente do Linux. Este exemplo cria chaves SSH, caso ainda não existam nos locais padrão. Para usar um conjunto específico de chaves, use a opção `--ssh-key-value`. Atualize o nome do cluster para algo apropriado para seu ambiente. 



```azurecli-interactive 
az acs create --orchestrator-type=kubernetes \
    --resource-group myResourceGroup \
    --name=myK8sCluster \
    --generate-ssh-keys 
```

Após alguns minutos, o comando é concluído e exibe informações sobre a implantação.

## <a name="install-kubectl"></a>Instalar kubectl

Para se conectar ao cluster do Kubernetes do computador cliente, use [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/), o cliente de linha de comando do Kubernetes. 

Se você estiver usando o Azure CloudShell, `kubectl` já estará instalado. Se você deseja instalá-lo localmente, pode usar o comando [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli).

O exemplo de CLI do Azure a seguir instala `kubectl` no seu sistema. Se você estiver executando a CLI do Azure no macOS ou Linux, talvez precise executar o comando com `sudo`.

```azurecli-interactive 
az acs kubernetes install-cli 
```

## <a name="connect-with-kubectl"></a>Conectar-se com kubectl

Para configurar `kubectl` e se conectar ao cluster Kubernetes, execute o comando [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials). O exemplo a seguir baixa a configuração de cluster para o cluster Kubernetes.

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

Para verificar a conexão ao seu cluster em seu computador, tente executar:

```azurecli-interactive
kubectl get nodes
```

`kubectl` lista os nós mestre e do agente.

```azurecli-interactive
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.5.3
k8s-agent-98dc3136-1    Ready                      5m        v1.5.3
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.5.3

```


## <a name="deploy-an-nginx-container"></a>Implantar um contêiner NGINX

Você pode executar um contêiner do Docker dentro de um *pod* Kubernetes, que contém um ou mais contêineres. 

O comando a seguir inicia o contêiner NGINX do Docker em um pod Kubernetes em um dos nós. Nesse caso, o contêiner executa o servidor Web NGINX extraído de uma imagem do [Hub do Docker](https://hub.docker.com/_/nginx/).

```azurecli-interactive
kubectl run nginx --image nginx
```
Para ver se o contêiner está em execução, execute:

```azurecli-interactive
kubectl get pods
```

## <a name="view-the-nginx-welcome-page"></a>Exibir a página de boas-vindas do NGINX
Para expor o servidor NGINX ao mundo com um endereço IP público, digite o seguinte comando:

```azurecli-interactive
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

Com este comando, o Kubernetes cria um serviço e uma [regra de balanceador de carga do Azure](container-service-kubernetes-load-balancing.md) com um endereço IP público para o serviço. 

Execute o seguinte comando para ver o status do serviço.

```azurecli-interactive
kubectl get svc
```

Inicialmente, o endereço IP é exibido como `pending`. Depois de alguns minutos, o endereço IP externo do serviço é definido:
  
```azurecli-interactive
NAME         CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE       
kubernetes   10.0.0.1       <none>          443/TCP        21h       
nginx        10.0.111.25    52.179.3.96     80/TCP         22m
```

Você pode usar um navegador da Web de sua escolha para ver a página de boas-vindas padrão do NGINX no endereço IP externo:

![Imagem de navegação até o Nginx](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


## <a name="delete-cluster"></a>Excluir cluster
Quando o cluster não for mais necessário, você poderá usar o comando [az group delete](/cli/azure/group#delete) para remover o grupo de recursos, o serviço de contêiner e todos os recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou um cluster Kubernetes, conectou-o a um `kubectl`e implantou um pod com um contêiner do NGINX. Para saber mais sobre o Serviço de Contêiner do Azure, continue no tutorial sobre o cluster Kubernetes.

> [!div class="nextstepaction"]
> [Gerenciar um cluster ACS Kubernetes](./container-service-tutorial-kubernetes-prepare-app.md)

