---
title: Serviço de Fala dos Serviços Cognitivos do Azure
description: Cenários
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 07/02/2018
ms.author: v-jerkin
ms.openlocfilehash: 98e79112ba70ff578975cea9d872b4fcc41512bc
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2018
ms.locfileid: "41936733"
---
# <a name="speech-scenarios"></a>Cenários de fala

Há muitos cenários que podem ser ter mais autonomia usando tecnologia de Fala. Estamos analisando alguns dos mais comuns e apontamos recursos relevantes na documentação. Para a maior parte do conteúdo, o [SDK](speech-sdk.md) é fundamental na habilitação desses cenários.

A página aborda como:
> [!div class="checklist"]
> * Criar aplicativos acionados por voz
> * Transcrever chamadas de áudio de call center
> * Bots de voz

## <a name="voice-triggered-apps"></a>Aplicativos acionados por voz

Muitos usuários querem habilitar a entrada de voz nos aplicativos. A entrada de voz é uma ótima maneira de tornar o aplicativo flexível, seja usando as mãos livres (por exemplo, em um carro) ou acelerando várias tarefas, como pedir informações sobre as notícias ou sobre o tempo. 

### <a name="voice-triggered-apps-with-baseline-models"></a>Aplicativos acionados por voz com modelos de linha de base

Se o aplicativo for utilizado por público geral em ambientes onde o ruído de fundo não for excessivo, a maneira mais fácil e rápida de fazer isso é simplesmente baixar o [SDK de Fala](speech-sdk.md) e seguir os [Exemplos](quickstart-csharp-dotnet-windows.md) relevantes. O SDK acionado pela [chave de Assinatura do Azure](https://azure.microsoft.com/try/cognitive-services/) permite que os desenvolvedores façam upload de áudio para modelos de reconhecimento de fala de linha de base que acionam a Cortana e o Skype. Os modelos são de última geração e utilizados pelos produtos acima mencionados. É possível estar funcionando em minutos.

### <a name="voice-triggered-apps-with-custom-models"></a>Aplicativos acionados por voz com modelos personalizados

Se o aplicativo abordar um domínio específico (digamos, química, biologia ou necessidades dietéticas especiais), convém adaptar um [modelo de linguagem](how-to-customize-language-model.md). Adaptar um modelo de linguagem ensinará o decodificador sobre as frases e palavras mais comuns usadas pelo aplicativo. O decodificador poderá transcrever com mais precisão uma entrada de voz com um modelo de linguagem personalizado para um domínio específico, em vez do modelo de linha de base. Da mesma forma, se o ruído de fundo onde o aplicativo será usado for elevado, convém adaptar um modelo acústico. Explore a documentação de outros casos em que a [adaptação de linguagem](how-to-customize-language-model.md) e [adaptação acústica](how-to-customize-acoustic-models.md) agregam valor e visite o [portal de adaptação](https://customspeech.ai) para iniciar a experiência na criação de modelos. Semelhante aos modelos de linha de base, os modelos personalizados são chamados através do [SDK de Fala](speech-sdk.md) e seguindo os [Exemplos](quickstart-csharp-dotnet-windows.md) relevantes.

## <a name="transcribe-call-center-audio-calls"></a>Transcrever chamadas de áudio de Call center

Call centers acumulam grandes quantidades de áudio. Oculto dentro desses arquivos de áudio reside o valor que pode ser obtido através da transcrição. A duração da chamada, o sentimento, a satisfação do cliente e o valor geral da chamada fornecida ao chamador podem ser descobertos através da obtenção de transcrições de chamadas.

O melhor ponto de partida é a [API de transcrição de lote](batch-transcription.md) juntamente com o [Exemplo](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

Primeiro, será necessário obter uma [chave de assinatura do Azure ](https://azure.microsoft.com/try/cognitive-services/) e, em seguida, consultar a documentação da [API de transcrição do lote](batch-transcription.md).

### <a name="transcribe-call-center-audio-calls-with-baseline-models"></a>Transcrever chamadas de áudio de call center com modelos de linha de base

A decisão que precisa ser tomada é se você usará os modelos de linha de base internos para realizar a transcrição, adaptar uma linguagem ou um modelo acústico, ou ambos. Para usar modelos de linha de base, a API exige apenas a chave de API. Internamente, a API invocará o melhor modelo para os dados e se adaptará.

### <a name="transcribe-call-center-audio-calls-with-custom-models"></a>Transcrever chamadas de áudio de Call center com modelos personalizados

Se você planeja usar um modelo personalizado, precisará da ID desse modelo junto com a chave de API. A ID do Modelo é obtida no [portal de adaptação](https://customspeech.ai). Essa não é a ID do ponto de extremidade localizada na exibição "Detalhes do Ponto de Extremidade", mas a ID do modelo que você pode recuperar ao clicar nos "Detalhes" desse modelo.

## <a name="voice-bots"></a>Bots de voz

Os desenvolvedores podem acionar o aplicativo com saída de voz. O Serviço de Fala pode sintetizar a voz em vários [idiomas](supported-languages.md) e fornecer [pontos de extremidade](rest-apis.md) para acessar e adicionar esse recurso ao aplicativo.

Além disso, para usuários que querem adicionar mais personalidade e exclusividade aos bots, o Serviço de Fala permite que os desenvolvedores personalizem uma fonte de voz exclusiva. Semelhante à personalização de modelos de reconhecimento de fala, as fontes de voz exigem dados de usuário. Os desenvolvedores fazem upload desses dados no [portal de adaptação de voz](https://customspeech.ai) e iniciam a construção da marca de voz exclusiva para o bot. Detalhes são descritos [aqui](how-to-text-to-speech.md), assim como as páginas de [Perguntas frequentes](faq-text-to-speech.md) 

## <a name="next-steps"></a>Próximas etapas

* [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
* [Iniciar com o SDK de Fala](speech-sdk.md)
