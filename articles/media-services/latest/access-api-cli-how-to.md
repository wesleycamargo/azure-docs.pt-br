---
title: Acessar a API dos Serviços de Mídia do Azure – CLI do Azure | Microsoft Docs
description: Execute as etapas desta instrução para acessar a API dos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 01/28/2019
ms.author: juliako
ms.openlocfilehash: 43f9443e4b5cd700500bd9803f2737ed9e0aa633
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55223147"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Acessar a API dos Serviços de Mídia do Azure com a CLI do Azure
 
Use a autenticação de entidade de serviço do Azure AD para se conectar à API dos Serviços de Mídia do Microsoft Azure. Seu aplicativo precisa solicitar um token do Azure AD que tenha os seguintes parâmetros:

* Ponto de extremidade de locatário do Azure AD
* URI de recursos dos Serviços de Mídia
* URI de recurso dos Serviços de Mídia REST
* Valores do aplicativo do Azure AD: a ID do cliente e o segredo do cliente

Este artigo mostra como usar a CLI do Azure para criar um aplicativo e uma entidade de serviço do Azure AD e obter os valores necessários para acessar os recursos dos Serviços de Mídia do Azure.

## <a name="prerequisites"></a>Pré-requisitos 

[Crie uma conta de Serviços de Mídia](create-account-cli-how-to.md).

Lembre-se dos valores que você usou para o nome do grupo de recursos e o nome da conta de Serviços de Mídia.
 
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="next-steps"></a>Próximas etapas

[Transmitir um arquivo por streaming](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>Consulte também

[CLI do Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
