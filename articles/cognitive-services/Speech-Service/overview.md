---
title: O que é o serviço de Fala (versão prévia)?
description: 'O serviço de Fala, parte dos Serviços Cognitivos da Microsoft, une vários serviços de fala do Azure que estavam disponíveis anteriormente de forma separada: Fala do Bing (que inclui o reconhecimento de fala e a conversão de texto em fala), Fala Personalizada e Tradução de Fala.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 60ff2f71766a14af17ebb1cb9d20825976471296
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/17/2018
ms.locfileid: "41929788"
---
# <a name="what-is-the-speech-service-preview"></a>O que é o serviço de Fala (versão prévia)?

Com uma assinatura, o serviço de Fala unificado fornece aos desenvolvedores um jeito fácil de adicionar recursos incríveis habilitados para fala aos seus aplicativos. Agora, seus aplicativos podem oferecer comando de voz, transcrição, ditado, sintetização de voz e tradução de fala.

O serviço de Fala conta com as tecnologias usadas em outros produtos da Microsoft, incluindo o Cortana e o Microsoft Office.

> [!NOTE]
> Atualmente, o serviço de Fala está em versão prévia pública. Volte aqui regularmente para conferir se há atualizações de documentação, exemplos de código adicionais e muito mais.

## <a name="speech-service-features"></a>Recursos do serviço de fala

|Função|DESCRIÇÃO|
|-|-|
|[Conversão de fala em texto](speech-to-text.md)| Transcreve transmissões de áudio em texto, o qual seu aplicativo pode aceitar como entrada. Também integra ao [Serviço Inteligente de Reconhecimento Vocal](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) para perceber a intenção nas declarações dos usuários.|
|[Conversão de texto em fala](text-to-speech.md)| Converte o texto sem formatação em uma fala que soa natural, e é entregue ao aplicativo em um arquivo de áudio. Há várias vozes disponíveis, variando em gênero ou sotaque, para vários idiomas com suporte. |
|[Tradução de fala](speech-translation.md)| Pode ser usada para traduzir uma transmissão de áudio quase em tempo real ou para processar uma fala gravada. |
|Fala em texto personalizada|Você pode personalizar a fala em texto criando seus próprios modelos [acústicos](how-to-customize-acoustic-models.md) e de [idioma](how-to-customize-language-model.md) e especificando regras personalizadas de [pronúncia](how-to-customize-pronunciation.md). |
|[Texto em fala personalizado](how-to-customize-voice-font.md)|Você pode criar suas próprios vozes de texto em fala.|
|[SDK de Dispositivos de Fala](speech-devices-sdk.md)| Com a introdução do serviço de Fala unificado, a Microsoft e seus parceiros oferecem uma plataforma integrada de hardware/software otimizada para o desenvolvimento de dispositivos habilitados para fala |

## <a name="access-to-the-speech-service"></a>Acesso ao serviço de Fala

O serviço de Fala é disponibilizado de duas maneiras. [O SDK](speech-sdk.md) extrai os detalhes dos protocolos de rede para facilitar o desenvolvimento em plataformas com suporte. A [API REST](rest-apis.md) funciona com qualquer linguagem de programação, mas não oferece todas as funções oferecidas pelo SDK.

|<br>Método|Fala<br>em Texto|Texto em<br>Fala|Fala<br>Tradução|<br>DESCRIÇÃO|
|-|-|-|-|-|
|[SDKs](speech-sdk.md)|SIM|Não |SIM|As bibliotecas para linguagens de programação específicas utilizam o protocolo baseado em Websocket que simplifica o desenvolvimento.|
|[REST](rest-apis.md)|SIM|sim|Não |Uma API simples baseada em HTTP que facilita a adição de fala ao seu aplicativo.|

## <a name="speech-scenarios"></a>Cenários de Fala

Alguns usos comuns da tecnologia de fala são discutidos brevemente abaixo. O [SDK de Fala](speech-sdk.md) é fundamental para a maioria desses cenários.

> [!div class="checklist"]
> * Criar aplicativos acionados por voz
> * Transcrever gravações de call center
> * Implementar bots de voz

### <a name="voice-triggered-apps"></a>Aplicativos acionados por voz

A entrada de voz é uma ótima maneira de tornar seu aplicativo flexível, não assistido e rápido de usar. Em um aplicativo habilitado para voz, os usuários podem simplesmente solicitar as informações que desejarem em vez de precisar navegar até elas por meio de cliques ou toques.

Se o aplicativo se destinar ao uso pelo público geral, você poderá usar o modelo de reconhecimento de fala de linha de base fornecido pelo serviço de Fala. Ele faz um bom trabalho de reconhecer uma ampla variedade de falantes em ambientes típicos.

Se o aplicativo for usado em um domínio específico (por exemplo, medicina ou TI), você poderá criar um [modelo de linguagem](how-to-customize-language-model.md) ensinar o serviço de Fala sobre a terminologia especial usada pelo seu aplicativo.

Se o aplicativo for usado em um ambiente barulhento, como uma fábrica, você poderá criar um [modelo acústico](how-to-customize-acoustic-models.md) personalizado para permitir que o serviço de Fala distinga melhor a fala do ruído.

Começar é tão fácil quanto baixar o [SDK de Fala](speech-sdk.md) e seguir um artigo de [Início Rápido](quickstart-csharp-dotnet-windows.md) relevante.

### <a name="transcribe-call-center-recordings"></a>Transcrever gravações de call center

Muitas vezes, as gravações de call center serão consultadas apenas se surgir um problema com uma chamada. Com o serviço de Fala, é fácil transcrever cada gravação em texto. Depois que cada gravação for transformada em texto, você pode indexá-las facilmente para [pesquisa de texto completo](https://docs.microsoft.com/azure/search/search-what-is-azure-search) ou aplicar [Análise de Texto](https://docs.microsoft.com/azure/cognitive-services/Text-Analytics/) para detectar o sentimento, a linguagem e as frases-chave.

Se suas gravações do call center geralmente tiverem terminologia especializada (por exemplo, nomes de produto ou jargões de TI), você pode criar um [modelo de linguagem](how-to-customize-language-model.md) para ensinar esse vocabulário ao serviço de Fala. Um [modelo acústico](how-to-customize-acoustic-models.md) personalizado pode ajudar o serviço de Fala a entender as conexões de telefone abaixo do ideal.

Para obter mais informações sobre esse cenário, leia mais sobre [transcrição em lote](batch-transcription.md) com o serviço de Fala.

### <a name="voice-bots"></a>Bots de voz

[Bots](https://dev.botframework.com/) são uma maneira cada vez mais popular de conectar os usuários às informações que eles querem e os clientes às empresas que eles adoram. Adicionar uma interface do usuário de conversa ao site ou aplicativo torna a localização de sua funcionalidade mai fácil e torna o acesso mais rápido. Com o serviço de Fala, essa conversa assume uma nova dimensão de fluência, na verdade, respondendo às consultas faladas com voz sintetizada.

Para adicionar uma personalidade exclusiva ao seu bot habilitado para voz (e fortalecer sua marca), você pode atribuir a ele uma voz própria. A criação de uma voz personalizada é um processo em duas etapas. Primeiro, você [faz gravações](record-custom-voice-samples.md) da voz que deseja usar. Em seguida, você [envia essas gravações](how-to-customize-voice-font.md) (juntamente com uma transcrição de texto) para o [portal de personalização de voz](https://cris.ai/Home/CustomVoice) do serviço de Fala, que faz o resto. Após a criação da sua voz personalizada, é simples usá-la em seu aplicativo.

## <a name="next-steps"></a>Próximas etapas

Obtenha uma chave de assinatura para o serviço de Fala.

> [!div class="nextstepaction"]
> [Experimente o serviço de Fala gratuitamente](get-started.md)
