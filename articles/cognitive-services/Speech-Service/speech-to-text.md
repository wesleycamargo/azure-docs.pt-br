---
title: Sobre a Conversão de Fala em Texto – Serviços de Fala
titleSuffix: Azure Cognitive Services
description: A API de Conversão de Fala em Texto transcreve fluxos de áudio em texto que o aplicativo pode exibir ou atuar como entrada. O serviço está disponível por meio do SDK e um ponto de extremidade RESTful.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 050d395614fa4a08e6d0d0967200029e8fc36010
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53094705"
---
# <a name="about-the-speech-to-text-api"></a>Sobre a API de Conversão de Fala em Texto

A **API de** Conversão de Fala em Texto *transcreve* fluxos de áudio em texto que o aplicativo pode exibir para o usuário ou atuar como entrada de comando. As APIs podem ser usadas com uma biblioteca de clientes do SDK (para plataformas e idiomas com suporte) ou com uma API REST.

A API de **Conversão de Fala em Texto** fornece os seguintes recursos:

- Tecnologia avançada de reconhecimento de fala da Microsoft—a mesma usada pela Cortana, Office e outros produtos da Microsoft.

- Reconhecimento contínuo em tempo real. A **Conversão de Fala em Texto** permite aos usuários transcrever o áudio em texto em tempo real. Também dá suporte para recebimento de resultados intermediários das palavras que foram reconhecidas até o momento. O serviço reconhece automaticamente o final da fala. Os usuários também podem escolher opções de formatação adicionais, incluindo capitalização e pontuação, mascaramento de conteúdo ofensivo e normalização de texto inverso.

- Resultados retornados nas formas léxica e de exibição (para resultados léxicos, consulte DetailedSpeechRecognitionResult nos exemplos ou na API).

- Suporte para muitos idiomas falados e dialetos. Para obter uma lista completa dos idiomas com suporte em cada modo de reconhecimento, consulte [Idiomas com suporte](language-support.md#speech-to-text).

- Linguagem personalizada e modelos acústicos, para que você possa adaptar o aplicativo ao vocabulário de domínio especializado dos usuários, ao ambiente de fala e ao modo de falar.

- Reconhecimento em idioma natural. Através da integração com LUIS ([Serviço Inteligente de Reconhecimento Vocal](https://docs.microsoft.com/azure/cognitive-services/luis/)), é possível derivar intenções e entidades da fala. Os usuários não precisam saber o vocabulário do aplicativo, mas podem descrever o que querem com suas próprias palavras.

- A pontuação de confiança será retornada do serviço se você especificar uma saída detalhada no objeto de configuração de fala (propriedade SpeechConfig.OutputFormat). Em seguida, você poderá usar o método Best () no resultado ou obter a pontuação diretamente do JSON retornado do serviço (algo como result.Properties.GetProperty(PropertyId.SpeechServiceResponse_JsonResult)).

## <a name="api-capabilities"></a>Recursos de API

Muitas das funcionalidades da API de **conversão de fala em texto**, principalmente em relação à personalização, estão disponíveis por meio de REST. A tabela a seguir resume os recursos de cada método de acesso à API. Para obter uma lista completa de recursos e detalhes da API, consulte [referência ao Swagger](https://westus.cris.ai/swagger/ui/index).

| Caso de uso | REST | SDKs |
|-----|-----|-----|----|
| Transcreve um enunciado curto, como um comando (comprimento <15 s); sem resultados provisórios | SIM | SIM |
| Transcreve uma frase mais longa (> 15 s) | Não  | SIM |
| Transcreve áudio de streaming com resultados provisórios opcionais | Não  | SIM |
| Reconhece as intenções do falante via LUIS | Não\* | SIM |
| Criar testes de precisão | SIM | Não  |
| Carregar conjuntos de dados para adaptação de modelo | SIM | Não  |
| Criar e gerenciar modelos de fala | SIM | Não  |
| Criar e gerenciar implantações de modelo | SIM | Não  |
| Gerenciar Assinaturas | SIM | Não  |
| Criar e gerenciar implantações de modelo | SIM | Não  |
| Criar e gerenciar implantações de modelo | SIM | Não  |

> [!NOTE]
> A API REST implementa a limitação que limita as solicitações de API a 25 a cada cinco segundos. Cabeçalhos de mensagens informarão sobre os limites

\* *entidades e intenções de LUIS podem ser derivadas usando uma assinatura do LUIS separada. Com essa assinatura, o SDK pode chamar o LUIS para você e fornecer resultados de entidade e intenção, bem como transcrições de fala. Com a API REST, você mesmo pode chamar o LUIS para derivar intenções e entidades com assinatura do LUIS.*

## <a name="next-steps"></a>Próximas etapas

* [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
* [Início Rápido: Como reconhecer fala no C#](quickstart-csharp-dotnet-windows.md)
* [Como reconhecer intenções da fala no C#](how-to-recognize-intents-from-speech-csharp.md)
