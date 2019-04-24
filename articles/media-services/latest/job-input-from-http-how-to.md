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
ms.date: 02/13/2019
ms.author: juliako
ms.openlocfilehash: 5e301a671551ee65e8dc56ca6f86e273fe2f6241
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322593"
---
# <a name="create-a-job-input-from-an-https-url"></a>Criar uma entrada de trabalho de uma URL HTTPS

Nos Serviços de Mídia v3, ao enviar Trabalhos para processar seus vídeos, você precisa informar os Serviços de Mídia onde encontrar o vídeo de entrada. Uma das opções é especificar uma URL HTTPS como um trabalho de entrada (conforme mostrado neste exemplo). Observe que, no momento, o AMS v3 não dá suporte à codificação de transferência em partes sobre URLs HTTPS. Para obter um exemplo completo, confira esta [amostra do GitHub](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>Exemplo de .NET

O código a seguir mostra como criar um trabalho com uma entrada de URL HTTPS.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Códigos de erro do trabalho

Confira [Códigos de erro](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Próximos passos

[Criar uma entrada de trabalho de um arquivo local](job-input-from-local-file-how-to.md).
