---
title: "Introdução ao Serviço de Aplicativo no Linux | Microsoft Docs"
description: "Saiba mais sobre o Serviço de Aplicativo no Linux."
keywords: "serviço de aplicativo do azure, linux, oss"
services: app-service
documentationcenter: 
author: naziml
manager: wpickett
editor: 
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: naziml
translationtype: Human Translation
ms.sourcegitcommit: a3df293a056f4b06281bf8b80717529286d528b8
ms.openlocfilehash: 48f26113d129ebfa613f1b8f2a372d0e7286aea1


---
# <a name="introduction-to-app-service-on-linux"></a>Introdução ao Serviço de Aplicativo no Linux
Atualmente, o Serviço de Aplicativo do Azure no Linux está em visualização pública e dá suporte nativamente a aplicativos Web em execução no Linux.

## <a name="overview"></a>Visão geral
Os clientes podem usar o Serviço de Aplicativo no Linux para hospedar aplicativos Web nativos no Linux para as pilhas de aplicativos com suporte. A seção a seguir lista as pilhas de aplicativos que têm suporte no momento. 

## <a name="features"></a>Recursos
Atualmente, o Serviço de Aplicativo no Linux dá suporte a estas pilhas de aplicativos:

* Node.js
* PHP
* .NET Core

Os clientes podem implantar seus aplicativos usando:

* FTP
* Git local
* GitHub ou Bitbucket

Para o dimensionamento de aplicativos:

* Os clientes podem dimensionar seus aplicativos Web para cima e para baixo, alterando a camada em seu plano do Serviço de Aplicativo.
* Os clientes podem escalar horizontalmente seus aplicativos e executar seu aplicativo em várias instâncias dentro dos limites de sua SKU.

Para o Kudu, algumas das funcionalidades básicas funcionam com o seguinte:

* Ambientes
* Implantações
* Consoles básicos

## <a name="limitations"></a>Limitações
O portal do Azure mostra somente as funcionalidades que funcionam para o Serviço de Aplicativo no Linux e oculta as demais. À medida que habilitarmos mais funcionalidades, você as verá refletidas no portal.

Algumas funcionalidades, como a integração de rede virtual, o Azure Active Directory/autenticação de terceiros ou as extensões de site do Kudu, não funcionam no momento. Mas quando elas estiverem funcionando, atualizaremos nossa documentação e faremos uma postagem de blog sobre as alterações.

Esta visualização pública está disponível atualmente nas seguintes regiões:

* Oeste dos EUA
* Europa Ocidental 
* Sudeste Asiático

No Linux, os aplicativos Web só têm suporte nos planos de serviço de aplicativo Dedicados e não têm uma camada Gratuita ou Compartilhada. Além disso, planos de Serviço de Aplicativo para aplicativos Web Linux e regulares são mutuamente exclusivos e, portanto, você não pode criar um aplicativo Linux em um plano do serviço de aplicativo não Linux.

Os Aplicativos Web no Linux devem ser criados em um grupo de recursos que não contenha aplicativos Web não Linux na mesma região.

## <a name="next-steps"></a>Próximas etapas
Veja os links a seguir para começar a usar o Serviço de Aplicativo no Linux. Você pode postar perguntas e problemas no [nosso fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Como criar Aplicativos Web no Serviço de Aplicativo no Linux](app-service-linux-how-to-create-a-web-app.md)
* [Como usar uma imagem personalizada do Docker para o Serviço de Aplicativo no Linux](app-service-linux-using-custom-docker-image.md)
* [Usando a configuração PM2 para Node.js em Aplicativos Web no Linux](app-service-linux-using-nodejs-pm2.md)
* [Usando o .NET Core em Aplicativos Web do Serviço de Aplicativo do Azure no Linux](app-service-linux-using-dotnetcore.md)



<!--HONumber=Feb17_HO2-->


