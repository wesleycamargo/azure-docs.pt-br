---
title: "Tutorial do Serviço de Contêiner do Azure – preparar o aplicativo | Microsoft Docs"
description: "Tutorial do Serviço de Contêiner do Azure – preparar o aplicativo"
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
ms.date: 06/26/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 49d727de69c700af0ae6402ad54b5880010ebb3b
ms.contentlocale: pt-br
ms.lasthandoff: 06/28/2017

---

# <a name="create-container-images-to-be-used-with-azure-container-service"></a>Criar imagens de contêiner a serem usadas com o Serviço de Contêiner do Azure

Neste tutorial, um aplicativo é preparado para o Kubernetes. As etapas concluídas incluem:  

> [!div class="checklist"]
> * Clonando a fonte do aplicativo do GitHub  
> * Criando imagens de contêiner da fonte do aplicativo
> * Testando as imagens em um ambiente do Docker local

Nos próximos tutoriais, essas imagens de contêiner serão carregadas em um Registro de Contêiner do Azure e, em seguida, executadas em um cluster do Kubernetes hospedado no Azure.

## <a name="before-you-begin"></a>Antes de começar

Este tutorial assume uma compreensão básica dos conceitos fundamentais do Docker como contêineres, imagens de contêiner e comandos básicos do docker. Se necessário, consulte [Get started with Docker]( https://docs.docker.com/get-started/) (Introdução ao Docker) para conhecer os conceitos básicos de contêiner. 

Para concluir este tutorial, você precisa de um ambiente de desenvolvimento do Docker. O Docker fornece pacotes que configuram facilmente o Docker em qualquer sistema [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ou [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

## <a name="get-application-code"></a>Obter o código de aplicativo

O aplicativo de exemplo usado neste tutorial é um aplicativo de votação básico. O aplicativo consiste em um componente Web front-end e um banco de dados de back-end. 

Use o git para baixar uma cópia do aplicativo em seu ambiente de desenvolvimento.

```bash
git clone https://github.com/Azure-Samples/azure-voting-app.git
```

Dentro do diretório de aplicativo, podem ser encontrados os arquivos de manifesto Dockerfiles e Kubernetes pré-criados. Esses arquivos são usados para criar ativos em todo o conjunto de tutoriais. 

## <a name="create-container-images"></a>Criar imagens de contêiner

Para criar uma imagem de contêiner para o aplicativo front-end, use o comando [docker build](https://docs.docker.com/engine/reference/commandline/build/).

```bash
docker build ./azure-voting-app/azure-vote -t azure-vote-front
```

Repita o comando, neste momento para a imagem de contêiner de back-end.

```bash
docker build ./azure-voting-app/azure-vote-mysql -t azure-vote-back
```

Quando concluído, use o comando `docker images` para ver as imagens criadas. 

```bash
docker images
```

Saída:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              c13c4f50ede1        39 seconds ago       716 MB
azure-vote-back              latest              33fe5afc1885        About a minute ago   407 MB
mysql                        latest              e799c7f9ae9c        4 weeks ago          407 MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        8 months ago         694 MB
```

## <a name="test-application"></a>Testar aplicativo

Agora que duas imagens de contêiner foram criadas, teste essas imagens em seu ambiente de desenvolvimento local. 

Primeiro, crie uma rede do Docker. Essa rede é usada para a comunicação entre os contêineres.  

```bash
docker network create azure-vote
```

Execute uma instância da imagem de contêiner de back-end usando o comando `docker run`.

Neste exemplo, o arquivo de banco de dados MySQL é armazenado dentro do contêiner. Depois que esse aplicativo é movido para os clusters do Kubernete, um volume de dados externo é usado para armazenar o arquivo de banco de dados. Além disso, variáveis de ambiente estão sendo usadas para definir as credenciais do MySQL.

```bash
docker run -p 3306:3306 --name azure-vote-back -d --network azure-vote -e MYSQL_ROOT_PASSWORD=Password12 -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote azure-vote-back 
```

Execute uma instância da imagem de contêiner de front-end.

Variáveis de ambiente estão sendo usadas para configurar as informações de conexão de banco de dados.

```bash
docker run -d -p 8080:80 --name azure-vote-front --network=azure-vote -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote -e MYSQL_HOST=azure-vote-back azure-vote-front
```

Ao concluir, execute `docker ps` para ver os contêineres em execução.  

```bash
docker ps
```

Saída:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                           NAMES
3aa02e8ae965        azure-vote-front     "/usr/bin/supervisord"   59 seconds ago      Up 57 seconds       443/tcp, 0.0.0.0:8080->80/tcp   flaskmysqlvote_azure-vote-front_1
5ae60b3ba181        azure-vote-backend   "docker-entrypoint..."   59 seconds ago      Up 58 seconds       0.0.0.0:3306->3306/tcp          azure-vote-back
```

Navegue até `http://localhost:8080` para ver o aplicativo em execução. O aplicativo leva alguns segundos para ser inicializado. Se um erro for encontrado, tente novamente.

![Imagem do cluster Kubernetes no Azure](media/container-service-kubernetes-tutorials/vote-app.png)

## <a name="clean-up-resources"></a>Limpar recursos

Agora que a funcionalidade do aplicativo foi validada, os contêineres em execução podem ser interrompidos e removidos. Não exclua as imagens de contêiner. Essas imagens serão carregadas em uma instância do Registro de Contêiner do Azure no próximo tutorial.

Pare e exclua o contêiner de front-end com o comando [docker rm](https://docs.docker.com/engine/reference/commandline/rm/). 

```bash
docker rm -f azure-vote-front
```

Pare e exclua o contêiner de back-end com o comando [docker rm](https://docs.docker.com/engine/reference/commandline/rm/). 

```bash
docker rm -f azure-vote-back
```

Exclua a rede com o comando [docker network rm](https://docs.docker.com/engine/reference/commandline/network_rm/).

```bash
docker network rm azure-vote
```

Ao concluir, você terá duas imagens de contêiner que constituem o aplicativo de Voto do Azure.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, um aplicativo foi testado e imagens de contêiner foram criadas para o aplicativo. As etapas a seguir foram concluídas:

> [!div class="checklist"]
> * Clonando a fonte do aplicativo do GitHub  
> * Criando imagens de contêiner da fonte do aplicativo
> * Testando as imagens em um ambiente do Docker local

Avance para o próximo tutorial para saber mais sobre como armazenar imagens de contêiner em um Registro de Contêiner do Azure.

> [!div class="nextstepaction"]
> [Enviar imagens por push ao Registro de Contêiner do Azure](./container-service-tutorial-kubernetes-prepare-acr.md)
