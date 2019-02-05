---
title: Tutorial para adição de preenchimento automático à caixa de pesquisa – Azure Search
description: Exemplos de como melhorar a experiência do usuário final de seus aplicativos centrados em dados usando o preenchimento automático do Azure Search e as APIs de sugestões.
manager: pablocas
author: mrcarter8
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: mcarter
ms.custom: seodec2018
ms.openlocfilehash: a0b4301177fa1307244a784031ec890b9a20f01a
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55099101"
---
# <a name="tutorial-add-auto-complete-to-your-search-box-using-azure-search"></a>Tutorial: Adicionar preenchimento automático à caixa de pesquisa usando o Azure Search

Neste tutorial, você aprenderá a usar [sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions), [preenchimento automático](https://docs.microsoft.com/rest/api/searchservice/autocomplete) e [facetas](search-faceted-navigation.md) na [API REST do Azure Search](https://docs.microsoft.com/rest/api/searchservice/) e no [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions?view=azure-dotnet) para criar uma caixa de pesquisa avançada. A opção *Sugestões* fornece recomendações de resultados reais com base no que o usuário digitou até agora. A opção *Preenchimento automático*, [um novo recurso de versão prévia](search-api-preview.md) no Azure Search, oferece termos do índice para concluir o que o usuário está digitando no momento. Vamos comparar as várias técnicas para melhorar a produtividade do usuário e agilizar e facilitar a localização do que estão procurando, levando a riqueza da pesquisa diretamente ao usuário enquanto ele digita.

Este tutorial orienta você por um aplicativo baseado em ASP.NET MVC que usa C# para chamar as [bibliotecas de cliente .NET do Azure Search](https://aka.ms/search-sdk) e JavaScript para chamar a API REST do Azure Search diretamente. O aplicativo para este tutorial usa um índice populado pelos dados de exemplo [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). Você pode usar o índice já configurado na demonstração NYC Jobs ou preencher seu próprio índice usando um carregador de dados na solução de exemplo NYCJobs. O exemplo usa as bibliotecas JavaScript [jQuery UI](https://jqueryui.com/autocomplete/) e [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) para criar uma caixa de pesquisa que dá suporte a preenchimento automático. Usando esses componentes, juntamente com o Azure Search, você verá vários exemplos de como dar suporte a preenchimento automático com antecipação de digitação em sua caixa de pesquisa.

Você executará as seguintes etapas:

> [!div class="checklist"]
> * Baixa e configura a solução
> * Adiciona informações de serviço de pesquisa às configurações do aplicativo
> * Implementar uma caixa de entrada de pesquisa
> * Adicionar suporte para obter uma lista de preenchimento automático que usa uma fonte remota como base 
> * Recuperar sugestões e preenchimento automático usando o SDK do .NET e a API REST
> * Suporte a cache no lado do cliente para melhorar o desempenho 

## <a name="prerequisites"></a>pré-requisitos

* Visual Studio 2017. Você pode usar a versão gratuita do [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). 

* Baixe o exemplo de [código-fonte](https://github.com/azure-samples/search-dotnet-getting-started) para o tutorial.

* (Opcional) Uma conta do Azure ativa e um serviço Azure Search. Se não tiver uma conta do Azure, você poderá assinar uma versão de [avaliação gratuita](https://azure.microsoft.com/free/). Para obter ajuda com o serviço de provisionamento, consulte [Criar um serviço de pesquisa](search-create-service-portal.md). A conta e o serviço são opcionais, pois este tutorial pode ser concluído usando um índice NYCJobs hospedado já existente para uma demonstração diferente.

* (Opcional) Baixe o código de exemplo de [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) para importar os dados do NYCJobs para um índice em seu próprio serviço Azure Search.

> [!Note]
> Se você estiver usando o serviço Azure Search gratuito, estará limitado a três índices. O carregador de dados NYCJobs cria dois índices. Não se esqueça de deixar um espaço em seu serviço para aceitar os novos índices.

### <a name="set-up-azure-search-optional"></a>Configurar o Azure Search (opcional)

Siga as etapas nesta seção se quiser importar os dados do aplicativo de exemplo NYCJobs para seu próprio índice. Esta etapa é opcional.  Se você quiser usar o índice de exemplo fornecido, pule para a próxima seção, Como executar o exemplo.

1. Na pasta do DataLoader do código de exemplo NYCJobs, abra o arquivo de solução DataLoader.sln no Visual Studio.

1. Atualize as informações de conexão para seu serviço Azure Search.  Abra o App.config dentro do projeto DataLoader e altere o appSettings TargetSearchServiceName e TargetSearchServiceApiKey para refletir seu serviço Azure Search e a chave de API do serviço Azure Search.  Ele pode ser encontrado no portal do Azure.

1. Pressione F5 para iniciar o aplicativo.  Isso criará dois índices e importará os dados do exemplo NYCJob.

1. No código de exemplo do tutorial, abra o arquivo de solução AutocompleteTutorial.sln no Visual Studio.  Abra Web.config no projeto AutocompleteTutorial e altere os valores de SearchServiceName e SearchServiceApiKey para o mesmo visto acima.

### <a name="running-the-sample"></a>Executando o exemplo

Agora você está pronto para executar o aplicativo de exemplo do tutorial.  Abra o arquivo de solução AutocompleteTutorial.sln no Visual Studio para executar o tutorial.  A solução contém um projeto ASP.NET MVC.  Pressione F5 para executar o projeto e carregue a página em seu navegador preferido.  Na parte superior, você verá uma opção para selecionar C# ou JavaScript.  A opção C# chama o HomeController do navegador e usa o SDK do .Net do Azure Search para recuperar os resultados.  A opção JavaScript chama a API REST do Azure Search diretamente do navegador.  Essa opção normalmente tem desempenho claramente melhor, pois ela usa o controlador fora do fluxo.  Você pode escolher a opção que atender às suas necessidades e preferências de linguagem.  Há vários exemplos de preenchimento automático na página com algumas diretrizes para cada um.  Cada exemplo tem um texto de exemplo recomendado que você pode usar para experimentar.  Tente digitar algumas letras em cada caixa de pesquisa para ver o que acontece.

## <a name="how-this-works-in-code"></a>Como isso funciona no código

Agora que você já viu os exemplos no navegador, vamos examinar o primeiro exemplo em detalhes para analisar os componentes envolvidos e como eles funcionam.

### <a name="search-box"></a>Caixa de pesquisa

Para qualquer uma das opções de linguagem, a caixa de pesquisa é exatamente a mesma.  Abra o arquivo index.cshtml na pasta \Views\Home. A caixa de pesquisa em si é simples:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Essa é uma caixa de texto de entrada simples com uma classe para definir o estilo, uma ID para referência pelo JavaScript e o texto de espaço reservado.  A mágica está no JavaScript.

### <a name="javascript-code-c"></a>Código JavaScript (C#)

O exemplo de linguagem C# usa JavaScript no index.cshtml para aproveitar a biblioteca de preenchimento automático da interface do usuário do jQuery.  Essa biblioteca adiciona a experiência de preenchimento automático à caixa de pesquisa, fazendo chamadas assíncronas para o controlador MVC a fim de recuperar as recomendações.  Vamos examinar o código JavaScript do primeiro exemplo:

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Esse código é executado no navegador no carregamento da página para configurar o preenchimento automático para a caixa de entrada "example1a".  `minLength: 3` faz com que as recomendações só sejam exibidas quando há pelo menos três caracteres na caixa de pesquisa.  O valor de origem é importante:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

Essa linha informa à API de preenchimento automático de onde obter a lista de itens a serem mostrados na caixa de pesquisa.  Como esse é um projeto do MVC, ele chama a função Suggest em HomeController.cs.  Vamos examinar isso melhor na próxima seção.  Ele também passa alguns parâmetros para destaques de controle, correspondência difusa e termo.  A API JavaScript de preenchimento automático adiciona o parâmetro de termo.

#### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Estendendo o exemplo para dar suporte à correspondência difusa

A pesquisa difusa permite que você obtenha resultados com base em correspondências próximas, mesmo se o usuário errou a escrita de uma palavra na caixa de pesquisa.  Vamos experimentar isso alterando a linha de origem para habilitar a correspondência difusa.

Altere a linha abaixo da seguinte forma:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

para isto:

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

Inicie o aplicativo pressionando F5.

Tente digitar algo como "execativo" e observe como os resultados retornam para "Executivo", mesmo que não sejam uma correspondência exata para as letras digitadas.

### <a name="homecontrollercs-c"></a>HomeController.cs (C#)

Agora que examinamos o código JavaScript para o exemplo, vamos examinar o código do controlador em C# que, na verdade, recupera as recomendações usando o SDK do .NET do Azure Search.

1. Abra o arquivo HomeController.cs no diretório Controllers. 

1. A primeira coisa que você pode notar é um método na parte superior da classe chamada InitSearch.  Isso cria um cliente de índice HTTP autenticado para o serviço Azure Search.  Se você quiser saber mais sobre como isso funciona, acesse o tutorial a seguir: [Como usar o Azure Search de um aplicativo .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

1. Mude para a função Suggest.

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult resp = _indexClient.Documents.Suggest(term, "sg", sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = resp.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

A função Suggest utiliza dois parâmetros que determinam se os destaques de ocorrências são retornados ou se correspondência difusa é usada em conjunto com o termo de pesquisa de entrada.  O método cria um objeto SuggestParameters, que é passado para a API de sugestão. O resultado, em seguida, é convertido em JSON para que ele possa ser exibido no cliente.
(Opcional) Adicione um ponto de interrupção no início da função Suggest e explore o código.  Observe que a resposta retornada pelo SDK e como ela é convertida no resultado retornado pelo método.

Outros exemplos na página seguem o mesmo padrão para adicionar o destaque de ocorrências, a digitação antecipada para recomendações de preenchimento automático e facetas para dar suporte a cache dos resultados de preenchimento automático no lado do cliente.  Examine cada um deles para entender como eles funcionam e como aproveitá-los em sua experiência de pesquisa.

### <a name="javascript-language-example"></a>Exemplo de linguagem JavaScript

No exemplo de linguagem JavaScript, o código JavaScript na página IndexJavaScript.cshtml aproveita o preenchimento automático da interface do usuário do jQuery.  Essa é uma biblioteca que faz a maior parte do trabalho pesado para apresentar uma caixa de pesquisa interessante e facilita as chamadas assíncronas para o Azure Search a fim de recuperar as recomendações.  Vamos examinar o código JavaScript do primeiro exemplo:

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: function (request, response) {
        $.ajax({
            type: "POST",
            url: suggestUri,
            dataType: "json",
            headers: {
                "api-key": searchServiceApiKey,
                "Content-Type": "application/json"
            },
            data: JSON.stringify({
                top: 5,
                fuzzy: false,
                suggesterName: "sg",
                search: request.term
            }),
                success: function (data) {
                    if (data.value && data.value.length > 0) {
                        response(data.value.map(x => x["@@search.text"]));
                    }
                }
            });
        },
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Se você comparar isso com o exemplo acima que chama o controlador Home, verá várias semelhanças.  A configuração de preenchimento automático para `minLength` e `position` são exatamente iguais.  A alteração significativa aqui está na origem.  Em vez de chamar o método Suggest no controlador principal, uma solicitação REST é criada em uma função de JavaScript e executada usando o ajax.  A resposta, em seguida, é processada em "success" e usada como a origem.

## <a name="takeaways"></a>Observações

Este tutorial demonstra as etapas básicas para a criação de uma caixa de pesquisa que dá suporte a preenchimento automático e sugestões.  Você viu como poderia criar um aplicativo ASP.NET MVC e usar a API REST ou SDK do .NET do Azure Search para recuperar sugestões.

## <a name="next-steps"></a>Próximas etapas

Integrar sugestões e preenchimento automático em sua experiência de pesquisa.  Considerar como o uso do SDK do .NET ou a API REST diretamente pode ajudar a trazer o poder do Azure Search para seus usuários quando eles digitam para torná-los mais produtivos.

> [!div class="nextstepaction"]
> [API REST de preenchimento automático](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [API REST de sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Atributo de índice de facetas em uma API REST de criação de índice](https://docs.microsoft.com/rest/api/searchservice/create-index)

