---
title: SDKs e exemplos – Content Moderator, Python, Java, Node.js e .NET
titlesuffix: Azure Cognitive Services
description: Obter SDKs e exemplos para Content Moderator
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 02/27/2018
ms.author: sajagtap
ms.openlocfilehash: fd71a48372bcdb459bb3b7509e9a9c5dba529555
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756600"
---
# <a name="content-moderator-sdks-and-samples"></a>SDKs do Content Moderator e exemplos

## <a name="sdks-for-python-java-nodejs-and-net"></a>SDKs para Python, Java, Node.js e .NET

- [SDK do Content Moderator para Python](https://pypi.python.org/pypi/azure-cognitiveservices-vision-contentmoderator)
- [SDK do Content Moderator para Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-cognitiveservices-contentmoderator%22)
- [SDK do Content Moderator para Node.js](https://www.npmjs.com/package/azure-cognitiveservices-contentmoderator)
- [SDK do Content Moderator para .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/)

## <a name="net-sdk-samples"></a>Exemplos de SDK do .NET

A lista a seguir inclui links para exemplos de código criados usando o SDK do Content Moderator do Azure para .NET.

- **Biblioteca auxiliar**: [Crie um cliente do Content Moderator para uso em outras amostras](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs). Consulte o [início rápido](content-moderator-helper-quickstart-dotnet.md).

### <a name="moderation"></a>Moderação 
- **Moderação de imagem**: [Avalie uma imagem em relação a conteúdo para adulto e sexual, texto e rostos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Consulte o [início rápido](image-moderation-quickstart-dotnet.md).
- **Imagens personalizadas**: [Modere com listas de imagem personalizadas](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Consulte o [início rápido](image-lists-quickstart-dotnet.md).

> [!NOTE]
> Há um limite máximo de **cinco listas de imagens**, e cada lista **não deve exceder 10 mil imagens**.
>

- **Moderação de texto**: [Tela de texto para conteúdo ofensivo e dados pessoais](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Consulte o [início rápido](text-moderation-quickstart-dotnet.md).
- **Termos personalizados**: [Modere com listas de termos personalizadas](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Consulte o [início rápido](term-lists-quickstart-dotnet.md).

> [!NOTE]
> Há um limite máximo de **5 listas de termos** com cada lista para **não exceder 10.000 termos**.
>

- **Moderação de vídeo**: [Examine um vídeo em relação a conteúdo para adulto e sexual e obtenha resultados](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). Consulte o [início rápido](video-moderation-api.md).

### <a name="review"></a>Análise
- **Trabalhos de imagem**: [Inicie um trabalho de moderação que examina e cria revisões](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Consulte o [início rápido](moderation-jobs-quickstart-dotnet.md).
- **Análises de imagem**: [Crie revisões com interação humana](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Consulte o [início rápido](moderation-reviews-quickstart-dotnet.md).
- **Revisões de vídeo**: [Crie revisões de vídeo com interação humana](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Consulte o [início rápido](video-reviews-quickstart-dotnet.md)
- **Revisões de transcrição de vídeo**: [Crie revisões de transcrição de vídeo com interação humana](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs) Confira o [início rápido](video-reviews-quickstart-dotnet.md)

Veja todos os exemplos de .NET nos [Exemplos amostras de .NET do Content Moderator no GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).

## <a name="rest-api-samples-in-c"></a>Exemplos de API REST em C#

- [Moderação de imagem](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageModeration)
- [Moderação de texto](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/TextModeration)
- [Moderação de vídeo](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/VideoModeration)
- [Análises de imagem](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageReviews)
- [Trabalhos de imagem](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageJob)

Para obter explicações passo a passo desses exemplos, confira o [webinar sob demanda](https://info.microsoft.com/cognitive-services-content-moderator-ondemand.html).

## <a name="tutorials"></a>Tutoriais
- [Moderação de catálogo de comércio eletrônico](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration). Consulte o [tutorial](ecommerce-retail-catalog-moderation.md).
- [Moderação de conteúdo do Facebook](https://github.com/MicrosoftContentModerator/samples-fbPageModeration). Consulte o [tutorial](facebook-post-moderation.md).
- [Solução de moderação e análise de vídeo e transcrição](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) Consulte o [tutorial](video-transcript-moderation-review-tutorial-dotnet.md)

## <a name="on-demand-webinars"></a>Webinars sob demanda
- [Moderação de conteúdo assistido por computador em escala com Content Moderator](https://info.microsoft.com/cognitive-services-content-moderator-ondemand.html)
