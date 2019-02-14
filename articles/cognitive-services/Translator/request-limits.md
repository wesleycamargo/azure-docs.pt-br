---
title: Solicitar limites – API de Tradução de Texto
titleSuffix: Azure Cognitive Services
description: Este artigo lista os limites de solicitação para a API de Tradução de Texto. Cobranças são incorridas com base na contagem de caracteres, não a frequência de solicitação com um limite de 5.000 caracteres por solicitação. Limites de caractere são assinatura com base com F0 limitado a 2 milhões de caracteres por hora.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: erhopf
ms.openlocfilehash: f89e7e674efe3a823b7c969840772565650d8d07
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55859463"
---
# <a name="request-limits-for-translator-text"></a>Limites de solicitação para a Tradução de Texto

Este artigo fornece limites de limitação para a API de Tradução de Texto. Os serviços incluem tradução, transliteração, detecção de comprimento de frase, detecção de idioma e traduções alternativas.

## <a name="character-limits-per-request"></a>Limites de caracteres por solicitação

Cada solicitação é limitada a 5.000 caracteres. Você é cobrado por personagem, não pelo número de solicitações. É recomendável enviar solicitações mais curtas e ter algumas solicitações pendentes a qualquer momento.

Não há limite no número de solicitações pendentes para a API de Tradução de Texto.

## <a name="character-limits-per-hour"></a>Limites de caractere por hora

Seu limite de caractere por hora baseia-se em sua camada de assinatura de Tradução de Texto. Se você atinge ou ultrapassar esses limites, você provavelmente receberá uma falta de resposta de cota:

| Camada | Limite de caracteres |
|------|-----------------|
| F0 | 2 milhões de caracteres por hora |
| S1 | 40 milhões de caracteres por hora |
| S2 | 40 milhões de caracteres por hora |
| S3 | 120 milhões de caracteres por hora |
| S4 | 200 milhões de caracteres por hora |

Esses limites são restritos aos sistemas genéricos da Microsoft. Sistemas de conversão personalizada que usam o Hub do Microsoft Translator são limitados a caracteres 1.800 por segundo.

## <a name="latency"></a>Latency

Tradução de Texto tem uma latência máxima de 13 segundos. Nesse momento você vai ter recebido um resultado ou uma resposta de tempo limite. Normalmente, as respostas são retornadas em 150 milissegundos para 300 milissegundos. Os tempos de resposta variam de acordo com o tamanho ou a solicitação e o par de idiomas.

## <a name="sentence-length-limits"></a>Limites de duração de sentença

Ao usar a função [BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence), o comprimento da sentença é limitado a 275 caracteres. Existem exceções para esses idiomas:

| Linguagem | Código | Limite de caracteres |
|----------|------|-----------------|
| Chinês | zh | 132 |
| Alemão | de | 290 |
| Italiano | it | 280 |
| Japonês | ja | 150 |
| Português | pt | 290 |
| Espanhol | es | 280 |
| Italiano | it | 280 |
| Tailandês | th | 258 |

> [!NOTE]
> Esse limite não se aplica a traduções.

## <a name="next-steps"></a>Próximas etapas

* [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Disponibilidade regional](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [referência de API de Tradução de Texto v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
