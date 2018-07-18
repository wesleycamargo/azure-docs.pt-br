---
title: Criar uma conta dos Serviços de Mídia do Azure com a CLI 2.0 | Microsoft Docs
description: Execute as etapas neste início rápido para criar uma conta dos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: cflower
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 03/27/2018
ms.author: juliako
ms.openlocfilehash: a9660ac61bab9f8b9eb9563aab4cc584786b25ae
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="create-an-azure-media-services-account"></a>Criar uma conta de Serviços de Mídia do Azure

Para iniciar a criptografia, codificação, analise, gerenciamento e streaming de conteúdo de mídia no Azure, você precisa criar uma conta dos Serviços de Mídia. Quando você cria uma conta de Serviços de Mídia, você também cria uma conta de armazenamento associada (ou usa uma existente) na mesma região geográfica que a conta de Serviços de Mídia.

Este tópico descreve as etapas para criar uma nova conta dos Serviços de Mídia do Azure usando a CLI 2.0.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no [Portal do Azure](http://portal.azure.com) e inicie o **CloudShell** para executar comandos da CLI, conforme mostra as etapas a seguir.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá a CLI do Azure versão 2.0 ou posterior. Execute `az --version` descobrir a versão que você tem. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

## <a name="set-the-azure-subscription"></a>Definir a assinatura do Azure

No comando a seguir, forneça a ID de assinatura do Azure que você deseja usar para a conta de Serviços de Mídia. Veja uma lista das assinaturas as quais você tem acesso navegando até [Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli-interactive
az account set --subscription mySubscriptionId
```
 
[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]
 
## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Transmitir um arquivo por streaming](stream-files-dotnet-quickstart.md)
