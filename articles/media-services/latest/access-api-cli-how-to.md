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
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 54a92e56df21b59430ed12f191a9cf7a918e14c9
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51612892"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Acessar a API dos Serviços de Mídia do Azure com a CLI do Azure
 
Use a autenticação de entidade de serviço do Azure AD para se conectar à API dos Serviços de Mídia do Microsoft Azure. Seu aplicativo precisa solicitar um token do Azure AD que tenha os seguintes parâmetros:

* Ponto de extremidade de locatário do Azure AD
* URI de recursos dos Serviços de Mídia
* URI de recurso dos Serviços de Mídia REST
* Valores do aplicativo do Azure AD: a ID do cliente e o segredo do cliente

Este artigo mostra como usar a CLI do Azure para criar um aplicativo e uma entidade de serviço do Azure AD e obter os valores necessários para acessar os recursos dos Serviços de Mídia do Azure.

## <a name="prerequisites"></a>Pré-requisitos 

- Instalar e usar a CLI localmente, este artigo requer a versão 2.0 ou posterior do CLI do Azure. Execute `az --version` descobrir a versão que você tem. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

    Atualmente, nem todos os comandos [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) funcionam no Azure Cloud Shell. É recomendável usar a CLI localmente.

- [Crie uma conta de Serviços de Mídia](create-account-cli-how-to.md).

    Lembre-se dos valores que você usou para o nome do grupo de recursos e o nome da conta de Serviços de Mídia.

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="next-steps"></a>Próximas etapas

[Transmitir um arquivo por streaming](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>Consulte também

[CLI do Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
