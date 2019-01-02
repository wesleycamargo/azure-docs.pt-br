---
title: Use a CLI para dimensionar as Unidades Reservadas de Mídia - Azure | Microsoft Docs
description: Este tópico mostra como usar a CLI para dimensionar processamento de mídia com os Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 2b10de83e00b3668f70461f76634c560bcbea1a4
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53133784"
---
# <a name="scaling-media-processing"></a>Dimensionamento de processamento de mídia

Os Serviços de Mídia do Azure permitem dimensionar o processamento de mídia em sua conta, gerenciando Unidades Reservadas de Mídia (MRUs). Para uma visão geral detalhada, consulte [Scaling media processing](../previous/media-services-scale-media-processing-overview.md). 

Este artigo mostra como usar o [Serviços de Mídia do Microsoft Azure v3 CLI](https://aka.ms/ams-v3-cli-ref) para dimensionar MRUs.

> [!NOTE]
> Para os trabalhos de análise de áudio e análise de vídeo acionados pelo Serviços de Mídia do Microsoft Azure v3 ou pelo Video Indexer, é altamente recomendável provisionar sua conta com 10 MRUs do S3. <br/>Se você precisar de mais de 10 MRUs do S3, abra um ticket de suporte usando o [Portal do Microsoft Azure](https://portal.azure.com/).

## <a name="prerequisites"></a>Pré-requisitos 

- Instalar e usar a CLI localmente, este artigo requer a versão 2.0 ou posterior da CLI do Azure. Execute `az --version` descobrir a versão que você tem. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

    Atualmente, nem todos os comandos da [CLI V3 dos Serviços de Mídia](https://aka.ms/ams-v3-cli-ref) funcionam no Azure Cloud Shell. É recomendável usar a CLI localmente.

- [Crie uma conta de Serviços de Mídia](create-account-cli-how-to.md).

## <a name="scale-media-reserved-units-with-cli"></a>Unidades com a CLI reservadas de mídia de escala

O comando [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) a seguir define as Unidades Reservadas de Mídia na conta "amsaccount" usando os parâmetros **count** e **type**.

```azurecli
az account set mru -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Cobrança

Você é cobrado com base no número, tipo e quantidade de tempo que os MRUs são provisionados em sua conta. Encargos se aplicam se você executa trabalhos. Para obter uma explicação detalhada, consulte a seção Perguntas frequentes da página [Preços dos Serviços de Mídia](https://azure.microsoft.com/pricing/details/media-services/).   

## <a name="next-step"></a>Próxima etapa

[Analisar vídeos](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Consulte também

[CLI do Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
