---
title: Dimensionar o processamento de mídia adicionando unidades de codificação - Azure | Microsoft Docs
description: Saiba como adicionar unidades de codificação com o .NET
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2018
ms.author: juliako;milangada;
ms.openlocfilehash: 8f17d5e6d45b678f5c4a0c4318e74a18c42ff0c8
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51035709"
---
# <a name="how-to-scale-encoding-with-net-sdk"></a>Como dimensionar a codificação com o SDK do .NET
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Visão geral
> [!IMPORTANT]
> Lembre-se de examinar a [Visão geral](media-services-scale-media-processing-overview.md) para obter mais informações sobre o dimensionamento de processamento de mídia.
> 
> 

Para alterar o tipo de unidade reservada e o número de unidades reservadas para codificação usando o SDK do .NET, faça o seguinte:

    IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
    encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
    encodingS1ReservedUnit.Update();
    Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);

    encodingS1ReservedUnit.CurrentReservedUnits = 2;
    encodingS1ReservedUnit.Update();

    Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

## <a name="opening-a-support-ticket"></a>Abrindo um tíquete de suporte

Por padrão, todas as contas de Serviços de Mídia podem ser escaladas para até 10 S2 ou S3 Unidades Reservadas de Mídia (MRUs) ou 25 S1 MRUs e cinco Unidades Reservadas de Streaming sob demanda. Você pode solicitar um limite mais alto abrindo um tíquete de suporte.

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

