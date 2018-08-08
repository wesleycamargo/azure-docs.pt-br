---
title: Reconhecer fala usando a API REST
description: Saiba como usar a Conversão de Fala em Texto no serviço de Fala
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: v-jerkin
ms.openlocfilehash: 15db2d9b872c76d70fd531af07fb55c701e86494
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39331206"
---
# <a name="recognize-speech-by-using-the-rest-api"></a>Reconhecer fala usando a API REST

[!include[Selector](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-selector.md)]

A API REST pode ser usada para reconhecer declarações curtas usando uma solicitação HTTP POST.

A API REST é a maneira mais simples de reconhecer a fala se você não estiver usando uma linguagem com suporte do SDK.
Faça uma solicitação HTTP POST para o ponto de extremidade do serviço, passando a declaração inteira no corpo da solicitação.
Você recebe uma resposta que contém o texto reconhecido.

> [!NOTE]
> As declarações ficam limitadas a 15 segundos ou menos ao usar a API REST.
> Fazer check-out a [Speech SDK](how-to-recognize-speech-csharp.md) para reconhecimento de declarações mais tempo.

Para saber mais sobre a API REST de **Conversão de Fala em Texto**, veja [APIs REST](rest-apis.md#speech-to-text). Para vê-los em ação, baixe os [exemplos de API REST](https://github.com/Azure-Samples/SpeechToText-REST) do GitHub.

## <a name="next-steps"></a>Próximas etapas

- [Consulte a visão geral da API REST](rest-apis.md)
