---
title: O que é o Azure Content Moderator?
titlesuffix: Azure Cognitive Services
description: Saiba como usar Content Moderator para acompanhar, sinalizar, avaliar e filtrar material inadequado em conteúdo gerado pelo usuário.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 4e0b5070a9ee5b8c8554505dcf81c8226a9fddc0
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55227209"
---
# <a name="what-is-azure-content-moderator"></a>O que é o Azure Content Moderator?

A API do Azure Content Moderator é um serviço cognitivo que verifica conteúdo de textos, imagens e vídeos para verificar material que possa ser ofensivo, de risco ou, de alguma maneira, indesejável. Quando tal material é encontrado, o serviço aplica rótulos (sinalizadores) apropriados ao conteúdo. Seu aplicativo pode tratar de conteúdo sinalizado para cumprir as normas ou manter o ambiente desejado para os usuários. Confira a seção [APIs do Content Moderator](#content-moderator-apis) para saber mais sobre o que os vários sinalizadores de conteúdo indicam.

## <a name="where-it-is-used"></a>Em local é usado

Estes são alguns cenários nos quais um desenvolvedor ou equipe de software usaria o Content Moderator:

- Mercados online que moderam catálogos de produtos e outros tipos de conteúdo gerado pelo usuário
- Empresas de jogos que moderam artefatos de jogos gerados pelo usuário e salas de chat
- Plataformas de mensagens sociais que moderam imagens, textos e vídeos adicionados pelos usuários
- Empresas de mídia corporativa que implementam moderação de conteúdo centralizada para seu conteúdo
- Provedores de soluções de ensino fundamental e médio que filtram conteúdo considerado inapropriado para alunos e professores

## <a name="what-it-includes"></a>O que inclui

O serviço Content Moderator consiste em várias APIs de serviço Web disponíveis por meio de chamadas REST e um SDK do .NET. Ele também inclui a ferramenta de análise humana, o que permite que revisores humanos ajudem o serviço e aprimorem ou ajustem sua função de moderação.

![diagrama de bloco para o Content Moderator mostrando as APIs de Moderação, as APIs de Análise e a ferramenta de análise humana](images/content-moderator-block-diagram.png)

### <a name="content-moderator-apis"></a>APIs do Content Moderator

O serviço Content Moderator inclui APIs para os cenários a seguir.

| Ação | DESCRIÇÃO |
| ------ | ----------- |
|[**Moderação de texto**](text-moderation-api.md)| Examina conteúdo ofensivo, conteúdo sexual explícito ou sugerido, palavrões e PII (informações de identificação pessoal) no texto.|
|[**Listas de termos personalizadas**](try-terms-list-api.md)| Examina o texto em relação a uma lista de termos personalizados além dos termos internos. Use listas personalizadas para bloquear ou permitir conteúdo de acordo com suas próprias políticas de conteúdo.|  
|[**Moderação de imagem**](image-moderation-api.md)| Verifica conteúdo adulto ou erótico em imagens, detecta textos em imagens com o recurso de OCR (reconhecimento óptico de caracteres) e detecta rostos.|
|[**Listas de imagens personalizadas**](try-image-list-api.md)| Verifica imagens em relação a uma lista personalizada de imagens. Use listas de imagem personalizadas para filtrar instâncias de conteúdo recorrentes que você não deseja classificar novamente.|
|[**Moderação de vídeo**](video-moderation-api.md)| Verifica conteúdo adulto ou erótico em vídeos e retorna os marcadores de tempo para esse conteúdo.|
|[**Análise**](try-review-api-job.md)| Use as operações [Trabalhos](try-review-api-job.md), [Análises](try-review-api-review.md) e [Fluxo de Trabalho](try-review-api-workflow.md) para criar e automatizar fluxos de trabalho com envolvimento humano (human-in-the-loop) dentro da ferramenta de análise humana. A API de Fluxo de Trabalho ainda não está disponível pelo SDK do .NET.|

### <a name="human-review-tool"></a>Ferramenta de análise humana

O serviço Content Moderator também inclui [ferramenta de análise humana](Review-Tool-User-Guide/human-in-the-loop.md) baseada na Web. 

![Página inicial da ferramenta de análise humana do Content Moderator](images/homepage.PNG)

Você pode usar as APIs de Análise para configurar as análises da equipe de conteúdo de texto, imagem e vídeo de acordo com os filtros especificados. Em seguida, os moderadores humanos podem tomar as decisões de moderação finais. A entrada humana não treina o serviço, mas o trabalho combinado do serviço e das equipes de análise humana permite que os desenvolvedores alcancem o equilíbrio entre a precisão e a eficiência.

## <a name="data-privacy-and-security"></a>Segurança e privacidade de dados
Assim como ocorre com todos os Serviços Cognitivos, os desenvolvedores que usam o serviço Content Moderator devem estar cientes das políticas da Microsoft em relação aos dados do cliente. Confira a [página de Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na Central de Confiabilidade da Microsoft para saber mais.

## <a name="next-steps"></a>Próximas etapas

Comece a usar o serviço Content Moderator seguindo as instruções descritas em [Experimentar o Content Moderator na Web](quick-start.md).
