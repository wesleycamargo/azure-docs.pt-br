---
title: Explorar APIs REST em Fiddler ou Postman (REST do Azure Search) | Microsoft Docs
description: "Como usar o Fiddler ou Postman para emitir solicitações HTTP e chamadas da API REST para o Azure Search."
services: search
documentationcenter: 
author: HeidiSteen
manager: cgronlun
editor: 
ms.assetid: 
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 01/04/2018
ms.author: heidist
ms.openlocfilehash: a9a6bc035ba9f02057e3086a9682b54282da19f3
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/13/2018
---
# <a name="explore-azure-search-rest-apis-using-fiddler-or-postman"></a>Explorar as APIs REST do Azure Search usando o Fiddler ou Postman

Uma das maneiras mais fáceis de explorar a [API REST do Azure Search](https://docs.microsoft.com/rest/api/searchservice) é usar Fiddler ou Postman para formular solicitações HTTP e inspecionar as respostas. Com as ferramentas certas e essas instruções, você pode enviar pedidos e exibir respostas antes de escrever qualquer código.

> [!div class="checklist"]
> * Baixe uma ferramenta de teste de API da Web
> * Obtenha a chave de API e o ponto de extremidade para o serviço de pesquisa
> * Configure os cabeçalhos de solicitações
> * Crie um índice
> * Carregue um índice
> * Pesquisar um índice

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar e, em seguida, [inscreva-se no Azure Search](search-create-service-portal.md).

## <a name="download-and-install-tools"></a>Baixar e instalar ferramentas

As ferramentas a seguir são amplamente usadas no desenvolvimento para a Web, mas se você estiver familiarizado com outra ferramenta, as instruções neste artigo ainda devem ser aplicáveis.

+ [Postman (suplemento do Google Chrome)](https://www.getpostman.com/)
+ [Telerik Fiddler](http://www.telerik.com/fiddler)

## <a name="get-the-api-key-and-endpoint"></a>Obter a chave de API e o ponto de extremidade

As chamadas REST exigem a URL do serviço e uma chave de acesso em cada solicitação. Um serviço de pesquisa é criado com ambos, portanto, se você adicionou o Azure Search à sua assinatura, siga estas etapas para obter as informações necessárias:

1. No portal do Azure, abra a página de serviço de pesquisa no painel ou [localize o seu serviço](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na lista de serviços.
2. Obtenha o ponto de extremidade em **Visão geral** > **Informações gerais** > **Url**. Um ponto de extremidade de exemplo pode parecer com `https://my-service-name.search.windows.net`.
3. Obtenha a chave de API em **Configurações** > **Chaves**. Existem duas chaves de administração para redundância no caso de você desejar sobrepor chaves. As chaves de administração concedem as permissões de gravação no seu serviço, necessárias para criar e carregar índices. É possível usar a chave primária ou secundária para operações de gravação.

## <a name="configure-request-headers"></a>Configure os cabeçalhos de solicitações

Cada ferramenta mantém as informações do cabeçalho de solicitação para a sessão, o que significa que você só precisa inserir o ponto de extremidade da URL, a versão da API, a chave da API e o tipo de conteúdo uma vez.

A URL completa deve ser semelhante ao exemplo a seguir, só que a sua deve ter uma substituição válida para o nome do espaço reservado **`my-app`**: `https://my-app.search.windows.net/indexes/hotels?api-version=2016-09-01`

A composição da URL do serviço inclui os seguintes elementos:

+ Prefixo HTTPS.
+ URL do serviço, obtida a partir do portal.
+ Recurso, uma operação que cria um objeto em seu serviço. Nesta etapa, é um índice chamado hotéis.
+ versão da API, uma cadeia de caracteres em minúsculas necessária especificada como "?versão-API = 2016-09-01" para a versão atual. [As versões de API](search-api-versions.md) são atualizadas regularmente. Incluir a versão de API em cada solicitação lhe dá controle total sobre qual é usada.  

A composição de cabeçalho de solicitação inclui dois elementos, o tipo de conteúdo e a chave de API descrita na seção anterior:

         content-type: application/json
         api-key: <placeholder>

### <a name="fiddler"></a>Fiddler

Formule uma solicitação semelhante à captura de tela a seguir. Escolha **PUT** como o verbo. O Fiddler adiciona `User-Agent=Fiddler`. Você pode colar os dois cabeçalhos de solicitação adicionais em novas linhas abaixo dele. Inclua o tipo de conteúdo e a chave de API para seu serviço, usando a chave de acesso de administrador para seu serviço.

![Cabeçalho da solicitação do Fiddler][1]

> [!Tip]
> Você pode desativar o tráfego da Web para ocultar atividade HTTP irrelevante e não relacionada às tarefas que você está executando. No Fiddler, vá até o menu **Arquivo** e desative **Capturar tráfego**. 

### <a name="postman"></a>postman

Formule uma solicitação semelhante à captura de tela a seguir. Escolha **PUT** como o verbo. 

![Cabeçalho da solicitação do Postman][6]

## <a name="create-the-index"></a>Criar o índice

O corpo da solicitação contém a definição do índice. Adicionar o corpo da solicitação conclui a solicitação que produz o índice.

Além do nome do índice, o componente mais importante na solicitação é a coleção de campos. A coleção de campos define o esquema do índice. Em cada campo, especifique seu tipo. Os campos de cadeia de caracteres são usados na pesquisa de texto completo, portanto, talvez você queira converter dados numéricos como cadeias de caracteres, se você precisa que esse conteúdo seja pesquisável.

Os atributos no campo determinam a ação repetida. As APIs REST permitem muitas ações por padrão. Por exemplo, todas as cadeias de caracteres são pesquisáveis, recuperáveis, podem ser filtradas e podem possuir faceta por padrão. Geralmente, você só precisa definir atributos quando você precisa desabilitar um comportamento. Para obter mais informações sobre atributos, confira [Criar índice (REST)](https://docs.microsoft.com/rest/api/searchservice/create-index).

          {
         "name": "hotels",  
         "fields": [
           {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
           {"name": "baseRate", "type": "Edm.Double"},
           {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
           {"name": "hotelName", "type": "Edm.String"},
           {"name": "category", "type": "Edm.String"},
           {"name": "tags", "type": "Collection(Edm.String)"},
           {"name": "parkingIncluded", "type": "Edm.Boolean"},
           {"name": "smokingAllowed", "type": "Edm.Boolean"},
           {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
           {"name": "rating", "type": "Edm.Int32"},
           {"name": "location", "type": "Edm.GeographyPoint"}
          ]
         }


Quando você enviar esta solicitação, você deve obter uma resposta HTTP 201, indicando que o índice foi criado com sucesso. Você pode verificar essa ação no portal, mas observe que a página do portal possui intervalos de atualização, portanto, pode levar um minuto ou dois para que ela seja atualizada.

Se obtiver o HTTP 504, veja se a URL especifica HTTPS. Caso veja HTTP 400 ou 404, confira o corpo da solicitação para verificar se não houve erros ao copiar e colar. Um HTTP 403 normalmente indica um problema com a chave de API (uma chave inválida ou um problema de sintaxe com o modo que a chave de API está especificada).

### <a name="fiddler"></a>Fiddler

Copie a definição do índice para o corpo da solicitação, de forma semelhante à captura de tela a seguir e, em seguida, clique em **Executar** no canto superior direito para enviar a solicitação concluída.

![Corpo da solicitação do Fiddler][7]

### <a name="postman"></a>postman

Copie a definição do índice para o corpo da solicitação, de forma semelhante à captura de tela a seguir e, em seguida, clique em **Enviar** no canto superior direito para enviar a solicitação concluída.

![Corpo da solicitação do Postman][8]

## <a name="load-documents"></a>Carregue os documentos

Criar o índice e popular o índice são etapas separadas. No Azure Search, o índice contém todos os dados pesquisáveis, os quais você pode fornecer como documentos JSON. Para revisar a API para esta operação, confira [Adicionar, atualizar ou excluir documentos (REST)](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

+ Altere o verbo para **POST** para esta etapa.
+ Altere o ponto de extremidade para incluir `/docs/index`. A URL completa deve ser semelhante à `https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2016-09-01`
+ Mantenha os cabeçalhos de solicitação como eles estão. 

O Corpo da solicitação contém quatro documentos a serem adicionados ao índice de hotéis.

         {
         "value": [
         {
             "@search.action": "upload",
             "hotelId": "1",
             "baseRate": 199.0,
             "description": "Best hotel in town",
             "hotelName": "Fancy Stay",
             "category": "Luxury",
             "tags": ["pool", "view", "wifi", "concierge"],
             "parkingIncluded": false,
             "smokingAllowed": false,
             "lastRenovationDate": "2010-06-27T00:00:00Z",
             "rating": 5,
             "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
           },
           {
             "@search.action": "upload",
             "hotelId": "2",
             "baseRate": 79.99,
             "description": "Cheapest hotel in town",
             "hotelName": "Roach Motel",
             "category": "Budget",
             "tags": ["motel", "budget"],
             "parkingIncluded": true,
             "smokingAllowed": true,
             "lastRenovationDate": "1982-04-28T00:00:00Z",
             "rating": 1,
             "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
           },
           {
             "@search.action": "upload",
             "hotelId": "3",
             "baseRate": 279.99,
             "description": "Surprisingly expensive",
             "hotelName": "Dew Drop Inn",
             "category": "Bed and Breakfast",
             "tags": ["charming", "quaint"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
           },
           {
             "@search.action": "upload",
             "hotelId": "4",
             "baseRate": 220.00,
             "description": "This could be the one",
             "hotelName": "A Hotel for Everyone",
             "category": "Basic hotel",
             "tags": ["pool", "wifi"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
           }
          ]
         }

Em alguns segundos, você verá uma resposta HTTP 200 na lista de sessões. Isso indica que os documentos foram criados com êxito. 

Se você obtiver um 207, houve falha no carregamento de pelo menos um documento. Se você obtiver um 404, há um erro de sintaxe no cabeçalho ou no corpo da solicitação: verifique se você alterou o ponto de extremidade para incluir `/docs/index`.

> [!Tip]
> Para fontes de dados selecionadas, você pode escolher a abordagem alternativa do *indexador* que simplifica e reduz a quantidade de código necessária para indexação. Para obter mais informações, confira [Operações do indexador](https://docs.microsoft.com/rest/api/searchservice/indexer-operations).

### <a name="fiddler"></a>Fiddler

Altere o verbo para **POST**. Altere a URL para incluir `/docs/index`. Copie os documentos para o corpo da solicitação, semelhante à captura de tela a seguir e, em seguida, execute a solicitação.

![Conteúdo da solicitação do Fiddler][9]

### <a name="postman"></a>postman

Altere o verbo para **POST**. Altere a URL para incluir `/docs/index`. Copie os documentos para o corpo da solicitação, semelhante à captura de tela a seguir e, em seguida, execute a solicitação.

![Conteúdo da solicitação do Postman][10]

## <a name="query-the-index"></a>Consulte o índice
Agora que o índice e os documentos foram carregados, você pode consultá-los. Para obter mais informações sobre esta API, confira [Documentos do Azure Search (REST)](https://docs.microsoft.com/rest/api/searchservice/search-documents)  

+ Altere o verbo para **GET** para esta etapa.
+ Altere o ponto de extremidade para incluir parâmetros de consulta, incluindo cadeias de caracteres de pesquisa. Uma URL de consulta pode ser semelhante à `https://my-app.search.windows.net/indexes/hotels/docs?search=motel&$count=true&api-version=2016-09-01`
+ Mantenha os cabeçalhos de solicitação como eles estão

Essa consulta pesquisa o termo "hotel" e retorna uma contagem dos documentos nos resultados da pesquisa. A solicitação e resposta devem ser semelhantes à captura de tela a seguir para Postman depois de clicar em **Enviar**. O código de status deve ser 200.

 ![Resposta da consulta do Postman][11]

### <a name="tips-for-running-our-sample-queries-in-fiddler"></a>Dicas para executar os exemplos de consulta em Fiddler

O exemplo de consulta a seguir é do artigo [Operação Pesquisar índice (API do Azure Search)](http://msdn.microsoft.com/library/dn798927.aspx). Muitos dos exemplos de consulta deste artigo incluem espaços, que não são permitidos no Fiddler. Substitua cada espaço por um caractere + antes de colar na cadeia de consulta e tentar realizar a consulta no Fiddler.

**Os espaços à frente são substituídos (em lastRenovationDate desc):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2016-09-01

**Os espaços no final são substituídos por + (em lastRenovationDate+desc):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2016-09-01

## <a name="query-index-properties"></a>Propriedades do índice de consulta
Você também pode consultar as informações do sistema para obter contagens de documentos e consumo de armazenamento: `https://my-app.search.windows.net/indexes/hotels/stats?api-version=2016-09-01`

No Postman, sua solicitação deve ser semelhante à seguinte, e a resposta inclui uma contagem de documentos e o espaço usado em bytes.

 ![Consulta do sistema Postman][12]

Observe que a sintaxe de versão da API é diferente. Para esta solicitação, use `?` para anexar a versão da API. O ? separa o caminho da URL da cadeia de consulta, enquanto & separa cada par 'nome=valor' na cadeia de consulta. Para essa consulta, a versão da API é o primeiro e único item na cadeia de consulta.

Para obter mais informações sobre esta API, confira [Obter estatísticas do índice (REST)](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics).


### <a name="tips-for-viewing-index-statistic-in-fiddler"></a>Dicas para exibir estatísticas de índice no Fiddler

No Fiddler, clique na guia **Inspetores**, clique na guia **Cabeçalhos** e, em seguida, selecione o formato JSON. Você deverá ver a contagem de documentos e o tamanho do armazenamento (em KB).

## <a name="next-steps"></a>Próximas etapas

Os clientes REST são imprescindíveis para uma exploração improvisada, mas agora que você sabe como funcionam as APIs REST, você pode continuar com o código. Para as próximas etapas, confira os links a seguir:

+ [Criar um índice (REST)](search-create-index-rest-api.md)
+ [Importar dados (REST)](search-import-data-rest-api.md)
+ [Pesquisar um índice (REST)](search-query-rest-api.md)

<!--Image References-->
[1]: ./media/search-fiddler/fiddler-url.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png
[6]: ./media/search-fiddler/postman-url.png
[7]: ./media/search-fiddler/fiddler-request.png
[8]: ./media/search-fiddler/postman-request.png
[9]: ./media/search-fiddler/fiddler-docs.png
[10]: ./media/search-fiddler/postman-docs.png
[11]: ./media/search-fiddler/postman-query.png
[12]: ./media/search-fiddler/postman-system-query.png