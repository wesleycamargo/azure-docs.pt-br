---
title: "Usando o .NET Core em Aplicativos Web do Serviço de Aplicativo do Azure no Linux | Microsoft Docs"
description: "Usando o .NET Core em Aplicativos Web do Serviço de Aplicativo do Azure no Linux."
keywords: "serviço de aplicativo do azure, aplicativo web, dotnet, core, linux, oss"
services: app-service
documentationCenter: 
authors: aelnably
manager: wpickett
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2016
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: 317d6980d304cc503cc43358c4b91459d4abd1ba
ms.openlocfilehash: a54b7413c2eb6ae67b7424a8966d0cd82cd1ce6a


---

# <a name="using-net-core-in-web-apps-on-linux"></a>Usando o .NET Core nos Aplicativos Web no Linux #

Com a atualização mais recente para nosso back-end, introduzimos o suporte ao .NET Core v.1.0. Ao definir a configuração do seu aplicativo Web Linux, você pode alterar a pilha de aplicativos.


## <a name="using-xplat-cli"></a>Usando a CLI XPlat ##

Usando a CLI mais recente de plataforma cruzada do Azure, é possível usar o comando **azure webapp config set** para alterar a pilha de aplicativos. Veja um exemplo disso:

        azure webapp config set --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup --netframeworkversion v1.0 --appcommandline aspnetcore.dll

Observe que o arquivo **aspnetcore.dll** é a dll do aplicativo. Você pode usar o nome que quiser no aplicativo.

Isso carregará a imagem do .Net Core e iniciará o aplicativo Web. Você pode verificar se as configurações foram definidas corretamente usando **azure webapp config show**. Veja um exemplo disso:

        azure webapp config show --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Serviço de Aplicativo no Linux](./app-service-linux-intro.md) 
* [O que é Serviço de Aplicativo no Linux?](app-service-linux-intro.md)
* [Como criar Aplicativos Web no Serviço de Aplicativo no Linux](./app-service-linux-how-to-create-a-web-app.md)
* [CLI de plataforma cruzada de aplicativo Web do Azure](app-service-web-app-azure-resource-manager-xplat-cli.md)



<!--HONumber=Nov16_HO3-->


