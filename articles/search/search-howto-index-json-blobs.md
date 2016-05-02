<properties
pageTitle="Indexando blobs JSON com o indexador de blobs da Pesquisa do Azure"
description="Saiba como indexar blobs JSON com a Pesquisa do Azure"
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="04/20/2016"
ms.author="eugenesh" />

# Indexando blobs JSON com o indexador de blobs da Pesquisa do Azure 

Por padrão, o [indexador de blobs da Pesquisa do Azure](search-howto-indexing-azure-blob-storage.md) analisa blobs JSON como um único bloco de texto. Muitas vezes, você deseja preservar a estrutura dos seus documentos JSON. Por exemplo, considerando o documento JSON

	{ 
		"article" : {
	 		"text" : "A hopefully useful article explaining how to parse JSON blobs",
	        "datePublished" : "2016-04-13" 
			"tags" : [ "search", "storage", "howto" ]    
	    }
	}

talvez você queira analisá-lo para os campos "text", "datePublished" e "tags" em seu índice de pesquisa.

Este artigo mostra como configurar o indexador de blobs da Pesquisa do Azure para análise de JSON. Feliz indexação!

> [AZURE.IMPORTANT] Esta funcionalidade está em preview. Ela está disponível somente na API REST que usa a versão **2015-02-28-Preview**. Lembre-se de que as APIs de visualização servem para teste e avaliação, e não devem ser usadas em ambientes de produção.

## Configuração da indexação de JSON

Para indexar blobs JSON, use o indexador de blobs no modo de "Análise de JSON". Habilite a configuração `useJsonParser` na propriedade `parameters` da definição do indexador:

	{
	  "name" : "my-json-indexer",
	  ... other indexer properties
	  "parameters" : { "configuration" : { "useJsonParser" : true } }
	}

Se necessário, use **mapeamentos de campo** para selecionar as propriedades do documento JSON de origem usado para preencher o índice de pesquisa de destino. Isso é descrito em detalhes abaixo.

> [AZURE.IMPORTANT] Quando você usa o modo de análise de JSON, a Pesquisa do Azure pressupõe que todos os blobs em sua fonte de dados serão JSON. Se você precisar dar suporte a uma combinação de blobs JSON e não JSON na mesma fonte de dados, informe-nos em [nosso site UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## Usando mapeamentos de campo para criar documentos de pesquisa 

Atualmente, a Pesquisa do Azure não pode indexar documentos JSON arbitrários diretamente porque dá suporte somente a tipos de dados primitivos, matrizes de cadeia de caracteres e pontos GeoJSON. No entanto, você pode usar **mapeamentos de campo** para separar partes do documento JSON e "elevá-los" para campos de nível superior do documento de pesquisa. Para saber mais sobre os aspectos básicos dos mapeamentos de campo, confira [Personalização do Indexador da Pesquisa do Azure](search-indexers-customization.md).

Voltando ao nosso documento JSON de exemplo:

    { 
		"article" : {
	 		"text" : "A hopefully useful article explaining how to parse JSON blobs",
	        "datePublished" : "2016-04-13" 
			"tags" : [ "search", "storage", "howto" ]    
	    }
	}

Digamos que você tenha um índice de pesquisa com os seguintes campos: `text` do tipo Edm.String, `date` do tipo Edm.DateTimeOffset e `tags` do tipo Collection(Edm.String). Para mapear seu JSON para a forma desejada, use os seguintes mapeamentos de campo:

	"fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
  	]

Os nomes dos campos de origem nos mapeamentos são especificados usando a notação [JSON Pointer](http://tools.ietf.org/html/rfc6901). Comece com uma barra invertida para referir-se à raiz do documento JSON e chegue à propriedade desejada (em nível arbitrário de aninhamento) usando um caminho separado por barra invertida.

Você também pode se referir a elementos individuais da matriz usando um índice com base em zero. Por exemplo, para selecionar o primeiro elemento da matriz "tags" do exemplo anterior, use um mapeamento de campo como este:

	{ "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [AZURE.NOTE] Se o nome de um campo de origem em um caminho de mapeamento de campo se referir a uma propriedade que não existe em JSON, esse mapeamento será ignorado sem erro. Isso é feito para que possamos dar suporte a documentos com um esquema diferente (o que é um caso de uso comum). Como não há nenhuma validação, você precisa tomar cuidado para evitar erros de digitação na especificação do mapeamento do campo.

Se seus documentos JSON contiverem somente propriedades simples de nível superior, talvez você não precise de mapeamentos de campo. Por exemplo, se o seu JSON tiver esta aparência, as propriedades "text", "datePublished" e "tags" de nível superior mapearão diretamente para os campos correspondentes no índice de pesquisa:
 
	{ 
	   "text" : "A hopefully useful article explaining how to parse JSON blobs",
	   "datePublished" : "2016-04-13" 
       "tags" : [ "search", "storage", "howto" ]    
 	}

> [AZURE.NOTE] Atualmente, a Pesquisa do Azure dá suporte apenas à análise de um blob JSON para um documento de pesquisa. Se seus blobs contiverem matrizes JSON que você gostaria de analisar para vários documentos de pesquisa, vote [nesta sugestão no UserVoice](https://feedback.azure.com/forums/263029-azure-search/suggestions/13431384-parse-blob-containing-a-json-array-into-multiple-d) para nos ajudar a priorizar esse trabalho.

## Exemplos de solicitação

Reunindo tudo isso, estes são os exemplos das cargas completas.

Fonte de dados:

	POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	    "name" : "my-blob-datasource",
	    "type" : "azureblob",
	    "credentials" : { "connectionString" : "<my storage connection string>" },
	    "container" : { "name" : "my-container", "query" : "optional, my-folder" }
	}   

Indexador:

	POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "name" : "my-json-indexer",
	  "dataSourceName" : "my-blob-datasource",
	  "targetIndexName" : "my-target-index",
	  "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "useJsonParser" : true } }, 
      "fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
  	  ]
	}

## Ajude-nos a aprimorar a Pesquisa do Azure

Se você tiver solicitações de recursos ou ideias para o aperfeiçoamentos, entre em contato conosco pelo [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

<!---HONumber=AcomDC_0420_2016-->