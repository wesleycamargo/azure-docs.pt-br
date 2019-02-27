---
title: Migrar da Fala do Bing para o Serviço de Fala
titleSuffix: Azure Cognitive Services
description: Conheça as diferenças entre a Fala do Bing e o Serviço de Fala do ponto de vista do desenvolvedor e migre seu aplicativo para usar o Serviço de Fala.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: gracez
ms.openlocfilehash: bd9e9d64849c90d23146b814d2d48a8400d069f0
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447949"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Migrar da Fala do Bing para o Serviço de Fala

Use este artigo para migrar seus aplicativos da API de Fala do Bing para o Serviço de Fala.

Este artigo descreve as diferenças entre as APIs de Fala do Bing e o Serviço de Fala e sugere estratégias para migração de seus aplicativos. Sua chave de assinatura da API de Fala do Bing não será aceita pelo Serviço de Fala; será necessária uma nova assinatura do Serviço de Fala.

Uma única chave de assinatura do Serviço de Fala concede acesso aos recursos a seguir. Cada um deles é medido separadamente e, portanto, você será cobrado somente pelos recursos que usar.

* [Conversão de fala em texto](speech-to-text.md)
* [Conversão de fala em texto personalizada](https://cris.ai)
* [Conversão de texto em fala](text-to-speech.md)
* [Conversão de texto em vozes de fala personalizada](how-to-customize-voice-font.md)
* [Tradução de fala](speech-translation.md) (não inclui [Tradução de texto](../translator/translator-info-overview.md))

O [SDK de Fala](speech-sdk.md) é um substituto funcional para as bibliotecas de cliente de Fala do Bing, mas usa uma API diferente.

## <a name="comparison-of-features"></a>Comparação de recursos

O Serviço de Fala é muito semelhante à Fala do Bing, com as seguintes diferenças.

Recurso | Fala do Bing | Serviço de Fala | Detalhes
-|-|-|-
SDK do C++ | :heavy_minus_sign: | :heavy_check_mark: | O Serviço de Fala é compatível com o Windows e o Linux.
Java SDK | :heavy_check_mark: | :heavy_check_mark: | O Serviço de Fala é compatível com dispositivos de Fala e Android.
SDK do C# | :heavy_check_mark: | :heavy_check_mark: | O Serviço de Fala é compatível com o Windows 10, a UWP (Plataforma Universal do Windows) e o .NET Standard 2.0.
Reconhecimento de fala contínua | 10 minutos | Ilimitada (com o SDK) | Tanto os protocolos WebSockets do Serviço de Fala como da Fala do Bing são compatíveis com até dez minutos por chamada. No entanto, o SDK de Fala se reconecta automaticamente no tempo limite ou se desconecta.
Resultados intermediários ou parciais | :heavy_check_mark: | :heavy_check_mark: | Com o protocolo WebSockets ou SDK.
Modelos de fala personalizados | :heavy_check_mark: | :heavy_check_mark: | A Fala do Bing requer uma assinatura separada da Fala Personalizada.
Fontes de voz personalizadas | :heavy_check_mark: | :heavy_check_mark: | A Fala do Bing requer uma assinatura separada da Voz Personalizada.
Vozes de 24 KHz | :heavy_minus_sign: | :heavy_check_mark: 
Reconhecimento de intenção de fala | Requer uma chamada separada à API LUIS | Integrada (com o SDK) |  É possível usar uma chave do LUIS com o Serviço de Fala.
Reconhecimento simples de intenção | :heavy_minus_sign: | :heavy_check_mark: 
Transcrição em lote de arquivos de áudio longos | :heavy_minus_sign: | :heavy_check_mark:
Modo de reconhecimento | Manual por meio do URI do ponto de extremidade | Automático | O modo de reconhecimento não está disponível no Serviço de Fala.
Localidade do ponto de extremidade | Global | Regional | Os pontos de extremidade regionais melhoram a latência.
APIs REST | :heavy_check_mark: | :heavy_check_mark: | A API REST do Serviço de Fala é compatível com a Fala do Bing (ponto de extremidade diferente). As APIs REST são compatíveis com as funcionalidades de conversão de texto em fala e uma funcionalidade limitada de conversão de fala em texto.
Protocolos WebSockets | :heavy_check_mark: | :heavy_check_mark: | A API WebSockets do Serviço de Fala é compatível com a Fala do Bing (ponto de extremidade diferente). Se possível, migre para o SDK de Fala para simplificar seu código.
Chamadas à API de serviço a serviço | :heavy_check_mark: | :heavy_minus_sign: | Fornecidas na Fala do Bing por meio da Biblioteca de Serviço do C#. 
SDK de código-fonte aberto | :heavy_check_mark: | :heavy_minus_sign: |

O Serviço de Fala usa um modelo de preços baseado no tempo (em vez de um modelo baseado em transação). Confira [Preços do Serviço de Fala](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) para obter detalhes.

## <a name="migration-strategies"></a>Estratégias de migração

Se você ou sua organização tiver aplicativos em desenvolvimento ou em produção que usem a API de Fala do Bing, atualize-os para o Serviço de Fala o quanto antes. Confira os SDKs disponíveis, os exemplos de código e tutoriais na [documentação do Serviço de Fala](index.yml).

As [APIs REST](rest-apis.md) do Serviço de Fala são compatíveis com as APIs de Fala do Bing. Se no momento você estiver usando as APIs REST de Fala do Bing, basta alterar o ponto de extremidade REST e alternar para uma chave de assinatura do Serviço de Fala.

Os protocolos WebSockets do Serviço de Fala também são compatíveis com os usados pela Fala do Bing. É recomendável que você use o SDK do Speech em vez de WebSockets para o novo desenvolvimento. É uma boa ideia migrar o código existente para o SDK também. No entanto, da mesma forma que com as APIs REST, o código existente que usa a Fala do Bing por meio de WebSockets requer apenas uma alteração no ponto de extremidade e uma chave atualizada.

Se você estiver usando uma biblioteca de clientes de Fala do Bing para uma linguagem de programação específica, migrar para o [SDK de Fala](speech-sdk.md) exigirá alterações no seu aplicativo porque a API é diferente. O SDK de Fala pode tornar seu código mais simples e dar acesso a novos recursos.

Atualmente, o SDK de Fala é compatível com C# (Windows 10, UWP, .NET Standard), Java (dispositivos Android e personalizados), Objective C (iOS), C++ (Windows e Linux) e JavaScript. As APIs em todas as plataformas são semelhantes, facilitando o desenvolvimento multiplataforma.

No momento, o Serviço de Fala não oferece um ponto de extremidade global. Determine se o aplicativo funcionará de maneira eficiente usando um ponto de extremidade regional único para todo o tráfego. Caso contrário, use a geolocalização para determinar o ponto de extremidade mais eficiente. Você precisará de uma assinatura separada do Serviço de Fala em cada região que usar.

Se o aplicativo usar conexões de longa duração e não for possível usar os SDKs disponíveis, você poderá usar uma conexão WebSockets. Gerencie o limite de tempo limite de 10 minutos ao fazer a reconexão nos momentos apropriados.

Como começar a usar o SDK de Fala:

1. Faça o download do [SDK de Fala](speech-sdk.md).
1. Trabalhe com os [guias de início rápido](quickstart-csharp-dotnet-windows.md) e os [tutoriais](how-to-recognize-intents-from-speech-csharp.md) do Serviço de Fala. Examine também os [exemplos de código](samples.md) para ter experiência com as novas APIs.
1. Atualize o aplicativo para usar o Serviço de Fala e as APIs.

## <a name="support"></a>Suporte

Os clientes de Fala do Bing devem entrar em contato com o atendimento ao cliente abrindo um [tíquete de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Você também pode entrar em contato conosco caso sua necessidade de suporte precise de um [plano de suporte técnico](https://azure.microsoft.com/support/plans/).

Acesse a [página de suporte](support.md) do Serviço de Fala para obter suporte para a API, o SDK e o Serviço de Fala.

## <a name="next-steps"></a>Próximas etapas

* [Experimentar o Serviço de Fala gratuitamente](get-started.md)
* [Início Rápido: reconhecer a fala em um aplicativo UWP usando o SDK de Fala](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Consulte também
* [Notas de versão do Serviço de Fala](releasenotes.md)
* [O que é o Serviço de Fala](overview.md)
* [Documentação do SDK e do Serviço de Fala](speech-sdk.md#get-the-sdk)
