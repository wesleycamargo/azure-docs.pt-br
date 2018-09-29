---
title: Exemplo de script da CLI do Azure - Criar e enviar um trabalho | Microsoft Docs
description: O script da CLI do Azure neste tópico mostra como enviar um trabalho para uma Transformação de codificação simples usando a URL HTTPs.
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
ms.openlocfilehash: 0b11d095e1c4a5aa75936c0437cbeb5c65550937
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47094046"
---
# <a name="cli-example-create-and-submit-a-job"></a>Exemplo da CLI: criar e enviar um trabalho

O script da CLI do Azure neste artigo mostra como criar e enviar um trabalho para uma Transformação de codificação simples usando a URL HTTPs.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Caso opte por instalar e usar a CLI localmente, este artigo exige que seja executada a CLI do Azure versão 2.0.20 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-jobs/Create-Jobs.sh "Create and submit jobs")]

## <a name="next-steps"></a>Próximas etapas

Para obter mais exemplos, consulte [exemplos de CLI do Azure](../cli-samples.md).
