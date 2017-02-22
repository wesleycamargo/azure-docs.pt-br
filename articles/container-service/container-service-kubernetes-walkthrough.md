---
title: "Guia de início rápido do cluster Kubernetes no Azure | Microsoft Docs"
description: "Implantar e começar a usar um cluster Kubernetes no Serviço de Contêiner do Azure"
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
ms.date: 11/15/2016
ms.author: anhowe
translationtype: Human Translation
ms.sourcegitcommit: 7ed8fb75f057d5a7cfde5436e72e8fec52d07156
ms.openlocfilehash: f3b2fc301bf7083f192c0ec872c4e032472eef97


---

# <a name="microsoft-azure-container-service-engine---kubernetes-walkthrough"></a>Mecanismo do Serviço de Contêiner do Microsoft Azure – Passo a passo do Kubernetes

## <a name="prerequisites"></a>Pré-requisitos
Este passo a passo presume que você tenha a [ferramenta de linha de comando 'azure-cli'](https://github.com/azure/azure-cli#installation) instalada e que tenha criado a [chave pública SSH](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) no `~/.ssh/id_rsa.pub`.

## <a name="overview"></a>Visão geral

As instruções a seguir criarão um cluster Kubernetes com um mestre e dois nós de trabalho.
O mestre serve a API REST do Kubernetes.  Os nós de trabalho estão agrupados em um conjunto de disponibilidade do Azure e executam os seus contêineres. Todas as VMs estão na mesma VNET privada e são totalmente acessíveis entre si.

> [!NOTE]
> O suporte a Kubernetes no Serviço de Contêiner do Azure está atualmente em visualização.
>

A imagem a seguir mostra a arquitetura de um cluster do serviço de contêiner com um mestre e dois agentes:

![Imagem do cluster Kubernetes no Azure](media/container-service-kubernetes-walkthrough/kubernetes.png)

## <a name="creating-your-kubernetes-cluster"></a>Criando o cluster Kubernetes

### <a name="create-a-resource-group"></a>Criar um grupos de recursos
Para criar o cluster, você precisa primeiro criar um grupo de recursos em um local específico. Você pode fazer isso com:
```console
RESOURCE_GROUP=my-resource-group
LOCATION=westus
az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="create-a-cluster"></a>Criar um cluster
Quando você tiver um grupo de recursos, é possível criar um cluster nesse grupo com:
```console
DNS_PREFIX=some-unique-value
SERVICE_NAME=any-acs-service-name
az acs create --orchestrator-type=kubernetes --resource-group $RESOURCE_GROUP --name=$SERVICE_NAME --dns-prefix=$DNS_PREFIX
```

> [!NOTE]
> azure-cli carregará `~/.ssh/id_rsa.pub` nas VMs do Linux.
>

Quando esse comando for concluído, você deverá ter um cluster Kubernetes em funcionamento.

### <a name="configure-kubectl"></a>Configurar kubectl
`kubectl` é o cliente de linha de comando do Kubernetes.  Se você ainda não o tiver instalado, você pode instalá-lo com:

```console
az acs kubernetes install-cli
```

Após a instalação do `kubectl`, executar o comando abaixo baixará a configuração do cluster kubernetes mestre para o arquivo ~/.kube/config
```console
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$SERVICE_NAME
```

Agora você deve estar pronto para acessar o cluster em seu computador. Experimente executar:
```console
kubectl get nodes
```

E validar que é possível ver os computadores no cluster.

## <a name="create-your-first-kubernetes-service"></a>Criar seu Primeiro Serviço Kubernetes

Depois de concluir este passo a passo, você saberá como:
 * implantar um aplicativo Docker e expor ao mundo,
 * usar `kubectl exec` para executar comandos em um contêiner, 
 * e acessar o painel do Kubernetes.

### <a name="start-a-simple-container"></a>Iniciar um contêiner simples
Você pode executar um contêiner simples (nesse caso o servidor Web `nginx`), executando:

```console
kubectl run nginx --image nginx
```

Esse comando inicia o contêiner do Docker nginx em um pod em um dos nós.

Você pode executar
```console
kubectl get pods
```

para ver o contêiner em execução.

### <a name="expose-the-service-to-the-world"></a>Expor o serviço para o mundo
Para expor o serviço para o mundo.  Criar um `Service` Kubernetes do tipo `LoadBalancer`:

```console
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

Agora, isso levará o Kubernetes a criar um Azure Load Balancer com um IP público. A alteração demora cerca de 2 a 3 minutos para se propagar ao balanceador de carga.

Para observar o serviço alterar de "pendente" para um tipo de IP externo:
```console
watch 'kubectl get svc'
```

  ![Imagem da ação de assistir a transição de pendente para IP externo](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

Depois que visualizar o IP externo, você poderá navegar até ele no seu navegador:

  ![Imagem de navegação para nginx](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


### <a name="browse-the-kubernetes-ui"></a>Navegar na interface do usuário do Kubernetes
Para ver a interface da Web do Kubernetes, você pode usar:

```console
kubectl proxy
```
Isso executa um proxy autenticado simples no localhost, que você pode usar para exibir a [interface do usuário do kubernetes](http://localhost:8001/ui)

![Imagem do painel de Kubernetes](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

### <a name="remote-sessions-inside-your-containers"></a>Sessões remotas em seus contêineres
O Kubernetes permite executar comandos em um contêiner remoto do Docker que está execução no seu cluster.

```console
# Get the name of your nginx pod
kubectl get pods
```

Usando seu nome pod, é possível executar um comando remoto no seu pod.  Por exemplo:
```console
kubectl exec nginx-701339712-retbj date
```

Você também pode obter uma sessão totalmente interativa usando os sinalizadores `-it`:

```console
kubectl exec nginx-701339712-retbj -it bash
```

![Imagem do curl para podIP](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)


## <a name="details"></a>Detalhes
### <a name="installing-the-kubectl-configuration-file"></a>Instalando o arquivo de configuração kubectl
Quando você executou `az acs kubernetes get-credentials`, o arquivo de configuração kube para acesso remoto foi armazenado no diretório base ~/.kube/config.

Se você precisar baixá-lo diretamente, será possível usar `ssh` no Linux ou no OS X ou `Putty` no Windows:

#### <a name="windows"></a>Windows
Para usar pscp de [putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).  Certifique-se de que seu certificado esteja exposto por meio do [pageant](https://github.com/Azure/acs-engine/blob/master/docs/ssh.md#key-management-and-agent-forwarding-with-windows-pageant):
  ```
  # MASTERFQDN is obtained in step1
  pscp azureuser@MASTERFQDN:.kube/config .
  SET KUBECONFIG=%CD%\config
  kubectl get nodes
  ```

#### <a name="os-x-or-linux"></a>OS X ou Linux:
  ```
  # MASTERFQDN is obtained in step1
  scp azureuser@MASTERFQDN:.kube/config .
  export KUBECONFIG=`pwd`/config
  kubectl get nodes
  ```
## <a name="learning-more"></a>Para saber mais

### <a name="azure-container-service"></a>Serviço de Contêiner do Azure

1. [Documentação do Serviço de Contêiner do Azure](https://azure.microsoft.com/en-us/documentation/services/container-service/)
2. [Mecanismo de Software Livre do Serviço de Contêiner do Azure](https://github.com/azure/acs-engine)

### <a name="kubernetes-community-documentation"></a>Documentação de Comunidade do Kubernetes

1. [Kubernetes Bootcamp](https://katacoda.com/embed/kubernetes-bootcamp/1/) – Mostra como implantar, dimensionar, atualizar e depurar aplicativos em contêineres.
2. [Guia do Usuário do Kubernetes](http://kubernetes.io/docs/user-guide/) – fornece informações sobre como executar programas em um cluster Kubernetes existente.
3. [Exemplos de Kubernetes](https://github.com/kubernetes/kubernetes/tree/master/examples) – Fornecem vários exemplos de como executar aplicativos reais com Kubernetes.



<!--HONumber=Jan17_HO4-->


