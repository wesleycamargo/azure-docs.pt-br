---
title: Personalizar um modelo de Pessoa no Video Indexer – Azure
titlesuffix: Azure Media Services
description: Este artigo fornece uma visão geral do que é um modelo de Pessoa no Video Indexer e como personalizá-lo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/05/2018
ms.author: anzaman
ms.openlocfilehash: 073cff22f17f496c2ff85cfbf716751dfea1e03e
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53283232"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Personalizar um modelo de Pessoa no Video Indexer


O Video Indexer dá suporte à detecção facial e ao reconhecimento de celebridades para conteúdo de vídeo. O recurso de reconhecimento de celebridades abrange aproximadamente um milhão de rostos baseados em fontes de dados normalmente solicitadas, como IMDB, Wikipédia e principais influenciadores do LinkedIn. Os rostos não reconhecidos pelo recurso de reconhecimento de celebridades são detectados; no entanto, eles são deixados sem nome. Depois de carregar o vídeo no Video Indexer e obter os resultados, você pode voltar e nomear os rostos que não foram reconhecidos. Depois que você rotular um rosto com um nome, o rosto e o nome serão adicionados ao modelo de Pessoa de sua conta. Em seguida, o Video Indexer reconhecerá esse rosto em seus vídeos futuros e antigos.

Use a API ou o site do Video Indexer para editar os rostos detectados em um vídeo em sua conta, conforme descrito nos tópicos a seguir:

- [Personalizar o modelo de Pessoa usando APIs](customize-person-model-with-api.md)
- [Personalizar o modelo de Pessoa usando o site](customize-person-model-with-website.md)
