---
title: O que é Voz Personalizada? -Serviços de fala
titlesuffix: Azure Cognitive Services
description: Voz personalizada é um conjunto de ferramentas online que permitem que você crie uma voz reconhecível, um de tipo para sua marca. Para começar a usar são necessários um punhado de arquivos de áudio e transcrições de associados. Siga os links abaixo para começar a criar uma experiência personalizada de fala em texto.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: 8cf9dc6cbfc96448462aac3a64807f8beb6036ad
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65156906"
---
# <a name="get-started-with-custom-voice"></a>Introdução a voz personalizada

Voz personalizada é um conjunto de ferramentas online que permitem que você crie uma voz reconhecível, um de tipo para sua marca. Para começar a usar são necessários um punhado de arquivos de áudio e transcrições de associados. Siga os links abaixo para começar a criar uma experiência de texto em fala personalizada.

## <a name="whats-in-custom-voice"></a>O que é voz personalizada?

Antes de iniciar com voz personalizada, você precisará de uma conta do Azure e uma assinatura dos serviços de fala. Depois de criar uma conta, você pode preparar seus dados, treinar e testar seus modelos, avaliar a qualidade de voz e, por fim, implantar seu modelo de voz personalizadas.

O diagrama a seguir destaca as etapas para criar um modelo de voz personalizada usando o portal de voz personalizadas. Use os links para saber mais.

![Diagrama da arquitetura de voz personalizado](media/custom-voice/custom-voice-diagram.png)

1.  [Inscrever-se e crie um projeto](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice?branch=release-build-cogserv-speech-services#set-up-your-azure-account) - criar uma conta do Azure e criar uma assinatura de serviços de fala. Esta assinatura unificada fornece acesso a fala em texto, texto em fala, tradução de fala e o portal de voz personalizada. Em seguida, usando sua assinatura dos serviços de fala, crie seu primeiro projeto de voz personalizada.

2.  [Carregar dados](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font?branch=release-build-cogserv-speech-services#upload-your-datasets) -carregar dados (áudio e texto) usando o portal de voz personalizada ou a API de voz personalizada. No portal, você pode investigar e avaliar as pontuações de pronúncia e taxas de sinal-ruído. Para obter mais informações, consulte [como preparar dados para voz personalizada](how-to-custom-voice-prepare-data.md).

3.  [Treinar seu modelo](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font?branch=release-build-cogserv-speech-services#build-your-voice-font) – usar seus dados para criar um modelo de texto em fala de voz personalizadas. É possível treinar um modelo em diferentes idiomas. Após o treinamento, teste seu modelo e, se você estiver satisfeito com o resultado, você pode implantar o modelo.

4.  [Implantar seu modelo](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font?branch=release-build-cogserv-speech-services#create-and-use-a-custom-endpoint) - criar um ponto de extremidade personalizado para seu modelo de texto em fala de voz e usá-lo para síntese de fala em seus produtos, ferramentas e aplicativos.

## <a name="set-up-your-azure-account"></a>Configurar sua conta do Azure

Uma assinatura de serviços de fala é necessária antes que você pode usar o portal de fala personalizado para criar um modelo personalizado. Siga estas instruções para criar uma assinatura de serviços de fala no Azure. Se você não tiver uma conta do Azure, você pode se inscrever para uma nova.  

Depois de criar uma conta do Azure e uma assinatura dos serviços de fala, você precisará entrar no portal de voz personalizada e conecte-se a sua assinatura.

1. Obtenha sua chave de assinatura de serviços de fala do portal do Azure.
2. Entrar para o [portal de voz personalizada](https://aka.ms/custom-voice).
3. Selecione sua assinatura e crie um projeto de fala.
4. Se você quiser alternar para outra assinatura de fala, use o ícone de engrenagem localizado no painel de navegação superior.

> [!NOTE]
> O serviço de voz personalizada não dá suporte a chave de avaliação gratuita de 30 dias. Você deve ter um F0 ou uma chave de S0 criada no Azure antes de poder usar o serviço.

## <a name="how-to-create-a-project"></a>Como criar um projeto

Como dados, modelos, testes e pontos de extremidade são organizados em de conteúdo **projetos** no portal de voz personalizada. Cada projeto é específico para um país/idioma e o gênero da voz a qual que você deseja criar. Por exemplo, você pode criar um projeto para uma voz feminina para robôs de bate-papo do seu centro de chamada que usam o inglês dos Estados Unidos (en-US).

Para criar seu primeiro projeto, selecione a **voz Text-to-Speech/personalizada** guia e, em seguida, clique em **novo projeto**. Siga as instruções fornecidas pelo Assistente para criar seu projeto. Depois de criar um projeto, você verá quatro guias: **Dados**, **treinamento**, **testes**, e **implantação**. Use os links fornecidos na [próximas etapas](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice?branch=release-build-cogserv-speech-services#next-steps) para aprender a usar cada guia.

## <a name="next-steps"></a>Próximas etapas

- [Preparar os dados de voz personalizada](how-to-custom-voice.md)
- [Criar uma voz personalizadas](how-to-custom-voice-create-voice.md)
- [Guia: Registre seus exemplos de voz](record-custom-voice-samples.md)
