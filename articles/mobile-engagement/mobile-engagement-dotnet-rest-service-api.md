---
title: Usando a API REST para acessar as APIs de serviço do Azure Mobile Engagement
description: Descreve como usar as APIs REST do Mobile Engagement para acessar as APIs de Serviço do Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: wesmc7777
manager: erikre
editor: ''
ms.assetid: e8df4897-55ee-45df-b41e-ff187e3d9d12
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/05/2016
ms.author: wesmc;ricksal
ms.openlocfilehash: c3c0de39ba13ed47345b1042a9dceda075dc50b3
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2018
---
# <a name="using-rest-to-access-azure-mobile-engagement-service-apis"></a>Usando REST para acessar as APIs de Serviço do Azure Mobile Engagement
> [!IMPORTANT]
> O Azure Mobile Engagement será desativado em 31/03/2018. Esta página será excluída logo em seguida.
> 

O Azure Mobile Engagement fornece a [API REST do Azure Mobile Engagement](https://msdn.microsoft.com/library/azure/mt683754.aspx) para gerenciar campanhas de dispositivos, push/alcance, etc.

> [!NOTE]
> O serviço Azure Mobile Engagement será desativado em março de 2018 e, no momento, está disponível somente para os clientes existentes. Para saber mais, confira [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/).

Se você não quiser usar as APIs REST diretamente, também forneceremos um [arquivo Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json) que pode ser usado com ferramentas para gerar SDKs em seu idioma de preferência. Recomendamos usar a ferramenta [AutoRest](https://github.com/Azure/AutoRest) para gerar o SDK do nosso arquivo de Swagger. Criamos um SDK do .NET de forma semelhante que permite que você interaja com essas APIs usando um wrapper C#. Você não precisará fazer a negociação do token de autenticação e a atualização por conta própria. Consulte [Amostra do SDK do .NET da API de serviço](mobile-engagement-dotnet-sdk-service-api.md) para aprender a usar o SDK do .Net para API
