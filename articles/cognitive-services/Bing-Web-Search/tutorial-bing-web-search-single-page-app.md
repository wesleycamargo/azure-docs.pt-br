---
title: Aplicativo Web de página única da Pesquisa na Web do Bing | Microsoft Docs
description: Mostra como usar a API de Pesquisa na Web do Bing em um aplicativo Web de página única.
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 10/04/2017
ms.author: v-jerkin
ms.openlocfilehash: f22e38a1d6ee4042684b9822b58669bed6fe29a0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364425"
---
# <a name="tutorial-single-page-web-app"></a>Tutorial: aplicativo Web de página única

A API de Pesquisa na Web do Bing permite que você faça pesquisas na Web e obtenha resultados de tipos variados relevantes para uma consulta de pesquisa. Neste tutorial, criaremos um aplicativo Web de página única que usa a API de Pesquisa na Web do Bing para exibir os resultados da pesquisa à direita na página. O aplicativo inclui componentes HTML, CSS e JavaScript.

<!-- Remove until this can be replaced with a sanitized version.
![[Single-page Bing Web Search app]](media/cognitive-services-bing-web-api/web-search-spa-demo.png)
-->

> [!NOTE]
> Os cabeçalhos JSON e HTTP na parte inferior da página revelam as informações da resposta JSON e da solicitação HTTP quando recebem um clique. Esses detalhes são úteis ao explorar o serviço.

O aplicativo de tutorial ilustra como:

> [!div class="checklist"]
> * Executar uma chamada à API de Pesquisa na Web do Bing em JavaScript
> * Passar opções de pesquisa para a API de Pesquisa na Web do Bing
> * Exibir resultados da pesquisa na Web, de notícias, de imagens e de vídeo
> * Percorrer a página dos resultados da pesquisa
> * Manipular a ID do cliente do Bing e a chave de assinatura da API
> * Tratar erros que podem ocorrer

A página do tutorial é completamente autossuficiente; ela não usa estruturas externas, folhas de estilo, nem mesmo arquivos de imagem. Ela usa apenas recursos de linguagem JavaScript amplamente compatíveis e funciona com as versões atuais de todos os principais navegadores da Web.

Neste tutorial, abordaremos somente as partes selecionadas do código-fonte. O código-fonte completo está disponível [em uma página separada](tutorial-bing-web-search-single-page-app-source.md). Copie e cole esse código em um editor de texto e salve-o como `bing.html`.

## <a name="app-components"></a>Componentes do aplicativo

Como qualquer aplicativo Web de página única, o aplicativo de tutorial inclui três partes:

> [!div class="checklist"]
> * HTML – define a estrutura e o conteúdo da página
> * CSS – define a aparência da página
> * JavaScript – define o comportamento da página

Este tutorial não aborda a maior parte do CSS ou do HTML em detalhes, pois eles são simples.

O HTML contém o formulário de pesquisa no qual o usuário insere uma consulta e escolhe as opções de pesquisa. O formulário está conectado ao JavaScript que, de fato, executa a pesquisa pelo atributo `onsubmit` da marcação `<form>`:

```html
<form name="bing" onsubmit="return newBingWebSearch(this)">
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

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/search";

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

A marca `onsubmit` HTML `form` chama a função `bingWebSearch` para retornar os resultados da pesquisa. `bingWebSearch` usa `getSubscriptionKey` para autenticar cada consulta. Conforme mostrado na definição anterior, `getSubscriptionKey` solicita ao usuário a chave, caso ela ainda não tenha sido inserida. A chave é então armazenada para continuar o uso pelo aplicativo.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="selecting-search-options"></a>Selecionando as opções de pesquisa

![[Formulário da Pesquisa na Web do Bing]](media/cognitive-services-bing-web-api/web-search-spa-form.png)

O formulário HTML inclui elementos com os seguintes nomes:

| | |
|-|-|
| `where` | Um menu suspenso para selecionar o mercado (local e idioma) usado para a pesquisa. |
| `query` | O campo de texto no qual inserir os termos de pesquisa. |
| `what` | Caixas de seleção para promover tipos específicos de resultados. Por exemplo, a promoção de imagens aumenta a classificação das imagens. |
| `when` | Menu suspenso para, opcionalmente, limitar a pesquisa para o dia, a semana ou o mês mais recente. |
| `safe` | Uma caixa de seleção que indica se o recurso Pesquisa Segura do Bing deve ser usado para filtrar resultados "para adultos". |
| `count` | Campo oculto. O número de resultados da pesquisa a ser retornado em cada solicitação. Altere para exibir menos ou mais resultados por página. |
| `offset` | Campo oculto. O deslocamento do primeiro resultado da pesquisa na solicitação; usado para paginação. Ele é redefinido para `0` em uma nova solicitação. |

> [!NOTE]
> A Pesquisa na Web do Bing oferece muito mais parâmetros de consulta. Estamos usando apenas alguns deles aqui.

A função JavaScript `bingSearchOptions()` converte esses campos no formato exigido pela API de Pesquisa do Bing.

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var what = [];
    for (var i = 0; i < form.what.length; i++) 
        if (form.what[i].checked) what.push(form.what[i].value);
    if (what.length) {
        options.push("promote=" + what.join(","));
        options.push("answerCount=9");
    }
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    options.push("textDecorations=true");
    options.push("textFormat=HTML");
    return options.join("&");
}
```

Por exemplo, o parâmetro `SafeSearch` em uma chamada à API real pode ser `strict`, `moderate` ou `off`, com `moderate` sendo o padrão. No entanto, nosso formulário usa uma caixa de seleção, que tem apenas dois estados. O código JavaScript converte essa configuração para `strict` ou `off` (`moderate` não é usado).

Se uma das caixas de seleção **Promover** estiver marcada, também adicionaremos um parâmetro `answerCount` à consulta. `answerCount` é necessário ao usar o parâmetro `promote`. Apenas o definimos como `9` (o número de tipos de resultado ao qual a API de Pesquisa na Web do Bing dá suporte) para garantir que obteremos o número máximo possível de tipos de resultados.

> [!NOTE]
> A promoção de um tipo de resultado não *assegura* que os resultados da pesquisa incluirão esse tipo de resultado. Em vez disso, a promoção aumenta a classificação desses tipos de resultados em relação à classificação normal. Para limitar as pesquisas a determinados tipos de resultados, use o parâmetro de consulta `responseFilter` ou chame um ponto de extremidade mais específico, como a Pesquisa de Imagem do Bing ou a Pesquisa de Notícias do Bing.

Enviamos também os parâmetros de consulta `textDecoration` e `textFormat` para fazer com que o termo de pesquisa seja colocado em negrito nos resultados da pesquisa. Esses valores são embutidos em código no script.

## <a name="performing-the-request"></a>Executando a solicitação

Considerando a consulta, a cadeia de caracteres de opções e a chave de API, a função `BingWebSearch` usa um objeto `XMLHttpRequest` para fazer a solicitação ao ponto de extremidade da Pesquisa na Web do Bing.

```javascript
// perform a search given query, options string, and API key
function bingWebSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "paging1", "paging2", "error");

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
        return;
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
            if (jsobj._type === "SearchResponse" && "rankingResponse" in jsobj) {
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
> Uma solicitação HTTP bem-sucedida *não* necessariamente significa que a pesquisa em si foi bem-sucedida. Se ocorrer um erro na operação de pesquisa, a API de Pesquisa na Web do Bing retornará um código de status HTTP não 200 e incluirá informações de erro na resposta JSON. Além disso, se a solicitação tinha limite de taxa, a API retorna uma resposta vazia.

Grande parte do código nas duas funções anteriores é dedicada ao tratamento de erro. Podem ocorrer erros nos seguintes estágios:

|Estágio|Possíveis erros|Manipulado por|
|-|-|-|
|Criando um objeto de solicitação do JavaScript|URL inválida|Bloco `try`/`catch`|
|Fazendo a solicitação|Erros de rede, conexões anuladas|Manipuladores de eventos `error` e `abort`|
|Executando a pesquisa|Solicitação inválida, JSON inválido, limites de taxa|testes no manipulador de eventos `load`|

Os erros são tratados com uma chamada a `renderErrorMessage()` com os detalhes conhecidos sobre o erro. Se a resposta passar no desafio completo de testes de erro, chamaremos `renderSearchResults()` para exibir os resultados da pesquisa na página.

## <a name="displaying-search-results"></a>Exibindo os resultados da pesquisa

A API de Pesquisa na Web do Bing [exige a exibição dos resultados em uma ordem especificada](useanddisplayrequirements.md). Como a API pode retornar diferentes tipos de respostas, não é suficiente iterar pela coleção `WebPages` de nível superior na resposta JSON e exibir os resultados. (Caso deseje obter apenas um tipo de resultado, use o parâmetro de consulta `responseFilter` ou outro ponto de extremidade da Pesquisa do Bing.)

Em vez disso, usamos o `rankingResponse` nos resultados da pesquisa para ordenar os resultados para exibição. Esse objeto se refere a itens nas coleções `News`, `Images` e/ou `Videos` de `WebPages` ou em outras coleções de respostas de nível superior na resposta JSON.

`rankingResponse` pode conter até três coleções de resultados da pesquisa, designadas `pole`, `mainline` e `sidebar`. 

`pole`, se estiver presente, será o resultado de pesquisa mais relevante e deverá ser exibido em destaque. `mainline` refere-se à maior parte dos resultados da pesquisa. Os resultados da linha principal devem ser exibidos imediatamente após `pole` (ou primeiro, caso `pole` não esteja presente). 

Por fim, `sidebar` refere-se aos resultados da pesquisa auxiliares. Geralmente, esses resultados são pesquisas ou imagens relacionadas. Se possível, esses resultados deverão ser exibidos em uma barra lateral real. Se os limites da tela impossibilitarem a exibição de uma barra lateral (por exemplo, em um dispositivo móvel), eles deverão ser exibidos após os resultados `mainline`.

Cada item em uma coleção `rankingResponse` se refere aos itens de resultado da pesquisa real de duas maneiras diferentes, mas equivalentes.

| | |
|-|-|
|`id`|A `id` se parece com uma URL, mas não deve ser usada para links. O tipo `id` de um resultado de classificação corresponde à `id` de um item de resultado da pesquisa em uma coleção de respostas *ou* a uma coleção inteira de respostas (como `Images`).
|`answerType`, `resultIndex`|O `answerType` refere-se à coleção de respostas de nível superior que contém o resultado (por exemplo, `WebPages`). O `resultIndex` refere-se ao índice do resultado nessa coleção. Se `resultIndex` é omitido, o resultado de classificação refere-se à coleção inteira.

> [!NOTE]
> Para obter mais informações sobre essa parte da resposta da pesquisa, confira [Resultados de classificação](rank-results.md).

Use qualquer método de localização do item de resultado da pesquisa referenciado que seja mais conveniente para seu aplicativo. Em nosso código do tutorial, usamos o `answerType` e o `resultIndex` para localizar cada resultado da pesquisa.

Por fim, é hora de examinar nossa função `renderSearchResults()`. Essa função itera pelas três coleções `rankingResponse` que representam as três seções dos resultados da pesquisa. Para cada seção, chamamos `renderResultsItems()` para renderizar os resultados para essa seção.

```javascript
// render the search results given the parsed JSON response
function renderSearchResults(results) {

    // if spelling was corrected, update search field
    if (results.queryContext.alteredQuery) 
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);
    
    // for each possible section, render the resuts from that section
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

`renderResultsItems()`, por sua vez, itera pelos itens em cada seção `rankingResponse`, mapeia cada resultado de classificação para um resultado da pesquisa usando os campos `answerType` e `resultIndex` e chama a função de renderização apropriada para gerar o HTML do resultado. 

Se `resultIndex` não é especificado para determinado item de classificação, `renderResultsItems()` itera por todos os resultados desse tipo e chama a função de renderização de cada item. 

De qualquer forma, o HTML resultante é inserido no elemento `<div>` apropriado na página.

```javascript
// render search results from rankingResponse object in specified order
function renderResultsItems(section, results) {

    var items = results.rankingResponse[section].items;
    var html = [];
    for (var i = 0; i < items.length; i++) {
        var item = items[i];
        // collection name has lowercase first letter while answerType has uppercase
        // e.g. `WebPages` rankingResult type is in the `webPages` top-level collection
        var type = item.answerType[0].toLowerCase() + item.answerType.slice(1);
        // must have results of the given type AND a renderer for it
        if (type in results && type in searchItemRenderers) {
            var render = searchItemRenderers[type];
            // this ranking item refers to ONE result of the specified type
            if ("resultIndex" in item) {
                html.push(render(results[type].value[item.resultIndex], section));
            // this ranking item refers to ALL results of the specified type
            } else {
                var len = results[type].value.length;
                for (var j = 0; j < len; j++) {
                    html.push(render(results[type].value[j], section, j, len));
                }
            }
        }
    }
    return html.join("\n\n");
}
```

## <a name="rendering-result-items"></a>Renderizando os itens de resultado

Em nosso código JavaScript, há um objeto, `searchItemRenderers`, que contém funções *renderers:* que geram um HTML para cada tipo de resultado da pesquisa.

```javascript
// render functions for various types of search results
searchItemRenderers = { 
    webPages: function(item) { ... },
    news: function(item) { ... },
    images: function(item, section, index, count) { ... },
    videos: function(item, section, index, count) { ... },
    relatedSearches: function(item, section, index, count) { ... }
}
```

> [!NOTE]
> Nosso aplicativo de tutorial tem renderizadores para páginas da Web, itens de notícias, imagens, vídeos e pesquisas relacionadas. Seu próprio aplicativo precisa de renderizadores para todos os tipos de resultados que você poderá receber, que podem incluir cálculos, sugestões de ortografia, entidades, fusos horários e definições.

Algumas de nossas funções de renderização aceitam somente o parâmetro `item`: um objeto JavaScript que representa um único resultado da pesquisa. Outras aceitam parâmetros adicionais, que podem ser usados para renderizar itens de maneira diferente em diferentes contextos. (Um renderizador que não usa essas informações não precisa aceitar esses parâmetros.)

Os argumentos de contexto são:

| | |
|-|-|
|`section`|A seção de resultados (`pole`, `mainline` ou `sidebar`) na qual o item é exibido.
|`index`<br>`count`|Disponível quando o item `rankingResponse` especifica que todos os resultados de determinada coleção devem ser exibidos; caso contrário, `undefined`. Esses parâmetros recebem o índice do item na coleção e o número total de itens na coleção. Use essas informações para numerar os resultados, gerar um HTML diferente para o primeiro ou o último resultado e assim por diante.|

Em nosso aplicativo de tutorial, os renderizadores `images` e `relatedSearches` usam os argumentos do contexto para personalizar o HTML gerado. Vamos examinar o renderizador `images` mais detalhadamente:

```javascript
searchItemRenderers = { 
    // render image result using thumbnail
    images: function(item, section, index, count) {
        var height = 60;
        var width = Math.round(height * item.thumbnail.width / item.thumbnail.height);
        var html = [];
        if (section === "sidebar") {
            if (index) html.push("<br>");
        } else {
            if (!index) html.push("<p class='images'>");
        }
        html.push("<a href='" + item.hostPageUrl + "'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + 
            "' height=" + height + " width=" + width + " title='" + title + "' alt='" + title + "'>");
        html.push("</a>");
        return html.join("");
    }, // other renderers omitted
}
```

Nossa função de renderizador de imagem:

> [!div class="checklist"]
> * Calcula o tamanho da miniatura da imagem (a largura varia, enquanto a altura é fixada em 60 pixels).
> * Insere o HTML que precede o resultado de imagem, dependendo do contexto.
> * Cria a marca HTML `<a>` vinculada à página que contém a imagem.
> * Cria a marca HTML `<img>` para exibir a miniatura da imagem. 

O renderizador de imagem usa as variáveis `section` e `index` para exibir os resultados de maneira diferente, dependendo do local em que são exibidos. Uma quebra de linha (marcação `<br>`) é inserida entre os resultados de imagem na barra lateral, para que a barra lateral exiba uma coluna de imagens. Em outras seções, o primeiro resultado de imagem `(index === 0)` é precedido por uma marcação `<p>`. Caso contrário, as miniaturas se aglutinam e são encapsuladas, conforme necessário, na janela do navegador.

O tamanho da miniatura é usado na marcação `<img>` e nos campos `h` e `w` na URL da miniatura. Em seguida, o [serviço em miniatura do Bing](resize-and-crop-thumbnails.md) fornece uma miniatura exatamente desse tamanho. Os atributos `title` e `alt` (uma descrição textual da imagem) são construídos com base no nome da imagem e no nome do host na URL.

As imagens são exibidas, conforme mostrado aqui, nos resultados da pesquisa da linha principal.

![[Resultados de imagem do Bing]](media/cognitive-services-bing-web-api/web-search-spa-images.png)

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
> [Tutorial de aplicativo móvel da Pesquisa Visual](computer-vision-web-search-tutorial.md)

> [!div class="nextstepaction"]
> [Referência da API de Pesquisa na Web do Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
