---
title: Exemplo de Script da CLI do Azure - Publicar um ativo | Microsoft Docs
description: Use o script de CLI do Azure para publicar um ativo.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/11/2018
ms.author: juliako
ms.openlocfilehash: 1ce85c18b93f22e9078e551deda74c6e6ff0b28b
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614915"
---
# <a name="cli-example-publish-an-asset"></a>Exemplo de CLI: publicar um ativo

O script de CLI do Azure neste artigo mostra como criar um Localizador de Streaming e recuperas as URLs de Streaming. 

## <a name="prerequisites"></a>Pré-requisitos 

- Instalar e usar a CLI localmente, este artigo requer a versão 2.0 ou posterior da CLI do Azure. Execute `az --version` descobrir a versão que você tem. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

    Atualmente, nem todos os comandos da [CLI V3 dos Serviços de Mídia](https://aka.ms/ams-v3-cli-ref) funcionam no Azure Cloud Shell. É recomendável usar a CLI localmente.

- [Crie uma conta de Serviços de Mídia](../create-account-cli-how-to.md).

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/publish-asset/Publish-Asset.sh "Publish an asset")]

## <a name="next-steps"></a>Próximas etapas

Para obter mais exemplos, consulte [exemplos de CLI do Azure](../cli-samples.md).
