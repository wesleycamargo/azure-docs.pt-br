---
title: Como escolher o modo de reconhecimento de Fala do Bing | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Como escolher o melhor modo de reconhecimento na Fala do Bing.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 99e69691d9904285ecff356523813b426c98fca2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60515013"
---
# <a name="bing-speech-recognition-modes"></a>Modos de reconhecimento da Fala do Bing

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

As APIs de Conversão de Fala em Texto do Bing dão suporte a vários modos de reconhecimento de fala. Escolha o modo que produz os melhores resultados de reconhecimento para seu aplicativo.

| Mode | DESCRIÇÃO |
|---|---|
| *interativo* | Reconhecimento de "comando e controle" para cenários de aplicativo do usuário interativo. Os usuários falam frases curtas planejadas como comandos para um aplicativo. |
| *ditado* | Reconhecimento contínuo para cenários de ditado. Os usuários falam sentenças mais longas do que o exibido como texto. Os usuários adotam um estilo de fala mais formal. |
| *conversa* | Reconhecimento contínuo para transcrever conversas entre humanos. Os usuários adotam um estilo de fala menos formal e podem alternar entre sentenças maiores e frases mais curtas.

> [!NOTE]
> Esses modos são aplicáveis quando você usar as [APIs REST](../GetStarted/GetStartedREST.md). As [bibliotecas de cliente](../GetStarted/GetStartedClientLibraries.md) usam parâmetros diferentes para especificar o modo de reconhecimento. Para obter mais informações, consulte a biblioteca de cliente de sua escolha.

Para saber obter mais informações, confira a página [Modos de Reconhecimento](../concepts.md#recognition-modes).
