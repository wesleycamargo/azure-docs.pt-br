---
title: "Usando a API REST para acessar as APIs de serviço do Azure Mobile Engagement"
description: "Descreve como usar as APIs REST do Mobile Engagement para acessar as APIs de Serviço do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: wesmc7777
manager: erikre
editor: 
ms.assetid: e8df4897-55ee-45df-b41e-ff187e3d9d12
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/05/2016
ms.author: wesmc;ricksal
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: e381eef22b68e89d5407fb452c05eba089b98780
ms.contentlocale: pt-br
ms.lasthandoff: 05/18/2017


---
# <a name="using-rest-to-access-azure-mobile-engagement-service-apis"></a>Usando REST para acessar as APIs de Serviço do Azure Mobile Engagement
O Azure Mobile Engagement fornece a [API REST do Azure Mobile Engagement](https://msdn.microsoft.com/library/azure/mt683754.aspx) para gerenciar campanhas de dispositivos, push/alcance, etc.

Se você não quiser usar as APIs REST diretamente, também forneceremos um [arquivo Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json) que pode ser usado com ferramentas para gerar SDKs em seu idioma de preferência. Recomendamos usar a ferramenta [AutoRest](https://github.com/Azure/AutoRest) para gerar o SDK do nosso arquivo de Swagger. Criamos um SDK do .NET de forma semelhante que permite que você interaja com essas APIs usando um wrapper C#. Você não precisará fazer a negociação do token de autenticação e a atualização por conta própria. Consulte [Amostra do SDK do .NET da API de serviço](mobile-engagement-dotnet-sdk-service-api.md) para aprender a usar o SDK do .Net para API

