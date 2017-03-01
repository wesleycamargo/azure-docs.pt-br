---
title: "Usando o .NET Core em Aplicativos Web do Serviço de Aplicativo do Azure no Linux | Microsoft Docs"
description: "Usando o .NET Core em Aplicativos Web do Serviço de Aplicativo do Azure no Linux."
keywords: "serviço de aplicativo do azure, aplicativo web, dotnet, core, linux, oss"
services: app-service
documentationCenter: 
authors: aelnably
manager: erikre
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: aelnably;wesmc
translationtype: Human Translation
ms.sourcegitcommit: bb4c7ea7adfe1326ae8259782b5de2762c8c2bf5
ms.openlocfilehash: 769de52a85d1d5078b2ba583e94cabd22b0fde65
ms.lasthandoff: 02/17/2017


---

# <a name="using-net-core-in-web-apps-on-linux"></a>Usando o .NET Core nos Aplicativos Web no Linux #

Com a atualização mais recente para nosso back-end, introduzimos o suporte ao .NET Core v.1.0. Ao definir a configuração do seu aplicativo Web Linux, você pode alterar a pilha de aplicativos.


## <a name="using-xplat-cli"></a>Usando a CLI XPlat ##

Usando a CLI mais recente de plataforma cruzada do Azure, é possível usar o comando **azure webapp config set** para alterar a pilha de aplicativos. Aqui está um exemplo:

        azure webapp config set --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup --netframeworkversion v1.0 --appcommandline aspnetcore.dll

O arquivo **aspnetcore.dll** é a dll do aplicativo. Use o nome que quiser no aplicativo.

Isso carrega a imagem do .Net Core e inicia o aplicativo Web. Você pode verificar se as configurações foram definidas corretamente usando **azure webapp config show**. Aqui está um exemplo:

        azure webapp config show --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup

## <a name="next-steps"></a>Próximas etapas
* [O que é Serviço de Aplicativo no Linux?](app-service-linux-intro.md)
* [Como criar Aplicativos Web no Serviço de Aplicativo no Linux](./app-service-linux-how-to-create-a-web-app.md)
* [CLI de plataforma cruzada de aplicativo Web do Azure](app-service-web-app-azure-resource-manager-xplat-cli.md)
* [Perguntas frequentes sobre Aplicativos Web do Serviço de Aplicativo do Azure no Linux](app-service-linux-faq.md)
