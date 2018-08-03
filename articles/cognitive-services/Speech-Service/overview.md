---
title: O que é o serviço de Fala (versão prévia)? | Microsoft Docs
description: 'O serviço de Fala, parte dos Serviços Cognitivos da Microsoft, une vários serviços de fala do Azure que estavam disponíveis anteriormente de forma separada: Fala do Bing (que inclui o reconhecimento de fala e a conversão de texto em fala), Fala Personalizada e Tradução de Fala.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: adfc854fc24b9e285c405f3038a21ec84cd2f4c2
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989328"
---
# <a name="what-is-the-speech-service-preview"></a>O que é o serviço de Fala (versão prévia)?

O serviço de Fala, parte dos Serviços Cognitivos da Microsoft, une vários serviços de fala do Azure que estavam disponíveis anteriormente de forma separada: Fala do Bing (que inclui o reconhecimento de fala e a conversão de texto em fala), Fala Personalizada e Tradução de Fala. Como seus precursores, o serviço de Fala conta com as tecnologias usadas em outros produtos da Microsoft, incluindo o Cortana e o Microsoft Office.

> [!NOTE]
> Atualmente, o serviço de Fala está em versão prévia pública. Volte aqui regularmente para conferir se há atualizações de documentação, exemplos de código adicionais e muito mais.

Com uma assinatura, o serviço de Fala unificado fornece aos desenvolvedores um jeito fácil de conferir aos seus aplicativos recursos incríveis habilitados para fala. Agora, seus aplicativos podem oferecer comando de voz, transcrição, ditado, sintetização de voz e tradução.

|Função|DESCRIÇÃO|
|-|-|
|Conversão de Fala em Texto|Converte a fala humana contínua em um texto que pode ser usado como entrada para o seu aplicativo. Permite a integração com o LUIS [(Serviço Inteligente de Reconhecimento Vocal)](https://docs.microsoft.com/azure/cognitive-services/luis/) para perceber a intenção nas declarações dos usuários.|
|Texto em fala|Converte texto em arquivos de áudio de voz sintetizada com sonoridade natural.|
|Tradução de&nbsp;Fala|Fornece traduções da fala para outros idiomas, com saída de fala ou de texto.|

## <a name="using-the-speech-service"></a>Usar o serviço de Fala

O serviço de Fala é disponibilizado de duas maneiras. [O SDK](speech-sdk.md) abstrai os detalhes dos protocolos de rede. A [API REST](rest-apis.md) funciona com qualquer linguagem de programação, mas não oferece todas as funções oferecidas pelo SDK.

|<br>Método|Fala<br>em Texto|Texto em<br>Fala|Fala<br>Tradução|<br>DESCRIÇÃO|
|-|-|-|-|-|
|[SDKs](speech-sdk.md)|SIM|Não |SIM|Bibliotecas para linguagens de programação específicas que simplificam o desenvolvimento.|
|[REST](rest-apis.md)|SIM|sim|Não |Uma API simples baseada em HTTP que facilita a adição de fala ao seu aplicativo.|

## <a name="speech-to-text"></a>Conversão de Fala em Texto

A API de STT [Conversão de Fala em Texto](speech-to-text.md), ou reconhecimento de fala, transcreve transmissões de áudio em texto, o qual seu aplicativo pode aceitar como entrada. Seu aplicativo pode, por exemplo, inserir o texto em um documento ou agir nele como um comando.

A Conversão de Fala em Texto foi otimizada separadamente para cenários interativos, de conversa e de ditado. Veja a seguir casos de uso comuns para a API de Conversão de Fala em Texto. 

* Reconhecer um enunciado breve, como um comando, sem resultados provisórios
* Transcrever uma declaração longa gravada anteriormente, como uma mensagem de voz
* Transcrever fala transmitida em tempo real, com resultados parciais, para ditado
* Determinar o que os usuários desejam fazer com base em uma solicitação de linguagem natural falada

A API de Conversão de Fala em Texto dá suporte à transcrição de fala interativa com reconhecimento contínuo em tempo real e resultados intermediários. Ele também oferece suporte à detecção de final de fala, capitalização e pontuação automáticas opcionais, mascaramento de palavras de baixo calão e normalização de texto.

Você pode personalizar modelos acústico e de linguagem da Conversão de Fala em Texto para aceitar um vocabulário especializado, ambientes com ruídos e diferentes maneiras de falar.

## <a name="text-to-speech"></a>Texto em fala

A API de TTS [(Conversão de Texto em Fala)](text-to-speech.md), ou sintetização de voz, converte o texto sem formatação em uma fala que soa natural, e é entregue ao aplicativo em um arquivo de áudio. Há várias vozes disponíveis, variando em gênero ou sotaque, para vários idiomas com suporte.

A API dá suporte às marcas [SSML (Linguagem de Marcação de Sintetização de Voz)](speech-synthesis-markup.md), para que você possa especificar a pronúncia fonética exata para palavras problemáticas. O SSML também pode indicar características de fala (incluindo ênfase, taxa, volume, gênero e tom) diretamente no texto.

Veja a seguir casos de uso comuns da API de Conversão de Texto em Fala.

* Saída de fala como uma saída de tela alternativa para usuários com deficiências visuais
* Avisar de voz para aplicativos de carro, como navegação
* Interfaces de usuário de conversação em conjunto com a API da Conversão de Fala em Texto

Se você precisar de um dialeto sem suporte ou quiser apenas uma voz exclusiva para o aplicativo, a API de Conversão de Texto em Fala dá suporte a [modelos de voz personalizados](how-to-customize-voice-font.md).

## <a name="speech-translation"></a>Tradução de Fala

A API de [Tradução de Fala](speech-translation.md) pode ser usada para traduzir uma transmissão de áudio quase em tempo real ou para processar uma fala gravada. Na tradução de transmissão, o serviço retorna os resultados intermediários que podem ser exibidos para o usuário para indicar o progresso da tradução. Os resultados podem ser retornados como texto ou como voz.

Entre os casos de uso de Tradução de Fala estão os seguintes.

* Implementar um dispositivo ou aplicativo móvel de tradução "conversacional" para viajantes 
* Fornecer traduções automáticas para colocação de legenda em gravações de áudio e vídeos

## <a name="speech-devices-sdk"></a>SDK de Dispositivos de Fala

Com a introdução do serviço de Fala unificado, a Microsoft e seus parceiros oferecem uma plataforma integrada de hardware/software otimizada para o desenvolvimento de dispositivos habilitados para fala: o [SDK de Dispositivos de Fala](speech-devices-sdk.md). Este SDK é adequado para o desenvolvimento de dispositivos de fala inteligentes para todos os tipos de aplicativos.

O SDK de Dispositivos de Fala permite que você crie seus próprios dispositivos de ambiente com uma palavra de ativação personalizada, para que a dica que dispara a captação de áudio seja exclusiva à sua marca. Ele também fornece processamento superior de áudio de fontes multicanais para um reconhecimento de fala mais preciso, incluindo a supressão de ruídos, voz de campo distante e beamforming.

O SDK é baseado em soquetes da Web usando a porta 443.

## <a name="next-steps"></a>Próximas etapas

Obtenha uma chave de assinatura de avaliação gratuita para o serviço de Fala.

> [!div class="nextstepaction"]
> [Experimente o serviço de Fala gratuitamente](get-started.md)
