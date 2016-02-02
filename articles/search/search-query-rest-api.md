<properties
	pageTitle="Criar consultas na Pesquisa do Azure usando chamadas REST | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
	description="Crie uma consulta de pesquisa na pesquisa do Azure e use os parâmetros de pesquisa para filtrar, classificar e criar uma faceta do resultado da pesquisa usando a biblioteca ou o SDK do .NET."
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
	ms.date="11/17/2015"
	ms.author="heidist"/>

# Criar consultas na Pesquisa do Azure usando chamadas REST
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Search Explorer](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

Este artigo mostra como construir uma consulta em relação a um índice usando a [API REST da Pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). Parte do conteúdo abaixo é de [Pesquisar documentos (API REST da Pesquisa do Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx). Consulte o artigo pai para obter mais contexto.

Os pré-requisitos da importação incluem ter um índice existente já em vigor, carregado com documentos que forneçam dados pesquisáveis.

Para pesquisar o índice usando a API REST, você emitirá uma solicitação HTTP GET. Os parâmetros de consulta serão definidos na URL da solicitação HTTP.

**Solicitação e Cabeçalhos da Solicitação**:

Na URL, será necessário fornecer o nome do serviço, o nome do índice e a versão de API apropriada. A cadeia de caracteres de consulta ao final da URL será onde você fornecerá os parâmetros da consulta. Um dos parâmetros na cadeia de caracteres de consulta deve ser a versão de API apropriada (a versão de API atual é "2015-02-28" no momento da publicação deste documento).

Nos cabeçalhos da solicitação, você precisará definir o tipo de conteúdo e fornecer a Chave de Administração Primária ou Secundária do serviço.

	GET https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

a Pesquisa do Azure oferece várias opções para a criação de consultas extremamente poderosas. Para saber mais sobre todos os diferentes parâmetros de uma cadeia de caracteres de consulta, visite [esta página](https://msdn.microsoft.com/library/azure/dn798927.aspx).

**Exemplos**:

A seguir, alguns exemplos com várias cadeias de caracteres de consulta. Esses exemplos usam um índice fictício chamado "hotéis":

Pesquise o índice inteiro em busca do termo "qualidade":

	GET https://[service name].search.windows.net/indexes/hotels/docs?search=quality&$orderby=lastRenovationDate desc&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

Pesquise o índice inteiro:

	GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

Pesquise o índice inteiro e ordene por um campo específico (lastRenovationDate):

	GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

Uma solicitação de consulta bem-sucedida resultará em um código de Status "200 OK" e os resultados da pesquisa serão encontrados no formato JSON no corpo da resposta. Para saber mias, visite a seção "Resposta" [desta página](https://msdn.microsoft.com/library/azure/dn798927.aspx).

<!---HONumber=AcomDC_0128_2016-->