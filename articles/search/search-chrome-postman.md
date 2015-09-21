<properties
	pageTitle="Usar o Chrome Postman com a Pesquisa do Azure | Microsoft Azure"
	description="Usar o Chrome Postman com a Pesquisa do Azure. Instalar e configurar o Postman. Criar um índice de Pesquisa do Azure. Publicar documentos e consulta o índice com o Postman."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="09/08/2015"
	ms.author="heidist"/>

# Como usar o Chrome Postman com a Pesquisa do Azure #

O [Postman](https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm "Chrome Postman") é uma ferramenta fornecida como parte do Google Chrome e que permite aos desenvolvedores trabalhar eficientemente com a API baseada em REST de serviços, como a Pesquisa do Azure. Você pode usar o Postman para criar e consultar rapidamente seus índices de pesquisa enviando chamadas de API por meio do Postman, sem precisar escrever nenhum código. Essa abordagem é uma maneira eficiente para aprender sobre a API e experimentar os novos recursos.

![][1]

## Requisitos ##

Você deve ter um serviço de Pesquisa do Azure. Como com qualquer aplicativo personalizado que usa a pesquisa do Azure, você precisará da URL para o serviço, além de um administrador `api-key` para que você possa criar o índice. Para obter instruções sobre como obter os valores para o serviço de Pesquisa, consulte [Criar um serviço no portal](search-create-service-portal.md).

## Instalar o Postman ##
Para baixar o Postman, visite o [Repositório do Google Chrome](https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm). O link nesta página permite que você baixe e instale o cliente REST para Postman. Uma vez instalado, você pode iniciar o Postman no Inicializador do Aplicativo Chrome.

![][2]

## Configurar o Postman para a consulta de Pesquisa do Azure ##
Para configurar o Postman, siga as etapas a seguir:

1. Insira a URL do serviço de Pesquisa do Azure onde se vê a mensagem "Insira a URL de solicitação aqui".  
2. Acrescentar à URL: `?api-version=2015-02-28`. Você também pode especificar uma versão diferente da API. Consulte [Controle de versão de serviço de Pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes.
3. Certifique-se de que `GET` seja escolhido.
4. Clique no botão **Cabeçalhos**.
5. Insira valores para:
	- `api-key`: [Chave Admin]
	- `Content-Type`: `application/json; charset=utf-8`
6. Clique em **Enviar** para emitir a chamada REST para Pesquisa do Azure e visualize a resposta JSON.

![][3]

## Criar um índice de Pesquisa do Azure com o Postman ##

A seguir falaremos mais do que concluímos na última etapa, emitindo uma chamada REST para criar um novo índice de Pesquisa do Azure. Ao contrário da chamada anterior, a criação de índice requer um HTTP PUT, e um documento JSON com a definição do esquema de índice. Para este exemplo, vamos criar um índice que armazenará uma lista de trilhas de caminhada. Para fazer isso:

1. Altere a URL para: `https://[SEARCH SERVICE].search.windows.net/indexes/trails?api-version=2015-02-28`, usando seu nome de Serviço de Pesquisa.
2. Altere o tipo de solicitação de `GET` para `PUT`.
3. No conteúdo do corpo BRUTO, insira o JSON a seguir:

	    {
	    "name": "trails",
	    "fields": [
	    {"name": "id", "type": "Edm.String", "key": true, "searchable": false},
	    {"name": "name", "type": "Edm.String"},
	    {"name": "county", "type": "Edm.String"},
	    {"name": "elevation", "type": "Edm.Int32"},
	    {"name": "location", "type": "Edm.GeographyPoint"} ]
	    }

4. Clique em **Enviar**.

![][4]

## Publicar documentos para um índice de Pesquisa do Azure com o Postman ##
Agora que o índice é criado, podemos carregar documentos nele. Para fazer isso, publicaremos um grupo de documentos em um lote, usando dados para cinco trilhas do conjunto de dados do USGS (United States Geological Survey - pesquisa geológica dos Estados Unidos):

1. Altere a URL para: `https://[SEARCH SERVICE].windows.net/indexes/trails/docs/index?api-version=2015-02-28`, usando seu nome de Serviço de Pesquisa. Observe que a URL inclui um caminho para o índice que você acabou de criar.
2. Altere o tipo HTTP para `POST`.
3. No conteúdo do corpo BRUTO, insira o JSON a seguir:

	    {
	      "value": [
		    {"@search.action": "upload", "id": "233358", "name": "Pacific Crest National Scenic Trail", "county": "San Diego", "elevation":1294, "location": { "type": "Point", "coordinates": [-120.802102,49.00021] }},
		    {"@search.action": "upload", "id": "801970", "name": "Lewis and Clark National Historic Trail", "county": "Richland", "elevation":584, "location": { "type": "Point", "coordinates": [-104.8546903,48.1264084] }},
		    {"@search.action": "upload", "id": "1144102", "name": "Intake Trail", "county": "Umatilla", "elevation":1076, "location": { "type": "Point", "coordinates": [-118.0468873,45.9981939] }},
		    {"@search.action": "upload", "id": "1509897", "name": "Burke Gilman Trail", "county": "King", "elevation":10, "location": { "type": "Point", "coordinates": [-122.2754036,47.7059315] }},
		    {"@search.action": "upload", "id": "1517508", "name": "Cavanaugh-Oso Truck Trail", "county": "Skagit", "elevation":339, "location": { "type": "Point", "coordinates": [-121.9470829,48.2981608] }}
	      ]
	    }

4. Clique em **Enviar**.

![][5]

## Consultar o Índice com o Postman ##
A etapa final é consultar o índice e emitir uma solicitação de pesquisa de texto completo simples para a palavra *trilha*.

1. Digite o seguinte na URL: `https://[SEARCH SERVICE].search.windows.net/indexes/trails/docs?api-version=2015-02-28&search=trail` usando seu nome de Serviço de Pesquisa. Observe que a URL inclui o parâmetro de consulta `search` e um termo de pesquisa *trilha*.
2. Altere o tipo de solicitação HTTP para `GET`.
3. Clique em **Enviar**.

Na resposta, você deve ver os resultados da pesquisa JSON que retornam da Pesquisa do Azure.

![][6]

## Próximas etapas ##
Agora que já descrevemos os fundamentos básicos do uso da Pesquisa do Azure com o Postman, existem algumas coisas que podem ajudá-lo com as próximas etapas:

1. O Postman dá suporte a `Collections`, que são uma maneira conveniente de salvar solicitações comumente emitidas. Você pode compartilhar coleções com outras pessoas, a serem emitidas em sua própria cópia do Postman.
2. Na documentação da Pesquisa do Azure, certifique-se de anotar o tipo de solicitação HTTP (`GET`, `PUT`, e assim por diante) associada a cada chamada e alterar conforme apropriado no Postman.

Documentação da API REST pode ser encontrada no [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx).

Você também pode visitar a [Lista de tutoriais e vídeos](search-video-demo-tutorial-list.md) para obter mais exemplos.

<!-- Image References -->
[1]: ./media/search-chrome-postman/full_postman_client.png
[2]: ./media/search-chrome-postman/postman.png
[3]: ./media/search-chrome-postman/configure.png
[4]: ./media/search-chrome-postman/create_index.png
[5]: ./media/search-chrome-postman/upload_documents.png
[6]: ./media/search-chrome-postman/query.png

<!---HONumber=Sept15_HO2-->