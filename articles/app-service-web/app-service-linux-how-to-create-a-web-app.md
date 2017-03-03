---
title: "Criar um aplicativo web em execução no Linux | Microsoft Docs"
description: "Fluxo de trabalho da criação de aplicativo Web para Serviço de Aplicativo no Linux."
keywords: "serviço de aplicativo do azure, aplicativo web, linux, oss"
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: 3a71d10a-a0fe-4d28-af95-03b2860057d5
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
translationtype: Human Translation
ms.sourcegitcommit: bb4c7ea7adfe1326ae8259782b5de2762c8c2bf5
ms.openlocfilehash: 19a42a8dc9a4026d93c10dd209e84d4e14b8f00a
ms.lasthandoff: 02/17/2017


---
# <a name="create-an-azure-web-app-running-on-linux"></a>Criar um aplicativo web em execução no Linux
## <a name="use-the-azure-portal-to-create-your-web-app"></a>Usar o portal do Azure para criar o aplicativo Web
Você pode começar a criar seu aplicativo Web em Linux no [portal do Azure](https://portal.azure.com), conforme mostrado na imagem a seguir:

![Iniciar a criação de um aplicativo Web no portal do Azure][1]

A folha **Criar** é aberta, conforme mostrado na seguinte imagem:

![A folha Criar][2]

1. Dê um nome ao aplicativo Web.
2. Escolha um grupo de recursos existente ou crie um novo. (Veja as regiões disponíveis na [seção de limitações](app-service-linux-intro.md).)
3. Escolha um plano do Serviço de Aplicativo do Azure existente ou crie um. (Veja as observações do plano do Serviço de Aplicativo na [seção de limitações](app-service-linux-intro.md).)
4. Escolha a pilha de aplicativos que você pretende usar. É possível escolher entre várias versões de Node.js, PHP, .Net Core e Ruby.

Depois de criar o aplicativo, você pode alterar a pilha de aplicativos nas configurações do aplicativo, conforme mostrado na seguinte imagem:

![Configurações do aplicativo][3]

## <a name="deploy-your-web-app"></a>Implantar o aplicativo Web
A escolha das **opções de implantação** no portal de gerenciamento permite usar um repositório local Git ou GitHub para implantar o aplicativo. O restante das instruções são semelhantes àquelas de um aplicativo Web não Linux. Você pode seguir as instruções em [implantação do Git local](app-service-deploy-local-git.md) ou [implantação contínua](app-service-continuous-deployment.md) para implantar seu aplicativo.

Você também pode usar o FTP para carregar o aplicativo no site. É possível obter o ponto de extremidade FTP para o aplicativo Web na seção de logs de diagnóstico, conforme mostrado na seguinte imagem:

![Logs de diagnóstico][4]

## <a name="next-steps"></a>Próximas etapas
* [O que é Serviço de Aplicativo no Linux?](app-service-linux-intro.md)
* [Usando a configuração PM2 para Node.js em Aplicativos Web no Linux](app-service-linux-using-nodejs-pm2.md)
* [Usando o Ruby em Aplicativos Web do Serviço de Aplicativo do Azure no Linux](app-service-linux-using-ruby.md)
* [Perguntas frequentes sobre Aplicativos Web do Serviço de Aplicativo do Azure no Linux](app-service-linux-faq.md)

<!--Image references-->
[1]: ./media/app-service-linux-how-to-create-a-web-app/top-level-create.png
[2]: ./media/app-service-linux-how-to-create-a-web-app/create-blade.png
[3]: ./media/app-service-linux-how-to-create-a-web-app/application-settings-change-stack.png
[4]: ./media/app-service-linux-how-to-create-a-web-app/diagnostic-logs-ftp.png

