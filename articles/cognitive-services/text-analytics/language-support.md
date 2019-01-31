---
title: Idiomas compatíveis – API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: 'Uma lista dos idiomas naturais compatíveis com a API de Análise de Texto. Este artigo explica quais idiomas têm suporte para cada operação: análise de sentimento, extração de frases-chave, detecção de idioma e reconhecimento de entidade.'
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: ashmaka
ms.openlocfilehash: ec1858256c942fd50818a214de426fd00c3c2c88
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55216924"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Idiomas e regiões compatíveis com a API de Análise de Texto

Este artigo explica quais idiomas têm suporte para cada operação: análise de sentimento, extração de frases-chave e detecção de idioma.

## <a name="language-detection"></a>Detecção de Idioma

A API de Análise de Texto pode detectar até 120 idiomas diferentes. A detecção de idioma retorna o "script" de um idioma. Por exemplo, a frase "Eu tenho um cachorro" retornará `en`, em vez de `en-US`. O único especial é chinês, em que a funcionalidade de detecção de idioma retornará `zh_CHS` ou `zh_CHT` se ela puder determinar o script fornecido pelo texto fornecido. Em situações em que um script específico não pode ser identificado para um documento em chinês, ela retornará simplesmente `zh`.

## <a name="sentiment-analysis-key-phrase-extraction-and-entity-recognition"></a>Análise de Sentimento, Extração de Frases-chave e Reconhecimento de Entidade

Para análise de sentimento, extração de frases-chave e reconhecimento de entidade, a lista de idiomas compatíveis é mais seletiva, uma vez que os analisadores são refinados para acomodar as regras linguísticas de idiomas adicionais.

## <a name="language-list-and-status"></a>Status e lista de idiomas

O suporte a idiomas é implantado inicialmente em versão prévia, passando para o status GA (disponível ao público em geral), de modo independente um do outro e do serviço de Análise de Texto como um todo. É possível que idiomas permaneçam em versão prévia mesmo quando a API de Análise de Texto passar para disponível ao público em geral.

| Linguagem    | Código de idioma | Sentimento | Frases principais | Reconhecimento de Entidade |   Observações  |
|:----------- |:-------------:|:---------:|:-----------:|:-----------:|:-----------:
| Dinamarquês      | `da`          | ✔ \*     | ✔           |             |     |
| Holandês       | `nl`          | ✔ \*     | ✔          |             |     |
| Inglês     | `en`          | ✔        | ✔           |  ✔ \*   |      |
| Finlandês     | `fi`          | ✔ \*     | ✔           |             |     |
| Francês      | `fr`          | ✔        | ✔           |             |     |
| Alemão      | `de`          | ✔ \*     | ✔           |            |     |
| Grego       | `el`          | ✔ \*     |             |            |     |
| Italiano     | `it`          | ✔ \*     | ✔           |             |     |
| Japonês    | `ja`          |          | ✔           |            |     |
| Coreano      | `ko`          |          | ✔           |            |     |
| Norueguês (Bokmål) | `no`          | ✔ \*     |  ✔          |             |     |
| Polonês      | `pl`          | ✔ \*     |  ✔          |             |     |
| Português (Portugal) | `pt-PT`| ✔        |  ✔          |       |`pt` também é aceito|
| Português (Brasil)   | `pt-BR`|          |  ✔   |         |     |
| Russo     | `ru`          | ✔ \*     | ✔           |             |     |
| Espanhol     | `es`          | ✔        | ✔           |   ✔ \*\*      |     |
| Sueco     | `sv`          | ✔ \*     | ✔           |             |     |
| Turco     | `tr`          | ✔ \*     |             |             |  |

\* indica suporte a idiomas na versão prévia

\*\* A extração de entidade para espanhol só está disponível no [(versão 2.1-prévia)](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634)

## <a name="see-also"></a>Consulte também

[Página Documentação dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/)   
[Página do Produto de Serviços Cognitivos](https://azure.microsoft.com/services/cognitive-services/)
