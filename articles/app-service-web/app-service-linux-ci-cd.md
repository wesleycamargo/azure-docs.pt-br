---
title: "Implantação contínua com o Aplicativo Web do Azure no Linux | Microsoft Docs"
description: "Como configurar a implantação contínua no Aplicativo Web do Azure no Linux."
keywords: "serviço de aplicativo do azure, linux, oss, acr"
services: app-service
documentationcenter: 
author: ahmedelnably
manager: erikre
editor: 
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: aelnably;wesmc
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: f8f7d51003f8a55b7f51e8cc2cea838e8e5a6196
ms.contentlocale: pt-br
ms.lasthandoff: 08/28/2017

---
# <a name="continuous-deployment-with-azure-web-app-on-linux"></a>Implantação contínua com o Aplicativo Web do Azure no Linux

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

Neste tutorial, você configura a implantação contínua para uma imagem de contêiner personalizada de repositórios do [Registro de Contêiner do Azure](https://azure.microsoft.com/en-us/services/container-registry/) Gerenciado ou do [Hub do Docker](https://hub.docker.com).

## <a name="step-1---sign-in-to-azure"></a>Etapa 1– entrar no Azure

Entrar no Portal do Azure em http://portal.azure.com

## <a name="step-2---enable-container-continuous-deployment-feature"></a>Etapa 2 – Habilitar recurso de implantação contínua do contêiner

Você pode habilitar o recurso de implantação contínua usando a [CLI do Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) e executando o seguinte comando

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
``` 

No  **[portal do Azure](https://portal.azure.com/)**, clique a opção **Serviço de Aplicativo** à esquerda da página.

Clique no nome do aplicativo para o qual você deseja configurar a implantação contínua do Hub do Docker.

Nas **Configurações do aplicativo**, adicione uma configuração de aplicativo chamada `DOCKER_ENABLE_CI` com o valor `true`.

![inserir imagem da configuração de aplicativo](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="step-3---prepare-webhook-url"></a>Etapa 3 – preparar a URL do Webhook

Você pode obter a URL do Webhook usando a [CLI do Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) e executando o seguinte comando

```azurecli-interactive
az webapp deployment container -n sname1 -g rgname -e true --show-cd-url
``` 

Para a URL do Webhook, você precisa ter o seguinte ponto de extremidade: `https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

Você pode obter seu `publishingusername` e `publishingpwd` baixando o perfil de publicação do aplicativo Web usando o Portal do Azure.

![inserir imagem da adição do webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="step-4---add-a-web-hook"></a>Etapa 4 – adicionar um webhook

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
* [O que é um Aplicativo Web do Azure no Linux?](./app-service-linux-intro.md)
* [Registro de Contêiner do Azure](https://azure.microsoft.com/en-us/services/container-registry/)
* [Usando a configuração de PM2 para Node.js no Aplicativo Web do Azure no Linux](app-service-linux-using-nodejs-pm2.md)
* [Usando o .NET Core no Aplicativo Web do Azure no Linux](app-service-linux-using-dotnetcore.md)
* [Usando Ruby no Aplicativo Web do Azure no Linux](app-service-linux-ruby-get-started.md)
* [Como usar uma imagem personalizada do Docker para o Aplicativo Web do Azure no Linux](./app-service-linux-using-custom-docker-image.md)
* [Perguntas frequentes sobre o Aplicativo Web do Serviço de Aplicativo do Azure no Linux](./app-service-linux-faq.md) 
* [Gerenciar o aplicativo Web no Linux usando a CLI do Azure 2.0](./app-service-linux-cli.md)




