---
title: "Usando o .NET Core no Aplicativo Web do Serviço de Aplicativo do Azure no Linux | Microsoft Docs"
description: "Usando o .NET Core no Aplicativo Web do Serviço de Aplicativo do Azure no Linux."
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
ms.date: 05/07/2017
ms.author: aelnably;wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 3608ddf86c3d8010b577e6f745dcd5cef016acd9
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---

# <a name="using-net-core-in-azure-web-app-on-linux"></a>Usando o .NET Core no Aplicativo Web do Azure no Linux #

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]


Com a atualização mais recente para nosso back-end, introduzimos o suporte ao .NET Core v.1.0. Ao definir a configuração do seu aplicativo Web Linux, você pode alterar a pilha de aplicativos.


## <a name="using-the-azure-cli"></a>Usando a CLI do Azure ##

Usando a [mais recente CLI (interface de linha de comando) do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli), você pode usar o comando **azure webapp config set** para alterar a pilha de aplicativos. Veja um exemplo:

        azure webapp config set --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup --netframeworkversion v1.0 --appcommandline aspnetcore.dll

O arquivo **aspnetcore.dll** é a dll do aplicativo. Use o nome que quiser no aplicativo.

Isso carrega a imagem do .Net Core e inicia o aplicativo Web. Você pode verificar se as configurações foram definidas corretamente usando **azure webapp config show**. Aqui está um exemplo:

        azure webapp config show --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup

## <a name="next-steps"></a>Próximas etapas
* [O que é um Aplicativo Web do Azure no Linux?](app-service-linux-intro.md)
* [Criando Aplicativos Web no Aplicativo Web do Azure no Linux](./app-service-linux-how-to-create-web-app.md)
* [CLI de plataforma cruzada de aplicativo Web do Azure](app-service-web-app-azure-resource-manager-xplat-cli.md)
* [Perguntas frequentes sobre o Aplicativo Web do Serviço de Aplicativo do Azure no Linux](app-service-linux-faq.md)
