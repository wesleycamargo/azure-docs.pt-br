---
title: Transcrição de conversa - serviços de fala
titleSuffix: Azure Cognitive Services
description: Transcrição de conversa é um recurso avançado dos serviços de fala que combina o reconhecimento de fala em tempo real, a identificação do locutor e diarization. Transcrição de conversa é perfeita para transcrevê reuniões presenciais, com a capacidade de distinguir os alto-falantes, ele permite que você saiba quem disse que e quando, permitindo que os participantes para se concentrar na reunião e rapidamente acompanhamento sobre as próximas etapas. Esse recurso também melhora a acessibilidade. Com a transcrição, você pode envolver ativamente participantes com deficiências auditivas.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: eebe3ed15d21b08b208667a571bd59eac98a674d
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190270"
---
# <a name="what-is-conversation-transcription"></a>O que é a transcrição de conversa?

Transcrição de conversa é um recurso avançado dos serviços de fala que combina o reconhecimento de fala em tempo real, a identificação do locutor e diarization. Transcrição de conversa é perfeita para transcrevê reuniões presenciais, com a capacidade de distinguir os alto-falantes, ele permite que você saiba quem disse que e quando, permitindo que os participantes para se concentrar na reunião e rapidamente acompanhamento sobre as próximas etapas. Esse recurso também melhora a acessibilidade. Com a transcrição, você pode envolver ativamente participantes com deficiências auditivas.   

Transcrição de conversação oferece reconhecimento preciso com modelos de fala personalizáveis que você pode adaptar para entender o vocabulário específico da empresa e do setor. Além disso, você pode combinar a transcrição de conversa com o SDK de dispositivos de fala para otimizar a experiência para microfone de vários dispositivos.

>[!NOTE]
> Atualmente, a transcrição de conversa é recomendada para pequenas reuniões. Se você quiser estender a transcrição de conversa para reuniões grandes em grande escala, entre em contato conosco.

Este diagrama ilustra o hardware, software e serviços que trabalham em conjunto com a transcrição de conversa.

![O diagrama de transcrição de conversa de importação](media/scenarios/conversation-transcription-service.png)

>[!IMPORTANT]
> Uma matriz de sete microfone circular com a configuração de geometria específica é necessária. Para obter detalhes de especificação e design, consulte [microfone de SDK de dispositivo do Microsoft Speech](https://aka.ms/cts/microphone). Para saber mais ou comprar um kit de desenvolvimento, consulte [obter o SDK de dispositivo do Microsoft Speech](https://aka.ms/cts/getsdk).

## <a name="get-started-with-conversation-transcription"></a>Introdução a transcrição de conversa

Há três etapas que você precise executar para começar a transcrição de conversa.

1. Colete amostras de voz de usuários.
2. Gerar perfis de usuário usando os exemplos de voz do usuário
3. Use o SDK de fala para identificar os usuários (alto-falantes) e transcrição de fala

## <a name="collect-user-voice-samples"></a>Coletar amostras de voz do usuário

A primeira etapa é coletar gravações de áudio de cada usuário. Fala de usuário deve ser registrada em um ambiente silencioso, sem ruído de fundo. O tamanho recomendado para cada amostra de áudio é entre 30 segundos e dois minutos. Amostras de áudio mais longas resultará em precisão aprimorada ao identificar os alto-falantes. Áudio deve ser um canal mono com uma taxa de amostragem de 16 KHz.

Além da orientação mencionado anteriormente, como o áudio é registrado e armazenada cabe a você – um banco de dados seguro é recomendado. Na próxima seção, vamos examinar como esse áudio é usado para gerar perfis de usuário que são usados com o SDK de fala para reconhecer os alto-falantes.

## <a name="generate-user-profiles"></a>Gerar perfis de usuário

Em seguida, você precisará enviar as gravações de áudio que você coletou para o serviço de geração de assinatura para validar o áudio e gerar perfis de usuário. O [serviço de geração de assinatura](https://aka.ms/cts/signaturegenservice) é um conjunto de APIs REST, que permitem que você gerar e recuperar perfis de usuário.

Para criar um perfil de usuário, você precisará usar o `GenerateVoiceSignature` API. Detalhes da especificação e código de exemplo estão disponíveis:

> [!NOTE]
> Transcrição de conversa está atualmente disponível em "en-US" e "zh-CN" nas seguintes regiões: `centralus` e `eastasia`.

* [Especificação de REST](https://aka.ms/cts/signaturegenservice)
* [Como usar a transcrição de conversa](https://aka.ms/cts/howto)

## <a name="transcribe-and-identify-speakers"></a>Transcreva e identificar os alto-falantes

Transcrição de conversa espera que os fluxos de Áudio multicanais e perfis de usuário como entradas para gerar transcrições e identificar os alto-falantes. Dados de perfil de usuário e de áudio são enviados para o serviço de transcrição de conversa usando o SDK de dispositivos de fala. Conforme mencionado anteriormente, uma matriz de sete microfone circular e o SDK de dispositivos de fala são necessárias para usar a transcrição de conversa.

>[!NOTE]
> Para obter detalhes de especificação e design, consulte [microfone de SDK de dispositivo do Microsoft Speech](https://aka.ms/cts/microphone). Para saber mais ou comprar um kit de desenvolvimento, consulte [obter o SDK de dispositivo do Microsoft Speech](https://aka.ms/cts/getsdk).

Para saber como usar a transcrição de conversa com o Speech SDK de dispositivos, consulte [como usar a transcrição de conversa](https://aka.ms/cts/howto).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre o SDK de dispositivos de fala](speech-devices-sdk.md)
