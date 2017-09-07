---
title: "Exemplo de Script CLI do Azure - mapeie um domínio personalizado para um aplicativo de funções | Microsoft Docs"
description: "Exemplo de Script CLI do Azure - mapeie um domínio personalizado para um aplicativo de funções no Azure."
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: d127e347-7581-47d7-b289-e0f51f2fbfbc
ms.service: functions
ms.workload: na
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 06/01/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 5a17830ad3b879382b7e82d435f1286c7ecdf7c5
ms.contentlocale: pt-br
ms.lasthandoff: 08/29/2017

---
# <a name="map-a-custom-domain-to-a-function-app"></a>Mapear um domínio personalizado para um aplicativo de funções

Este exemplo de script cria um aplicativo de funções com seus recursos relacionados e depois mapeia `www.<yourdomain>` para ele. Para mapear um domínio personalizado, seu aplicativo de funções deve ser criado em um Plano do Serviço de Aplicativo, e não em um plano de consumo. O Azure Functions oferece suporte ao mapeamento apenas um domínio personalizado usando um registro A.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, será necessário usar a CLI do Azure 2.0 ou uma versão posterior. Para saber qual é a versão, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 


## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-custom-domain/configure-custom-domain.sh?highlight=3 "Mapear um domínio personalizado para um aplicativo de funções")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos: cada comando na tabela contém links para a respectiva documentação específica.

| Command | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#create) | Cria uma conta de armazenamento necessária para o aplicativo de funções. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Cria um Plano do Serviço de Aplicativo necessário para mapear um domínio personalizado. |
| [az functionapp create]() | Cria um aplicativo de funções. |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | Mapear um domínio personalizado para um aplicativo de funções. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos adicionais de scripts da CLI do Functions podem ser encontrados na [Documentação do Azure Functions]().

