---
title: "Implantação contínua de um Registro de contêiner do Docker com o Aplicativo Web para Contêineres – Azure | Microsoft Docs"
description: "Como configurar a implantação contínua de um Registro de contêiner do Docker no Aplicativo Web para Contêineres."
keywords: "serviço de aplicativo do azure, linux, docker, across"
services: app-service
documentationcenter: 
author: ahmedelnably
manager: cfowler
editor: 
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: aelnably;msangapu
ms.openlocfilehash: e61c767ada31fc32e28bfd9a2a4e843e9ca88053
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Implantação contínua com o Aplicativo Web para Contêineres

Neste tutorial, você configura a implantação contínua para uma imagem de contêiner personalizada de repositórios do [Registro de Contêiner do Azure](https://azure.microsoft.com/services/container-registry/) Gerenciado ou do [Hub do Docker](https://hub.docker.com).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [Portal do Azure](https://portal.azure.com)

## <a name="enable-container-continuous-deployment-feature"></a>Habilitar recurso de implantação contínua do contêiner

Você pode habilitar o recurso de implantação contínua usando a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) e executando o seguinte comando

```azurecli-interactive
az webapp deployment container config --name name --resource-group myResourceGroup --enable-cd true
```

No  **[portal do Azure](https://portal.azure.com/)**, clique a opção **Serviço de Aplicativo** à esquerda da página.

Clique no nome do aplicativo para o qual você deseja configurar a implantação contínua do Hub do Docker.

No **Contêiner do Docker**, selecione 'On' e, em seguida, pressione Save para habilitar a implantação contínua.

![inserir imagem da configuração de aplicativo](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="prepare-webhook-url"></a>Preparar a URL do Webhook

Você pode obter a URL do Webhook usando a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) e executando o seguinte comando

```azurecli-interactive
az webapp deployment container show-cd-url --name sname1 --resource-group rgname
```

Para a URL do Webhook, você precisa ter o seguinte ponto de extremidade: `https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

Você pode obter seu `publishingusername` e `publishingpwd` baixando o perfil de publicação do aplicativo Web usando o Portal do Azure.

![inserir imagem da adição do webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="add-a-web-hook"></a>Adicionar um webhook

### <a name="azure-container-registry"></a>Registro de Contêiner do Azure

Na sua folha de portal de registro, clique em **Webhooks** e crie um novo webhook clicando em **Adicionar**. Na folha **Criar webhook**, nomeie o webhook. Para o URI do Webhook, você precisa fornecer a URL obtida na **Etapa 3**

Verifique se você definiu o escopo como o repositório que contém a imagem de contêiner.

![inserir imagem de webhook](./media/app-service-webapp-service-linux-ci-cd/step3ACRWebhook-1.png)

Quando a imagem é atualizada, o aplicativo Web é atualizado automaticamente com a nova imagem.

### <a name="docker-hub"></a>Hub do Docker

Na página do Hub do Docker, clique em **Webhooks** e, em seguida, em **CRIAR UM WEBHOOK**.

![inserir imagem da adição do webhook 1](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

Para a URL do Webhook, você precisa fornecer a URL obtida na **Etapa 3**

![inserir imagem da adição do webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

Quando a imagem é atualizada, o aplicativo Web é atualizado automaticamente com a nova imagem.

## <a name="next-steps"></a>Próximas etapas

* [O que é o Serviço de Aplicativo do Azure no Linux?](./app-service-linux-intro.md)
* [Registro de Contêiner do Azure](https://azure.microsoft.com/services/container-registry/)
* [Usando o .NET Core no Serviço de Aplicativo do Azure no Linux](quickstart-dotnetcore.md)
* [Usando o Ruby no Serviço de Aplicativo do Azure no Linux](quickstart-ruby.md)
* [Como usar uma imagem personalizada do Docker para o Aplicativo Web para Contêineres](quickstart-docker-go.md)
* [Perguntas frequentes sobre o Aplicativo Web para Contêineres do Serviço de Aplicativo do Azure](./app-service-linux-faq.md)
* [Gerenciar aplicativo Web para Contêineres usando a CLI do Azure 2.0](./app-service-linux-cli.md)
