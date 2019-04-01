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
ms.openlocfilehash: 257fe51cae245708816cd9a7bb0c33b6edf5aa05
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756013"
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

## <a name="see-also"></a>Consulte também

- [Dimensionar as unidades reservadas para mídia – CLI](media-reserved-units-cli-how-to.md)
- [Criar uma conta dos Serviços de Mídia – CLI](./scripts/cli-create-account.md) 
- [Redefinir as credenciais da conta – CLI](./scripts/cli-reset-account-credentials.md)
- [Criar ativos – CLI](./scripts/cli-create-asset.md)
- [Carregar um arquivo – CLI](./scripts/cli-upload-file-asset.md)
- [Criar transformações – CLI](./scripts/cli-create-transform.md)
- [Criar trabalhos – CLI](./scripts/cli-create-jobs.md)
- [Criar uma EventGrid – CLI](./scripts/cli-create-event-grid.md)
- [Publicar um ativo – CLI](./scripts/cli-publish-asset.md)
- [Filtro – CLI](filters-dynamic-manifest-cli-howto.md)

## <a name="next-steps"></a>Próximas etapas

[CLI do Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
