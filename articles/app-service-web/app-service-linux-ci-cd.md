---
title: "Implantação contínua do Hub do Docker com o Aplicativo Web do Azure no Linux | Microsoft Docs"
description: "Como configurar a implantação contínua no Aplicativo Web do Azure no Linux."
keywords: "serviço de aplicativo do azure, linux, oss"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 68e11f07520bbe8da6812a34fe95708fc9e932ac
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
# <a name="docker-hub-continuous-deployment-with-azure-web-app-on-linux"></a>Implantação contínua do Hub do Docker com o Aplicativo Web do Azure no Linux

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

Neste tutorial, você configura a implantação contínua para uma imagem de contêiner personalizada do [Hub do Docker](https://hub.docker.com).

## <a name="step-1---log-in-to-azure"></a>Etapa 1 - Fazer logon no Azure

Faça logon no Portal do Azure em http://portal.azure.com

## <a name="step-2---enable-docker-hub-continuous-deployment"></a>Etapa 2 – Habilitar a implantação contínua do Hub do Docker

No  **[portal do Azure](https://portal.azure.com/)**, clique a opção **Serviço de Aplicativo** à esquerda da página.

Clique no nome do aplicativo para o qual você deseja configurar a implantação contínua do Hub do Docker.

Nas **Configurações do aplicativo**, adicione uma configuração de aplicativo chamada `DOCKER_ENABLE_CI` com o valor `true`.

![inserir imagem da configuração de aplicativo](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="step-3---add-a-web-hook-to-docker-hub"></a>Etapa 3 – Adicionar um webhook ao Hub do Docker

Na página do Hub do Docker, clique em **Webhooks** e, em seguida, em **CRIAR UM WEBHOOK**.

![inserir imagem da adição do webhook 1](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

Para a URL do Webhook, você precisa ter o seguinte ponto de extremidade: `https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

![inserir imagem da adição do webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

Você pode obter seu `publishingusername` e `publishingpwd` baixando o perfil de publicação do aplicativo Web usando o Portal do Azure.

![inserir imagem da adição do webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

Quando a imagem é atualizada, o aplicativo Web é atualizado automaticamente com a nova imagem.

## <a name="next-steps"></a>Próximas etapas
* [O que é um Aplicativo Web do Azure no Linux?](./app-service-linux-intro.md)
* [Criando Aplicativos no Aplicativo Web do Azure no Linux](./app-service-linux-how-to-create-web-app.md)
* [Usando a configuração de PM2 para Node.js no Aplicativo Web do Azure no Linux](app-service-linux-using-nodejs-pm2.md)
* [Usando o .NET Core no Aplicativo Web do Azure no Linux](app-service-linux-using-dotnetcore.md)
* [Usando Ruby no Aplicativo Web do Azure no Linux](app-service-linux-ruby-get-started.md)
* [Como usar uma imagem personalizada do Docker para o Aplicativo Web do Azure no Linux](./app-service-linux-using-custom-docker-image.md)
* [Perguntas frequentes sobre o Aplicativo Web do Serviço de Aplicativo do Azure no Linux](./app-service-linux-faq.md) 




