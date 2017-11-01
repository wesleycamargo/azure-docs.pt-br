---
title: "Tutorial do Kubernetes no Azure – implantar aplicativo | Microsoft Docs"
description: "Tutorial de AKS – implantar aplicativo"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: "Docker, Contêineres, Microsserviços, Kubernetes, DC/SO, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 73b943709fb3846058d4b1c09bc76b09460855ed
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2017
---
# <a name="run-applications-in-azure-container-service-aks"></a>Executar aplicativos no AKS (Serviço de Contêiner do Azure)

Neste tutorial, parte quatro de oito, um aplicativo de exemplo é implantado em um cluster Kubernetes. As etapas concluídas incluem:

> [!div class="checklist"]
> * Atualizar os arquivos de manifesto do Kubernetes
> * Executar um aplicativo no Kubernetes
> * Testar o aplicativo

Nos tutoriais subsequentes, esse aplicativo é escalado horizontalmente, atualizado e o Operations Management Suite configurado para monitorar o cluster Kubernetes.

Este tutorial assume uma compreensão básica dos conceitos de Kubernetes; para obter informações detalhadas sobre Kubernetes consulte a [documentação do Kubernetes](https://kubernetes.io/docs/home/).

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, um aplicativo foi empacotado em uma imagem de contêiner, essa imagem foi carregada no Registro de Contêiner do Azure e um cluster Kubernetes foi criado. 

Para concluir este tutorial, você precisa do arquivo de manifesto do Kubernetes `azure-vote-all-in-one-redis.yml` pré-criado. Esse arquivo foi baixado com o código-fonte do aplicativo em um tutorial anterior. Verifique se você clonou repositório e se você alterou os diretórios para o repositório clonado.

Se você ainda não realizou essas etapas e deseja continuar acompanhando, retorne ao [Tutorial 1 – Criar imagens de contêiner](./tutorial-kubernetes-prepare-app.md). 

## <a name="update-manifest-file"></a>Atualizar arquivo de manifesto

Neste tutorial, o ACR (Registro de Contêiner do Azure) foi usado para armazenar uma imagem de contêiner. Antes de executar o aplicativo, o nome do servidor de logon do ACR precisa ser atualizado no arquivo de manifesto do Kubernetes.

Obter o nome do servidor de logon ACR com o comando [az acr list](/cli/azure/acr#list).

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

O arquivo de manifesto foi pré-criado com um nome do servidor de logon `microsoft`. Abra o arquivo com qualquer editor de texto. Neste exemplo, o arquivo é aberto com o `vi`.

```console
vi azure-vote-all-in-one-redis.yml
```

Substitua `microsoft` pelo nome do servidor de logon do ACR. Esse valor pode ser encontrado na linha **47** do arquivo de manifesto.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:redis-v1
```

Salve e feche o arquivo.

## <a name="deploy-application"></a>Implantar um aplicativo

Use o comando [kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create) para executar o aplicativo. Esse comando analisa o arquivo de manifesto e cria objetos Kubernetes definidos.

```azurecli
kubectl create -f azure-vote-all-in-one-redis.yml
```

Saída:

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>Testar aplicativo

Um [serviço Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) é criado que expõe o aplicativo para a internet. Esse processo pode levar alguns minutos. 

Para monitorar o andamento, use o comando [kubectl get service](https://kubernetes.io/docs/user-guide/kubectl/v1.7/#get) com o argumento `--watch`.

```azurecli
kubectl get service azure-vote-front --watch
```

Inicialmente o *EXTERNAL -IP* para o serviço *azure-vote-front* aparece como *pendente*.
  
```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

Depois que o endereço *EXTERNAL-IP* for alterado de *pendente* para um *endereço IP*, use `CTRL-C` para interromper o processo kubectl watch. 

```
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

Para consultar o aplicativo, navegue até o endereço IP externo.

![Imagem do cluster Kubernetes no Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, o aplicativo Azure vote foi implantado em um cluster Kubernetes no AKS. As tarefas concluídas incluem:  

> [!div class="checklist"]
> * Baixar arquivos de manifesto Kubernetes
> * Executar um aplicativo no Kubernetes
> * Testado o aplicativo

Avance para o próximo tutorial para saber mais sobre como dimensionar um aplicativo Kubernetes e a infraestrutura do Kubernetes subjacente. 

> [!div class="nextstepaction"]
> [Dimensionar um aplicativo do Kubernetes e sua infraestrutura](./tutorial-kubernetes-scale.md)
