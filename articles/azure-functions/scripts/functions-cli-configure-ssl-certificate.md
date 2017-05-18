---
title: "Exemplo de Script da CLI do Azure - Associar um certificado SSL personalizado a um aplicativo de funções | Microsoft Docs"
description: "Exemplo de Script da CLI do Azure - Associar um certificado SSL personalizado a um aplicativo de funções"
services: functions
documentationcenter: 
author: ggailey777
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.service: functions
ms.workload: na
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 04/10/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 9b7f84f5ad01335a3ce72b6b2aa654968fdd9499
ms.contentlocale: pt-br
ms.lasthandoff: 05/15/2017

---
# <a name="bind-a-custom-ssl-certificate-to-a-function-app"></a>Associar um certificado SSL personalizado a um aplicativo de funções

Este exemplo de script cria um aplicativo de funções no Serviço de Aplicativo com seus recursos relacionados e associa o certificado SSL de um nome de domínio personalizado a ele. Para esta amostra, você precisará de:

* Acesso à página de configuração do DNS do registrador de seu domínio.
* Um arquivo .PFX válido e sua senha para o certificado SSL que você deseja carregar e associar.

Para associar um certificado SSL, seu aplicativo de funções deve ser criado em um Plano do Serviço de Aplicativo, e não em um plano de consumo.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[principal](../../../cli_scripts/azure-functions/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Associar um certificado SSL personalizado a um aplicativo Web")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Cria um Plano do Serviço de Aplicativo necessário para associar certificados SSL. |
| [az functionapp create]() | Cria um aplicativo de funções. |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | Mapeia um domínio personalizado para o aplicativo de funções. |
| [az appservice web config ssl upload](https://docs.microsoft.com/cli/azure/appservice/web/config/ssl#upload) | Carrega um certificado SSL em um aplicativo de funções. |
| [az appservice web config ssl bind](https://docs.microsoft.com/en-us/cli/azure/appservice/web/config/ssl#bind) | Associa um certificado SSL carregado a um aplicativo de funções. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Os exemplos de script da CLI do Serviço de Aplicativo adicionais podem ser encontrados na [documentação do Serviço de Aplicativo do Azure]().

