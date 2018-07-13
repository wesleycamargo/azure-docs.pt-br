---
title: Sobre Conversão de Fala em Texto | Microsoft Docs
description: Uma visão geral dos recursos da API de Conversão de Fala em Texto.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 2eee1c6f9158f128ed5ffe575f8f498f1d3eb5e9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364787"
---
# <a name="about-the-speech-to-text-api"></a>Sobre a API de Conversão de Fala em Texto

A **API de** Conversão de Fala em Texto *transcreve* fluxos de áudio em texto que o aplicativo pode exibir para o usuário ou atuar como entrada de comando. As APIs podem ser usadas com uma biblioteca de clientes do SDK (para plataformas e idiomas com suporte) ou com uma API REST.

A API de **Conversão de Fala em Texto** fornece os seguintes recursos:

- Tecnologia avançada de reconhecimento de fala da Microsoft—a mesma usada pela Cortana, Office e outros produtos da Microsoft.

- Reconhecimento contínuo em tempo real. A **Conversão de Fala em Texto** permite aos usuários transcrever o áudio em texto em tempo real. Também dá suporte para recebimento de resultados intermediários das palavras que foram reconhecidas até o momento. O serviço reconhece automaticamente o final da fala. Os usuários também podem escolher opções de formatação adicionais, incluindo capitalização e pontuação, mascaramento de conteúdo ofensivo e normalização de texto.

- Resultados de **Conversão de Fala em Texto** otimizados para cenários de ditado, conversação e interativo. 

- Suporte para muitos idiomas falados em múltiplos dialetos. Para obter uma lista completa dos idiomas com suporte em cada modo de reconhecimento, consulte [Idiomas com suporte](supported-languages.md#speech-to-text).

- Linguagem personalizada e modelos acústicos, para que você possa adaptar o aplicativo ao modo de falar, ambiente de fala e ao vocabulário especializado dos usuários.

- Reconhecimento em idioma natural. Através da integração com LUIS ([Serviço Inteligente de Reconhecimento Vocal](https://docs.microsoft.com/azure/cognitive-services/luis/)), é possível derivar intenções e entidades da fala. Os usuários não precisam saber o vocabulário do aplicativo, mas podem descrever o que querem com suas próprias palavras.

## <a name="api-capabilities"></a>Recursos de API

Alguns recursos de API de **Conversão de Fala em Texto** não estão disponíveis via REST. A tabela a seguir resume os recursos de cada método de acesso à API.

| Caso de uso | REST | SDKs |
|-----|-----|-----|----|
| Transcreve um enunciado curto, como um comando (comprimento <15 s); sem resultados provisórios | sim | sim |
| Transcreve uma frase mais longa (> 15 s) | Não  | sim |
| Transcreve áudio de streaming com resultados provisórios opcionais | Não  | sim |
| Reconhece as intenções do falante via LUIS | Não\* | sim |

\* *entidades e intenções de LUIS podem ser derivadas usando uma assinatura do LUIS separada. Com essa assinatura, o SDK pode chamar o LUIS para você e fornecer resultados de entidade e intenção, bem como transcrições de fala. Com a API REST, você mesmo pode chamar o LUIS para derivar intenções e entidades com assinatura do LUIS.*

## <a name="next-steps"></a>Próximas etapas

* [Obter a assinatura de avaliação do Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Saiba como reconhecer fala em C#](quickstart-csharp-windows.md)
