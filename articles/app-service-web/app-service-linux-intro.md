---
title: "Introdução ao Serviço de Aplicativo no Linux | Microsoft Docs"
description: "Saiba mais sobre o Serviço de Aplicativo no Linux."
keywords: "serviço de aplicativo do azure, linux, oss"
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
translationtype: Human Translation
ms.sourcegitcommit: 78fbef5aa26c52f5dd264581fd1525ada763d4c6
ms.openlocfilehash: fa5d210c5e73074803d5cfb874ad7891563aefd0
ms.lasthandoff: 02/22/2017


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
* Ruby

Os clientes podem implantar seus aplicativos usando:

* FTP
* Git local
* GitHub

Para o dimensionamento de aplicativos:

* Os clientes podem escalar ou reduzir verticalmente seus aplicativos Web, alterando a camada em seu plano do Serviço de Aplicativo.
* Os clientes podem escalar horizontalmente seus aplicativos e executar seu várias instâncias do aplicativo dentro dos limites de seu SKU.

Para o Kudu, algumas das funcionalidades básicas funcionam com o seguinte:

* Ambientes
* Implantações
* Consoles básicos

## <a name="limitations"></a>Limitações
O portal do Azure mostra somente as funcionalidades que funcionam para o Serviço de Aplicativo no Linux e oculta as demais. Conforme habilitarmos mais recursos, eles ficarão visíveis no portal.

Alguns recursos como a integração de rede virtual, a autenticação do Azure Active Directory/de terceiros ou as extensões de site do Kudu não estão completos. Mas quando eles estiverem concluídos, atualizaremos nossa documentação e faremos uma postagem de blog sobre as alterações.

Esta visualização pública está disponível atualmente nas seguintes regiões:

* Oeste dos EUA
* Europa Ocidental 
* Sudeste Asiático

No Linux, os aplicativos Web só têm suporte nos planos de serviço de aplicativo Dedicados e não têm uma camada Gratuita ou Compartilhada. Além disso, planos de Serviço de Aplicativo para aplicativos Web Linux e regulares são mutuamente exclusivos e, portanto, você não pode criar um aplicativo Linux em um plano do serviço de aplicativo não Linux.

Os Aplicativos Web no Linux devem ser criados em um grupo de recursos que não contenha aplicativos Web não Linux na mesma região.

Os Aplicativos Web no Linux ainda não dão suporte à implantação de aplicativos .NET Core de fonte não compilada. Você precisará primeiro publicar/compilar seu aplicativo .NET Core localmente e depois enviar por push os bits de site publicados para seu aplicativo.

## <a name="next-steps"></a>Próximas etapas
Veja os links a seguir para começar a usar o Serviço de Aplicativo no Linux. Você pode postar perguntas e problemas no [nosso fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Como criar Aplicativos Web no Serviço de Aplicativo no Linux](app-service-linux-how-to-create-a-web-app.md)
* [Como usar uma imagem personalizada do Docker para o Serviço de Aplicativo no Linux](app-service-linux-using-custom-docker-image.md)
* [Usando a configuração PM2 para Node.js em Aplicativos Web no Linux](app-service-linux-using-nodejs-pm2.md)
* [Usando o .NET Core em Aplicativos Web do Serviço de Aplicativo do Azure no Linux](app-service-linux-using-dotnetcore.md)
* [Usando o Ruby em Aplicativos Web do Serviço de Aplicativo do Azure no Linux](app-service-linux-using-ruby.md)
* [Perguntas frequentes sobre Aplicativos Web do Serviço de Aplicativo do Azure no Linux](app-service-linux-faq.md)


