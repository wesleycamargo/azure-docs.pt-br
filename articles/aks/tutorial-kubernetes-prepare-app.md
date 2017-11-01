---
title: "Tutorial do Kubernetes no Azure – preparar o aplicativo | Microsoft Docs"
description: "Tutorial do AKS – preparar o aplicativo"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: "Docker, Contêineres, Microsserviços, Kubernetes, DC/SO, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: db8169b1c3c30efa1b684e49e1f113bee6a7fd45
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2017
---
# <a name="prepare-application-for-azure-container-service-aks"></a>Preparar o aplicativo para o AKS (Serviço de Contêiner do Azure)

Neste tutorial, parte um de oito, um aplicativo de vários contêineres é preparado para ser usado no Kubernetes. As etapas concluídas incluem:  

> [!div class="checklist"]
> * Clonando a fonte do aplicativo do GitHub  
> * Criando uma imagem de contêiner a partir da origem de aplicativo
> * Testando o aplicativo em um ambiente Docker local

Uma vez concluído, o seguinte aplicativo estará acessível em seu ambiente de desenvolvimento local.

![Imagem do cluster Kubernetes no Azure](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

Nos tutoriais subsequentes, a imagem de contêiner é carregada para um Registro de Contêiner do Azure e, em seguida, executada em um cluster AKS.

## <a name="before-you-begin"></a>Antes de começar

Este tutorial assume uma compreensão básica dos conceitos fundamentais do Docker como contêineres, imagens de contêiner e comandos básicos do docker. Se necessário, consulte [Get started with Docker]( https://docs.docker.com/get-started/) (Introdução ao Docker) para conhecer os conceitos básicos de contêiner. 

Para concluir este tutorial, você precisa de um ambiente de desenvolvimento do Docker. O Docker fornece pacotes que configuram facilmente o Docker em qualquer sistema [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ou [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

Azure Cloud Shell não inclui os componentes de Docker necessários para concluir cada etapa neste tutorial. Portanto, é recomendável usar um ambiente de desenvolvimento completo do Docker.

## <a name="get-application-code"></a>Obter o código de aplicativo

O aplicativo de exemplo usado neste tutorial é um aplicativo de votação básico. O aplicativo consiste de um componente Web de front-end e uma instância Redis de back-end. O componente Web é empacotado em uma imagem de contêiner personalizada. A instância Redis utiliza uma imagem não modificada do Hub Docker.  

Use o git para baixar uma cópia do aplicativo em seu ambiente de desenvolvimento.

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Altere os diretórios pois você está trabalhando no diretório clonado.

```console
cd azure-voting-app-redis
```

Dentro do diretório está o código-fonte do aplicativo, um arquivo Docker Compose pré-criado e um arquivo de manifesto Kubernetes. Esses arquivos são usados em todo o conjunto do tutorial. 

## <a name="create-container-images"></a>Criar imagens de contêiner

O [Docker Compose](https://docs.docker.com/compose/) pode ser utilizado para automatizar a compilação de imagens de contêiner e a implantação de aplicativos de vários contêineres.

Execute o arquivo `docker-compose.yml` para criar a imagem de contêiner, baixe a imagem Redis e inicie o aplicativo.

```console
docker-compose up -d
```

Quando completado, use o comando [docker images](https://docs.docker.com/engine/reference/commandline/images/) para ver as imagens criadas.

```console
docker images
```

Observe que três imagens foram baixadas ou criadas. A imagem `azure-vote-front` contém o aplicativo e usa a imagem `nginx-flask` como base. A imagem `redis` é usada para iniciar uma instância do Redis.

```
REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Execute o comando [docker ps](https://docs.docker.com/engine/reference/commandline/ps/) para ver os contêineres em execução.

```console
docker ps
```

Saída:

```
CONTAINER ID        IMAGE             COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        azure-vote-front  "/usr/bin/supervisord"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
b68fed4b66b6        redis             "docker-entrypoint..."   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>Testar o aplicativo localmente

Navegue até http://localhost:8080 para ver o aplicativo em execução.

![Imagem do cluster Kubernetes no Azure](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

## <a name="clean-up-resources"></a>Limpar recursos

Agora que a funcionalidade do aplicativo foi validada, os contêineres em execução podem ser interrompidos e removidos. Não exclua as imagens de contêiner. A imagem `azure-vote-front` será carregada em uma instância do Registro de Contêiner do Azure no próximo tutorial.

Execute o seguinte para interromper os contêineres em execução.

```console
docker-compose stop
```

Exclua os contêineres e recursos interrompidos com o comando a seguir.

```console
docker-compose down
```

Ao concluir, você terá uma imagem de contêiner que contém o aplicativo Azure Vote.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, um aplicativo foi testado e imagens de contêiner foram criadas para o aplicativo. As etapas a seguir foram concluídas:

> [!div class="checklist"]
> * Clonando a fonte do aplicativo do GitHub  
> * Criando uma imagem de contêiner a partir da origem do aplicativo
> * Testando o aplicativo em um ambiente Docker local

Avance para o próximo tutorial para saber mais sobre como armazenar imagens de contêiner em um Registro de Contêiner do Azure.

> [!div class="nextstepaction"]
> [Enviar imagens por push ao Registro de Contêiner do Azure](./tutorial-kubernetes-prepare-acr.md)
