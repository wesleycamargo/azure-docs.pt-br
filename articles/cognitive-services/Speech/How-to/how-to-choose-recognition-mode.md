---
title: Como escolher o Modo de Reconhecimento | Microsoft Docs
description: Como escolher o melhor modo de reconhecimento.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 4f02b683dde16b537ae5554e6435c068f0fcb808
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363615"
---
# <a name="speech-recognition-modes"></a>Modos de reconhecimento de fala

As APIs de *Conversão de fala em texto* da Microsoft é compatível com vários modos de reconhecimento de texto. Escolha o modo que produz os melhores resultados de reconhecimento para seu aplicativo.

| Mode | DESCRIÇÃO |
|---|---|
| *interativo* | Reconhecimento de "comando e controle" para cenários de aplicativo do usuário interativo. Os usuários falam frases curtas planejadas como comandos para um aplicativo. |
| *ditado* | Reconhecimento contínuo para cenários de ditado. Os usuários falam sentenças mais longas do que o exibido como texto. Os usuários adotam um estilo de fala mais formal. |
| *conversa* | Reconhecimento contínuo para transcrever conversas entre humanos. Os usuários adotam um estilo de fala menos formal e podem alternar entre sentenças maiores e frases mais curtas.

> [!NOTE]
> Esses modos são aplicáveis quando você usar as [APIs REST](../GetStarted/GetStartedREST.md). As [bibliotecas de cliente](../GetStarted/GetStartedClientLibraries.md) usam parâmetros diferentes para especificar o modo de reconhecimento. Para obter mais informações, consulte a biblioteca de cliente de sua escolha.

Para saber obter mais informações, confira a página [Modos de Reconhecimento](../concepts.md#recognition-modes).
