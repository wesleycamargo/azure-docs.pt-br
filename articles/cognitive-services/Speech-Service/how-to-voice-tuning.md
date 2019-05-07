---
title: Ajustar a saída de texto em fala - serviços de fala
titleSuffix: Azure Cognitive Services
description: Habilite o log no SDK da fala.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 6d602491c66669007ae220c3b8143ce3e805246f
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148000"
---
# <a name="fine-tune-text-to-speech-output"></a>Ajustar a saída de texto em fala

Serviços de fala do Azure permitem que você ajuste a velocidade, pronúncia, volume, tom e delimitação de texto em fala saída usando [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md). SSML é uma linguagem de marcação baseada em XML que usa marcas para informar o serviço sobre qual recurso requer o ajuste. A mensagem SSML, em seguida, é enviada no corpo de cada solicitação para o serviço de texto em fala. Para simplificar o processo de personalização, os serviços de fala agora oferecem uma [voz ajuste](https://aka.ms/voicetuning) saídas de ferramenta que permite inspecionar visualmente e ajustar o texto em fala em tempo real.

A ferramenta de ajuste de voz suporta da Microsoft [standard](language-support.md#standard-voices), [neural](language-support.md#text-to-speech), e [vozes personalizadas](how-to-customize-voice-font.md).

## <a name="get-started-with-the-voice-tuning-tool"></a>Comece com a ferramenta de ajuste de voz

Antes de começar a ajustar a saída de texto em fala com a ferramenta de ajuste de voz, você precisará concluir estas etapas:

1. Criar uma [livres de conta da Microsoft](https://account.microsoft.com/account) se você ainda não tiver um.
2. Criar uma [conta gratuita do Azure](https://azure.microsoft.com/en-us/free/) se você ainda não tiver um. Clique em **iniciar gratuitamente**e criar uma nova conta do Azure usando sua conta da Microsoft.

3. Crie uma assinatura de serviços de fala no portal do Azure. Instruções passo a passo para [como criar um recurso de fala](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure) estão disponíveis.
   >[!NOTE]
   >Quando você cria um recurso de fala no portal do Azure, as informações de local do Azure precisam corresponder com a região de voz TTS. Voz TTS neural suporta um conjunto de sub dos locais do Azure. Para obter uma lista completa de suporte, consulte [regiões](regions.md#text-to-speech).

   >[!NOTE]
   >Você precisa ter um F0 ou uma chave de S0 criada no portal do Azure antes de poder usar o serviço. Ajuste de voz **não** dão suporte a [chave de avaliação gratuita de 30 dias](https://review.docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started?branch=release-build-cogserv-speech-services#free-trial).

4. Entrar para o [voz ajuste](https://aka.ms/voicetuning) portal e conecte-se a sua assinatura dos serviços de fala. Escolha uma única assinatura de serviços de fala e, em seguida, crie um projeto.
5. Selecione **nova ajuste**. Depois, siga estas etapas:

   * Localize e selecione **todas as assinaturas**.  
   * Selecione **conectar-se a assinatura existente**.  
     ![Conectar-se uma assinatura existente](./media/custom-voice/custom-voice-connect-subscription.png).
   * Insira sua chave de assinatura de serviços de fala do Azure, em seguida, selecione **adicionar**. Suas chaves de assinatura estão disponíveis no portal de personalização de fala dos [página de assinatura](https://go.microsoft.com/fwlink/?linkid=2090458). Você também pode obter as chaves no painel de gerenciamento de recursos do [portal do Azure](https://portal.azure.com/). 
   * Se você tiver mais de uma assinatura de serviços de fala que você planeja usar, repita essas etapas para cada assinatura.

## <a name="customize-the-text-to-speech-output"></a>Personalizar a saída de texto em fala

Agora que você criou contas e vincular sua assinatura, você pode iniciar o ajuste a saída de texto em fala.

1. Escolha uma voz.
2. Insira o texto que você deseja editar.
3. Antes de começar a fazer edições, reproduzir o áudio para ter uma noção de saída.
4. Selecionar a palavra/frase que você deseja refinar e começar a experimentar com diferentes funções baseadas em SSML.

>[!TIP]
> Para obter informações detalhadas sobre como ajustar SSML e ajustar a saída de voz, consulte [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md).

## <a name="limitations"></a>Limitações

Ajuste de voz neural é ligeiramente diferente de ajuste para vozes padrão e personalizadas.

* Não há suporte para entonação de vozes Neural.
* Densidade e o volume de recursos só funcionam com frases completas. Esses recursos não estão disponíveis no nível do word.
* Taxa de, alguns vozes Neural podem ser ajustadas com base em palavras, enquanto outras exigem que você selecione frases inteiras.

> [!TIP]
> A ferramenta de ajuste de voz fornece informações contextuais sobre os recursos e ajuste.

## <a name="next-steps"></a>Próximas etapas
* [Criar um recurso de fala no Azure](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure)
* [Iniciar o ajuste de voz](https://speech.microsoft.com/app.html#/VoiceTuning)
* [Linguagem de marcação de síntese de fala (SSML)](speech-synthesis-markup.md)
