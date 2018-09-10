---
title: O que é o serviço de Fala (versão prévia)?
description: 'O serviço de Fala, parte dos Serviços Cognitivos da Microsoft, une vários serviços de fala do Azure que estavam disponíveis anteriormente de forma separada: Fala do Bing (que inclui o reconhecimento de fala e a conversão de texto em fala), Fala Personalizada e Tradução de Fala.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: overview
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 922320bb0b880e933b27025257e6a533fe257680
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43091465"
---
# <a name="what-is-the-speech-service"></a>O que é Serviço de fala?

O serviço de Fala une os recursos de fala do Azure disponíveis anteriormente por meio dos serviços [API de Fala do Bing](https://docs.microsoft.com/azure/cognitive-services/speech/home), [Tradução de Fala](https://docs.microsoft.com/azure/cognitive-services/translator-speech/), [Fala Personalizada](https://docs.microsoft.com/azure/cognitive-services/custom-speech-service/cognitive-services-custom-speech-home) e [ Voz Personalizada](http://customvoice.ai/). Agora, uma assinatura fornece acesso a todos esses recursos.

Como outros serviços de fala do Azure, o serviço de Fala é alimentado pelas tecnologias de fala comprovadas usadas em produtos como o Cortana e o Microsoft Office. Você pode contar com a qualidade dos resultados e a confiabilidade da nuvem do Azure.

> [!NOTE]
> Atualmente, o serviço de Fala está em versão prévia pública. Volte aqui regularmente para ver atualizações na documentação, novos exemplos de código e muito mais.

## <a name="main-speech-service-functions"></a>Principais funções do serviço de Fala

As principais funções do serviço de Fala são Conversão de Fala em Texto (também chamado de reconhecimento de fala ou transcrição), Conversão de Texto em Fala (síntese de fala) e Tradução de Fala.

|Função|Recursos|
|-|-|
|[Conversão de Fala em Texto](speech-to-text.md)| <ul><li>Transcreve fala em tempo real contínua em texto.<li>Pode transcrever em lotes fala de gravações de áudio. <li>Oferece modos de reconhecimento para casos de uso interativos, de conversa e de ditado.<li>Dá suporte a resultados intermediários, detecção de final de fala, formatação automática de texto e mascaramento de conteúdo ofensivo. <li>Pode chamar [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/) (Reconhecimento vocal) para avaliar a intenção do usuário a partir da fala transcrita.\*|
|[Conversão de Texto em Fala](text-to-speech.md)| <ul><li>Converte o texto em fala que soa natural. <li>Oferece vários gêneros e/ou dialetos para muitos idiomas com suporte. <li>Dá suporte à entrada de texto sem formatação ou a SSML (Speech Synthesis Markup Language). |
|[Tradução de Fala](speech-translation.md)| <ul><li>Converte o fluxo de áudio em tempo quase real<li> Pode também processar fala gravada<li>Fornece resultados como texto ou fala sintetizada. |

\* *O reconhecimento de intenção requer uma assinatura do LUIS.*


## <a name="customizing-speech-features"></a>Personalizando recursos de fala

O serviço de Fala permite que você use seus próprios dados para treinar os modelos subjacentes aos recursos Conversão de Texto em Fala e Conversão de Fala em Texto do serviço de Fala. 

|Recurso|Modelo|Finalidade|
|-|-|-|
|Conversão de Fala em Texto|[Modelo acústico](how-to-customize-acoustic-models.md)|Ajuda a transcrever falantes e ambientes específicos, como carros ou fábricas|
||[Modelo de linguagem](how-to-customize-language-model.md)|Ajuda a transcrever vocabulário e gramática específicos de campo, como jargão médico ou de TI|
||[Modelo de pronúncia](how-to-customize-pronunciation.md)|Ajuda a transcrever abreviações e acrônimos, como "IOU" para "I owe you" (outro nome para “nota promissória”) |
|Conversão de Texto em Fala|[Fonte de voz](how-to-customize-voice-font.md)|Dá a seu aplicativo uma voz própria treinando o modelo com amostras de fala humana.|

Depois de criados, seus modelos personalizados podem ser usados em qualquer lugar onde você usaria os recursos de Conversão de Texto em Fala e Conversão de Fala em Texto de seu aplicativo.


## <a name="using-the-speech-service"></a>Usar o serviço de Fala

Para simplificar o desenvolvimento de aplicativos habilitados para fala, a Microsoft fornece o [SDK de Fala](speech-sdk.md) para uso com o novo serviço de Fala. O SDK de Fala fornece APIs de Conversão de Fala em Texto e de Tradução de Fala nativas consistentes para C#, C++ e Java. Se você estiver desenvolvendo em uma dessas linguagens, o SDK de Fala facilita o desenvolvimento lidando com os detalhes da rede para você.

O serviço de Fala também possui uma [API REST](rest-apis.md) que funciona com qualquer linguagem de programação que possa fazer solicitações HTTP. A interface REST, no entanto, não oferece a funcionalidade de streaming em tempo real do SDK.

|<br>Método|Fala<br>em Texto|Texto em<br>Fala|Fala<br>Tradução|<br>DESCRIÇÃO|
|-|-|-|-|-|
|[SDK da fala](speech-sdk.md)|SIM|Não |SIM|APIs nativas para C#, C++ e Java a fim de simplificar o desenvolvimento.|
|[REST](rest-apis.md)|SIM|sim|Não |Uma API simples baseada em HTTP que facilita a adição de fala a seus aplicativos.|

### <a name="websockets"></a>WebSockets

O serviço de Fala também possui protocolos WebSockets para transmitir Conversão de Fala em Texto e Tradução de Fala. Os SDKs de Fala usam esses protocolos para se comunicar com o serviço de Fala. Você deve usar o SDK de Fala em vez de tentar implementar sua própria comunicação de WebSockets com o serviço de Fala.

No entanto, se você já tem código que usa a Fala ou a Tradução de Fala do Bing pelo WebSockets, é simples atualizá-lo para usar o serviço de Fala. Os protocolos WebSocket são compatíveis; somente os pontos de extremidade são diferentes.

### <a name="speech-devices-sdk"></a>SDK de Dispositivos de Fala

O [SDK de Dispositivos de Fala](speech-devices-sdk.md) é uma plataforma integrada de hardware e software para desenvolvedores de dispositivos habilitados para fala. Nossos parceiros de hardware fornecem designs de referência e unidades de desenvolvimento. A Microsoft fornece um SDK otimizado para dispositivos que tira proveito dos recursos do hardware.


## <a name="speech-scenarios"></a>Cenários de Fala

Casos de uso para o serviço de Fala incluem:

> [!div class="checklist"]
> * Criar aplicativos acionados por voz
> * Transcrever gravações de call center
> * Implementar bots de voz

### <a name="voice-user-interface"></a>Interface de usuário de voz

A entrada de voz é uma ótima maneira de tornar seu aplicativo flexível, não assistido e rápido de usar. Em um aplicativo habilitado para voz, os usuários podem simplesmente solicitar as informações que desejarem em vez de precisar navegar até elas.

Se seu aplicativo se destina ao uso pelo público em geral, você pode usar os modelos de reconhecimento de fala padrão. Ele consegue reconhecer uma ampla variedade de falantes em ambientes comuns.

Se o aplicativo for usado em um domínio específico (por exemplo, medicina ou TI), você poderá criar um [modelo de linguagem](how-to-customize-language-model.md) ensinar o serviço de Fala sobre a terminologia especial usada pelo seu aplicativo.

Se o aplicativo for usado em um ambiente barulhento, como uma fábrica, você poderá criar um [modelo acústico](how-to-customize-acoustic-models.md) personalizado para permitir que o serviço de Fala distinga melhor a fala do ruído.

Começar é tão fácil quanto baixar o [SDK de Fala](speech-sdk.md) e seguir um artigo de [Início Rápido](quickstart-csharp-dotnet-windows.md) relevante.

### <a name="call-center-transcription"></a>Transcrição de call center

Muitas vezes, as gravações de call center serão consultadas apenas se surgir um problema com uma chamada. Com o serviço de Fala, é fácil transcrever cada gravação em texto. Depois que cada gravação for transformada em texto, você pode indexá-las facilmente para [pesquisa de texto completo](https://docs.microsoft.com/azure/search/search-what-is-azure-search) ou aplicar [Análise de Texto](https://docs.microsoft.com/azure/cognitive-services/Text-Analytics/) para detectar o sentimento, a linguagem e as frases-chave.

Se suas gravações do call center envolvem terminologia especializada (por exemplo, nomes de produto ou jargões de TI), você pode criar um [modelo de linguagem](how-to-customize-language-model.md) para ensinar esse vocabulário ao serviço de Fala. Um [modelo acústico](how-to-customize-acoustic-models.md) personalizado pode ajudar o serviço de Fala a entender as conexões de telefone abaixo do ideal.

Para obter mais informações sobre esse cenário, leia mais sobre [transcrição em lote](batch-transcription.md) com o serviço de Fala.

### <a name="voice-bots"></a>Bots de voz

[Bots](https://dev.botframework.com/) são uma maneira cada vez mais popular de conectar os usuários às informações que eles querem e os clientes às empresas que eles adoram. Adicionar uma interface do usuário de conversa ao site ou aplicativo torna a localização de sua funcionalidade mai fácil e torna o acesso mais rápido. Com o serviço de Fala, essa conversa assume uma nova dimensão de fluência respondendo às consultas faladas de maneira similar.

Para adicionar uma personalidade exclusiva ao seu bot habilitado para voz (e fortalecer sua marca), você pode atribuir a ele uma voz própria. A criação de uma voz personalizada é um processo em duas etapas. Primeiro, você [faz gravações](record-custom-voice-samples.md) da voz que deseja usar. Em seguida, você [envia essas gravações](how-to-customize-voice-font.md) (juntamente com uma transcrição de texto) para o [portal de personalização de voz](https://cris.ai/Home/CustomVoice) do serviço de Fala, que faz o resto. Após a criação da sua voz personalizada, é simples usá-la em seu aplicativo.

## <a name="next-steps"></a>Próximas etapas

Obtenha uma chave de assinatura para o serviço de Fala.

> [!div class="nextstepaction"]
> [Experimente o serviço de Fala gratuitamente](get-started.md)
