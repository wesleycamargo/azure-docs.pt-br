---
title: O que é o Content Moderator?
titlesuffix: Azure Cognitive Services
description: Saiba como usar Content Moderator para acompanhar, sinalizar, avaliar e filtrar conteúdo inadequado em conteúdo gerado pelo usuário.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: overview
ms.date: 06/15/2017
ms.author: sajagtap
ms.openlocfilehash: e109376f47d921fb18d7bb9a6252e80315419ec0
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47226034"
---
# <a name="what-is-content-moderator"></a>O que é o Content Moderator?

Moderação de conteúdo é o processo de monitoramento para possível conteúdo ofensivo, indesejável e arriscado. O conteúdo moderado pode incluir imagens, texto e vídeos.

## <a name="where-it-is-used"></a>Em local é usado

A lista a seguir mostra alguns exemplos de cenários em que o Content Moderator é usado:

- Mercados online moderando catálogos de produtos e conteúdo gerado pelo usuário
- Empresas de jogos moderando artefatos de jogos gerados pelo usuário e salas de chat
- Plataforma de mensagens sociais moderando imagens, textos e vídeos adicionados pelos usuários
- Empresas de mídia corporativa implementando moderação de conteúdo centralizada para o seu conteúdo
- Provedores de soluções de educação infantil filtrando conteúdo inadequado e ofensivo para alunos e professores

## <a name="what-it-includes"></a>O que inclui

O Content Moderator consiste em várias APIs de serviço Web e uma ferramenta de análise com envolvimento humano (human-in-the-loop) que ajudam a moderar imagens, textos e vídeos.

![Diagrama de bloco do Content Moderator](images/content-moderator-block-diagram.png)

## <a name="apis"></a>APIs

O Content Moderator inclui as seguintes APIs:
  - [**API de moderação de texto**](text-moderation-api.md): use essa API para verificar o texto quanto a possíveis linguagens vulgares, explícitas, sugestivas, ofensivas e PII (informações de identificação pessoal).
  - [**API de lista de termos personalizado**](try-terms-list-api.md): use essa API para fazer a correspondência com listas personalizadas de termos além os termos internos. Use essas listas para bloquear ou permitir conteúdo conforme suas políticas de conteúdo.  
  - [**API de moderação de imagem**](image-moderation-api.md): use essa API para verificar imagens quanto a conteúdo adulto e estimulante, detectar texto em imagens com a funcionalidade OCR (reconhecimento óptico de caracteres) e detectar faces.
  - [**API de lista de imagens personalizadas**](try-image-list-api.md): use essa API para fazer correspondência com listas personalizadas de imagens, conteúdo pré-identificado que você não precisa classificar novamente.
  - [**API de moderação de vídeo**](video-moderation-api.md): use essa API para verificar os vídeos quanto a possível conteúdo de adulto e estimulante.
  - [**APIs de análise**](try-review-api-job.md): use as operações [Trabalhos](try-review-api-job.md), [Análises](try-review-api-review.md) e [Fluxo de trabalho](try-review-api-workflow.md) para criar e automatizar fluxos de trabalho com envolvimento humano (human-in-the-loop) dentro da ferramenta de revisão.

## <a name="human-review-tool"></a>Ferramenta de análise humana

Sua assinatura do Content Moderator inclui a [ferramenta de análise humana](Review-Tool-User-Guide/human-in-the-loop.md) interna. Use a API de Análise mencionadas anteriormente para criar análises de texto, imagens e vídeos para seu moderadores humanos tomarem decisões.

![Ferramenta de análise de vídeo do Content Moderator](images/video-review-default-view.png)

## <a name="next-steps"></a>Próximas etapas

Use o [Início Rápido](quick-start.md) para começar a usar o Content Moderator.
