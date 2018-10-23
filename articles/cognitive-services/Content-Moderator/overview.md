---
title: O que é o Azure Content Moderator?
titlesuffix: Azure Cognitive Services
description: Saiba como usar Content Moderator para acompanhar, sinalizar, avaliar e filtrar material inadequado em conteúdo gerado pelo usuário.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: overview
ms.date: 10/05/2018
ms.author: sajagtap
ms.openlocfilehash: 5756e8fb451b073c68271359848ab27373ad85ed
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2018
ms.locfileid: "49309545"
---
# <a name="what-is-content-moderator"></a>O que é o Content Moderator?

A moderação de conteúdo é o processo de monitoramento de texto, imagem ou conteúdo de vídeo para verificar material potencialmente ofensivo, indesejável ou arriscado. O conteúdo sinalizado pode então ser oculto ou tratado de outra forma para cumprir as normas ou manter um determinado ambiente pretendido para os usuários.

## <a name="where-it-is-used"></a>Em local é usado

A lista a seguir mostra alguns exemplos de cenários em que o Content Moderator pode ser usado:

- Mercados online moderando catálogos de produtos e conteúdo gerado pelo usuário
- Empresas de jogos moderando artefatos de jogos gerados pelo usuário e salas de chat
- Plataformas de mensagens sociais moderando imagens, textos e vídeos adicionados pelos usuários
- Empresas de mídia corporativa implementando moderação de conteúdo centralizada para o seu conteúdo
- Provedores de soluções de educação infantil filtrando conteúdo inapropriado e ofensivo para alunos e professores

## <a name="what-it-includes"></a>O que inclui

O Content Moderator consiste em várias APIs de serviço Web e uma ferramenta de análise com envolvimento humano (human-in-the-loop) que ajudam a moderar imagens, textos e vídeos.

![Diagrama de bloco do Content Moderator](images/content-moderator-block-diagram.png)

### <a name="apis"></a>APIs

O serviço do Content Moderator inclui as seguintes APIs:
  - [**API de moderação de texto**](text-moderation-api.md): use essa API para verificar o texto quanto a possíveis linguagens vulgares, explícitas, sugestivas, ofensivas e PII (informações de identificação pessoal).
  - [**API de lista de termos personalizado**](try-terms-list-api.md): use essa API para fazer a correspondência com listas personalizadas de termos além os termos internos. Use essas listas para bloquear ou permitir conteúdo conforme suas políticas de conteúdo.  
  - [**API de moderação de imagem**](image-moderation-api.md): use essa API para verificar imagens quanto a conteúdo adulto e estimulante, detectar texto em imagens com a funcionalidade OCR (reconhecimento óptico de caracteres) e detectar faces.
  - [**API de lista de imagens personalizadas**](try-image-list-api.md): use essa API para fazer correspondência com listas personalizadas de imagens, conteúdo pré-identificado que você não precisa classificar novamente.
  - [**API de moderação de vídeo**](video-moderation-api.md): use essa API para verificar os vídeos quanto a possível conteúdo de adulto e estimulante.
  - [**APIs de análise**](try-review-api-job.md): use as operações [Trabalhos](try-review-api-job.md), [Análises](try-review-api-review.md) e [Fluxo de trabalho](try-review-api-workflow.md) para criar e automatizar fluxos de trabalho com envolvimento humano (human-in-the-loop) dentro da ferramenta de revisão.

### <a name="human-review-tool"></a>Ferramenta de análise humana

Sua assinatura do Content Moderator inclui a [ferramenta de análise humana](Review-Tool-User-Guide/human-in-the-loop.md) interna. Use a API de Análise mencionadas anteriormente para criar análises de texto, imagens e vídeos para seu moderadores humanos tomarem decisões.

![Ferramenta de análise de vídeo do Content Moderator](images/video-review-default-view.png)

## <a name="next-steps"></a>Próximas etapas

Use o [Início Rápido](quick-start.md) para começar a usar o Content Moderator.
