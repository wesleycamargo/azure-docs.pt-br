---
title: O que é a API da Pesquisa Visual Computacional? – Pesquisa Visual Computacional
titlesuffix: Azure Cognitive Services
description: O serviço de Pesquisa Visual Computacional fornece aos desenvolvedores o acesso a algoritmos avançados para processar imagens e retornar informações.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: overview
ms.date: 08/22/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: cd4eca669936a206241069f1f8a8d9440f45be33
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2019
ms.locfileid: "54229771"
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
|**[Marcar recursos visuais](concept-tagging-images.md)**|Identificar e marcar recursos visuais em uma imagem, de um conjunto de milhares de objetos reconhecíveis, seres vivos, cenários e ações. Quando as marcas são ambíguas ou pertencem a um conhecimento não comum, a resposta da API fornece "dicas" para esclarecer o significado da tag no contexto de um cenário conhecido. A marcação não está limitada ao assunto principal, como uma pessoa em primeiro plano, mas também inclui cenário (interno ou externo), móveis, ferramentas, plantas, animais, acessórios, gadgets e outros.|
|**[Detectar objetos](concept-object-detection.md)**| A detecção de objetos é semelhante à marcação, mas a API retorna as coordenadas da caixa delimitadora para cada tag aplicada. Por exemplo, se uma imagem contiver um cachorro, um gato e uma pessoa, a operação Detect listará esses objetos junto com as coordenadas na imagem. Você pode usar essa funcionalidade para processar ainda mais as relações entre os objetos em uma imagem. Também permite que você saiba quando há várias instâncias da mesma tag em uma imagem.|
|**[Categorizar uma imagem](concept-categorizing-images.md)**|Identifique e categorize uma imagem inteira usando uma [taxonomia de categoria](Category-Taxonomy.md) com hierarquias hereditárias de pai/filho. As categorias podem ser usadas autonomamente ou com nossos novos modelos de marcação.<br/>Atualmente, o inglês é o único idioma com suporte para a marcação e categorização de imagens.|
|**[Descrever uma imagem](concept-describing-images.md)**|Gere uma descrição de uma imagem inteira em linguagem compreendida por humanos, usando frases completas. Os algoritmos da Pesquisa Visual Computacional geram várias descrições baseadas nos objetos identificados na imagem. Cada uma das descrições é avaliada e uma pontuação de confiança é gerada. Uma lista é então retornada, ordenada pela pontuação de confiança mais alta à mais baixa.|
|**[Detectar rostos](concept-detecting-faces.md)** |Detecte rostos em uma imagem e forneça informações sobre cada rosto detectado. A Pesquisa Visual Computacional retorna as coordenadas, o retângulo, o sexo e a idade de cada rosto detectado.<br/>A Pesquisa Visual Computacional oferece um subconjunto da funcionalidade que pode ser encontrado na [Detecção Facial](/azure/cognitive-services/face/), e você pode usar o serviço Detecção Facial para uma análise mais detalhada, como identificação facial e detecção de pose.|
|**[Detectar tipos de imagem](concept-detecting-image-types.md)**|Detecte características sobre uma imagem, por exemplo, se uma imagem é um desenho de linha se é, possivelmente, um clip-art.|
|**[Detectar conteúdo específico de um domínio](concept-detecting-domain-content.md)**|Use modelos de domínio para detectar e identificar conteúdo específico de um assunto em uma imagem, como celebridades e pontos turísticos. Por exemplo, se uma imagem contém pessoas, a Pesquisa Visual Computacional pode usar um modelo de domínio para celebridades incluído com o serviço para determinar se as pessoas detectadas na imagem correspondem a celebridades conhecidas.|
|**[Detectar o esquema de cores](concept-detecting-color-schemes.md)**|Analise o uso de cores em uma imagem. A Pesquisa Visual Computacional pode determinar se uma imagem está em preto e branco ou em cores e, para imagens com cores, identificar as cores dominantes e de detalhes.|
|**[Gerar uma miniatura](concept-generating-thumbnails.md)**|Analise o conteúdo de uma imagem para gerar uma miniatura adequada dessa imagem. A Pesquisa Visual Computacional primeiro gera uma miniatura de alta qualidade e, em seguida, analisa os objetos na imagem para determinar a *área de interesse*. A Pesquisa Visual Computacional corta a imagem para atender aos requisitos da área de interesse. A miniatura gerada pode ser apresentada usando uma taxa de proporção diferente da taxa de proporção da imagem original, dependendo de suas necessidades.|
|**[Obter a área de interesse](concept-generating-thumbnails.md#area-of-interest)**|Analise o conteúdo de uma imagem para retornar as coordenadas da *área de interesse*. Essa é a mesma função usada para gerar uma miniatura, porém, em vez de cortar a imagem, a Pesquisa Visual Computacional retorna as coordenadas da caixa delimitadora da região para que o aplicativo de chamada possa modificar a imagem original conforme desejado.|

## <a name="extracting-text-from-images"></a>Extraindo texto de imagens

Você pode usar a Pesquisa Visual Computacional para [extrair texto usando OCR](concept-extracting-text-ocr.md) de uma imagem em um fluxo de caracteres legíveis por computador. Se necessário, o OCR corrige a rotação do texto reconhecido, em graus, no eixo horizontal da imagem e fornece as coordenadas de quadro de cada palavra. O OCR dá suporte a 25 idiomas e detecta automaticamente o idioma do texto extraído.

Você também pode [reconhecer texto impresso e manuscrito](concept-recognizing-text.md) de uma imagem. A Pesquisa Visual Computacional pode detectar e extrair texto impresso e manuscrito das imagens de vários objetos com diferentes superfícies e fundos, como recibos, cartazes, cartões de visita, cartas e quadros de comunicações. Atualmente, o reconhecimento de texto manuscrito e impresso está em versão prévia e inglês é o único idioma com suporte.  

## <a name="moderating-content-in-images"></a>Moderando conteúdo em imagens

Você pode usar a Pesquisa Visual Computacional para [detectar conteúdo adulto e erótico](concept-detecting-adult-content.md) em uma imagem, classificar a probabilidade de que a imagem contenha conteúdo adulto ou erótico e gerar uma pontuação de confiança para ambos. O filtro para detecção de conteúdo para adulto pode ser definido em uma escala deslizante para atender a suas preferências.

## <a name="using-containers"></a>Como usar contêineres

[Use os contêineres de Pesquisa Visual Computacional](computer-vision-how-to-install-containers.md) para reconhecer texto impresso e manuscrito localmente, por meio da instalação de um contêiner do Docker padronizado mais próximos aos seus dados.

## <a name="image-requirements"></a>Requisitos de imagem

A Pesquisa Visual Computacional pode analisar imagens que atendem aos seguintes requisitos:

- A imagem deve ser apresentada no formato JPEG, PNG, GIF ou BMP
- O tamanho do arquivo da imagem deve ser menor que 4 MB (megabytes)
- As dimensões da imagem devem ser maiores que 50 x 50 pixels  
  Para OCR, as dimensões da imagem de entrada precisam estar entre 50 x 50 e 4200 x 4200 pixels

## <a name="data-privacy-and-security"></a>Segurança e privacidade de dados

Assim como ocorre com todos os Serviços Cognitivos, os desenvolvedores que usam o serviço de Pesquisa Visual Computacional devem estar cientes das políticas da Microsoft em relação aos dados do cliente. Confira a [página de Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na Central de Confiabilidade da Microsoft para saber mais.

## <a name="next-steps"></a>Próximas etapas

Introdução à Pesquisa Visual Computacional com um de nossos inícios rápidos:

- [Analisar uma imagem](quickstarts-sdk/csharp-analyze-sdk.md)
- [Extrair texto escrito à mão](quickstarts-sdk/csharp-hand-text-sdk.md)
- [Gerar uma miniatura](quickstarts-sdk/csharp-thumb-sdk.md)
