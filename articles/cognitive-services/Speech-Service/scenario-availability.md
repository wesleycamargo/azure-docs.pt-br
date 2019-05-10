---
title: Disponibilidade do cenário - serviços de fala
titlesuffix: Azure Cognitive Services
description: Referência para regiões do serviço de Fala.
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cbasoglu
ms.openlocfilehash: d844b171ff99dc97e5d1107bcb745f9e8d5b3e9d
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65519821"
---
# <a name="scenario-availability"></a>Disponibilidade do cenário

O SDK do serviço de fala apresenta muitos cenários em uma ampla variedade de ambientes e linguagens de programação.  Nem todos os cenários ainda estão atualmente disponíveis em todas as linguagens de programação ou em todos os ambientes.  Abaixo está a disponibilidade de cada cenário.

- **Reconhecimento de fala (SR), lista de frases, intenção, tradução e locais dos contêineres**
  - Todos os idiomas/ambientes de programação em que há um link da seta <img src="media/index/link.jpg" height="15" width="15"></img> na tabela de início rápido [aqui](https://aka.ms/csspeech).
- **Texto em fala (TTS)**
  - C++/ Windows e Linux
  - C#/Windows
  - API de REST de TTS pode ser usado em todas as outras situações.
- **Ativação de Word (palavra-chave Spotter/KWS)**
  - C++/ Windows e Linux
  - C#/ Windows e Linux
  - Python/Windows e Linux
  - Java/Windows, Linux & Android (dispositivos de fala do SDK)
  - Funcionalidade de palavra (palavra-chave Spotter/KWS) de ativação pode funcionar com qualquer tipo de microfone, oficial KWS oferecer suporte a, no entanto, é atualmente limitado para as matrizes de microfone encontrado no hardware do Azure Kinect DK ou o SDK de dispositivos de fala
- **Assistente de voz-First Virtual**
  - C++/ Windows e Linux e macOS
  - C#/Windows
  - Java/Windows, Linux, macOS, & Android (SDK de dispositivos de fala)
- **Transcrição de Conversas**
  - C++/ Windows e Linux
  - C#(Framework e .NET Core) / Windows UWP & Linux
  - Java/Windows, Linux & Android (dispositivos de fala do SDK)
- **Transcrição de Call Center**
  - API REST e podem ser usados em qualquer situação
- **Codec de compactação a entrada de áudio**
  - C++/Linux
  - C#/Linux
  - Java/Linux e no Android
