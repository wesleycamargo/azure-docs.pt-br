---
title: 'Tutorial: Criar um aplicativo Web de página única – Pesquisa Visual do Bing'
titleSuffix: Azure Cognitive Services
description: Mostra como usar a API da Pesquisa Visual do Bing em um aplicativo Web de página única.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: tutorial
ms.date: 10/04/2017
ms.author: aahi
ms.openlocfilehash: fe7159e88bd70ba8af23909559264fa5f210ef10
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52443876"
---
# <a name="tutorial-visual-search-single-page-web-app"></a>Tutorial: Aplicativo Web de uma página Pesquisa Visual Única

A API de Pesquisa Visual do Bing fornece uma experiência semelhante aos detalhes da imagem mostrados em Bing.com/images. Com a Pesquisa Visual, você pode especificar uma imagem e obter informações sobre a imagem, como imagens visualmente semelhantes, fontes de compras, páginas da Web que incluem a imagem e muito mais. 

Para este tutorial, você precisará iniciar uma assinatura na faixa de preço S9, conforme mostra [Preços dos Serviços Cognitivos – API de Pesquisa do Bing](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/search-api/). 

Para iniciar uma assinatura no portal do Azure:
1. Insira "BingSearchV7" na caixa de texto na parte superior do portal do Azure que mostra `Search resources, services, and docs`.  
2. Em Marketplace, na lista suspensa, selecione `Bing Search v7`.
3. Insira `Name` para o novo recurso.
4. Selecionar assinatura `Pay-As-You-Go`.
5. Selecione o tipo de preço `S9`.
6. Clique em `Enable` para iniciar a assinatura.

Este tutorial estende o aplicativo Web de única página do tutorial de da Pesquisa de Imagem do Bing (veja [Aplicativo Web de página única](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md)). Para obter o código-fonte completo para começar este tutorial, veja [Aplicativo Web de página única (código-fonte)](../Bing-Image-Search/tutorial-bing-image-search-single-page-app-source.md). Para o código-fonte final deste tutorial, veja [Aplicativo Web de página única da Pesquisa Visual](tutorial-bing-visual-search-single-page-app-source.md).

As tarefas abordadas são:

> [!div class="checklist"]
> * Chamar a API de Pesquisa Visual do Bing com um token de informações da imagem
> * Exibir imagens semelhantes

## <a name="call-bing-visual-search"></a>Chamar a Pesquisa Visual do Bing
Edite o tutorial da Pesquisa de Imagem do Bing e adicione o código a seguir ao final do elemento de script na linha 409. Esse código chama a API da Pesquisa Visual do Bing e exibe os resultados.

``` javascript
function handleVisualSearchResponse(){
    if(this.status !== 200){
        console.log(this.responseText);
        return;
    }
    let json = this.responseText;
    let response = JSON.parse(json);
    for (let i =0; i < response.tags.length; i++) {
        let tag = response.tags[i];
        if (tag.displayName === '') {
            for (let j = 0; j < tag.actions.length; j++) {
                let action = tag.actions[j];
                if (action.actionType === 'VisualSearch') {
                    let html = '';
                    for (let k = 0; k < action.data.value.length; k++) {
                        let item = action.data.value[k];
                        let height = 120;
                        let width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
                        html += "<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + "' height=" + height + " width=" + width + "'>";
                    }
                    showDiv("insights", html);
                    window.scrollTo({top: document.getElementById("insights").getBoundingClientRect().top, behavior: "smooth"});
                }
            }
        }
    }
}

function bingVisualSearch(insightsToken){
    let visualSearchBaseURL = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch',
        boundary = 'boundary_ABC123DEF456',
        startBoundary = '--' + boundary,
        endBoundary = '--' + boundary + '--',
        newLine = "\r\n",
        bodyHeader = 'Content-Disposition: form-data; name="knowledgeRequest"' + newLine + newLine;

    let postBody = {
        imageInfo: {
            imageInsightsToken: insightsToken
        }
    }
    let requestBody = startBoundary + newLine;
    requestBody += bodyHeader;
    requestBody += JSON.stringify(postBody) + newLine + newLine;
    requestBody += endBoundary + newLine;       
    
    let request = new XMLHttpRequest();

    try {
        request.open("POST", visualSearchBaseURL);
    } 
    catch (e) {
        renderErrorMessage("Error performing visual search: " + e.message);
    }
    request.setRequestHeader("Ocp-Apim-Subscription-Key", getSubscriptionKey());
    request.setRequestHeader("Content-Type", "multipart/form-data; boundary=" + boundary);
    request.addEventListener("load", handleVisualSearchResponse);
    request.send(requestBody);
}
```

## <a name="capture-insights-token"></a>Capturar token de percepções
Adicione o código a seguir ao objeto `searchItemsRenderer` na linha 151. Esse código adiciona um link **encontrar semelhantes** que chama a função `bingVisualSearch` quando clicado. A função recebe o imageInsightsToken como um argumento.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>Exibir imagens semelhantes
Adicione o código HTML a seguir à linha 601. Esse código de marcação adiciona um elemento usado para exibir os resultados da chamada de API de Pesquisa Visual do Bing.

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

Com todos os elementos de código JavaScript e HTML em vigor, os resultados da pesquisa são exibidos com um link **encontrar semelhantes**. Clique no link para preencher a seção **Semelhantes** com imagens semelhantes à que você escolheu. Você pode precisar expandir a seção **Semelhante** para mostrar as imagens.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Origem do aplicativo Web de página única Pesquisa Visual](tutorial-bing-visual-search-single-page-app-source.md)
> [Referência da API da Pesquisa Visual do Bing](https://aka.ms/bingvisualsearchreferencedoc)