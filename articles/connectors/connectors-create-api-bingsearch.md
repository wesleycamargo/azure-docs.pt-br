---
title: "Adicionar os aplicativos lógicos do conector de Pesquisa do Bing | Microsoft Docs"
description: "Visão geral do conector de Pesquisa do Bing com os parâmetros de API REST"
services: 
suite: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: a7f530e8-1573-4612-8899-c9c84aa2de6d
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: dbb920f212d46365233e83ba1e0a0ae99917e405


---
# <a name="get-started-with-the-bing-search-connector"></a>Introdução ao conector de Pesquisa do Bing
Conecte-se à Pesquisa do Bing para pesquisar notícias, vídeos e muito mais. Com a Pesquisa do Bing, você pode: 

* Criar seu fluxo de negócios com base nos dados que você obtém da pesquisa. 
* Use ações para pesquisar imagens, notícias e muito mais. Essas ações obtêm uma resposta e disponibilizam a saída para outras ações. Por exemplo, você pode pesquisar por um vídeo e usar o Twitter para publicar esse vídeo em um feed do Twitter.

Para adicionar uma operação a aplicativos lógicos, consulte [Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Gatilhos e ações
A Pesquisa do Bing inclui as seguintes ações. Não há gatilhos. 

| Gatilhos | Ações |
| --- | --- |
| Nenhum |<ul><li>Pesquisar na Web</li><li>Pesquisar vídeos</li><li>Pesquisa imagens</li><li>Pesquisar notícias</li><li>Pesquisa relacionada</li><li>Pesquisar ortografias</li><li>Pesquisar tudo</li></ul> |

Todos os conectores dão suporte a dados nos formatos JSON e XML.

## <a name="swagger-rest-api-reference"></a>Referência da API REST do Swagger
Aplica-se à versão: 1.0.

### <a name="search-web"></a>Pesquisar na Web
Recupera sites de uma Pesquisa do Bing.  
```GET: /Web```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| query |string |sim |query |Nenhum |Texto a ser procurado (exemplo: ‘xbox’) |
| maxResult |inteiro |não |query |Nenhum |Número máximo de resultados a serem retornados. |
| startOffset |inteiro |não |query |Nenhum |Número de resultados a serem ignorados |
| adultContent |string |não |query |Nenhum |Filtro de conteúdo adulto. Valores válidos:  <ul><li>Desativar</li><li>Moderado</li><li>Rigoroso</li></ul> |
| market |string |não |query |Nenhum |Mercado ou região para restringir a pesquisa (exemplo: pt-BR) |
| longitude |número |não |query |Nenhum |Longitude (coordenada leste/oeste ) para restringir a pesquisa (exemplo: 47,603450) |
| latitude |número |não |query |Nenhum |Latitude (coordenada norte/sul) para restringir a pesquisa (exemplo: -122,329696) |
| webFileType |string |não |query |Nenhum |Tipo de arquivo para restringir a pesquisa (exemplo: 'DOC') |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na operação. |

### <a name="search-videos"></a>Pesquisar vídeos
Recupera vídeos de uma pesquisa do Bing.  
```GET: /Video```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| query |string |sim |query |Nenhum |Texto a ser procurado (exemplo: ‘xbox’) |
| maxResult |inteiro |não |query |Nenhum |Número máximo de resultados a serem retornados. |
| startOffset |inteiro |não |query |Nenhum |Número de resultados a serem ignorados |
| adultContent |string |não |query |Nenhum |Filtro de conteúdo adulto. Valores válidos:  <ul><li>Desativar</li><li>Moderado</li><li>Rigoroso</li></ul> |
| market |string |não |query |Nenhum |Mercado ou região para restringir a pesquisa (exemplo: pt-BR) |
| longitude |número |não |query |Nenhum |Longitude (coordenada leste/oeste ) para restringir a pesquisa (exemplo: 47,603450) |
| latitude |número |não |query |Nenhum |Latitude (coordenada norte/sul) para restringir a pesquisa (exemplo: -122,329696) |
| videoFilters |string |não |query |Nenhum |Filtre a pesquisa com base no tamanho, aspecto, cor, estilo, face ou qualquer combinação desses.  Valores válidos:  <ul><li>Duração:Curta</li><li>Duração:Média</li><li>Duração:Longa</li><li>Aspecto:Padrão</li><li>Aspecto:Widescreen</li><li>Resolução:Baixa</li><li>Resolução:Média</li><li>Resolução:Alta</li></ul> <br/><br/>Por exemplo: ‘Duração:Curta+Resolução:Alta’ |
| videoSortBy |string |não |query |Nenhum |Ordem de classificação dos resultados. Valores válidos:  <ul><li>Data</li><li>Relevância</li></ul> <p>Ordem de classificação por data implica ordem decrescente.</p> |

#### <a name="response"></a>Response
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na operação. |

### <a name="search-images"></a>Pesquisa imagens
Recupera imagens de uma pesquisa do Bing.  
```GET: /Image```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| query |string |sim |query |Nenhum |Texto a ser procurado (exemplo: ‘xbox’) |
| maxResult |inteiro |não |query |Nenhum |Número máximo de resultados a serem retornados. |
| startOffset |inteiro |não |query |Nenhum |Número de resultados a serem ignorados |
| adultContent |string |não |query |Nenhum |Filtro de conteúdo adulto. Valores válidos:  <ul><li>Desativar</li><li>Moderado</li><li>Rigoroso</li></ul> |
| market |string |não |query |Nenhum |Mercado ou região para restringir a pesquisa (exemplo: pt-BR) |
| longitude |número |não |query |Nenhum |Longitude (coordenada leste/oeste ) para restringir a pesquisa (exemplo: 47,603450) |
| latitude |número |não |query |Nenhum |Latitude (coordenada norte/sul) para restringir a pesquisa (exemplo: -122,329696) |
| imageFilters |string |não |query |Nenhum |Filtre a pesquisa com base no tamanho, aspecto, cor, estilo, face ou qualquer combinação desses. Valores válidos:  <ul><li>Tamanho:Pequeno</li><li>Tamanho:Médio</li><li>Tamanho:Grande</li><li>Tamanho:Largura:[Largura]</li><li>Tamanho:Altura:[Altura]</li><li>Aspecto:Quadrado</li><li>Aspecto:Largo</li><li>Aspecto:Longo</li><li>Cor:Cor</li><li>Cor:Monocromático</li><li>Estilo:Foto</li><li>Estilo:Gráficos</li><li>Face:Face</li><li>Face:Retrato</li><li>Face:Outros</li></ul><br/><br/>Por exemplo: “Tamanho:Pequeno+Aspecto:Quadrado” |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na operação. |

### <a name="search-news"></a>Pesquisar notícias
Recupera os resultados de notícias de uma pesquisa do Bing.  
```GET: /News```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| query |string |sim |query |Nenhum |Texto a ser procurado (exemplo: ‘xbox’) |
| maxResult |inteiro |não |query |Nenhum |Número máximo de resultados a serem retornados. |
| startOffset |inteiro |não |query |Nenhum |Número de resultados a serem ignorados |
| adultContent |string |não |query |Nenhum |Filtro de conteúdo adulto. Valores válidos:  <ul><li>Desativar</li><li>Moderado</li><li>Rigoroso</li></ul> |
| market |string |não |query |Nenhum |Mercado ou região para restringir a pesquisa (exemplo: pt-BR) |
| longitude |número |não |query |Nenhum |Longitude (coordenada leste/oeste ) para restringir a pesquisa (exemplo: 47,603450) |
| latitude |número |não |query |Nenhum |Latitude (coordenada norte/sul) para restringir a pesquisa (exemplo: -122,329696) |
| newsSortBy |string |não |query |Nenhum |Ordem de classificação dos resultados. Valores válidos:  <ul><li>Data</li><li>Relevância</li></ul> <p>Ordem de classificação por data implica ordem decrescente.</p> |
| newsCategory |string |não |query | |Categoria de notícias para restringir a pesquisa (exemplo: ‘rt_Business’) |
| newsLocationOverride |string |não |query |Nenhum |Substituição para detecção de local do Bing. Esse parâmetro só é aplicável no mercado en-US. O formato de entrada é US./<state /> (exemplo: “US.WA”) |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na operação. |

### <a name="search-spellings"></a>Pesquisar ortografias
Recupera sugestões de ortografia.  
```GET: /SpellingSuggestions```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| query |string |sim |query |Nenhum |Texto a ser procurado (exemplo: ‘xbox’) |
| maxResult |inteiro |não |query |Nenhum |Número máximo de resultados a serem retornados. |
| startOffset |inteiro |não |query |Nenhum |Número de resultados a serem ignorados |
| adultContent |string |não |query |Nenhum |Filtro de conteúdo adulto. Valores válidos:  <ul><li>Desativar</li><li>Moderado</li><li>Rigoroso</li></ul> |
| market |string |não |query |Nenhum |Mercado ou região para restringir a pesquisa (exemplo: pt-BR) |
| longitude |número |não |query |Nenhum |Longitude (coordenada leste/oeste ) para restringir a pesquisa (exemplo: 47,603450) |
| latitude |número |não |query |Nenhum |Latitude (coordenada norte/sul) para restringir a pesquisa (exemplo: -122,329696) |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na operação. |

### <a name="search-related"></a>Pesquisa relacionada
Recupera os resultados da pesquisa relacionados de uma pesquisa do Bing.  
```GET: /RelatedSearch```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| query |string |sim |query |Nenhum |Texto a ser procurado (exemplo: ‘xbox’) |
| maxResult |inteiro |não |query |Nenhum |Número máximo de resultados a serem retornados. |
| startOffset |inteiro |não |query |Nenhum |Número de resultados a serem ignorados |
| adultContent |string |não |query |Nenhum |Filtro de conteúdo adulto. Valores válidos:  <ul><li>Desativar</li><li>Moderado</li><li>Rigoroso</li></ul> |
| market |string |não |query |Nenhum |Mercado ou região para restringir a pesquisa (exemplo: pt-BR) |
| longitude |número |não |query |Nenhum |Longitude (coordenada leste/oeste ) para restringir a pesquisa (exemplo: 47,603450) |
| latitude |número |não |query |Nenhum |Latitude (coordenada norte/sul) para restringir a pesquisa (exemplo: -122,329696) |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na operação. |

### <a name="search-all"></a>Pesquisar tudo
Recupera todos os sites, vídeos, imagens, etc.  
```GET: /CompositeSearch```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| query |string |sim |query |Nenhum |Texto a ser procurado (exemplo: ‘xbox’) |
| maxResult |inteiro |não |query |Nenhum |Número máximo de resultados a serem retornados. |
| startOffset |inteiro |não |query |Nenhum |Número de resultados a serem ignorados |
| adultContent |string |não |query |Nenhum |Filtro de conteúdo adulto. Valores válidos:  <ul><li>Desativar</li><li>Moderado</li><li>Rigoroso</li></ul> |
| market |string |não |query |Nenhum |Mercado ou região para restringir a pesquisa (exemplo: pt-BR) |
| longitude |número |não |query |Nenhum |Longitude (coordenada leste/oeste ) para restringir a pesquisa (exemplo: 47,603450) |
| latitude |número |não |query |Nenhum |Latitude (coordenada norte/sul) para restringir a pesquisa (exemplo: -122,329696) |
| webFileType |string |não |query |Nenhum |Tipo de arquivo para restringir a pesquisa (exemplo: 'DOC') |
| videoFilters |string |não |query |Nenhum |Filtre a pesquisa com base no tamanho, aspecto, cor, estilo, face ou qualquer combinação desses.  Valores válidos:  <ul><li>Duração:Curta</li><li>Duração:Média</li><li>Duração:Longa</li><li>Aspecto:Padrão</li><li>Aspecto:Widescreen</li><li>Resolução:Baixa</li><li>Resolução:Média</li><li>Resolução:Alta</li></ul> <br/><br/>Por exemplo: ‘Duração:Curta+Resolução:Alta’ |
| videoSortBy |string |não |query |Nenhum |Ordem de classificação dos resultados. Valores válidos:  <ul><li>Data</li><li>Relevância</li></ul> <p>Ordem de classificação por data implica ordem decrescente.</p> |
| imageFilters |string |não |query |Nenhum |Filtre a pesquisa com base no tamanho, aspecto, cor, estilo, face ou qualquer combinação desses. Valores válidos:  <ul><li>Tamanho:Pequeno</li><li>Tamanho:Médio</li><li>Tamanho:Grande</li><li>Tamanho:Largura:[Largura]</li><li>Tamanho:Altura:[Altura]</li><li>Aspecto:Quadrado</li><li>Aspecto:Largo</li><li>Aspecto:Longo</li><li>Cor:Cor</li><li>Cor:Monocromático</li><li>Estilo:Foto</li><li>Estilo:Gráficos</li><li>Face:Face</li><li>Face:Retrato</li><li>Face:Outros</li></ul><br/><br/>Por exemplo: “Tamanho:Pequeno+Aspecto:Quadrado” |
| newsSortBy |string |não |query |Nenhum |Ordem de classificação dos resultados. Valores válidos:  <ul><li>Data</li><li>Relevância</li></ul> <p>Ordem de classificação por data implica ordem decrescente.</p> |
| newsCategory |string |não |query |nenhum |Categoria de notícias para restringir a pesquisa (exemplo: ‘rt_Business’) |
| newsLocationOverride |string |não |query |Nenhum |Substituição para detecção de local do Bing. Esse parâmetro só é aplicável no mercado en-US. O formato de entrada é US./<state /> (exemplo: “US.WA”) |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na operação. |

## <a name="object-definitions"></a>Definições de objeto
#### <a name="webresultmodel-bing-web-search-results"></a>WebResultModel: resultados da pesquisa da Web do Bing
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| Title |string |não |
| Descrição |string |não |
| DisplayUrl |string |não |
| ID |string |não |
| FullUrl |string |não |

#### <a name="videoresultmodel-bing-video-search-results"></a>VideoResultModel: resultados da pesquisa de vídeo do Bing
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| Title |string |não |
| DisplayUrl |string |não |
| ID |string |não |
| MediaUrl |string |não |
| Tempo de execução |inteiro |não |
| Miniatura |não definido |não |

#### <a name="thumbnailmodel-thumbnail-properties-of-the-multimedia-element"></a>ThumbnailModel: propriedades de miniatura do elemento multimídia
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| MediaUrl |string |não |
| ContentType |string |não |
| Largura |inteiro |não |
| Altura |inteiro |não |
| FileSize |inteiro |não |

#### <a name="imageresultmodel-bing-image-search-results"></a>ImageResultModel: resultados da pesquisa de imagens do Bing
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| Title |string |não |
| DisplayUrl |string |não |
| ID |string |não |
| MediaUrl |string |não |
| SourceUrl |string |não |
| Miniatura |não definido |não |

#### <a name="newsresultmodel-bing-news-search-results"></a>NewsResultModel: resultados da pesquisa de notícias do Bing
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| Title |string |não |
| Descrição |string |não |
| DisplayUrl |string |não |
| ID |string |não |
| Fonte |string |não |
| Data |string |não |

#### <a name="spellresultmodel-bing-spelling-suggestions-results"></a>SpellResultModel: resultados de sugestões de ortografia do Bing
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |string |não |
| Valor |string |não |

#### <a name="relatedsearchresultmodel-bing-related-search-results"></a>RelatedSearchResultModel: resultados de pesquisa relacionados ao Bing
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| Title |string |não |
| ID |string |não |
| BingUrl |string |não |

#### <a name="compositesearchresultmodel-bing-composite-search-results"></a>CompositeSearchResultModel: resultados de pesquisa compostas do Bing
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| WebResultsTotal |inteiro |não |
| ImageResultsTotal |inteiro |não |
| VideoResultsTotal |inteiro |não |
| NewsResultsTotal |inteiro |não |
| SpellSuggestionsTotal |inteiro |não |
| WebResults |array |não |
| ImageResults |array |não |
| VideoResults |array |não |
| NewsResults |array |não |
| SpellSuggestionResults |array |não |
| RelatedSearchResults |array |não |

## <a name="next-steps"></a>Próximas etapas
[Crie um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md).

Volte para a [Lista de APIs](apis-list.md).




<!--HONumber=Jan17_HO3-->


