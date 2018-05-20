---
title: Exemplo de Script da CLI do Azure - Criar uma assinatura de Grade de Eventos do Azure | Microsoft Docs
description: O script de CLI do Azure neste tópico mostra como criar um assinatura de nível de conta da Grade de Eventos para Alterações de Estado do Trabalho.
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
ms.openlocfilehash: b46cd5cf8f730a680078dde06b58eb31846e0a76
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2018
---
# <a name="cli-example-create-an-azure-event-grid-subscription"></a>Exemplo CLI: Criar uma assinatura de Grade de Eventos do Azure 

O script de CLI do Azure neste artigo mostra como criar um assinatura de nível de conta da Grade de Eventos para Alterações de Estado do Trabalho.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Caso opte por instalar e usar a CLI localmente, este artigo exige que seja executada a CLI do Azure versão 2.0.20 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-event-grid/Create-EventGrid.sh "Create an EventGrid subscription")]

## <a name="next-steps"></a>Próximas etapas

Para obter mais exemplos, consulte [exemplos de CLI do Azure](../cli-samples.md).
