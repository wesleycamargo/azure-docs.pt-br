---
title: Exemplo de Script da CLI do Azure - Redefinir as credenciais de sua conta | Microsoft Docs
description: Use o script da CLI do Azure para redefinir as credenciais de sua conta e recuperar as configurações do app.config.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/11/2018
ms.author: juliako
ms.openlocfilehash: 7f9868b0fb53eb75ccf4742c0bf522d35f6ff902
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47091805"
---
# <a name="cli-example-reset-the-account-credentials"></a>Exemplo da CLI: redefinir as credenciais da conta

O script da CLI do Azure neste artigo mostra como redefinir as credenciais da sua conta e recuperar as configurações de app.config.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Caso opte por instalar e usar a CLI localmente, este artigo exige que seja executada a CLI do Azure versão 2.0.20 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/reset-account-credentials/Reset-Account-Credentials.sh "Reset credentials")]

## <a name="next-steps"></a>Próximas etapas

Para obter mais exemplos, consulte [exemplos de CLI do Azure](../cli-samples.md).
