---
title: Obter SDK dos dispositivos de fala
titleSuffix: Azure Cognitive Services
description: Os Serviços de Fala funcionam com uma ampla variedade de dispositivos e fontes de áudio. Agora, você pode levar os aplicativos de fala para o próximo nível com hardware e software correspondentes. Neste artigo, você aprenderá a obter acesso para o SDK de dispositivos de fala e começar a desenvolver.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: b9a0890000cda0b3663ac29bee61fc1c702f6254
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65410703"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Obter o SDK de Dispositivos de Fala dos Serviços Cognitivos

O SDK de dispositivos de fala é uma biblioteca de pretuned projetada para trabalhar com os kits de desenvolvimento com finalidade específica e várias configurações de matriz de microfone.

## <a name="choose-a-development-kit"></a>Escolha um kit de desenvolvimento

|Dispositivos|Especificação|Descrição|Cenários|
|--|--|--|--|
|[Kit de desenvolvimento de áudio Roobo inteligente](https://ddk.roobo.com)</br>[Programa de instalação](speech-devices-sdk-roobo-v1.md) / [guia de início rápido](speech-devices-sdk-android-quickstart.md)![Roobo inteligente Kit de desenvolvimento de áudio](media/speech-devices-sdk/device-roobo-v1.jpg)|Wi-Fi de matriz do mic, SOC ARM, 7, a saída de áudio, e/s. </br>Android|O SDK de dispositivos fala primeiro para se adaptar a frente do SDK, de processamento para o desenvolvimento de cenários de transcrição e fala de alta qualidade e Microsoft Mic Array|Transcrição de conversa, alto-falante inteligente, agente, o dispositivo portátil de voz|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)![Azure Kinect DK](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|Câmeras de RGB de matriz do mic e a profundidade 7. </br>Windows/Linux|Um kit de desenvolvedor com sensores avançados a inteligência artificial (AI) para criar modelos de fala e visão do computador sofisticados. Ele combina uma câmera de microfone espacial do melhor da matriz e a profundidade com uma câmera de vídeo e o sensor de orientação — tudo em um dispositivo pequeno com vários modos de opções e SDKs para acomodar uma variedade de tipos de computação.|Construção de inteligente de transcrição, robótica, de conversa|
|Kit de desenvolvimento de áudio Roobo inteligente 2![Kit de desenvolvimento de áudio Roobo inteligente 2](media/speech-devices-sdk/device-roobo-v2.jpg)|Wi-Fi de matriz do mic, SOC ARM, 7, Bluetooth, e/s. </br>Linux|A SDK de dispositivos de fala de 2ª geração, que fornece mais recursos em um design de referência econômica e sistema operacional alternativo.|Transcrição de conversa, alto-falante inteligente, agente, o dispositivo portátil de voz|
|Placa de desenvolvimento URbetter T11![URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|Wi-Fi de matriz do mic, SOC ARM, 7, Ethernet, HDMI, câmera USB. </br>Linux|Um nível de setor Speech SDK de dispositivos que se adapta a matriz do Mic Microsoft e oferece suporte estendido e/s, como HDMI/Ethernet e mais USB periféricos|Transcrição de conversa, educação, Hospital, robôs, caixa OTT, agente, de voz unidade ' até '|

## <a name="download-the-speech-devices-sdk"></a>Fazer download do SDK de Dispositivos de Fala

Baixe o [fala dispositivos SDK](https://aka.ms/sdsdk-download).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Introdução ao SDK de Dispositivos de Fala](https://aka.ms/sdsdk-quickstart)
