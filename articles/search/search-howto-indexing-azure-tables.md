<properties
pageTitle="Indexação do Armazenamento de Tabelas do Azure com a Pesquisa do Azure"
description="Aprenda a indexar dados armazenados em Tabelas do Azure com a Pesquisa do Azure"
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
ms.date="04/12/2016"
ms.author="eugenesh" />

# Indexação do Armazenamento de Tabelas do Azure com a Pesquisa do Azure

Este artigo mostra como usar a Pesquisa do Azure para indexar dados armazenados no Armazenamento de Tabelas do Azure. O novo indexador de tabelas da Pesquisa do Azure torna esse processo rápido e direto.

> [AZURE.IMPORTANT] Atualmente, essa funcionalidade está no modo de visualização. Ela está disponível somente na API REST que usa a versão **2015-02-28-Preview**. Lembre-se de que as APIs de visualização servem para teste e avaliação, e não devem ser usadas em ambientes de produção.

## Configuração de indexação de tabela

Para instalar e configurar um indexador de tabelas do Azure, você pode usar a API REST da Pesquisa do Azure para criar e gerenciar **indexadores** e **fontes de dados**, conforme descrito em [Operações do indexador](https://msdn.microsoft.com/library/azure/dn946891.aspx). No futuro, o suporte para a indexação de tabelas será adicionado ao SDK .NET da Pesquisa do Azure e ao Portal do Azure.

Uma fonte de dados especifica quais dados indexar, as credenciais necessárias para acessar os dados e as políticas que permitem à Pesquisa do Azure identificar com eficiência as alterações nos dados (linhas novas, modificadas ou excluídas).

Um indexador lê dados de uma fonte de dados e carrega-os em um índice de pesquisa de destino.

Para configurar um indexador de tabela:

1. Criar uma fonte de dados do tipo `azuretable` que faz referência a uma tabela (e, opcionalmente, uma consulta) em uma conta de armazenamento do Azure
	- Passe a cadeia de conexão de sua conta de armazenamento como o parâmetro `credentials.connectionString`
	- Especifique o nome da tabela usando o parâmetro `container.name`
	- Opcionalmente, especifique uma consulta usando o parâmetro `container.query`. Sempre que possível, use um filtro em PartitionKey para obter o melhor desempenho. Qualquer outra consulta resultará em uma verificação completa da tavbela que pode resultar em baixo desempenho em tabelas grandes.
2. Crie o indexador conectando sua fonte de dados a um índice de destino existente (crie o índice caso ainda não tenha um)

O exemplo a seguir fornece uma ilustração:

	POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	    "name" : "table-datasource",
	    "type" : "azuretable",
	    "credentials" : { "connectionString" : "<my storage connection string>" },
	    "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
	}   

Em seguida, crie um indexador que faça referencia à fonte de dados e a um índice de destino. Por exemplo:

	POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "name" : "table-indexer",
	  "dataSourceName" : "table-datasource",
	  "targetIndexName" : "my-target-index",
	  "schedule" : { "interval" : "PT2H" }
	}

E isso é tudo. Boa indexação!

## Manipulando chaves de documento

Na Pesquisa do Azure, a chave do documento identifica exclusivamente um documento. Cada índice de pesquisa deve ter exatamente um campo de chave do tipo `Edm.String`. O campo de chave é necessário para cada documento adicionado ao índice (é, na verdade, o único campo obrigatório).

Como as linhas de tabela têm uma chave composta, a Pesquisa do Azure gera um campo sintético chamado `Key` que é uma concatenação dos valores de chave de linha e chave de partição. Por exemplo, se a PartitionKey de uma linha for `PK1` e a RowKey for `RK1`, em seguida, o valor do campo `Key` será `PK1RK1`.

> [AZURE.NOTE] O valor `Key` pode conter caracteres inválidos em chaves de documentos, como traços. Você pode lidar com caracteres inválidos habilitando a opção `base64EncodeKeys` nas propriedades do indexador. Se fizer isso, lembre-se de codificar as chaves de documento ao transmiti-las em chamadas de API, como Pesquisa. (Por exemplo, em .NET você pode usar o [método UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) para essa finalidade).

## Lidando com nomes de campos diferentes

Geralmente, os nomes de campos no índice existente serão diferentes dos nomes de propriedades da sua tabela. Você pode usar os **mapeamentos de campo** para mapear os nomes de propriedade da tabela para os nomes de campo em seu índice de pesquisa. Para saber mais sobre mapeamentos de campo, confira [Personalização do indexador da Pesquisa do Azure](search-indexers-customization.md).

## Indexação incremental e detecção de exclusão
 
Quando você configurar um indexador de tabela para ser executado de forma agendada, ele reindexará somente linhas novas ou atualizadas, conforme determinado pelo valor `Timestamp` de uma linha. Não é necessário especificar uma política de detecção de alteração, a indexação incremental é habilitada automaticamente para você.

Para indicar que determinados documentos devem ser removidos do índice, você pode usar uma estratégia de exclusão reversível: em vez de excluir uma linha, adicione uma propriedade para indicar que ela deve ser excluída e configure uma política de detecção de exclusão reversível na fonte de dados. Por exemplo, a política abaixo considerará que uma linha foi excluída se tiver uma propriedade `IsDeleted` com o valor `"true"`:

	PUT https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]
	
	{
	    "name" : "my-table-datasource",
	    "type" : "azuretable",
	    "credentials" : { "connectionString" : "<your storage connection string>" },
	    "container" : { "name" : "table name", "query" : "query" },
	    "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
	}   


## Ajude-nos a aprimorar a Pesquisa do Azure

Se você tiver solicitações de recursos ou ideias para o aperfeiçoamentos, entre em contato conosco pelo [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

<!---HONumber=AcomDC_0420_2016-->