---
title: "Implantar nos Serviços de Aplicativos do Azure usando o Visual Studio 2015 | Microsoft Docs"
description: "Este artigo descreve como implantar um aplicativo Web, Aplicativo de API ou Aplicativo Móvel no Azure Governamental usando o Visual Studio 2015 e o SDK do Azure."
services: azure-government
cloud: gov
documentationcenter: 
author: sdubeymsft
manager: zakramer
ms.assetid: 8f9a3700-b9ee-43b7-b64d-2e6c3b57d4c0
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 01/03/2016
ms.author: sdubeymsft
translationtype: Human Translation
ms.sourcegitcommit: 1cb57e5156dab976599ddfa9a58f26ca8ef1ee0e
ms.openlocfilehash: 159c891dedb66dfaa238981b395b4bba074c5716
ms.lasthandoff: 02/22/2017


---
# <a name="deploy-to-azure-app-services-using-visual-studio-2015"></a>Implantar nos Serviços de Aplicativos do Azure usando o Visual Studio 2015
Este artigo descreve como implantar um aplicativo dos Serviços de Aplicativos do Azure (Aplicativo de API, Aplicativo Web, Aplicativo Móvel) no Azure Governamental usando o Visual Studio 2015.

## <a name="prerequisites"></a>Pré-requisitos
* Confira [Pré-requisitos do Visual Studio] (../app-service-api/app-service-api-dotnet-get-started.md#prerequisites) para instalar e configurar o Visual Studio 2015 e o SDK do Azure.
* Siga [estas instruções] (documentation-government-manage-subscriptions.md) para configurar o Visual Studio para se conectar à conta do Azure Governamental.

## <a name="open-app-project-in-visual-studio"></a>Abrir o projeto Aplicativo no Visual Studio
* Abra o aplicativo existente solution\project no Visual Studio, crie um projeto seguindo [estas instruções] (../app-service-web/web-sites-dotnet-get-started.md#create-a-web-application) ou baixe o aplicativo de exemplo seguindo [estas etapas] (../app-service-api/app-service-api-dotnet-get-started.md#download-the-sample-application).
* Execute o aplicativo no Visual Studio para verificar se ele funciona localmente.

## <a name="deploy-to-azure-government"></a>Implantar no Azure Governamental
* Depois que o **Visual Studio estiver configurado para se conectar à conta do Azure Governamental** (já feito na seção de pré-requisitos), as instruções para implantar nos serviços de aplicativos são exatamente as mesmas para o Azure Public.
* Para implantar o aplicativo, siga [estas etapas] (../app-service-api/app-service-api-dotnet-get-started.md#a-idcreateapiappa-create-an-api-app-in-azure-and-deploy-code-to-it).

### <a name="references"></a>Referências
* [Implantar um aplicativo Web ASP.NET no Serviço de Aplicativo do Azure usando o Visual Studio] (../app-service-web/web-sites-dotnet-get-started.md)
* Para conhecer outras maneiras de implantação, confira [Implantar seu aplicativo no Serviço de Aplicativo do Azure] (../app-service-web/web-sites-deploy.md)
* Para ver a documentação geral do Serviço de Aplicativo, confira [Serviço de Aplicativo - Documentação de Aplicativos de API] (../app-service-api/index.md)

## <a name="next-steps"></a>Próximas etapas
Para obter informações complementares e atualizações, assine o [Blog do Microsoft Azure Governamental](https://blogs.msdn.microsoft.com/azuregov/).

