---
title: "Tutorial do Serviço de Contêiner do Azure – implantar cluster | Microsoft Docs"
description: "Tutorial do Serviço de Contêiner do Azure – implantar cluster"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, Contêineres, Microsserviços, Kubernetes, DC/SO, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2017
ms.author: nepeters
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 13d1582112468f55f473ce1b77d5d58213f0e6af
ms.contentlocale: pt-br
ms.lasthandoff: 07/25/2017

---

# <a name="deploy-a-kubernetes-cluster-in-azure-container-service"></a>Implantar um cluster Kubernetes no Serviço de Contêiner do Azure

Kubernetes fornece uma plataforma distribuída para aplicativos em contêineres. Com o Serviço de Contêiner do Azure, o provisionamento de um cluster Kubernetes pronto para produção é simples e rápido. Neste tutorial, parte 3 de 7, um cluster Kubernetes do Serviço de Contêiner do Azure é implantado. As etapas concluídas incluem:

> [!div class="checklist"]
> * Implantação de um cluster ACS Kubernetes
> * Instalação da CLI Kubernetes (kubectl)
> * Configuração de kubectl

Nos tutoriais subsequentes, o aplicativo Azure Vote é implantado no cluster, escalado, atualizado e o Operations Management Suite é configurado para monitorar o cluster Kubernetes.

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, uma imagem de contêiner foi criada e carregada em uma instância do Registro de Contêiner do Azure. Se você ainda não realizou essas etapas e deseja continuar acompanhando, retorne ao [Tutorial 1 – Criar imagens de contêiner](./container-service-tutorial-kubernetes-prepare-app.md).

## <a name="create-kubernetes-cluster"></a>Criar cluster Kubernetes

No [tutorial anterior](./container-service-tutorial-kubernetes-prepare-acr.md), um grupo de recursos denominado *myResourceGroup* foi criado. Se você não tiver feito isso, crie esse grupo de recursos agora.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Crie um cluster Kubernetes no Serviço de Contêiner do Azure com o comando [az acs create](/cli/azure/acs#create). 

O exemplo a seguir cria um cluster denominado *myK8sCluster* com um nó Linux mestre e três nós de agente do Linux.

```azurecli-interactive 
az acs create --orchestrator-type=kubernetes --resource-group myResourceGroup --name=myK8SCluster --generate-ssh-keys 
```

Após vários minutos, o comando é concluído e retorna as informações formatadas do json sobre a implantação do ACS.

## <a name="install-the-kubectl-cli"></a>Instalar a CLI kubectl

Para se conectar ao cluster Kubernetes do computador cliente, use [kubectl](https://kubernetes.io/docs/user-guide/kubectl/), o cliente de linha de comando do Kubernetes. 

Se você estiver usando o Azure CloudShell, `kubectl` já estará instalado. Se você deseja instalá-lo localmente, você pode usar o comando [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli).

Se estiver executando em Linux ou macOS, você precisará executar com sudo. No Windows, certifique-se de que shell foi executado como administrador.

```azurecli-interactive 
az acs kubernetes install-cli 
```

No Windows, a instalação padrão é *c:\program files (x86)\kubectl.exe*. Talvez seja necessário adicionar esse arquivo para o caminho do Windows. 

## <a name="connect-with-kubectl"></a>Conectar-se com kubectl

Para configurar `kubectl` e se conectar ao cluster Kubernetes, execute o comando [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials).

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8SCluster
```

Para verificar a conexão ao seu cluster, execute o comando [kubectl get nodes](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get).

```azurecli-interactive
kubectl get nodes
```

Saída:

```bash
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.6.2
k8s-agent-98dc3136-1    Ready                      5m        v1.6.2
k8s-agent-98dc3136-2    Ready                      5m        v1.6.2
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.6.2
```

Ao concluir o tutorial, você tem um cluster ACS Kubernetes pronto para cargas de trabalho. Em tutoriais subsequentes, um aplicativo de multicontêiner é implantado nesse cluster, escalado horizontalmente, atualizado e monitorado.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, um cluster Kubernetes do Serviço de Contêiner do Azure foi implantado. As etapas a seguir foram concluídas:

> [!div class="checklist"]
> * Implantação de um cluster ACS Kubernetes
> * Instalação da CLI Kubernetes (kubectl)
> * Configuração de kubectl

Avance para o próximo tutorial para saber mais sobre como executar o aplicativo no cluster.

> [!div class="nextstepaction"]
> [Implantar o aplicativo no Kubernetes](./container-service-tutorial-kubernetes-deploy-application.md)
