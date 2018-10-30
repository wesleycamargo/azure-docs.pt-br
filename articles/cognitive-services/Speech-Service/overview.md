---
title: O que é o Serviço de Fala?
titleSuffix: Azure Cognitive Services
description: 'O Serviço de Fala, parte dos Serviços Cognitivos do Azure, une vários serviços de fala que estavam disponíveis anteriormente de forma separada: Fala do Bing (que inclui o reconhecimento de fala e a conversão de texto em fala), Fala Personalizada e Tradução de Fala.'
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: overview
ms.date: 09/24/2018
ms.author: erhopf
ms.openlocfilehash: ba4204c23f3467ff07940fd6a72464e67604dde1
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49470439"
---
# <a name="what-is-the-speech-service"></a>O que é o Serviço de Fala?


Como outros serviços de fala do Azure, o serviço de Fala é alimentado pelas tecnologias de fala usadas em produtos como o Cortana e o Microsoft Office.

O serviço de Fala une os recursos de fala do Azure disponíveis anteriormente por meio dos serviços [API de Fala do Bing](https://docs.microsoft.com/azure/cognitive-services/speech/home), [Tradução de Fala](https://docs.microsoft.com/azure/cognitive-services/translator-speech/), [Fala Personalizada](https://docs.microsoft.com/azure/cognitive-services/custom-speech-service/cognitive-services-custom-speech-home) e [ Voz Personalizada](http://customvoice.ai/). Agora, uma assinatura fornece acesso a todos esses recursos.

## <a name="main-speech-service-functions"></a>Principais funções do serviço de Fala

As principais funções do serviço de Fala são Conversão de Fala em Texto (também chamado de reconhecimento de fala ou transcrição), Conversão de Texto em Fala (síntese de fala) e Tradução de Fala.

|Função|Recursos|
|-|-|
|[Conversão de Fala em Texto](speech-to-text.md)| <li>Transcreve fala em tempo real contínua em texto.<li>Pode transcrever em lotes fala de gravações de áudio. <li>Dá suporte a resultados intermediários, detecção de final de fala, formatação automática de texto e mascaramento de conteúdo ofensivo. <li>Pode chamar [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/) (Reconhecimento vocal) para avaliar a intenção do usuário a partir da fala transcrita.\*|
|[Conversão de Texto em Fala](text-to-speech.md)| <li>Converte o texto em fala que soa natural. <li>Oferece vários gêneros e/ou dialetos para muitos idiomas com suporte. <li>Dá suporte à entrada de texto sem formatação ou a SSML (Speech Synthesis Markup Language). |
|[Tradução de Fala](speech-translation.md)| <li>Converte o fluxo de áudio em tempo quase real.<li> Também pode processar fala gravada.<li>Fornece resultados como texto ou fala sintetizada. |


## <a name="customize-speech-features"></a>Personalizar os recursos de fala

Você pode usar seus próprios dados para treinar os modelos que dão suporte aos recursos de conversão de fala em texto e de texto em fala do serviço de Fala.

|Recurso|Modelo|Finalidade|
|-|-|-|
|Conversão de Fala em Texto|[Modelo acústico](how-to-customize-acoustic-models.md)|Ajuda a transcrever falantes e ambientes específicos, como carros ou fábricas.|
||[Modelo de linguagem](how-to-customize-language-model.md)|Ajuda a transcrever vocabulário e gramática específicos de campo, como jargão médico ou de TI.|
||[Modelo de pronúncia](how-to-customize-pronunciation.md)|Ajuda a transcrever abreviações e acrônimos, como "IOU" para "I owe you" (Sou grato a você). |
|Texto em fala|[Fonte de voz](how-to-customize-voice-font.md)|Dá a seu aplicativo uma voz própria treinando o modelo com amostras de fala humana.|

Você pode usar seus modelos personalizados em qualquer lugar em que usaria os modelos padrão na funcionalidade de Conversão de Fala em Texto ou Conversão de Texto em Fala do seu aplicativo.

## <a name="use-the-speech-service"></a>Usar o Serviço de Fala

Para simplificar o desenvolvimento de aplicativos habilitados para fala, a Microsoft fornece o [SDK de Fala](speech-sdk.md) para uso com o serviço de Fala. O SDK de Fala fornece APIs de Conversão de Fala em Texto e APIs de Tradução de Fala nativas consistentes para C#, C++ e Java. Se você desenvolver em uma dessas linguagens, o SDK de Fala facilitará o desenvolvimento lidando com os detalhes da rede para você.

O serviço de Fala também possui uma [API REST](rest-apis.md) que funciona com qualquer linguagem de programação que possa fazer solicitações HTTP. A interface REST não oferece a funcionalidade de streaming em tempo real do SDK.

|<br>Método|Fala<br>em Texto|Texto em<br>Fala|Fala<br>Tradução|<br>DESCRIÇÃO|
|-|-|-|-|-|
|[SDK da fala](speech-sdk.md)|SIM|Não |SIM|APIs nativas para C#, C++ e Java a fim de simplificar o desenvolvimento.|
|[REST](rest-apis.md)|SIM|sim|Não |Uma API simples baseada em HTTP que facilita a adição de fala a seus aplicativos.|

### <a name="websockets"></a>WebSockets

O serviço de Fala também tem protocolos WebSocket para transmitir Conversão de Fala em Texto e Tradução de Fala. Os SDKs de Fala usam esses protocolos para se comunicar com o serviço de Fala. Use o SDK de Fala em vez de tentar implementar sua própria comunicação WebSocket com o serviço de Fala.

Se você já tiver código que use a Fala do Bing ou a API de Fala de Tradução de Fala via WebSockets, poderá atualizá-las para usar o serviço de Fala. Os protocolos WebSocket são compatíveis, somente os pontos de extremidade são diferentes.

### <a name="speech-devices-sdk"></a>SDK de Dispositivos de Fala

O [SDK de Dispositivos de Fala](speech-devices-sdk.md) é uma plataforma integrada de hardware e software para desenvolvedores de dispositivos habilitados para fala. Nossos parceiros de hardware fornecem designs de referência e unidades de desenvolvimento. A Microsoft fornece um SDK otimizado para dispositivos que tira proveito dos recursos do hardware.


## <a name="speech-scenarios"></a>Cenários de Fala

Casos de uso para o serviço de Fala incluem:

> [!div class="checklist"]
> * Criar aplicativos acionados por voz
> * Transcrever gravações de call center
> * Implementar bots de voz

### <a name="voice-user-interface"></a>Interface de usuário de voz

A entrada de voz é uma ótima maneira de tornar seu aplicativo flexível, não assistido e rápido de usar. Com um aplicativo habilitado para voz, os usuários podem solicitar apenas as informações desejadas.

Se seu aplicativo se destina ao uso pelo público em geral, você pode usar os modelos de reconhecimento de fala padrão. Eles reconhecem uma ampla variedade de falantes em ambientes comuns.

Se o aplicativo é usado em um domínio específico, por exemplo, medicina ou IT, você pode criar uma [modelo de linguagem](how-to-customize-language-model.md). Você pode usar esse modelo para ensinar o serviço de Fala sobre a terminologia especial usada pelo seu aplicativo.

Se o aplicativo for usado em um ambiente barulhento, como uma fábrica, você poderá criar um [modelo acústico](how-to-customize-acoustic-models.md) personalizado. Esse modelo ajuda o serviço de Fala para distinguir fala de ruído.

### <a name="call-center-transcription"></a>Transcrição de call center

Muitas vezes, as gravações de call center serão consultadas apenas se surgir um problema com uma chamada. Com o serviço de Fala, é fácil transcrever cada gravação em texto. Você pode facilmente indexar o texto para [pesquisa de texto completo](https://docs.microsoft.com/azure/search/search-what-is-azure-search) ou aplicar [Análise de Texto](https://docs.microsoft.com/azure/cognitive-services/Text-Analytics/) para detectar o sentimento, a linguagem e as frases-chave.

Se as gravações do call center envolvem terminologia especializada, como nomes de produto ou jargões de TI, você pode criar um [modelo de linguagem](how-to-customize-language-model.md) para ensinar esse vocabulário ao serviço de Fala. Um [modelo acústico](how-to-customize-acoustic-models.md) personalizado pode ajudar o serviço de Fala a entender as conexões de telefone abaixo do ideal.

Para obter mais informações sobre esse cenário, leia mais sobre [transcrição em lote](batch-transcription.md) com o serviço de Fala.

### <a name="voice-bots"></a>Bots de voz

[Bots](https://dev.botframework.com/) são uma maneira popular de conectar os usuários com as informações que eles querem e os clientes com as empresas de que eles gostam. Quando você adiciona uma interface do usuário de conversa ao seu site ou aplicativo, fica mais fácil localizar e acessar a funcionalidade. Com o serviço de Fala, essa conversa assume uma nova dimensão de fluência respondendo às consultas faladas de maneira similar.

Para adicionar uma personalidade exclusiva ao seu bot habilitado para voz, você pode atribuir a ele uma voz própria. A criação de uma voz personalizada é um processo em duas etapas. Primeiro, você [faz gravações](record-custom-voice-samples.md) da voz que deseja usar. Em seguida, [envia essas gravações](how-to-customize-voice-font.md) juntamente com uma transcrição de texto para o [portal de personalização de voz](https://cris.ai/Home/CustomVoice) do serviço de Fala, que faz o resto. Depois de criar sua voz personalizada, as etapas para usá-la em seu aplicativo são simples.

## <a name="next-steps"></a>Próximas etapas

Obtenha uma chave de assinatura para o serviço de Fala.

> [!div class="nextstepaction"]
> [Experimente o serviço de Fala gratuitamente](get-started.md)
