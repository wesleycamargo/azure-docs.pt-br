---
title: "Usando o Ruby no Aplicativo Web do Serviço de Aplicativo do Azure no Linux | Microsoft Docs"
description: "Usando o Ruby no Aplicativo Web do Serviço de Aplicativo do Azure no Linux."
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 74f34bdbf5707510c682814716aa0b95c19a5503
ms.openlocfilehash: ad724b99426c1873c173da905c89b471ee64ffa6
ms.contentlocale: pt-br
ms.lasthandoff: 06/09/2017


---

# <a name="using-ruby-in-web-app-on-linux"></a>Usando o Ruby no Aplicativo Web no Linux #

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

Para saber mais sobre como criar um aplicativo Ruby, confira o [guia de introdução](app-service-linux-ruby-get-started.md)

## <a name="next-steps"></a>Próximas etapas
* [O que é o Aplicativo Web no Linux?](app-service-linux-intro.md)
* [Criando Aplicativos Web no Aplicativo Web no Linux](app-service-linux-how-to-create-web-app.md)
* [Implantação do Git local no Serviço de Aplicativo do Azure](app-service-deploy-local-git.md)
* [Perguntas frequentes sobre o Aplicativo Web do Serviço de Aplicativo do Azure no Linux](app-service-linux-faq.md)
* [Criar um aplicativo Ruby com o Aplicativo Web do Azure no Linux](app-service-linux-ruby-get-started.md)

<!--Image references-->
[1]: ./media/app-service-linux-using-ruby/New-Linux.png
[2]: ./media/app-service-linux-using-ruby/Ruby-UX.png
