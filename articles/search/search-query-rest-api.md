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
	ms.date="11/10/2015"
	ms.author="heidist"/>

#Criar consultas na Pesquisa do Azure usando chamadas REST 
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Fiddler](search-fiddler.md)
- [Postman](search-chrome-postman.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

Este artigo mostra como criar um índice usando a [API REST da Pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). Parte do conteúdo abaixo é de [Pesquisar documentos (API REST da Pesquisa do Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx). Consulte o artigo pai para obter mais contexto.

Os pré-requisitos da importação incluem ter um índice existente já em vigor, carregado com documentos que forneçam dados pesquisáveis.

Quando você estiver usando a API REST, as consultas serão baseadas em uma solicitação HTTP GET. Os trechos de código são do [exemplo de perfis de pontuação](search-get-started-scoring-profiles.md).

        static JObject ExecuteRequest(string action, string query = "")
        {
            // original:  string url = serviceUrl + indexName + "/" + action + "?" + ApiVersion; 
            string url = serviceUrl + indexName + "/docs?" + action ;
            if (!String.IsNullOrEmpty(query))
            {
                url += query + "&" + ApiVersion;
            }

            string response = ExecuteGetRequest(url);
            return JObject.Parse(response);

        }

        static string ExecuteGetRequest(string requestUri)
        {
            //This will execute a get request and return the response
            using (HttpClient client = new HttpClient())
            {
                client.DefaultRequestHeaders.Add("api-key", primaryKey);
                HttpResponseMessage response = client.GetAsync(requestUri).Result;        // Searches are done over port 80 using Get
                return response.Content.ReadAsStringAsync().Result;
            }

        }

<!---HONumber=Nov15_HO3-->