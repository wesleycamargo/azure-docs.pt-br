---
title: "Como adicionar unidades de codificação"
description: "Saiba como adicionar unidades de codificação com o .NET"
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2016
ms.author: juliako;milangada;gtrifonov
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3dc67791d7bfd7221ad484e967b5e9bf49ed8c02


---
# <a name="how-to-scale-encoding-with-net-sdk"></a>Como dimensionar a codificação com o SDK do .NET
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
> * [REST](https://msdn.microsoft.com/library/azure/dn859236.aspx)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Visão geral
> [!IMPORTANT]
> Lembre-se de examinar o tópico [Visão geral](media-services-scale-media-processing-overview.md) para obter mais informações sobre o tópico de dimensionamento de processamento de mídia.
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
Por padrão, todas as contas dos Serviços de Mídia podem ser dimensionadas para até 25 Unidades Reservadas para Codificação e cinco para Streaming por Demanda. Você pode solicitar um limite mais alto abrindo um tíquete de suporte.

### <a name="open-a-support-ticket"></a>Abra um tíquete de suporte
Para abrir um tíquete de suporte, faça o seguinte:

1. Clique em [Obter suporte](https://manage.windowsazure.com/?getsupport=true). Se você não estiver conectado, você será solicitado a inserir suas credenciais.
2. Selecione sua assinatura.
3. Em tipo de suporte, selecione "Técnico".
4. Clique em "Criar Tíquete".
5. Selecione "Serviços de Mídia do Azure" na lista de produtos apresentados na próxima página.
6. Selecione um "tipo de problema" que é apropriado para o seu problema.
7. Clique em Continuar.
8. Siga as instruções na próxima página e, em seguida, insira os detalhes sobre o problema.
9. Clique em Enviar para abrir o tíquete.

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Nov16_HO3-->


