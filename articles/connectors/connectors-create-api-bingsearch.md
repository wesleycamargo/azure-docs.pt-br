<properties
    pageTitle="Adicionar o conector de Pesquisa do Bing ao PowerApps ou aplicativos lógicos | Microsoft Azure"
    description="Visão geral do conector de Pesquisa do Bing com os parâmetros de API REST"
    services=""
    suite=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="05/18/2016"
   ms.author="mandia"/>

# Introdução ao conector de Pesquisa do Bing 
Conecte-se à Pesquisa do Bing para pesquisar notícias, vídeos e muito mais. O conector da Pesquisa do Bing pode ser usada em:

- Aplicativos lógicos 
- PowerApps

> [AZURE.SELECTOR]
- [Aplicativos lógicos](../articles/connectors/connectors-create-api-bingsearch.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-bingsearch.md)


Com a Pesquisa do Bing, você pode:

- Criar seu fluxo de negócios com base nos dados que você obtém da pesquisa. 
- Use ações para pesquisar imagens, notícias e muito mais. Essas ações obtêm uma resposta e disponibilizam a saída para outras ações. Por exemplo, você pode pesquisar por um vídeo e usar o Twitter para publicar esse vídeo em um feed do Twitter.
- Adicione o conector de Pesquisa do Bing ao PowerApps Enterprise. Assim, seus usuários poderão usar esse conector em seus próprios aplicativos. 

Para saber mais sobre como adicionar um conector ao PowerApps Enterprise, acesse [Registrar um conector no PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Para adicionar uma operação nos aplicativos lógicos, veja [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Gatilhos e ações
A Pesquisa do Bing inclui as seguintes ações. Não há gatilhos.

Gatilhos | Ações
--- | ---
Nenhum | <ul><li>Pesquisar na Web</li><li>Pesquisar vídeos</li><li>Pesquisar imagens</li><li>Pesquisar notícias</li><li>Pesquisar relacionado</li><li>Pesquisar ortografias</li><li>Pesquisar tudo</li></ul>

Todos os conectores dão suporte a dados nos formatos JSON e XML.


## Referência da API REST do Swagger
Aplica-se à versão: 1.0.

### Pesquisar na Web 
Recupera sites de uma Pesquisa do Bing.```GET: /Web```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|query|string|sim|query|nenhum |Texto a ser procurado (exemplo: ‘xbox’)|
|maxResult|inteiro|não|query|nenhum |Número máximo de resultados a serem retornados.|
|startOffset|inteiro|não|query| nenhum|Número de resultados a serem ignorados|
|adultContent|string|não|query|nenhum |Filtro de conteúdo adulto. Valores válidos: <ul><li>Desativado</li><li>Moderado</li><li>Estrito</li></ul>|
|market|string|não|query|nenhum |Mercado ou região para restringir a pesquisa (exemplo: pt-BR)|
|longitude|número|não|query| nenhum|Longitude (coordenada leste/oeste ) para restringir a pesquisa (exemplo: 47,603450)|
|latitude|número|não|query| nenhum|Latitude (coordenada norte/sul) para restringir a pesquisa (exemplo: -122,329696)|
|webFileType|string|não|query|nenhum |Tipo de arquivo para restringir a pesquisa (exemplo: 'DOC')|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Pesquisar vídeos 
Recupera vídeos de uma pesquisa do Bing.```GET: /Video```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|query|string|sim|query|nenhum |Texto a ser procurado (exemplo: ‘xbox’)|
|maxResult|inteiro|não|query| nenhum|Número máximo de resultados a serem retornados.|
|startOffset|inteiro|não|query|nenhum |Número de resultados a serem ignorados|
|adultContent|string|não|query|nenhum |Filtro de conteúdo adulto. Valores válidos: <ul><li>Desativado</li><li>Moderado</li><li>Estrito</li></ul>|
|market|string|não|query|nenhum |Mercado ou região para restringir a pesquisa (exemplo: pt-BR)|
|longitude|número|não|query|nenhum |Longitude (coordenada leste/oeste ) para restringir a pesquisa (exemplo: 47,603450)|
|latitude|número|não|query|nenhum |Latitude (coordenada norte/sul) para restringir a pesquisa (exemplo: -122,329696)|
|videoFilters|string|não|query|nenhum |Filtre a pesquisa com base no tamanho, aspecto, cor, estilo, face ou qualquer combinação desses. Valores válidos: <ul><li>Duração:Curta</li><li>Duração:Média</li><li>Duração:Longa</li><li>Aspecto:Padrão</li><li>Aspecto:Widescreen</li><li>Resolução:Baixa</li><li>Resolução:Média</li><li>Resolução:Alta</li></ul> <br/><br/>Por exemplo: ‘Duração:Curta+Resolução:Alta’|
|videoSortBy|string|não|query|nenhum |Ordem de classificação dos resultados. Valores válidos: <ul><li>Data</li><li>Relevância</li></ul> <p>A ordem de classificação de data implica em ordem decrescente.</p>|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Pesquisa imagens    
Recupera imagens de uma pesquisa do Bing.```GET: /Image```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|query|string|sim|query|nenhum |Texto a ser procurado (exemplo: ‘xbox’)|
|maxResult|inteiro|não|query|nenhum |Número máximo de resultados a serem retornados.|
|startOffset|inteiro|não|query|nenhum |Número de resultados a serem ignorados|
|adultContent|string|não|query|nenhum |Filtro de conteúdo adulto. Valores válidos: <ul><li>Desativado</li><li>Moderado</li><li>Estrito</li></ul>|
|market|string|não|query|nenhum |Mercado ou região para restringir a pesquisa (exemplo: pt-BR)|
|longitude|número|não|query| nenhum|Longitude (coordenada leste/oeste ) para restringir a pesquisa (exemplo: 47,603450)|
|latitude|número|não|query|nenhum |Latitude (coordenada norte/sul) para restringir a pesquisa (exemplo: -122,329696)|
|imageFilters|string|não|query|nenhum |Filtre a pesquisa com base no tamanho, aspecto, cor, estilo, face ou qualquer combinação desses. Valores válidos: <ul><li>Tamanho:Pequeno</li><li>Tamanho:Médio</li><li>Tamanho:Grande</li><li>Tamanho:Largura:[Largura]</li><li>Tamanho:Altura:[Altura]</li><li>Aspecto:Quadrado</li><li>Aspecto:Largo</li><li>Aspecto: Alto</li><li>Cor:Cor</li><li>Cor:Monocromático</li><li>Estilo:Foto</li><li>Estilo:Elemento Gráfico</li><li>Face:Face</li><li>Face:Retrato</li><li>Face:Outro</li></ul><br/><br/>Por exemplo: “Tamanho:Pequeno+Aspecto:Quadrado”|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Pesquisar notícias    
Recupera os resultados de notícias de uma pesquisa do Bing.```GET: /News```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|query|string|sim|query|nenhum |Texto a ser procurado (exemplo: ‘xbox’)|
|maxResult|inteiro|não|query|nenhum |Número máximo de resultados a serem retornados.|
|startOffset|inteiro|não|query| nenhum|Número de resultados a serem ignorados|
|adultContent|string|não|query|nenhum |Filtro de conteúdo adulto. Valores válidos: <ul><li>Desativado</li><li>Moderado</li><li>Estrito</li></ul>|
|market|string|não|query|nenhum |Mercado ou região para restringir a pesquisa (exemplo: pt-BR)|
|longitude|número|não|query|nenhum |Longitude (coordenada leste/oeste ) para restringir a pesquisa (exemplo: 47,603450)|
|latitude|número|não|query|nenhum |Latitude (coordenada norte/sul) para restringir a pesquisa (exemplo: -122,329696)|
|newsSortBy|string|não|query| nenhum|Ordem de classificação dos resultados. Valores válidos: <ul><li>Data</li><li>Relevância</li></ul> <p>A ordem de classificação de data implica em ordem decrescente.</p>|
|newsCategory|string|não|query| |Categoria de notícias para restringir a pesquisa (exemplo: ‘rt\_Business’)|
|newsLocationOverride|string|não|query|nenhum |Substituição para detecção de local do Bing. Esse parâmetro só é aplicável no mercado pt-BR. O formato de entrada é US./<state /> (exemplo: “US.WA”)|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Pesquisar ortografias    
Recupera sugestões de ortografia.```GET: /SpellingSuggestions```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|query|string|sim|query| nenhum|Texto a ser procurado (exemplo: ‘xbox’)|
|maxResult|inteiro|não|query|nenhum |Número máximo de resultados a serem retornados.|
|startOffset|inteiro|não|query| nenhum|Número de resultados a serem ignorados|
|adultContent|string|não|query|nenhum |Filtro de conteúdo adulto. Valores válidos: <ul><li>Desativado</li><li>Moderado</li><li>Estrito</li></ul>|
|market|string|não|query| nenhum|Mercado ou região para restringir a pesquisa (exemplo: pt-BR)|
|longitude|número|não|query|nenhum |Longitude (coordenada leste/oeste ) para restringir a pesquisa (exemplo: 47,603450)|
|latitude|número|não|query|nenhum |Latitude (coordenada norte/sul) para restringir a pesquisa (exemplo: -122,329696)|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Pesquisa relacionada    
Recupera os resultados da pesquisa relacionados de uma pesquisa do Bing.```GET: /RelatedSearch```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|query|string|sim|query|nenhum |Texto a ser procurado (exemplo: ‘xbox’)|
|maxResult|inteiro|não|query|nenhum |Número máximo de resultados a serem retornados.|
|startOffset|inteiro|não|query| nenhum|Número de resultados a serem ignorados|
|adultContent|string|não|query|nenhum |Filtro de conteúdo adulto. Valores válidos: <ul><li>Desativado</li><li>Moderado</li><li>Estrito</li></ul>|
|market|string|não|query|nenhum |Mercado ou região para restringir a pesquisa (exemplo: pt-BR)|
|longitude|número|não|query|nenhum |Longitude (coordenada leste/oeste ) para restringir a pesquisa (exemplo: 47,603450)|
|latitude|número|não|query| nenhum|Latitude (coordenada norte/sul) para restringir a pesquisa (exemplo: -122,329696)|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na operação.|


### Pesquisar tudo    
Recupera todos os sites, vídeos, imagens, etc. de uma pesquisa do Bing.```GET: /CompositeSearch```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|query|string|sim|query|nenhum |Texto a ser procurado (exemplo: ‘xbox’)|
|maxResult|inteiro|não|query|nenhum |Número máximo de resultados a serem retornados.|
|startOffset|inteiro|não|query|nenhum |Número de resultados a serem ignorados|
|adultContent|string|não|query|nenhum |Filtro de conteúdo adulto. Valores válidos: <ul><li>Desativado</li><li>Moderado</li><li>Estrito</li></ul>|
|market|string|não|query|nenhum |Mercado ou região para restringir a pesquisa (exemplo: pt-BR)|
|longitude|número|não|query|nenhum |Longitude (coordenada leste/oeste ) para restringir a pesquisa (exemplo: 47,603450)|
|latitude|número|não|query|nenhum |Latitude (coordenada norte/sul) para restringir a pesquisa (exemplo: -122,329696)|
|webFileType|string|não|query|nenhum |Tipo de arquivo para restringir a pesquisa (exemplo: 'DOC')|
|videoFilters|string|não|query|nenhum |Filtre a pesquisa com base no tamanho, aspecto, cor, estilo, face ou qualquer combinação desses. Valores válidos: <ul><li>Duração:Curta</li><li>Duração:Média</li><li>Duração:Longa</li><li>Aspecto:Padrão</li><li>Aspecto:Widescreen</li><li>Resolução:Baixa</li><li>Resolução:Média</li><li>Resolução:Alta</li></ul> <br/><br/>Por exemplo: ‘Duração:Curta+Resolução:Alta’|
|videoSortBy|string|não|query|nenhum |Ordem de classificação dos resultados. Valores válidos: <ul><li>Data</li><li>Relevância</li></ul> <p>A ordem de classificação de data implica em ordem decrescente.</p>|
|imageFilters|string|não|query|nenhum |Filtre a pesquisa com base no tamanho, aspecto, cor, estilo, face ou qualquer combinação desses. Valores válidos: <ul><li>Tamanho:Pequeno</li><li>Tamanho:Médio</li><li>Tamanho:Grande</li><li>Tamanho:Largura:[Largura]</li><li>Tamanho:Altura:[Altura]</li><li>Aspecto:Quadrado</li><li>Aspecto:Largo</li><li>Aspecto: Alto</li><li>Cor:Cor</li><li>Cor:Monocromático</li><li>Estilo:Foto</li><li>Estilo:Elemento Gráfico</li><li>Face:Face</li><li>Face:Retrato</li><li>Face:Outro</li></ul><br/><br/>Por exemplo: “Tamanho:Pequeno+Aspecto:Quadrado”|
|newsSortBy|string|não|query|nenhum |Ordem de classificação dos resultados. Valores válidos: <ul><li>Data</li><li>Relevância</li></ul> <p>A ordem de classificação de data implica em ordem decrescente.</p>|
|newsCategory|string|não|query|nenhum |Categoria de notícias para restringir a pesquisa (exemplo: ‘rt\_Business’)|
|newsLocationOverride|string|não|query|nenhum |Substituição para detecção de local do Bing. Esse parâmetro só é aplicável no mercado pt-BR. O formato de entrada é US./<state /> (exemplo: “US.WA”)|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


## Definições de objeto

#### WebResultModel: resultados da pesquisa da Web do Bing

|Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|Title|string|não|
|Descrição|string|não|
|DisplayUrl|string|não|
|ID|string|não|
|FullUrl|string|não|

#### VideoResultModel: resultados da pesquisa de vídeo do Bing

|Nome da Propriedade | Tipo de Dados |Obrigatório |
|---|---|---|
|Title|string|não|
|DisplayUrl|string|não|
|ID|string|não|
|MediaUrl|string|não|
|Tempo de execução|inteiro|não|
|Miniatura|não definido|não|

#### ThumbnailModel: propriedades de miniatura do elemento multimídia

|Nome da Propriedade | Tipo de Dados |Obrigatório |
|---|---|---|
|MediaUrl|string|não|
|ContentType|string|não|
|Largura|inteiro|não|
|Altura|inteiro|não|
|FileSize|inteiro|não|

#### ImageResultModel: resultados da pesquisa de imagens do Bing

|Nome da Propriedade | Tipo de Dados |Obrigatório |
|---|---|---|
|Title|string|não|
|DisplayUrl|string|não|
|ID|string|não|
|MediaUrl|string|não|
|SourceUrl|string|não|
|Miniatura|não definido|não|

#### NewsResultModel: resultados da pesquisa de notícias do Bing

|Nome da Propriedade | Tipo de Dados |Obrigatório |
|---|---|---|
|Title|string|não|
|Descrição|string|não|
|DisplayUrl|string|não|
|ID|string|não|
|Fonte|string|não|
|Data|string|não|

#### SpellResultModel: resultados de sugestões de ortografia do Bing

|Nome da Propriedade | Tipo de Dados |Obrigatório |
|---|---|---|
|ID|string|não|
|Valor|string|não|

#### RelatedSearchResultModel: resultados de pesquisa relacionados ao Bing

|Nome da Propriedade | Tipo de Dados |Obrigatório |
|---|---|---|
|Title|string|não|
|ID|string|não|
|BingUrl|string|não|

#### CompositeSearchResultModel: resultados de pesquisa compostas do Bing

|Nome da Propriedade | Tipo de Dados |Obrigatório |
|---|---|---|
|WebResultsTotal|inteiro|não|
|ImageResultsTotal|inteiro|não|
|VideoResultsTotal|inteiro|não|
|NewsResultsTotal|inteiro|não|
|SpellSuggestionsTotal|inteiro|não|
|WebResults|array|não|
|ImageResults|array|não|
|VideoResults|array|não|
|NewsResults|array|não|
|SpellSuggestionResults|array|não|
|RelatedSearchResults|array|não|

## Próximas etapas

[Crie um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

Volte para a [Lista de APIs](apis-list.md).

<!---HONumber=AcomDC_0525_2016-->