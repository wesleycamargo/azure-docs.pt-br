---
title: Exemplos de código – Content Moderator, .NET
description: Usar o Content Moderator em seus aplicativos
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: sample
ms.date: 01/10/2018
ms.author: sajagtap
ms.openlocfilehash: c5465a2de0924037eb93b9de652e93e85f166737
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223144"
---
# <a name="net-sdk-samples"></a>Amostras do SDK do .NET

A lista a seguir inclui links para exemplos de código criados usando o SDK do Content Moderator do Azure para .NET.

- **Biblioteca auxiliar**: [Criar um cliente do Content Moderator para uso em outros exemplos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs). Consulte o [início rápido](content-moderator-helper-quickstart-dotnet.md).

## <a name="moderation"></a>Moderação

- **Moderação de imagem**: [Avaliar uma imagem de texto, faces e conteúdo para adulto e explícito](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Consulte o [início rápido](image-moderation-quickstart-dotnet.md).
- **Imagens personalizadas**: [Moderar com listas de imagens personalizadas](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Consulte o [início rápido](image-lists-quickstart-dotnet.md).

> [!NOTE]
> Há um limite máximo de **5 listas de imagens** com cada lista para **não exceder 10.000 imagens**.
>

- **Moderação de texto**: [Classificar conteúdo ofensivo e PII (informações de identificação pessoal)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Consulte o [início rápido](text-moderation-quickstart-dotnet.md).
- **Termos personalizados**: [Moderar com lista de termos personalizados](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Consulte o [início rápido](term-lists-quickstart-dotnet.md).

> [!NOTE]
> Há um limite máximo de **5 listas de termos** com cada lista para **não exceder 10.000 termos**.
>

- **Moderação de vídeo**: [Examinar um vídeo em relação à conteúdo para adulto e explícito e obter resultados](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). Consulte o [início rápido](video-moderation-api.md).

## <a name="review"></a>Análise

- **Trabalhos de imagem**: [Iniciar um trabalho de moderação que examina e cria análises](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Consulte o [início rápido](moderation-jobs-quickstart-dotnet.md).
- **Análise de imagens**: [Criar análises para human-in-the-loop](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Consulte o [início rápido](moderation-reviews-quickstart-dotnet.md).
- **Análise de vídeos**: [Criar análises de vídeo para human-in-the-loop](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Consulte o [início rápido](video-reviews-quickstart-dotnet.md)
- **Análises de transcrição de vídeo**: [Criar análises de transcrição de vídeo para human-in-the-loop](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs) Consulte o [início rápido](video-reviews-quickstart-dotnet.md)

Veja todos os exemplos de .NET nos [Exemplos amostras de .NET do Content Moderator no GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).
