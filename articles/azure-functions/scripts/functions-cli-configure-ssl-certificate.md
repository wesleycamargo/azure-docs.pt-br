---
title: Exemplo de Script da CLI do Azure - Associar um certificado SSL personalizado a um aplicativo de funções | Microsoft Docs
description: Exemplo de Script da CLI do Azure - Associar um certificado SSL personalizado a um aplicativo de funções
services: functions
documentationcenter: ''
author: ggailey777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.service: functions
ms.workload: na
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 07/03/2013
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 9b6779ac7778b721ff566c8553433853dbadbf13
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38988254"
---
# <a name="bind-a-custom-ssl-certificate-to-a-function-app"></a>Associar um certificado SSL personalizado a um aplicativo de funções

Este exemplo de script cria um aplicativo de funções no Serviço de Aplicativo com seus recursos relacionados e associa o certificado SSL de um nome de domínio personalizado a ele. Neste exemplo, você precisa de:

* Acesso à página de configuração do DNS do registrador de seu domínio.
* Um arquivo .PFX válido e sua senha para o certificado SSL que você deseja carregar e associar.
* Ter configurado um registro em seu domínio personalizado que aponte para o nome de domínio do seu aplicativo Web padrão. Para saber mais, veja [Mapear instruções de domínio personalizadas para o Serviço de Aplicativo do Azure](https://aka.ms/appservicecustomdns).

Para associar um certificado SSL, seu aplicativo de funções deve ser criado em um Plano do Serviço de Aplicativo, e não em um plano de consumo.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, será necessário executar a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Cria uma conta de armazenamento necessária para o aplicativo de funções. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az-appservice-plan-create) | Cria um Plano do Serviço de Aplicativo necessário para associar certificados SSL. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Implante um aplicativo de funções no plano do Serviço de Aplicativo. |
| [az functionapp config hostname add](https://docs.microsoft.com/cli/azure/functionapp/config/hostname#az-functionapp-config-hostname-add) | Mapear um domínio personalizado para um aplicativo de funções. |
| [az functionapp config ssl upload](https://docs.microsoft.com/cli/azure/functionapp/config/ssl#az-functionapp-config-ssl-upload) | Carrega um certificado SSL em um aplicativo de funções. |
| [az functionapp config ssl bind](https://docs.microsoft.com/cli/azure/functionapp/config/ssl#az-functionapp-config-ssl-bind) | Associa um certificado SSL carregado a um aplicativo de funções. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Os exemplos de script da CLI do Serviço de Aplicativo adicionais podem ser encontrados na [documentação do Serviço de Aplicativo do Azure](../functions-cli-samples.md).
