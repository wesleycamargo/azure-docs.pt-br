---
title: Exemplo de Script da CLI do Azure - Publicar um ativo | Microsoft Docs
description: Use o script de CLI do Azure para publicar um ativo.
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
ms.openlocfilehash: 5394ec12e0a11ac68585dcd64dc5255c38249937
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161356"
---
# <a name="cli-example-publish-an-asset"></a>Exemplo de CLI: publicar um ativo

O script de CLI do Azure neste artigo mostra como criar um Localizador de Streaming e recuperas as URLs de Streaming. 

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Caso opte por instalar e usar a CLI localmente, este artigo exige que seja executada a CLI do Azure versão 2.0.20 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/publish-asset/Publish-Asset.sh "Publish an asset")]

## <a name="next-steps"></a>Próximas etapas

Para obter mais exemplos, consulte [exemplos de CLI do Azure](../cli-samples.md).
