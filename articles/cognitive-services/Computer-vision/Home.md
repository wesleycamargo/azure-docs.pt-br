---
title: Pesquisa Visual Computacional para os Serviços Cognitivos do Azure | Microsoft Docs
description: Use algoritmos avançados na Pesquisa Visual Computacional para ajudá-lo a processar imagens e retornar informações nos Serviços Cognitivos do Azure.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: overview
ms.date: 08/22/2018
ms.author: v-deken
ms.openlocfilehash: ec3ffa8599192ecd9a4092b026a8d249f50c64c9
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43248613"
---
# <a name="what-is-computer-vision"></a>O que é a Pesquisa Visual Computacional?

O serviço Pesquisa Visual Computacional baseado em nuvem permite aos desenvolvedores o acesso a algoritmos avançados para processar imagens e retornar informações. A Pesquisa Visual Computacional funciona com formatos de imagem populares, como JPEG e PNG. Para analisar uma imagem, você pode carregar uma imagem ou especificar uma URL de imagem. Os algoritmos de Pesquisa Visual Computacional podem analisar o conteúdo de uma imagem de maneiras diferentes, dependendo dos recursos visuais que interessam a você. Por exemplo, a Pesquisa Visual computacional pode determinar se uma imagem apresenta conteúdo adulto ou erótico ou localizar todos os rostos em uma imagem.

Você pode usar a Pesquisa Visual Computacional em seu aplicativo, usando nossas [bibliotecas de cliente](quickstarts-sdk/csharp-analyze-sdk.md) para invocar o serviço ou invocando a [API REST](vision-api-how-to-topics/howtocallvisionapi.md) diretamente, para:

- [Analisar imagens e obter insights](#analyzing-images-for-insight)
- [Extrair texto de imagens](#extracting-text-from-images)
- [Moderar conteúdo em imagens](#moderating-content-in-images)

## <a name="analyzing-images-for-insight"></a>Analisando de imagens para obter insight

Você pode analisar imagens usando Pesquisa Visual Computacional para detectar e fornecer informações sobre os recursos visuais e características de suas imagens. É possível carregar o conteúdo de uma imagem para analisar imagens locais ou especificar a URL de uma imagem para a análise de imagens remotas.

A Pesquisa Visual Computacional pode fazer as seguintes ações ao analisar uma imagem:

| Ação | DESCRIÇÃO |
| ------ | ----------- |
|**[Marcar recursos visuais](quickstarts/csharp-analyze.md)**|Identificar e marcar recursos visuais em uma imagem, com base em mais de 2 mil objetos, seres, cenários e ações reconhecíveis. Quando as marcações são ambíguas ou pertencem a um conhecimento não comum, a resposta fornece 'dicas' para esclarecer o significado da marcação no contexto de um cenário conhecido. A marcação não está limitada ao assunto principal, como uma pessoa em primeiro plano, mas também inclui cenário (interno ou externo), móveis, ferramentas, plantas, animais, acessórios, gadgets e outros.|
|**[Categorizar uma imagem](quickstarts/csharp-analyze.md)**|Identifique e categorize uma imagem inteira usando uma [taxonomia de categoria](Category-Taxonomy.md) com hierarquias hereditárias de pai/filho. As categorias podem ser usadas autonomamente ou com nossos novos modelos de marcação.<br/>Atualmente, o inglês é o único idioma com suporte para a marcação e categorização de imagens.|
|**[Descrever uma imagem](quickstarts/csharp-analyze.md)**|Gere uma descrição de uma imagem inteira em linguagem compreendida por humanos, usando frases completas. Os algoritmos da Pesquisa Visual Computacional geram várias descrições baseadas nos objetos identificados na imagem. Cada uma das descrições é avaliada e uma pontuação de confiança é gerada. Uma lista é então retornada, ordenada pela pontuação de confiança mais alta à mais baixa.<br/>Um exemplo de bot que usa essa tecnologia para gerar as legendas de imagem pode ser encontrado [no GitHub](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-ImageCaption).|
|**[Detectar rostos](quickstarts/csharp-analyze.md)** |Detecte rostos em uma imagem e forneça informações sobre cada rosto detectado. A Pesquisa Visual Computacional retorna as coordenadas, o retângulo, o sexo e a idade de cada rosto detectado.<br/>A Pesquisa Visual Computacional oferece um subconjunto da funcionalidade que pode ser encontrado na [Detecção Facial](/azure/cognitive-services/face/), e você pode usar o serviço Detecção Facial para uma análise mais detalhada, como identificação facial e detecção de pose.|
|**[Detectar tipos de imagem](quickstarts/csharp-analyze.md)**|Detecte características sobre uma imagem, por exemplo, se uma imagem é um desenho de linha se é, possivelmente, um clip-art.|
|**[Detectar conteúdo específico de um domínio](quickstarts/python-domain.md)**|Use modelos de domínio para detectar e identificar conteúdo específico de um assunto em uma imagem, como celebridades e pontos turísticos. Por exemplo, se uma imagem contém pessoas, a Pesquisa Visual Computacional pode usar um modelo de domínio para celebridades incluído com o serviço para determinar se as pessoas detectadas na imagem correspondem a celebridades conhecidas.|
|**[Detectar o esquema de cores](quickstarts/csharp-analyze.md)**|Analise o uso de cores em uma imagem. A Pesquisa Visual Computacional pode determinar se uma imagem está em preto e branco ou em cores e, para imagens com cores, identificar as cores dominantes e de detalhes.|
|**[Gerar uma miniatura](quickstarts/csharp-thumb.md)**|Analise o conteúdo de uma imagem para gerar uma miniatura adequada dessa imagem. A Pesquisa Visual Computacional primeiro gera uma miniatura de alta qualidade e, em seguida, analisa os objetos na imagem para determinar a *ROI* (região de interesse). A Pesquisa Visual Computacional corta a imagem para atender aos requisitos da região de interesse. A miniatura gerada pode ser apresentada usando uma taxa de proporção diferente da taxa de proporção da imagem original, dependendo de suas necessidades.|

## <a name="extracting-text-from-images"></a>Extraindo texto de imagens

Você pode usar a Pesquisa Visual Computacional para [extrair texto usando OCR](quickstarts/csharp-print-text.md) de uma imagem em um fluxo de caracteres legíveis por computador. Se necessário, o OCR corrige a rotação do texto reconhecido, em graus, no eixo horizontal da imagem e fornece as coordenadas de quadro de cada palavra. O OCR dá suporte a 25 idiomas e detecta automaticamente o idioma do texto extraído.

Você também pode [reconhecer texto impresso e manuscrito](quickstarts/csharp-hand-text.md) de uma imagem. A Pesquisa Visual Computacional pode detectar e extrair texto impresso e manuscrito das imagens de vários objetos com diferentes superfícies e fundos, como recibos, cartazes, cartões de visita, cartas e quadros de comunicações. Atualmente, o reconhecimento de texto manuscrito e impresso está em versão prévia e inglês é o único idioma com suporte.  

## <a name="moderating-content-in-images"></a>Moderando conteúdo em imagens

Você pode usar a Pesquisa Visual Computacional para [detectar conteúdo adulto e erótico](quickstarts/csharp-analyze.md) em uma imagem, classificar a probabilidade de que a imagem contenha conteúdo adulto ou erótico e gerar uma pontuação de confiança para ambos. O filtro para detecção de conteúdo para adulto pode ser definido em uma escala deslizante para atender a suas preferências.

## <a name="image-requirements"></a>Requisitos de imagem

A Pesquisa Visual Computacional pode analisar imagens que atendem aos seguintes requisitos:

- A imagem deve ser apresentada no formato JPEG, PNG, GIF ou BMP
- O tamanho do arquivo da imagem deve ser menor que 4 MB (megabytes)
- As dimensões da imagem devem ser maiores que 50 x 50 pixels  
  Para o OCR, as dimensões da imagem devem estar entre 40 x 40 e 3200 x 3200 pixels, e a imagem não pode ter mais de 10 megapixels.

## <a name="next-steps"></a>Próximas etapas

Introdução à Pesquisa Visual Computacional com um de nossos inícios rápidos:

- [Analisar uma imagem](/quickstarts-sdk/csharp-analyze-sdk.md)
- [Extrair texto escrito à mão](/quickstarts-sdk/csharp-hand-text-sdk.md)
- [Gerar uma miniatura](/quickstarts-sdk/csharp-thumb-sdk.md)
