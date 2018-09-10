---
title: Usar conversão de Fala em Texto | Microsoft Docs
description: Saiba como usar a Conversão de Fala em Texto no serviço de Fala
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: v-jerkin
ms.openlocfilehash: 26cecedfc3ad2d472b9686e25054fe08253cee77
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39068515"
---
# <a name="use-speech-to-text-in-the-speech-service"></a>Usar a "Conversão de Fala em Texto" no serviço de Fala

Você pode usar a **Conversão de Fala em Texto** em seus aplicativos de duas maneiras diferentes.

| Método | DESCRIÇÃO |
|-|-|
| [SDK](speech-sdk.md) | Método mais simples para desenvolvedores em C/C++, C# e Java |
| [REST](rest-apis.md) | Reconhecer declarações curtas usando uma solicitação HTTP POST | 

## <a name="using-the-sdk"></a>Usar o SDK

O [SDK de Fala](speech-sdk.md) fornece a maneira mais simples de usar a **Conversão de Fala em Texto** em seu aplicativo com a funcionalidade completa.

1. Crie um alocador de fala, fornecendo uma chave de assinatura do serviço de Fala e [região](regions.md) ou um token de autorização. Neste ponto, você também pode configurar opções, como o idioma de reconhecimento ou um ponto de extremidade personalizado, para seus próprios modelos de reconhecimento de fala.

2. Obter um reconhecedor do alocador. Há três tipos diferentes de reconhecedores disponíveis. Cada tipo de reconhecedor pode usar o microfone padrão do dispositivo, um fluxo de áudio ou áudio de um arquivo.

    Reconhecedor | Função
    -|-
    Reconhecedor de Fala|Fornece a transcrição de texto da fala
    Reconhecedor de intenção|Obtém a intenção do locutor via [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/) após o reconhecimento\*
    Reconhecedor de tradução|Traduz o texto transcrito para outro idioma (confira [Tradução de Fala](how-to-translate-speech.md))

    \* *Para o reconhecimento de intenção, você precisa usar uma chave de assinatura do LUIS separada ao criar um alocador de fala para o reconhecedor de intenção.*
    
4. Associa eventos para operação assíncrona, se você quiser. Depois, o reconhecedor chama seus manipuladores de eventos quando houver resultados intermediários e finais. Caso contrário, o aplicativo receberá um resultado de transcrição final.

5. Iniciar reconhecimento.
   Para reconhecimento de único disparo, como comando ou reconhecimento de consulta, use `RecognizeAsync()`, que retorna o primeiro enunciado sendo reconhecido.
   Para reconhecimento de execução longa, como transcrição, use `StartContinuousRecognitionAsync()` e vincule os eventos para resultados de reconhecimento assíncrono.

### <a name="sdk-samples"></a>Amostras do SDK

Para obter o último conjunto de exemplos, consulte o [Repositório GitHub de exemplo do SDK de Fala dos Serviços Cognitivos](https://aka.ms/csspeech/samples).

## <a name="using-the-rest-api"></a>Usando a API REST

A API REST é a maneira mais simples de reconhecer a fala se você não estiver usando uma linguagem com suporte do SDK. Faça uma solicitação HTTP POST para o ponto de extremidade do serviço, passando a declaração inteira no corpo da solicitação. Você recebe uma resposta que contém o texto reconhecido.

> [!NOTE]
> As declarações ficam limitadas a 15 segundos ou menos ao usar a API REST.

Para saber mais sobre a API REST de **Conversão de Fala em Texto**, veja [APIs REST](rest-apis.md#speech-to-text). Para vê-los em ação, baixe os [exemplos de API REST](https://github.com/Azure-Samples/SpeechToText-REST) do GitHub.

## <a name="next-steps"></a>Próximas etapas

- [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
- [Como reconhecer a fala em C++](quickstart-cpp-windows.md)
- [Como reconhecer fala no C#](quickstart-csharp-dotnet-windows.md)
- [Como reconhecer fala em Java](quickstart-java-android.md)
