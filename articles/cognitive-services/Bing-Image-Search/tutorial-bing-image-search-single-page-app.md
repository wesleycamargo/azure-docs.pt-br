---
title: Aplicativo Web de página única da Pesquisa de Imagem do Bing | Microsoft Docs
description: Mostra como usar a API de Pesquisa de Imagem do Bing em um aplicativo Web de página única.
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 10/04/2017
ms.author: v-jerkin
ms.openlocfilehash: d0e1dc24513c8fc3a405cf1c18f531a0c58fad13
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364424"
---
# <a name="tutorial-single-page-web-app"></a>Tutorial: aplicativo Web de página única

A API de Pesquisa de Imagem do Bing permite que você faça pesquisas na Web e obtenha os resultados de imagem relevantes para a consulta de pesquisa. Neste tutorial, criaremos um aplicativo Web de página única que usa a API de Pesquisa de Imagem do Bing para exibir os resultados da pesquisa à direita na página. O aplicativo inclui componentes HTML, CSS e JavaScript.

<!-- Remove until we can sanitize images
![[Single-page Bing Image Search app]](media/cognitive-services-bing-images-api/image-search-spa-demo.png)
-->

> [!NOTE]
> Os cabeçalhos JSON e HTTP na parte inferior da página revelam as informações da resposta JSON e da solicitação HTTP quando recebem um clique. Esses detalhes são úteis ao explorar o serviço.

O aplicativo de tutorial ilustra como:

> [!div class="checklist"]
> * Executar uma chamada à API de Pesquisa de Imagem do Bing em JavaScript
> * Passar opções de pesquisa para a API de Pesquisa de Imagem do Bing
> * Exibir os resultados da pesquisa
> * Percorrer a página dos resultados da pesquisa
> * Manipular a ID do cliente do Bing e a chave de assinatura da API
> * Tratar erros que podem ocorrer

A página do tutorial é completamente autossuficiente; ela não usa estruturas externas, folhas de estilo, nem mesmo arquivos de imagem. Ela usa apenas recursos de linguagem JavaScript amplamente compatíveis e funciona com as versões atuais de todos os principais navegadores da Web.

Neste tutorial, abordaremos somente as partes selecionadas do código-fonte. O código-fonte completo está disponível [em uma página separada](tutorial-bing-image-search-single-page-app-source.md). Copie e cole esse código em um editor de texto e salve-o como `bing.html`.

> [!NOTE]
> Este tutorial é muito semelhante ao [tutorial de aplicativo da Pesquisa na Web do Bing de página única](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md), mas lida apenas com os resultados da pesquisa de imagem.

## <a name="app-components"></a>Componentes do aplicativo

Como qualquer aplicativo Web de página única, o aplicativo de tutorial inclui três partes:

> [!div class="checklist"]
> * HTML – define a estrutura e o conteúdo da página
> * CSS – define a aparência da página
> * JavaScript – define o comportamento da página

Este tutorial não aborda a maior parte do CSS ou do HTML em detalhes, pois eles são simples.

O HTML contém o formulário de pesquisa no qual o usuário insere uma consulta e escolhe as opções de pesquisa. O formulário está conectado ao JavaScript que, de fato, executa a pesquisa pelo atributo `onsubmit` da marcação `<form>`:

```html
<form name="bing" onsubmit="return newBingImageSearch(this)">
```

O manipulador `onsubmit` retorna `false`, o que impede o envio do formulário para um servidor. Na verdade, o código JavaScript, faz o trabalho de coletar as informações necessárias do formulário e realizar a pesquisa.

O HTML também contém as divisões (marcas HTML `<div>`) nas quais os resultados da pesquisa são exibidos.

## <a name="managing-subscription-key"></a>Gerenciando a chave de assinatura

Para evitar a necessidade de incluir a chave de assinatura da API de Pesquisa do Bing no código, usamos o armazenamento persistente do navegador para armazenar a chave. Se nenhuma chave for armazenada, solicitaremos a chave do usuário, armazenando-a para uso posterior. Se a chave for rejeitada pela API mais tarde, invalidaremos a chave armazenada para que o usuário receba a solicitação novamente.

Definimos as funções `storeValue` e `retrieveValue` que usam o objeto `localStorage` (se o navegador dá suporte e ele) ou um cookie. Nossa função `getSubscriptionKey()` usa essas funções para armazenar e recuperar a chave do usuário.

```javascript
// cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/images/search";

// ... omitted definitions of storeValue() and retrieveValue()

// get stored API subscription key, or prompt if it's not found
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```

A marca `onsubmit` HTML `<form>` chama a função `bingWebSearch` para retornar os resultados da pesquisa. `bingWebSearch` usa `getSubscriptionKey` para autenticar cada consulta. Conforme mostrado na definição anterior, `getSubscriptionKey` solicita ao usuário a chave, caso ela ainda não tenha sido inserida. A chave é então armazenada para continuar o uso pelo aplicativo.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="selecting-search-options"></a>Selecionando as opções de pesquisa

![[Formulário da Pesquisa de Imagem do Bing]](media/cognitive-services-bing-images-api/image-search-spa-form.png)

O formulário HTML inclui os seguintes controles:

| | |
|-|-|
|`where`|Um menu suspenso para selecionar o mercado (local e idioma) usado para a pesquisa.|
|`query`|O campo de texto no qual inserir os termos de pesquisa.|
|`aspect`|Botões de opção para escolher as proporções das imagens encontradas: aproximadamente quadrada, larga ou alta.|
|`color`|Seleciona a cor, preto e branco ou uma cor predominante.
|`when`|Menu suspenso para, opcionalmente, limitar a pesquisa para o dia, a semana ou o mês mais recente.|
|`safe`|Uma caixa de seleção que indica se o recurso Pesquisa Segura do Bing deve ser usado para filtrar resultados "para adultos".|
|`count`|Campo oculto. O número de resultados da pesquisa a ser retornado em cada solicitação. Altere para exibir menos ou mais resultados por página.|
|`offset`|Campo oculto. O deslocamento do primeiro resultado da pesquisa na solicitação; usado para paginação. Ele é redefinido para `0` em uma nova solicitação.|
|`nextoffset`|Campo oculto. Ao receber um resultado da pesquisa, esse campo é definido com o valor de `nextOffset` na resposta. O uso desse campo previne resultados sobrepostos em páginas sucessivas.|
|`stack`|Campo oculto. Uma lista codificada em JSON de deslocamentos de páginas anteriores dos resultados da pesquisa, para navegar novamente para páginas anteriores.|

> [!NOTE]
> A Pesquisa de Imagem do Bing oferece muito mais parâmetros de consulta. Estamos usando apenas alguns deles aqui.

Nossa função JavaScript `bingSearchOptions()` converte esses campos em uma cadeia de caracteres de consulta parcial no formato exigido pela API de Pesquisa do Bing.

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var aspect = "all";
    for (var i = 0; i < form.aspect.length; i++) {
        if (form.aspect[i].checked) {
            aspect = form.aspect[i].value;
            break;
        }
    }
    options.push("aspect=" + aspect);
    if (form.color.value) options.push("color=" + form.color.value);
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    return options.join("&");
}
```

Por exemplo, o recurso Pesquisa Segura pode ser `strict`, `moderate` ou `off`, com `moderate` sendo o padrão. Porém, nosso formulário usa uma caixa de seleção, que tem apenas dois estados. O código JavaScript converte essa configuração para `strict` ou `off` (não usamos `moderate`).

## <a name="performing-the-request"></a>Executando a solicitação

Considerando a consulta, a cadeia de caracteres de opções e a chave de API, a função `BingImageSearch` usa um objeto `XMLHttpRequest` para fazer a solicitação ao ponto de extremidade da Pesquisa de Imagem do Bing.

```javascript
// perform a search given query, options string, and API key
function bingImageSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("results", "related", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

    // open the request
    try {
        request.open("GET", queryurl);
    } 
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // add request headers
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);
    
    // event handler for successful response
    request.addEventListener("load", handleBingResponse);
    
    // event handler for erorrs
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // event handler for aborted request
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // send the request
    request.send();
    return false;
}
```

Após a conclusão bem-sucedida da solicitação HTTP, o JavaScript chama nosso manipulador de eventos `load`, a função `handleBingResponse()`, para manipular uma solicitação HTTP GET bem-sucedida para a API. 

```javascript
// handle Bing search request results
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
    }

    // show raw JSON and HTTP request
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " + 
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "Images") {
                if (jsobj.nextOffset) document.forms.bing.nextoffset.value = jsobj.nextOffset;
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    }

    // Any other HTTP response is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSubscriptionKey();

        // some error responses don't have a top-level errors object, so gin one up
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // display HTTP status code
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // add all fields from all error responses
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // also display Bing Trace ID if it isn't blocked by CORS
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // and display the error message
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> Uma solicitação HTTP bem-sucedida *não* necessariamente significa que a pesquisa em si foi bem-sucedida. Se ocorrer um erro na operação de pesquisa, a API de Pesquisa de Imagem do Bing retornará um código de status HTTP não 200 e incluirá informações de erro na resposta JSON. Além disso, se a solicitação tinha limite de taxa, a API retorna uma resposta vazia.

Grande parte do código nas duas funções anteriores é dedicada ao tratamento de erro. Podem ocorrer erros nos seguintes estágios:

|Estágio|Possíveis erros|Manipulado por|
|-|-|-|
|Criando um objeto de solicitação do JavaScript|URL inválida|Bloco `try`/`catch`|
|Fazendo a solicitação|Erros de rede, conexões anuladas|Manipuladores de eventos `error` e `abort`|
|Executando a pesquisa|Solicitação inválida, JSON inválido, limites de taxa|testes no manipulador de eventos `load`|

Os erros são tratados com uma chamada a `renderErrorMessage()` com os detalhes conhecidos sobre o erro. Se a resposta passar no desafio completo de testes de erro, chamaremos `renderSearchResults()` para exibir os resultados da pesquisa na página.

## <a name="displaying-search-results"></a>Exibindo os resultados da pesquisa

A função principal para exibir os resultados da pesquisa é `renderSearchResults()`. Essa função usa o JSON retornado pelo serviço de Pesquisa de Imagem do Bing e renderiza as imagens e as pesquisas relacionadas se houver.

```javascript
function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);
    
    showDiv("results", renderImageResults(results.value));
    if (results.relatedSearches)
        showDiv("sidebar", renderRelatedItems(results.relatedSearches));
}
```

Os resultados da pesquisa de imagem principal são retornados como o objeto `value` de nível superior na resposta JSON. Passamos esses resultados para a função `renderImageResults()`, que itera por eles e chama uma função separada para renderizar cada item em HTML. O HTML resultante é retornado para `renderSearchResults()`, no qual ele é inserido na divisão `results` da página.

```javascript
function renderImageResults(items) {
    var len = items.length;
    var html = [];
    if (!len) {
        showDiv("noresults", "No results.");
        hideDivs("paging1", "paging2");
        return "";
    }
    for (var i = 0; i < len; i++) {
        html.push(searchItemRenderers.images(items[i], i, len));
    }
    return html.join("\n\n");
}
```

A API de Pesquisa de Imagem do Bing retorna até quatro tipos diferentes de resultados relacionados, cada um em seu próprio objeto de nível superior. Eles são:

|||
|-|-|
|`pivotSuggestions`|Consultas que substituem uma palavra original na pesquisa original por outra. Por exemplo, se você pesquisar "flores vermelhas", uma palavra original pode ser "vermelhas" e uma sugestão dinâmica poderá ser "flores amarelas".|
|`queryExpansions`|Consultas que restringem a pesquisa original com a adição de mais termos. Por exemplo, se você pesquisar "Microsoft Surface", uma expansão da consulta poderá ser "Microsoft Surface Pro".|
|`relatedSearches`|Consultas que também foram inseridas por outros usuários que inseriram a pesquisa original. Por exemplo, se você pesquisar "Monte Rainier", uma pesquisa relacionada poderá ser "Mt. Santa Helena".|
|`similarTerms`|Consultas que são semelhantes em significado à pesquisa original. Por exemplo, se você pesquisar "gatinhos", um termo semelhante poderá ser "bonito".|

Conforme visto anteriormente em `renderSearchResults()`, renderizamos apenas as sugestões de `relatedItems` e colocamos os links resultantes na barra lateral da página.

## <a name="rendering-result-items"></a>Renderizando os itens de resultado

Em nosso código JavaScript, há um objeto, `searchItemRenderers`, que contém funções *renderers:* que geram um HTML para cada tipo de resultado da pesquisa.

```javascript
searchItemRenderers = { 
    images: function(item, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```

Uma função de renderizador pode aceitar os seguintes parâmetros:

| | |
|-|-|
|`item`|O objeto do JavaScript que contém as propriedades do item, como a URL e a descrição.|
|`index`|O índice do item de resultado na coleção.|
|`count`|O número de itens na coleção do item de resultado da pesquisa.|

Os parâmetros `index` e `count` podem ser usados para numerar os resultados, para gerar um HTML especial no início ou no fim de uma coleção, para inserir quebras de linha após determinado número de itens e assim por diante. Se um renderizador não precisa dessa funcionalidade, ele não precisa aceitar esses dois parâmetros.

Vamos examinar o renderizador `images` mais detalhadamente:

```javascript
    images: function (item, index, count) {
        var height = 120;
        var width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
        var html = [];
        if (index === 0) html.push("<p class='images'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<p class='images' style='max-width: " + width + "px'>");
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + 
            "' height=" + height + " width=" + width + "'>");
        html.push("<br>");
        html.push("<nobr><a href='" + item.contentUrl + "'>Image</a> - ");
        html.push("<a href='" + item.hostPageUrl + "'>Page</a></nobr><br>");
        html.push(title.replace("\n", " (").replace(/([a-z0-9])\.([a-z0-9])/g, "$1.<wbr>$2") + ")</p>");
        return html.join("");
    }, // relatedSearches renderer omitted
```

Nossa função de renderizador de imagem:

> [!div class="checklist"]
> * Calcula o tamanho da miniatura da imagem (a largura varia, com um mínimo de 120 pixels, enquanto a altura é fixada em 90 pixels).
> * Cria a marca HTML `<img>` para exibir a miniatura da imagem. 
> * Cria as marcas HTML `<a>` que fornecem links para a imagem e a página que a contém.
> * Cria a descrição que exibe informações sobre a imagem e o site no qual ela está localizada.

Testamos a variável `index` para inserir uma marcação `<p>` antes do primeiro resultado de imagem. Caso contrário, as miniaturas se aglutinam e são encapsuladas, conforme necessário, na janela do navegador.

O tamanho da miniatura é usado na marcação `<img>` e nos campos `h` e `w` na URL da miniatura. Em seguida, o [serviço em miniatura do Bing](resize-and-crop-thumbnails.md) fornece uma miniatura exatamente desse tamanho.

## <a name="persisting-client-id"></a>Persistindo a ID do cliente

As respostas das APIs de Pesquisa do Bing podem incluir um cabeçalho `X-MSEdge-ClientID` que deve ser enviado novamente para a API com solicitações sucessivas. Se várias APIs de Pesquisa do Bing estiverem sendo usadas, a mesma ID do cliente deverá ser usada com todas elas, se possível.

O fornecimento do cabeçalho `X-MSEdge-ClientID` permite que as APIs do Bing associem todas as pesquisas do usuário, o que oferece dois benefícios importantes.

Primeiro, ele permite que o mecanismo de pesquisa do Bing aplique o contexto passado às pesquisas para encontrar resultados que melhor atendam às expectativas do usuário. Se um usuário tiver pesquisado termos relacionados à navegação anteriormente, por exemplo, uma pesquisa posterior por "nós" poderá, preferencialmente, retornar informações sobre os nós usados em navegação.

Em segundo lugar, o Bing poderá selecionar usuários aleatoriamente para experimentar novos recursos antes de eles ficarem amplamente disponíveis. O fornecimento da mesma ID do cliente com cada solicitação garante que os usuários que foram escolhidos para ver um recurso sempre o vejam. Sem a ID do cliente, o usuário poderá ver um recurso aparecer e desaparecer, aparentemente de forma aleatória, nos resultados da pesquisa.

As políticas de segurança do navegador (CORS) podem impedir que o cabeçalho `X-MSEdge-ClientID` fique disponível para o JavaScript. Essa limitação ocorre quando a resposta da pesquisa tem uma origem diferente da página que a solicitou. Em um ambiente de produção, você deve lidar com essa política hospedando um script do servidor que faz a chamada à API no mesmo domínio da página da Web. Como o script tem a mesma origem da página da Web, o cabeçalho `X-MSEdge-ClientID` estará disponível para o JavaScript.

> [!NOTE]
> Em um aplicativo Web de produção, você deve executar a solicitação do servidor mesmo assim. Caso contrário, a chave de API de Pesquisa do Bing precisará ser incluída na página da Web, na qual ela estará disponível para qualquer pessoa que exiba a origem. Você é cobrado por todos os usos em sua chave de assinatura de API, até mesmo por solicitações feitas por partes não autorizadas. Portanto, é importante não expor sua chave.

Para fins de desenvolvimento, você pode fazer a solicitação da API de Pesquisa na Web do Bing por meio de um proxy CORS. A resposta desse proxy tem um cabeçalho `Access-Control-Expose-Headers` que inclui os cabeçalhos de resposta na lista de permissões e disponibiliza-os para o JavaScript.

É fácil instalar um proxy CORS para permitir que o aplicativo de tutorial acesse o cabeçalho da ID do cliente. Primeiro, caso ainda não tenha, [instale o Node.js](https://nodejs.org/en/download/). Em seguida, emita o seguinte comando em uma janela Comando:

    npm install -g cors-proxy-server

Em seguida, altere o ponto de extremidade da Pesquisa na Web do Bing no arquivo HTML para:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Por fim, inicie o proxy CORS com o seguinte comando:

    cors-proxy-server

Deixe a janela Comando aberta enquanto usa o aplicativo de tutorial, porque se você fechá-la, isso interromperá o proxy. Na seção expansível Cabeçalhos HTTP abaixo dos resultados da pesquisa, agora você pode ver o cabeçalho `X-MSEdge-ClientID` (entre outros) e verificar se é o mesmo para cada solicitação.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API de Pesquisa de Imagem do Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)

