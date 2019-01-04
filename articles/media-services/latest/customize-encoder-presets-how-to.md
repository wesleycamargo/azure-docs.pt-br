---
title: Codificar transformação personalizada usando os Serviços de Mídia - Azure v3 | Microsoft Docs
description: Este tópico mostra como usar os Serviços de Mídia do Azure v3 para codificar uma transformação personalizada.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 12/08/2018
ms.author: juliako
ms.openlocfilehash: c62d9132cdd7eb2ebcbecc3c417ad30d368a278a
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53138697"
---
# <a name="how-to-encode-with-a-custom-transform"></a>Como codificar com uma transformação personalizada

Ao codificar com os Serviços de Mídia do Azure, você pode iniciar de forma rápida com uma das predefinições internas recomendadas com base nas melhores práticas do setor, conforme demonstrado no tutorial [Arquivos por streaming](stream-files-tutorial-with-api.md), ou você pode optar por criar uma predefinição personalizada para segmentar os requisitos de dispositivo ou cenário específico. 

> [!Note]
> Nos Serviços de Mídia do Azure v3, todas as taxas de bits de codificação estão em bits por segundo. Isso é diferente das predefinições do Media Encoder Standard do REST v2. Por exemplo, a taxa de bits na v2 seria especificada como 128, mas em v3 seria 128000.

## <a name="download-the-sample"></a>Baixar o exemplo

Clone um repositório do GitHub que contém o exemplo de .NET Core completo em sua máquina usando o comando a seguir:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
O exemplo de predefinição personalizado está localizado na pasta [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/).

## <a name="create-a-transform-with-a-custom-preset"></a>Criar uma transformação com uma predefinição personalizada 

Ao criar uma nova [Transformação](https://docs.microsoft.com/rest/api/media/transforms), você precisará especificar o que deseja produzir como uma saída. O parâmetro necessário é um objeto **TransformOutput**, como mostrado no código a seguir. Cada **TransformOutput** contém um **Predefinição**. **Predefinição** descreve as instruções passo a passo das operações de processamento de áudio e/ou vídeo que serão usadas para gerar o **TransformOutput** desejado. O seguinte **TransformOutput** cria configurações personalizadas de saída de codec e camada.

Ao criar uma [Transformação](https://docs.microsoft.com/rest/api/media/transforms), você deverá verificar primeiro se já existe uma usando o método **Get**, conforme mostrado no código a seguir.  Em Serviços de Mídia v3, os métodos **Get** em entidades retornarão **nulo** se a entidade não existir (uma verificação de maiúsculas e minúsculas no nome).

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>Próximas etapas

[Arquivos por streaming](stream-files-tutorial-with-api.md) 
