---
title: O que é o Serviço de Fala do Azure?
titleSuffix: Azure Cognitive Services
description: O Serviço de Fala do Azure é a unificação da conversão de fala em texto, da conversão de texto em fala e da tradução de fala em uma única assinatura do Azure. É fácil adicionar serviços de fala a seus aplicativos, ferramentas e dispositivos usando o SDK de Fala, o SDK de Dispositivos de Fala ou APIs REST. Adicione funcionalidades de fala a um chatbot existente, converta texto em fala em um aplicativo de tradução ou transcreva grandes volumes de dados de call center.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 03/13/2019
ms.author: erhopf
ms.openlocfilehash: d4587b5268635691d55b51a7bf88bbe01df2a0c4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57871629"
---
# <a name="what-are-the-speech-services"></a>O que é o Serviço de Fala?

O Serviço de Fala do Azure é a unificação do reconhecimento de fala, da conversão de texto em fala e da tradução de fala em uma única assinatura do Azure. É fácil habilitar serviços de fala em seus aplicativos, ferramentas e dispositivos usando o [SDK de Fala](speech-sdk-reference.md), o [SDK de Dispositivos de Fala](speech-devices-sdk-qsg.md) ou [APIs REST](rest-apis.md).

> [!IMPORTANT]
> O Serviço de Fala substituiu a API de Fala do Bing, a Tradução de Fala e a Fala Personalizada. Confira *Guias de instruções > Migração* para obter instruções de migração.

Esses recursos compõem o Serviço de Fala do Azure. Use os links nesta tabela para saber mais sobre os casos de uso comuns de cada recurso ou navegue pela referência de API.

| Serviço | Recurso | DESCRIÇÃO | . | REST |
|---------|---------|-------------|-----|------|
| [Conversão de fala em texto](speech-to-text.md) | Conversão de fala em texto | A conversão de fala em texto transcreve, em tempo real, fluxos de áudio em texto que seus aplicativos, ferramentas ou dispositivos podem consumir ou exibir. Use a conversão de fala em texto com o [LUIS (Reconhecimento vocal)](https://docs.microsoft.com/azure/cognitive-services/luis/) para derivar intenções do usuário de dados de fala transcrita e agir com base em comandos de voz. | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Transcrição em lote](batch-transcription.md) | A Transcrição em lote permite a transcrição de fala em texto assíncrona de grandes volumes de dados. Trata-se de um serviço baseado em REST que usa o mesmo ponto de extremidade que o gerenciamento de modelos e a personalização. | Não  | [Sim](https://westus.cris.ai/swagger/ui/index) |
| | [Personalização](#customize-your-speech-experience) | Se estiver usando a conversão de fala em texto para funcionalidades de reconhecimento e transcrição em um ambiente exclusivo, você poderá criar e treinar modelos acústicos, de idioma e de pronúncia personalizados para lidar com o ruído ambiente ou vocabulário específico do setor. | Não  | [Sim](https://westus.cris.ai/swagger/ui/index) |
| [Conversão de Texto em Fala](text-to-speech.md) | Conversão de texto em fala | A conversão de texto em fala converte o texto de entrada em uma fala sintetizada semelhante à humana. Escolha entre as vozes padrão e vozes neurais (consulte [Suporte para idiomas](language-support.md)). | Não  | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Personalização](#customize-your-speech-experience) | Crie fontes de voz personalizadas exclusivas para sua marca ou produto. | Não  | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Tradução de Fala](speech-translation.md) | Tradução de fala | A tradução de fala possibilita a tradução em tempo real e em vários idiomas de fala para seus aplicativos, suas ferramentas e seus dispositivos. Use esse serviço para tradução de fala em fala e fala em texto. | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Não  |

## <a name="news-and-updates"></a>Novidades e atualizações

Saiba o que há de novo no Serviço de Fala do Azure.

* Fevereiro de 2019 – lançado SDK de Fala 1.3.0 com suporte para [Unity (beta)](quickstart-csharp-unity.md). Adicionado suporte para a classe `AudioInput`, que permite que você escolha a fonte do streaming de áudio. Para obter uma lista completa de aperfeiçoamentos e problemas conhecidos, confira as [Notas sobre a versão](releasenotes.md).
* Dezembro de 2018 – lançado o SDK de Fala 1.2.0 com suporte para [Python](quickstart-python.md) e [Node.js](quickstart-js-node.md), assim como o Ubuntu 18.04 LTS. Para obter mais informações, confira as [Notas sobre a versão](releasenotes.md).
* Dezembro de 2018 – guias de início rápido de conversão de texto em fala adicionados para [.NET Core](quickstart-dotnet-text-to-speech.md), [Python](quickstart-python-text-to-speech.md) e [Node.js](quickstart-nodejs-text-to-speech.md). Exemplos adicionais estão disponíveis no [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http).

## <a name="try-speech-services"></a>Experimente o Serviço de Fala

Nós oferecemos guias de início rápido nas linguagens de programação mais populares, todos eles desenvolvidos para que você executar o código em menos de 10 minutos. Esta tabela contém os guias de início rápido mais populares de cada recurso. Use o painel de navegação esquerdo para explorar plataformas e linguagens adicionais.

| Conversão de fala em texto (SDK) | Tradução (SDK) | Texto em fala (REST) |
|-------------------|-------------------|-----------------------|
| [C#, .NET Core (Windows)](quickstart-csharp-dotnet-windows.md) | [Java (Windows, Linux)](quickstart-translate-speech-java-jre.md) | [Python (Windows, Linux, macOS)](quickstart-python-text-to-speech.md) |
| [Javascript (Navegador)](quickstart-js-browser.md) | [C#, .NET Core (Windows)](quickstart-translate-speech-dotnetcore-windows.md) | [C#, .NET Core (Windows, Linux, macOS)](quickstart-dotnet-text-to-speech.md) |
| [Python (Windows, Linux, macOS)](quickstart-python.md) | [C#, .NET Framework (Windows)](quickstart-translate-speech-dotnetframework-windows.md) | [Node.js (Windows, Linux, macOS)](quickstart-nodejs-text-to-speech.md) |
| [Java (Windows, Linux)](quickstart-java-jre.md) | [C++ (Windows)](quickstart-translate-speech-cpp-windows.md) | |

Depois que tiver a oportunidade de usar o Serviço de Fala, experimente nosso tutorial que ensina a reconhecer intenções de fala usando o SDK de Fala e o LUIS.

* [Tutorial: Reconhecer intenções de fala com o SDK de Fala e o LUIS, C#](how-to-recognize-intents-from-speech-csharp.md)

## <a name="get-sample-code"></a>Obter código de exemplo

Há um código de exemplo disponível no GitHub para cada um dos Serviços de Fala do Azure. Esses exemplos abordam cenários comuns, como ler áudio de um arquivo ou fluxo, reconhecimento contínuo e único e trabalho com modelos personalizados. Use estes links para exibir exemplos de SDK e REST:

* [Exemplos de conversão de fala em texto e tradução de fala (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Exemplos de transcrição em lote (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [Exemplos de conversão de texto em fala (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customize-your-speech-experience"></a>Personalize sua experiência de fala

O Serviço de Fala do Azure funciona bem com modelos internos, no entanto, talvez você queira personalizar e ajustar ainda mais a experiência para seu ambiente ou produto. As opções de personalização vão do ajuste do modelo acústico a fontes de voz exclusivas para sua marca. Depois de criar um modelo personalizado, você pode usá-lo com qualquer um dos Serviços de Fala do Azure.

| Serviço de Fala | Modelo | DESCRIÇÃO |
|----------------|-------|-------------|
| Conversão de fala em texto | [Modelo acústico](how-to-customize-acoustic-models.md) | Crie um modelo acústico personalizado para aplicativos, ferramentas ou dispositivos usados em ambientes particulares, como um carro ou um chão de fábrica, cada um deles com condições específicas de gravação. Exemplos incluem fala com sotaque, ruídos de fundo específicos ou uso de um microfone específico para gravação. |
| | [Modelo de linguagem](how-to-customize-language-model.md) | Crie um modelo de linguagem personalizado para melhorar a transcrição do vocabulário e da gramática específicos de um campo, por exemplo, terminologia médica ou jargão de TI. |
| | [Modelo de pronúncia](how-to-customize-pronunciation.md) | Usando um modelo de pronúncia personalizado, você pode definir a forma fonética e a exibição de uma palavra ou termo. É útil para lidar com termos personalizados, como nomes de produtos ou acrônimos. Tudo o que você precisa para começar é de um arquivo de pronúncia (um arquivo .txt simples). |
| Conversão de texto em fala | [Fonte de voz](how-to-customize-voice-font.md) | Fontes de voz personalizadas permitem que você crie uma voz reconhecível exclusiva para sua marca. É necessária apenas uma pequena quantidade de dados para começar a usar. Quanto mais dados que você fornecer, mais natural e humana sua fonte de voz soará. |

## <a name="reference-docs"></a>Documentos de Referência

* [SDK da fala](speech-sdk-reference.md)
* [SDK de Dispositivos de Fala](speech-devices-sdk.md)
* [API REST: conversão de fala em texto](rest-speech-to-text.md)
* [API REST: conversão de texto em fala](rest-text-to-speech.md)
* [API REST: transcrição e personalização em lote](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Obter gratuitamente uma chave de assinatura do Serviço de Fala](get-started.md)
