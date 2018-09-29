---
title: Exemplo de script da CLI do Azure - Criar uma transformação | Microsoft Docs
description: Use o script da CLI do Azure para criar uma Transformação.
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
ms.openlocfilehash: cbe20c4b75fff27fad60446fb933a9c8ba2e3312
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47091771"
---
# <a name="cli-example-create-a-transform"></a>Exemplo da CLI: criar uma transformação

O script da CLI do Azure neste artigo mostra como criar uma transformação. Transformações descreve um fluxo de trabalho simples de tarefas para processar seus arquivos de áudio ou vídeos (também conhecidos como “receita”). Você sempre deve verificar se uma Transformação com o nome desejado e "receita" já existe. Se existir, você deve reutilizá-la.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Caso opte por instalar e usar a CLI localmente, este artigo exige que seja executada a CLI do Azure versão 2.0.20 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>Próximas etapas

Para obter mais exemplos, consulte [exemplos de CLI do Azure](../cli-samples.md).
