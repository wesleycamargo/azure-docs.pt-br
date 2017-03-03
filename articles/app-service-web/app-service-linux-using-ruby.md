---
title: "Usando o Ruby nos Aplicativos Web do Serviço de Aplicativo do Azure no Linux | Microsoft Docs"
description: "Usando o Ruby em Aplicativos Web do Serviço de Aplicativo do Azure no Linux."
keywords: "serviço de aplicativo do azure, aplicativo Web, perguntas frequentes, linux, oss, ruby"
services: app-service
documentationCenter: 
authors: aelnably
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: aelnably;wesmc
translationtype: Human Translation
ms.sourcegitcommit: bb4c7ea7adfe1326ae8259782b5de2762c8c2bf5
ms.openlocfilehash: 8427034e5229000faf134bcd5b7174b0f7046321
ms.lasthandoff: 02/17/2017


---

# <a name="using-ruby-in-web-apps-on-linux"></a>Usando o Ruby em Aplicativos Web no Linux #

Com a atualização mais recente para nosso back-end, introduzimos o suporte ao Ruby v.2.3. Ao definir a configuração do seu aplicativo Web Linux, você pode alterar a pilha de aplicativos.

## <a name="using-the-azure-portal"></a>Usando o portal do Azure ##

Do novo menu no [Portal do Azure](https://portal.azure.com), você pode optar por criar um aplicativo Web no Linux utilizando a opção Web + Móvel, conforme mostrado na imagem a seguir:

![Iniciar a criação de um aplicativo Web no portal do Azure][1]

A folha **Criar** é aberta, conforme mostrado na seguinte imagem:

![A folha Criar][2]

1. Dê um nome ao aplicativo Web.
2. Escolha um grupo de recursos existente ou crie um novo. (Veja as regiões disponíveis na [seção de limitações](app-service-linux-intro.md).)
3. Escolha um plano do Serviço de Aplicativo do Azure existente ou crie um. (Veja as observações do plano do Serviço de Aplicativo na [seção de limitações](app-service-linux-intro.md).)
4. Escolha o Ruby das pilhas de tempo de execução internas.

Depois que seu aplicativo Web Ruby é criado, você pode implantar para ele usando Git ou FTP.

## <a name="next-steps"></a>Próximas etapas
* [O que é Serviço de Aplicativo no Linux?](app-service-linux-intro.md)
* [Como criar Aplicativos Web no Serviço de Aplicativo no Linux](app-service-linux-how-to-create-a-web-app.md)
* [Implantação do Git local no Serviço de Aplicativo do Azure](app-service-deploy-local-git.md)
* [Perguntas frequentes sobre Aplicativos Web do Serviço de Aplicativo do Azure no Linux](app-service-linux-faq.md)

<!--Image references-->
[1]: ./media/app-service-linux-using-ruby/New-Linux.png
[2]: ./media/app-service-linux-using-ruby/Ruby-UX.png
