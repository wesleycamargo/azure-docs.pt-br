---
title: Criar uma entrada de trabalho dos Serviços de Mídia do Azure com base em uma URL HTTPS | Microsoft Docs
description: Este tópico mostra como criar uma entrada de trabalho de uma URL HTTP(s).
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: bac11640f5256223c1053f03da42c763508af98f
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377371"
---
# <a name="create-a-job-input-from-an-https-url"></a>Criar uma entrada de trabalho de uma URL HTTPS

Nos Serviços de Mídia v3, ao enviar Trabalhos para processar seus vídeos, você precisa informar os Serviços de Mídia onde encontrar o vídeo de entrada. Uma das opções é especificar uma URL HTTP (s) como um trabalho de entrada (conforme mostrado neste exemplo). Observe que, no momento, o AMS v3 não dá suporte à codificação de transferência em partes sobre URLs HTTPS. Para obter um exemplo completo, confira este [exemplo do github](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>Exemplo de .NET

O código a seguir mostra como criar um trabalho com uma entrada de URL HTTPS.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>Próximas etapas

[Criar uma entrada de trabalho de um arquivo local](job-input-from-local-file-how-to.md).
