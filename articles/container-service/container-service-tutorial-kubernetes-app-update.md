---
title: "Tutorial do Serviço de Contêiner do Azure – atualizar aplicativo | Microsoft Docs"
description: "Tutorial do Serviço de Contêiner do Azure – atualizar aplicativo"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, Contêineres, Microsserviços, Kubernetes, DC/SO, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: ce4a88a7958116890ec17eb2405ba809487b9c0f
ms.contentlocale: pt-br
ms.lasthandoff: 07/06/2017

---

# <a name="update-an-application-in-kubernetes"></a>Atualizar um aplicativo no Kubernetes

Depois de implantar um aplicativo no Kubernetes, você pode atualizá-lo especificando uma nova imagem de contêiner ou versão de imagem. Quando você atualizar um aplicativo, a distribuição de atualização é realizada em etapas para que apenas uma parte da implantação seja atualizada simultaneamente. 

Essa atualização em etapas permite que o aplicativo permaneça em execução durante a atualização e fornece um mecanismo de reversão, caso ocorra alguma falha de implantação. 

Neste tutorial, o aplicativo de exemplo Voto do Azure é atualizado. As tarefas a serem concluídas incluem:

> [!div class="checklist"]
> * Atualizando o código do aplicativo front-end.
> * Criando uma imagem de contêiner atualizada.
> * Enviando a imagem de contêiner por push para o Registro de Contêiner do Azure.
> * Implantando um aplicativo atualizado.

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, colocamos um aplicativo em imagens de contêiner, carregamos as imagens no Registro de Contêiner do Azure e criamos um cluster do Kubernetes. Em seguida, executamos o aplicativo no cluster do Kubernetes. 

Se você ainda não executou essas etapas e deseja fazer isso agora, retorne ao [Tutorial 1 – Criar imagens de contêiner](./container-service-tutorial-kubernetes-prepare-app.md). 

No mínimo, este tutorial requer um cluster do Kubernetes com um aplicativo em execução.

## <a name="update-application"></a>Atualizar aplicativo

Para concluir as etapas neste tutorial, você deve clonar uma cópia do aplicativo Voto do Azure. Se necessário, crie essa cópia clonada com o seguinte comando:

```bash
git clone https://github.com/Azure-Samples/azure-voting-app.git
```

Abra o arquivo `config_file.cfg` com qualquer editor de texto ou de código. Você pode encontrar esse arquivo no seguinte diretório do repositório clonado.

```bash
 /azure-voting-app/azure-vote/azure-vote/config_file.cfg
```

Altere os valores de `VOTE1VALUE` e `VOTE2VALUE` e, em seguida, salve o arquivo.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Half Full'
VOTE2VALUE = 'Half Empty'
SHOWHOST = 'false'
```

Use `docker build` para recriar a imagem de front-end.

```bash
docker build --no-cache ./azure-voting-app/azure-vote -t azure-vote-front:v2
```

## <a name="test-application"></a>Testar aplicativo

Crie uma rede do Docker. Essa rede é usada para a comunicação entre os contêineres.  

```bash
docker network create azure-vote
```

Execute uma instância da imagem de contêiner de back-end usando o comando `docker run`.

```bash
docker run -p 3306:3306 --name azure-vote-back -d --network azure-vote -e MYSQL_ROOT_PASSWORD=Password12 -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote azure-vote-back 
```

Execute uma instância da imagem de contêiner de front-end.

```bash
docker run -d -p 8080:80 --name azure-vote-front --network=azure-vote -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote -e MYSQL_HOST=azure-vote-back azure-vote-front:v2
```

Acesse `http://localhost:8080` para ver o aplicativo atualizado. O aplicativo leva alguns segundos para ser inicializado. Se você receber um erro, tente novamente.

![Imagem do cluster Kubernetes no Azure](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>Marcar e enviar mensagens por push

Marque a imagem *azure-vote-front* com o loginServer do registro de contêiner.

Se você estiver usando o Registro de Contêiner do Azure, obtenha o nome do servidor de logon com o comando [az acr list](/cli/azure/acr#list).

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Use a [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) para marcar a imagem, lembrando-se de atualizar o comando com o nome do servidor de logon do Registro de Contêiner do Azure ou com o nome do host do registro público.

```bash
docker tag azure-vote-front:v2 <acrLoginServer>/azure-vote-front:v2
```

Envie a imagem por push ao registro. Substitua `<acrLoginServer>` pelo nome do servidor de logon do Registro de Contêiner do Azure ou pelo nome do host do registro público.

```bash
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-update-to-kubernetes"></a>Implantar atualização no Kubernetes

### <a name="verify-multiple-pod-replicas"></a>Verifique se há várias réplicas de POD

Para garantir o tempo de atividade máximo, várias instâncias do pod de aplicativos devem estar em execução. Verifique essa configuração com o comando [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get).

```bash
kubectl get pod
```

Saída:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Se não houver vários pods executando a imagem do azure-vote-front, dimensione a implantação do *azure-vote-front*.


```bash
kubectl scale --replicas=3 deployment/azure-vote-front
```

### <a name="update-application"></a>Atualizar aplicativo

Para atualizar o aplicativo, execute o comando a seguir. Atualize `<acrLoginServer>` com o servidor de logon ou o nome do host do registro de contêiner.

```bash
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2
```

Para monitorar a implantação, use o comando [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get). Conforme o aplicativo atualizado é implantado, os pods são encerrados e recriados com a nova imagem de contêiner.

```bash
kubectl get pod
```

Saída:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running   0          5m
azure-vote-front-1297194256-tpjlg   1/1       Running   0         1m
azure-vote-front-1297194256-tptnx   1/1       Running   0         5m
azure-vote-front-1297194256-zktw9   1/1       Terminating   0         1m
```

## <a name="test-updated-application"></a>Testar aplicativo atualizado

Obtenha o endereço IP externo do serviço *azure-vote-front*.

```bash
kubectl get service
```

Acesse o endereço IP para ver o aplicativo atualizado.

![Imagem do cluster Kubernetes no Azure](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, atualizamos um aplicativo e distribuímos essa atualização para um cluster do Kubernetes. Concluímos as seguintes tarefas:  

> [!div class="checklist"]
> * Atualizamos o código do aplicativo front-end.
> * Criamos uma imagem de contêiner atualizada.
> * Enviamos a imagem de contêiner por push para o Registro de Contêiner do Azure.
> * Implantamos o aplicativo atualizado.

Avance para o próximo tutorial para saber mais sobre como monitorar o Kubernetes com o Operations Management Suite.

> [!div class="nextstepaction"]
> [Monitorar o Kubernetes com o OMS](./container-service-tutorial-kubernetes-monitor.md)
