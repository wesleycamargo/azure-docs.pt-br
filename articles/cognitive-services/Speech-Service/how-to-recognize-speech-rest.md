---
title: Reconhecer fala usando a API REST
description: Saiba como usar a API de Conversão de Fala em Texto no serviço de Fala dos Serviços Cognitivos.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: v-jerkin
ms.openlocfilehash: eafec2dd262098bc4b7e485293818b79debe3d27
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43126839"
---
# <a name="recognize-speech-by-using-the-rest-api"></a>Reconhecer fala usando a API REST

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-selector.md)]

A API REST pode ser usada para reconhecer declarações curtas usando uma solicitação HTTP POST.

A API REST é a maneira mais simples de reconhecer a fala se você não está usando uma linguagem compatível com o [SDK](speech-sdk.md). Faça uma solicitação HTTP POST para o ponto de extremidade de serviço e passe a declaração inteira no corpo da solicitação. Você recebe uma resposta que contém o texto reconhecido.

> [!NOTE]
> As declarações ficam limitadas a 15 segundos ou menos ao usar a API REST.
> Fazer check-out a [Speech SDK](how-to-recognize-speech-csharp.md) para reconhecimento de declarações mais tempo.

Para saber mais sobre a API REST de **Conversão de Fala em Texto**, veja o artigo [APIs REST](rest-apis.md#speech-to-text). Para ver a API em ação, baixe os [exemplos de API REST](https://github.com/Azure-Samples/SpeechToText-REST) do GitHub.

## <a name="next-steps"></a>Próximas etapas

- Veja a [visão geral da API REST](rest-apis.md).
