<properties title="" pageTitle="Operações de indexador (API REST do serviço Azure Search: 2014-10-20-Preview)" description="Operações de indexador (API REST do serviço Azure Search: 2014-10-20-Preview)" metaKeywords="" services="search" solutions="" documentationCenter="" authors="HeidiSteen" manager="mblythe" videoId="" scriptId="" />

<tags ms.service="search" ms.devlang="rest-api" ms.workload="search" ms.topic="article"  ms.tgt_pltfrm="na" ms.date="07/08/2015" ms.author="heidist" />

#Operações de indexador (API REST do serviço Azure Search: 2014-10-20-Preview)

> [AZURE.NOTE]Este artigo descreve um protótipo da nova funcionalidade que não está na versão lançada da API. Leia mais sobre versões e a capacidade de suporte em [Controle de versão do serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) no MSDN. Para obter mais informações sobre outros recursos nesta API de visualização, consulte [Versão da API REST do serviço Azure Search: 2014-10-20-Preview](search-api-2014-10-20-preview.md).

## Visão geral

O Azure Search pode se integrar diretamente a algumas fontes de dados comuns, eliminando a necessidade de escrever código para indexar seus dados. Para configurar isso, você pode chamar a API do Azure Search para criar e gerenciar **indexadores** e **fontes de dados**.

Uma **fonte de dados** especifica quais dados precisam ser indexados, as credenciais para acessar os dados e dicas para habilitar o Azure Search a identificar com eficiência as alterações nos dados (como linhas modificadas ou excluídas em uma tabela de banco de dados).

Um **indexador** descreve como os dados vão da fonte de dados para um índice de pesquisa. Um indexador pode:

- Fazer uma cópia única dos dados
- Sincronizar um índice do Azure Search com as alterações da fonte de dados segundo uma agenda.
- Ser invocado sob demanda a qualquer momento para fazer a cópia

Atualmente, há suporte às seguintes fontes de dados:

- Banco de Dados SQL Azure
- Banco de Dados de Documentos 

Estamos considerando a adição de suporte a fontes de dados adicionais no futuro. Para nos ajudar a priorizar essas decisões, forneça seus comentários no [Fórum de comentários do Azure Search](http://feedback.azure.com/forums/263029-azure-search).

**OBSERVAÇÃO** HÁ suporte aos recursos descritos aqui a partir da API do Azure Search versão `2014-10-20-Preview`.

## Fluxo de uso típico

As etapas comuns para configurar a indexação automática são as seguintes:

1. Identifique a fonte de dados que contém os dados que precisam ser indexados. Lembre-se de que o Azure Search pode não dar suporte a todos os tipos de dados presentes na fonte de dados

2. Crie um índice do Azure Search cujo esquema seja compatível com a fonte de dados
  
3. Crie uma fonte de dados do Azure Search conforme descrito na seção Operações de fonte de dados a seguir
  
4. Crie um indexador do Azure Search e monitore sua execução, conforme descrito na seção Operações do indexador a seguir

## Regras de nomenclatura

- Um nome de fonte de dados deve conter apenas letras minúsculas, dígitos ou traços, não pode começar ou terminar com traços e está limitado a 128 caracteres.
- Um nome de indexador deve conter apenas letras minúsculas, dígitos ou traços, não pode começar ou terminar com traços e está limitado a 128 caracteres.

## Limites e restrições

Consulte a [página Limites e restrições](http://msdn.microsoft.com/library/azure/dn798934.aspx) para obter detalhes.

## Operações de fonte de dados
Você pode criar e gerenciar fonte de dados no serviço Azure Search por meio de solicitações HTTP simples (POST, GET, PUT, DELETE) em relação a um recurso de fonte de dados específico.

### Criar fonte de dados

Você pode criar uma nova fonte de dados em um serviço Azure Search usando uma solicitação HTTP POST.
	
    POST https://[service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Como alternativa, você pode usar PUT e especificar o nome da fonte de dados no URI. Se a fonte de dados não existir, ela será criada.

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]

**Observação**: o número máximo de fontes de dados permitidas varia por camada de preços. O serviço gratuito permite até três fontes de dados. O serviço padrão permite 50 fontes de dados.

**Solicitação**

HTTPS é necessário para todas as solicitações de serviço. A solicitação **Criar Fonte de Dados** pode ser criada usando um método POST ou PUT. Ao usar POST, você deve fornecer um nome de fonte de dados no corpo da solicitação, juntamente com a definição de esquema de fonte de dados. Com PUT, o nome da fonte de dados é parte da URL. Se a fonte de dados não existir, ela será criada. Se já existir, ela será atualizada para a nova definição.

O nome da fonte de dados deve estar em letras minúsculas, começar com uma letra ou número, não conter barras ou pontos e ter menos de 128 caracteres. Depois de iniciar o nome da fonte de dados com uma letra ou número, o restante do nome pode incluir qualquer letra, número e traços, desde que os traços não sejam consecutivos.

A `api-version` é obrigatória. Os valores válidos incluem `2014-10-20-Preview` ou uma versão posterior.

**Cabeçalhos de solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.

- `Content-Type`: obrigatório. Defina-o como `application/json`
- `api-key`: obrigatório. A `api-key` é usada para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres exclusivo de seu serviço. A solicitação **Criar Fonte de Dados** deve incluir um cabeçalho de `api-key` definido como sua chave de administração (em vez de uma chave de consulta). 
 
Você também precisará do nome de serviço para criar a URL da solicitação. Você pode obter o nome do serviço e a `api-key` por meio do painel de serviço no Portal de Visualização do Azure. Consulte [Criar um serviço Azure Search no portal](search-create-service-portal.md) para obter ajuda sobre a navegação na página.

<a name="CreateDataSourceRequestSyntax"></a> **Sintaxe de corpo da solicitação**

O corpo da solicitação contém uma definição de fonte de dados, que inclui o tipo de fonte de dados, credenciais para ler os dados, bem como políticas de detecção de alterações de dados opcionais e detecção de exclusão de dados que são usadas para identificar com eficiência dados alterados ou excluídos na fonte de dados quando usadas com um indexador agendado periodicamente.


A sintaxe para estruturar a carga da solicitação é indicada a seguir. Uma solicitação de exemplo é fornecida mais adiante neste tópico.

    { 
		"name" : "Required for POST, optional for PUT. The name of the data source",
    	"description" : "Optional. Anything you want, or nothing at all",
    	"type" : "Required. Must be 'azuresql' or 'docdb'",
    	"credentials" : { "connectionString" : "Required. Connection string for your Azure SQL database" },
    	"container" : { "name" : "Required. The name of the table or collection you wish to index" },
    	"dataChangeDetectionPolicy" : { Optional. See below for details }, 
    	"dataDeletionDetectionPolicy" : { Optional. See below for details }
	}

A solicitação pode conter as seguintes propriedades:

- `name`: obrigatório. O nome da fonte de dados. Um nome de fonte de dados deve conter apenas letras minúsculas, dígitos ou traços, não pode começar ou terminar com traços e está limitado a 128 caracteres.
- `description`: uma descrição opcional. 
- `type`: obrigatório. Use `azuresql` para uma fonte de dados do Azure SQL e `docdb` para uma fonte de dados DocumentDB.
- `container`: 
	- A propriedade obrigatória `name` especifica a tabela ou o modo de exibição (para a fonte de dados do Azure SQL) ou uma coleção (para uma fonte de dados DocumentDB) que será indexado. 
	- As fontes de dados DocumentDB também dão suporte a uma propriedade opcional `query` que permite que você especifique uma consulta que nivela um layout de documento JSON arbitrário em um esquema simples que o Azure Search pode indexar.   
- A `dataChangeDetectionPolicy` e a `dataDeletionDetectionPolicy` opcionais são descritas abaixo.

<a name="DataChangeDetectionPolicies"></a> **Políticas de Detecção de Alteração de Dados**

A finalidade de uma política de detecção de alteração de dados é identificar de maneira eficaz dados alterados. As políticas com suporte variam com base no tipo de fonte de dados. As seções a seguir descrevem cada política.

***Política de Detecção de Alteração de Marca d’água Alta***

Use essa política quando sua fonte de dados contiver uma coluna ou propriedade que atenda aos seguintes critérios:
 
- Todas as inserções especificam um valor para a coluna. 
- Todas as atualizações de um item também alteram o valor da coluna. 
- O valor dessa coluna aumenta com cada alteração.
- As consultas que usam uma cláusula de filtro semelhante à `WHERE [High Water Mark Column] > [Current High Water Mark Value]` a seguir podem ser executadas com eficiência.

Por exemplo, ao se usar fontes de dados Azure SQL, uma coluna `rowversion` indexada é o candidato ideal para uso com a política de marca d'água alta.

Ao usar fontes de dados DocumentDB, você deve usar a propriedade `_ts` fornecida pelo DocumentDB.
 
Essa política pode ser especificada da seguinte maneira:

	{ 
		"@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
		"highWaterMarkColumnName" : "[a row version or last_updated column name]" 
	} 

***Política de Detecção de Alteração de Dados Integrada do SQL***

Se o banco de dados SQL der suporte ao [Controle Integrado de Alterações do SQL](http://technet.microsoft.com/library/cc280462.aspx), recomendamos o uso da Política de Controle Integrado de Alterações do SQL. Essa política habilita o controle de alterações mais eficiente e permite que o Azure Search identifique linhas excluídas sem que seja preciso ter uma coluna explícita de "exclusão reversível" no esquema.

Há suporte ao controle integrado de alterações do SQL a partir das seguintes versões de banco de dados SQL: ‒ SQL Server 2008 R2, se você estiver usando VMs SQL IaaS. ‒ Banco de Dados Azure SQL V12, se você estiver usando o Azure SQL.

**OBSERVAÇÃO:** ao usar a política de Controle Integrado de Alterações do SQL, não especifique uma política separada de detecção de exclusão de dados, pois essa política tem suporte interno para identificação de linhas excluídas.

**OBSERVAÇÃO:** essa política só pode ser usada com tabelas; ela não pode ser usada com exibições. Para poder usar essa política, você precisa habilitar o controle de alterações para a tabela que está usando.
 
A política de controle integrado de alterações do SQL pode ser especificada da seguinte maneira:

	{ 
		"@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
	}

<a name="DataDeletionDetectionPolicies"></a> **Políticas de Detecção de Exclusão de Dados**

A finalidade de uma política de detecção de exclusão de dados é identificar de maneira eficaz dados excluídos. Atualmente, a única política com suporte é a política `Soft Delete`, que é especificada da seguinte forma:

	{ 
		"@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
		"softDeleteColumnName" : "the column that specifies whether a row was deleted", 
		"softDeleteMarkerValue" : "the value that identifies a row as deleted" 
	}

<a name="CreateDataSourceRequestExamples"></a> **Exemplos de corpo de solicitação**

Se você pretende usar a fonte de dados com um indexador que é executado de acordo com uma agenda, este exemplo mostra como especificar dicas de política:

    { 
		"name" : "asqldatasource",
		"description" : "a description",
    	"type" : "azuresql",
    	"credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
    	"container" : { "name" : "sometable" },
    	"dataChangeDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy", "highWaterMarkColumnName" : "RowVersion" }, 
    	"dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : true }
	}

Se você pretende usar a fonte de dados para uma cópia única dos dados, as dicas de política podem ser omitidas:

    { 
		"name" : "asqldatasource",
    	"description" : "anything you want, or nothing at all",
    	"type" : "azuresql",
    	"credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
    	"container" : { "name" : "sometable" }
	} 

**Resposta**

Para uma solicitação bem-sucedida: "201 Criado".

### Atualizar Fonte de Dados

Você pode atualizar uma fonte de dados usando uma solicitação HTTP PUT. Especifique o nome da fonte de dados a ser atualizada no URI da solicitação:

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Solicitação** a sintaxe do corpo da solicitação é a mesmo usada para [solicitações Criar Fonte de Dados](#CreateDataSourceRequestSyntax).

**Resposta** para uma solicitação bem-sucedida: 201 Criado se uma nova fonte de dados for criada e 204 sem Conteúdo se uma fonte de dados for atualizada.

**OBSERVAÇÃO:** algumas propriedades não podem ser atualizadas em uma fonte de dados existente. Por exemplo, você não pode alterar o tipo de fonte de dados existente.

### Listar Fontes de Dados

A operação **Listar Fontes de Dados** retorna uma lista das fontes de dados existentes no momento em seu serviço Azure Search.

    GET https://[service name].search.windows.net/datasources?api-version=[api-version]
    api-key: [admin key]

**Resposta**

Para uma solicitação bem-sucedida: 200 OK.

Aqui está um exemplo de corpo de resposta:

    {
      "value" : [
        {
          "name": "datasource1",
          "type": "azuresql",
		  ... other data source properties
        }]
    }

Observe que você pode filtrar a resposta para apenas as propriedades em que está interessado. Por exemplo, se você quiser apenas uma lista de nomes de fonte de dados, use a opção de consulta OData `$select`:

    GET /datasources?api-version=2014-10-20-Preview&$select=name

Nesse caso, a resposta do exemplo anterior seria exibida da seguinte maneira:

    {
      "value" : [ { "name": "datasource1" }, ... ]
    }

Essa é uma técnica útil para economizar largura de banda se você tiver muitas fontes de dados em seu serviço de pesquisa.

### Obter Fonte de Dados

A operação **Obter Fonte de Dados** obtém a definição da fonte de dados do Azure Search.

    GET https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

**Resposta**

Código de status: 200 OK é retornado para uma resposta bem-sucedida.

A resposta é semelhante ao exemplos em [Solicitações de exemplo Criar Fonte de Dados](#CreateDataSourceRequestExamples):

	{ 
		"name" : "asqldatasource",
		"description" : "a description",
    	"type" : "azuresql",
    	"credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
    	"container" : { "name" : "sometable" },
    	"dataChangeDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
			"highWaterMarkColumnName" : "RowVersion" }, 
    	"dataDeletionDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
			"softDeleteColumnName" : "IsDeleted", 
			"softDeleteMarkerValue" : true }
	}

**OBSERVAÇÃO** não defina o `Accept` cabeçalho da solicitação como `application/json;odata.metadata=none` ao chamar essa API, pois isso fará com que o atributo `@odata.type` seja omitido da resposta, e você não poderá diferenciar políticas de detecção de alteração de dados e exclusão de dados de diferentes tipos.

### Excluir Fonte de Dados

A operação **Excluir Fonte de Dados** remove uma fonte de dados de seu serviço Azure Search.

    DELETE https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

**OBSERVAÇÃO** se quaisquer indexadores fizerem referência à fonte de dados que você está excluindo, a operação de exclusão ainda continuará. No entanto, esses indexadores farão a transição para um estado de erro em sua próxima execução.

**Resposta**

Código de status: 204 sem Conteúdo é retornado para uma resposta bem-sucedida.

## Operações de Indexador

Um indexador é o recurso que conecta fontes de dados a índices de pesquisa de destino. Você deve planejar a criação de um indexador para cada combinação de índice de destino e fonte de dados. Você pode ter vários indexadores gravando no mesmo índice. No entanto, um indexador pode gravar apenas em um único índice.

Você pode criar e gerenciar índices por meio de solicitações HTTP simples (POST, GET, PUT, DELETE) em relação a um recurso de indexador específico.

Depois de criar um indexador, você pode recuperar seu status de execução usando a operação [Obter Status do Indexador](#GetIndexerStatus). Você também pode executar um indexador a qualquer momento (em vez de ou além de executá-lo periodicamente de acordo com uma agenda) usando a operação [Executar Indexador](#RunIndexer).

### Criar Indexador

Você pode criar um novo indexador em um serviço Azure Search usando uma solicitação HTTP POST.
	
    POST https://[service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Como alternativa, você pode usar PUT e especificar o nome da fonte de dados no URI. Se a fonte de dados não existir, ela será criada.

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]

**Observação**: o número máximo de indexadores permitidos varia por camada de preços. O serviço gratuito permite até três indexadores. O serviço padrão permite 50 indexadores.

<a name="CreateIndexerRequestSyntax"></a> **Sintaxe de corpo da solicitação**

O corpo da solicitação contém uma definição de indexador, que especifica a fonte de dados e o índice de destino para indexação, bem como a agenda e parâmetros de indexação opcionais.


A sintaxe para estruturar a carga da solicitação é indicada a seguir. Uma solicitação de exemplo é fornecida mais adiante neste tópico.

    { 
		"name" : "Required for POST, optional for PUT. The name of the indexer",
    	"description" : "Optional. Anything you want, or null",
    	"dataSourceName" : "Required. The name of an existing data source",
        "targetIndexName" : "Required. The name of an existing index",
        "schedule" : { Optional. See Indexing Schedule below. },
        "parameters" : { Optional. See Indexing Parameters below. }
	}

**Agenda de indexação**

Um indexador pode, também, especificar uma agenda. Se houver uma agenda, o indexador será executado periodicamente segundo a agenda. A agenda tem os seguintes atributos:

- `interval`: obrigatório. Um valor de duração que especifica o intervalo ou período de execução do indexador. O menor intervalo permitido é de cinco minutos, e o maior é de um dia. Ele deve ser formatado como um valor XSD de "dayTimeDuration" (um subconjunto restrito de um [valor de duração ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)). O padrão para isso é: `P(nD)(T(nH)(nM))`. Exemplos: `PT15M` para cada 15 minutos, `PT2H` para cada 2 horas. 

- `startTime`: obrigatório. Uma data/hora, no horário UTC, quando o indexador deve começar a ser executado.

**Parâmetros de indexação**

Um indexador pode, opcionalmente, especificar vários parâmetros que afetam seu comportamento. Todos os parâmetros são opcionais.

- `maxFailedItems`: o número de itens que podem não ser indexados antes que a execução de um indexador seja considerada como falha. O padrão é `0`. Informações sobre itens com falha são retornadas pela operação [Obter Status do Indexador](#GetIndexerStatus). 

- `maxFailedItemsPerBatch`: o número de itens que podem não ser indexados em cada lote antes que a execução de um indexador seja considerada como falha. O padrão é `0`.

- `base64EncodeKeys`: especifica se as chaves de documento serão ou não codificadas em base 64. O Azure Search impõe restrições em relação aos caracteres que podem estar presentes em uma chave de documento. No entanto, os valores na fonte de dados podem conter caracteres que são inválidos. Se for necessário indexar esses valores como chaves de documento, esse sinalizador poderá ser definido como true. O padrão é `false`.

<a name="CreateIndexerRequestExamples"></a> **Exemplos de corpo de solicitação**

O exemplo a seguir cria um indexador que copia dados da tabela referenciada pela fonte de dados `ordersds` para o índice `orders` em uma agenda que se inicia em 1º de janeiro de 12015 UTC e é executado de hora em hora. Cada invocação do indexador será bem-sucedida se não mais do que cinco itens não forem indexados em cada lote e se não mais do que dez itens não forem indexados no total.

	{
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
	}

**Resposta**

201 Criado para uma solicitação bem-sucedida.

### Atualizar Indexador

Você pode atualizar um indexador usando uma solicitação HTTP PUT. Especifique o nome do indexador a ser atualizado no URI da solicitação:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Solicitação**

A sintaxe do corpo da solicitação é a mesma usada para [solicitações Criar Indexador](#CreateIndexerRequestSyntax).

**Resposta**

Para uma solicitação bem-sucedida: 201 Criado se um novo indexador for criado e 204 sem Conteúdo se um indexador existente for atualizado.

### Listar Indexadores

A operação **Listar Indexadores** retorna a lista de indexadores em seu serviço Azure Search.

    GET https://[service name].search.windows.net/indexers?api-version=[api-version]
    api-key: [admin key]

**Resposta**

Para uma solicitação bem-sucedida: 200 OK.

Aqui está um exemplo de corpo de resposta:

    {
      "value" : [
      {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        ... other indexer properties
	  }]
    }

Observe que você pode filtrar a resposta para apenas as propriedades em que está interessado. Por exemplo, se você quiser apenas uma lista de nomes de indexadores, use a opção de consulta OData `$select`:

    GET /indexers?api-version=2014-10-20-Preview&$select=name

Nesse caso, a resposta do exemplo anterior seria exibida da seguinte maneira:

    {
      "value" : [ { "name": "myindexer" } ]
    }

Essa é uma técnica útil para economizar largura de banda se você tiver muitos indexadores em seu serviço de pesquisa.

### Obter Indexador

A operação **Obter Indexador** obtém a definição de indexador do Azure Search.

    GET https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

**Resposta**

Código de status: 200 OK é retornado para uma resposta bem-sucedida.

A resposta é semelhante aos exemplos em [Solicitações de exemplo criar Indexador](#CreateIndexerRequestExamples):

	{
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
	}

### Excluir Indexador

A operação **Excluir Indexador** remove um indexador de seu serviço Azure Search.

    DELETE https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

Quando um indexador é excluído, as execuções do indexador em andamento no momento serão executadas até a conclusão, mas outras execuções não serão agendadas. As tentativas de usar um indexador inexistente resultarão no código de status HTTP 404 Não Encontrado.
 
**Resposta**

Código de status: 204 Sem Conteúdo é retornado para uma resposta bem-sucedida.

<a name="RunIndexer"></a>
### Executar Indexador

Além de ser executado periodicamente segundo uma agenda, o indexador também pode ser invocado sob demanda por meio da operação **Executar Indexador**:

	POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [admin key]

**Resposta**

Código de status: 202 Aceito é retornado para uma resposta bem-sucedida.

<a name="GetIndexerStatus"></a>
### Obter Status do Indexador

A operação **Obter Status do Indexador** recupera o status atual e o histórico de execução de um indexador:

	GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [admin key]

**Resposta**

Código de status: 200 OK para uma resposta bem-sucedida.

O corpo da resposta contém informações sobre o status geral de integridade, a última invocação e o histórico de invocações recentes do indexador (se houver).

Um exemplo de corpo de resposta tem esta aparência:

	{
		"status":"running",
		"lastResult": {
			"status":"success",
			"errorMessage":null,
			"startTime":"2014-11-26T03:37:18.853Z",
			"endTime":"2014-11-26T03:37:19.012Z",
			"errors":[],
			"itemsProcessed":11,
			"itemsFailed":0,
			"initialTrackingState":null,
			"finalTrackingState":null
         },
		"executionHistory":[ {
			"status":"success",
         	"errorMessage":null,
			"startTime":"2014-11-26T03:37:18.853Z",
			"endTime":"2014-11-26T03:37:19.012Z",
			"errors":[],
			"itemsProcessed":11,
			"itemsFailed":0,
			"initialTrackingState":null,
			"finalTrackingState":null
		}]
	}

**Status do Indexador**

O status do indexador pode ter um dos seguintes valores:

- `running` indica que o indexador tem execução normal. Observe que algumas das execuções do indexador ainda podem estar falhando. Portanto, é uma boa ideia verificar também a propriedade `lastResult`. 

- `error` indica que o indexador encontrou um erro que não pode ser corrigido sem intervenção humana. Por exemplo, as credenciais da fonte de dados podem ter expirado ou o esquema da fonte de dados ou do índice de destino foi alterado de maneira que causa erros.

**Resultado da Execução do Indexador**

Um resultado de execução do indexador contém informações sobre a execução de um único indexador. O resultado mais recente é mostrado como a propriedade `lastResult` do status do indexador. Outros resultados recentes, caso presentes, serão retornados como a propriedade `executionHistory` do status do indexador.

O resultado da execução do indexador contém as seguintes propriedades:

- `status`: o status desta execução. Consulte [Status de Execução do Indexador](#IndexerExecutionStatus) a seguir para obter detalhes. 

- `errorMessage`: mensagem de erro para uma execução com falha.

- `startTime`: a hora em UTC quando essa execução foi iniciada.

- `endTime`: a hora em UTC quando essa execução foi finalizada. Esse valor não será definido se a execução ainda estiver em andamento.

- `errors`: uma lista de erros de nível de item, se houver.

- `itemsProcessed`: o número de itens da fonte de dados (por exemplo, linhas de tabela) que o indexador tentou indexar durante esta execução.

- `itemsFailed`: o número de itens que falharam durante esta execução.
 
- `initialTrackingState`: sempre `null` para a primeira execução do indexador ou se a política de controle de alteração de dados não estiver habilitada na fonte de dados usada. Se essa política estiver habilitada, em execuções subsequentes, esse valor indicará o primeiro valor de controle de alterações (menor) processado por essa execução.

- `finalTrackingState`: sempre `null` se a política de controle de alteração de dados não estiver habilitada na fonte de dados usada. Caso contrário, indica o último valor de controle de alterações (mais alto) processado com êxito por essa execução.

<a name="IndexerExecutionStatus"></a> **Status de Execução do Indexador**

O status de execução do indexador captura o status de uma única execução do indexador. Ele pode ter os seguintes valores:

- `success` indica que a execução do indexador foi concluída com êxito.

- `inProgress` indica que a execução do indexador está em andamento.

- `transientFailure` indica que a invocação do indexador falhou, mas a falha pode ser temporária. As invocações do indexador continuarão de acordo com a agenda, se houver.

- `persistentFailure` indica que o indexador falhou de uma forma que, provavelmente, requer intervenção humana (por exemplo, devido a uma incompatibilidade de esquema entre a fonte de dados e o índice de destino). As execuções do indexador agendadas param; a ação do usuário é necessária para resolver o problema (descrito na propriedade `errorMessage`) e reiniciar a execução do indexador.

- `reset` indica que o indexador foi redefinido por uma chamada para Redefinir a API do Indexador (veja a seguir).

<a name="ResetIndexer"></a>
### Redefinir Indexador

A operação **Redefinir Indexador** redefine o estado de controle de alterações associado ao indexador. Isso permite disparar a reindexação do zero (por exemplo, se o esquema de fonte de dados for alterado) ou alterar a política de detecção de alteração de dados para uma fonte de dados associada ao indexador.

	POST https://[service name].search.windows.net/indexers/[indexer name]/reset?api-version=[api-version]
    api-key: [admin key]

**Resposta**

Código de status: 204 sem Conteúdo para uma resposta bem-sucedida.

## Mapeamento entre tipos de dados SQL e tipos de dados do Azure Search

<table style="font-size:12">
<tr>
<td>Tipo de dados SQL</td>	
<td>Tipos de campos de índice de destino permitidos</td>
<td>Observações</td>
</tr>
<tr>
<td>bit</td>
<td>Edm.Boolean, Edm.String</td>
<td></td>
</tr>
<tr>
<td>int, smallint, tinyint</td>
<td>Edm.Int32, Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>bigint</td>
<td>Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>real, float</td>
<td>Edm.Double, Edm.String</td>
<td></td>
</tr>
<tr>
<td>smallmoney, dinheiro<br>decimal<br>numérico
</td>
<td>Edm.String</td>
<td>O Azure Search não dá suporte à conversão de tipos decimais em Edm.Double porque isso causaria a perda de precisão
</td>
</tr>
<tr>
<td>char, nchar, varchar, nvarchar</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>smalldatetime, datetime, datetime2, date, datetimeoffset</td>
<td>Edm.DateTimeOffset, Edm.String</td>
<td></td>
</tr>
<tr>
<td>uniqueidentifer</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>rowversion</td>
<td>N/D</td>
<td>Colunas de linha-versão não podem ser armazenadas no índice de pesquisa, mas podem ser usadas para o controle de alterações</td>
</tr>
<tr>
<td>tempo, período de tempo<br>binário, varbinary, imagem<br>xml<br>geometria<br> geografia<br>Tipos CLR</td>
<td>N/D</td>
<td>Sem suporte</td>
</tr>
</table>

## Mapeamento entre tipos de dados JSON e tipos de dados da Pesquisa do Azure

<table style="font-size:12">
<tr>
<td>Tipo de dados JSON</td>	
<td>Tipos de campos de índice de destino permitidos</td>
<td>Observações</td>
</tr>
<tr>
<td>bool</td>
<td>Edm.Boolean, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Números integrais</td>
<td>Edm.Int32, Edm.Int64, Edm.String</td>
<td>O JSON não tem tipo inteiros; portanto, devemos presumir os inteiros mais amplos – de 64 bits</td>
</tr>
<tr>
<td>Números de ponto flutuante</td>
<td>Edm.Double, Edm.String</td>
<td></td>
</tr>
<tr>
<td>string</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>Matrizes de tipos primitivos, como [ "a", "b", "c" ]</td>
<td>Collection(Edm.String)</td>
<td></td>
</tr>
<tr>
<td>Cadeias de caracteres que se parecem com datas</td>
<td>Edm.DateTimeOffset, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Objetos JSON</td>
<td>N/D</td>
<td>Sem suporte; no momento, o Azure Search dá suporte apenas a tipos primitivos e coleções de cadeias de caracteres</td>
</tr>
</table>

<!---HONumber=Oct15_HO3-->