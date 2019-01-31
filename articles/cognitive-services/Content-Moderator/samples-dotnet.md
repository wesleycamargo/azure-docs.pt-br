---
title: Exemplos de código – Content Moderator, .NET
description: Use o Content Moderator em seus aplicativos .NET por meio do SDK.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 43aa1ca624bce78fa113c34fa19da9ccfea69bbc
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55222092"
---
# <a name="content-moderator-net-sdk-samples"></a>Amostras do SDK do .NET do Content Moderator

A lista a seguir inclui links para exemplos de código criados usando o SDK do Content Moderator do Azure para .NET.

- **Biblioteca auxiliar**: [Crie um cliente do Content Moderator para uso em outras amostras](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs). Consulte o [início rápido](content-moderator-helper-quickstart-dotnet.md).

## <a name="moderation"></a>Moderação

- **Moderação de imagem**: [Avalie uma imagem em relação a conteúdo para adulto e sexual, texto e rostos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Consulte o [início rápido](image-moderation-quickstart-dotnet.md).
- **Imagens personalizadas**: [Modere com listas de imagem personalizadas](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Consulte o [início rápido](image-lists-quickstart-dotnet.md).

> [!NOTE]
> Há um limite máximo de **cinco listas de imagens**, e cada lista **não deve exceder 10 mil imagens**.
>

- **Moderação de texto**: [Classifique um texto em relação a profanação e PII (informações de identificação pessoal)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Consulte o [início rápido](text-moderation-quickstart-dotnet.md).
- **Termos personalizados**: [Modere com listas de termos personalizadas](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Consulte o [início rápido](term-lists-quickstart-dotnet.md).

> [!NOTE]
> Há um limite máximo de **5 listas de termos** com cada lista para **não exceder 10.000 termos**.
>

- **Moderação de vídeo**: [Examine um vídeo em relação a conteúdo para adulto e sexual e obtenha resultados](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). Consulte o [início rápido](video-moderation-api.md).

## <a name="review"></a>Análise

- **Trabalhos de imagem**: [Inicie um trabalho de moderação que examina e cria revisões](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Consulte o [início rápido](moderation-jobs-quickstart-dotnet.md).
- **Análises de imagem**: [Crie revisões com interação humana](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Consulte o [início rápido](moderation-reviews-quickstart-dotnet.md).
- **Revisões de vídeo**: [Crie revisões de vídeo com interação humana](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Consulte o [início rápido](video-reviews-quickstart-dotnet.md)
- **Revisões de transcrição de vídeo**: [Crie revisões de transcrição de vídeo com interação humana](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs) Confira o [início rápido](video-reviews-quickstart-dotnet.md)

Veja todos os exemplos de .NET nos [Exemplos amostras de .NET do Content Moderator no GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).
