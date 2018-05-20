---
title: Criar uma entrada de trabalho dos Serviços de Mídia do Azure a partir de uma URL HTTP(s) | Microsoft Docs
description: Este tópico mostra como criar uma entrada de trabalho de uma URL HTTP(s).
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: d429665de64dacc5818d1d26c2a9029531cd39b3
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2018
---
# <a name="create-a-job-input-from-an-https-url"></a>Criar uma entrada de trabalho de uma URL HTTP(s)

Nos Serviços de Mídia v3, ao enviar Trabalhos para processar seus vídeos, você precisa informar os Serviços de Mídia onde encontrar o vídeo de entrada. Uma das opções é especificar uma URL HTTP (s) como um trabalho de entrada (conforme mostrado neste exemplo). Para obter um exemplo completo, confira este [exemplo do github](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>Exemplo de .NET

O código a seguir mostra como criar um trabalho com uma entrada de URL HTTPS.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>Próximas etapas

[Criar uma entrada de trabalho de um arquivo local](job-input-from-local-file-how-to.md).
