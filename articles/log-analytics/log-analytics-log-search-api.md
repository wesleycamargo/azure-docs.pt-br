<properties
	pageTitle="API de pesquisa de log do Log Analytics | Microsoft Azure"
	description="Este guia fornece um tutorial básico que descreve como você pode usar a API de pesquisa do Log Analytics no OMS (Operations Management Suite) e fornece exemplos que mostram como usar os comandos."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>


# API de pesquisa de log do Log Analytics

Este guia fornece um tutorial básico que descreve como você pode usar a API de Pesquisa do Log Analytics no OMS (Operations Management Suite) e fornece exemplos que mostram como usar os comandos. Alguns dos exemplos neste artigo fazem referência ao Operational Insights, que é o nome da versão anterior do Log Analytics.

## Visão geral da API de pesquisa de log

A API de Pesquisa do Log Analytics é RESTful e pode ser acessada por meio da API do Azure Resource Manager. Neste documento, você encontrará exemplos em que a API é acessada por meio de [ARMClient](https://github.com/projectkudu/ARMClient), uma ferramenta de linha de comando de software livre que simplifica a invocação da API do Gerenciador de Recursos do Azure. O uso do ARMClient e do PowerShell é uma das muitas opções para acessar a API de Pesquisa do Log Analytics. Com essas ferramentas, você pode utilizar a API RESTful do Azure Resource Manager para fazer chamadas aos espaços de trabalho do OMS e executar comandos de pesquisa dentro deles. A API produzirá resultados da pesquisa para você no formato JSON, permitindo que você use os resultados da pesquisa de diferentes maneiras por meio de programação.

O Gerenciador de Recursos do Azure pode ser usado por meio de uma [Biblioteca para .NET](https://msdn.microsoft.com/library/azure/dn910477.aspx) bem como por meio da [API REST](https://msdn.microsoft.com/library/azure/mt163658.aspx). Examine as páginas da Web vinculadas para obter mais informações.

## Tutorial básico da API de Pesquisa do Log Analytics

### Para usar o cliente ARM

1. Instale [Chocolatey](https://chocolatey.org/), que é um Gerenciador de Pacotes de Máquina de software livre para Windows. Abra uma janela do prompt de comando como administrador e execute o seguinte comando:

    ```
    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
    ```

2. Instale o Cliente do ARM executando o seguinte comando:

    ```
    choco install armclient
    ```

### Para realizar uma pesquisa simples usando o ARMClient

1. Faça logon em sua conta da Microsoft ou OrgID:

    ```
    armclient login
    ```

    Um logon bem-sucedido lista todas as assinaturas vinculadas à conta especificada. Por exemplo:

    ```
    PS C:\Users\SampleUserName> armclient login
    Welcome YourEmail@ORG.com (Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz)
    User: YourEmail@ORG.com, Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz (Example org)
    There are 3 subscriptions
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 1)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 2)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 3)
    ```

2. Obtenha os espaços de trabalho do Operations Management Suite. Por exemplo:

    ```
    armclient get /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces?api-version=2015-03-20
    ```

    Uma chamada Get bem-sucedida geraria todos os espaços de trabalho associados à assinatura. Por exemplo:

    ```
    {
    "value": [
    {
      "properties": {
    "source": "External",
    "customerId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "portalUrl": "https://eus.login.mms.microsoft.com/SignIn.aspx?returnUrl=https%3a%2f%2feus.mms.microsoft.com%2fMain.aspx%3fcid%xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      },
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/{WORKSPACE NAME}",
      "name": "{WORKSPACE NAME}",
      "type": "Microsoft.OperationalInsights/workspaces",
      "location": "East US"
       ]
    }
    ```
3. Crie sua variável de pesquisa. Por exemplo:

    ```
    $mySearch = "{ 'top':150, 'query':'Error'}";
    ```
4. Pesquise usando sua nova variável de pesquisa. Por exemplo:

    ```
    armclient post /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{WORKSPACE NAME}/search?api-version=2015-03-20 $mySearch
    ```

## Exemplos de referência da API de Pesquisa do Log Analytics
Os exemplos a seguir mostram como você pode usar a API de pesquisa.

### Pesquisa - ação/leitura

**Url de exemplo:**

```
	/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search?api-version=2015-03-20
```

**Solicitação:**

```
	$savedSearchParametersJson =
	{
	  "top":150,
	  "highlight":{
	    "pre":"{[hl]}",
	    "post":"{[/hl]}"
	  },
	  "query":"*",
	  "start":"2015-02-04T21:03:29.231Z",
	  "end":"2015-02-11T21:03:29.231Z"
	}
	armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```
A tabela a seguir descreve as propriedades que estão disponíveis.

|**Propriedade**|**Descrição**|
|---|---|
|top|O número máximo de resultados a serem retornados.|
|highlight|Contém parâmetros anteriores e subsequentes, geralmente usados para realçar campos correspondentes|
|pre|Usado como prefixo da cadeia de caracteres específica para os campos correspondentes.|
|post|Acrescenta a cadeia de caracteres específica para os campos correspondentes.|
|query|A consulta de pesquisa usada para coletar e retornar os resultados.|
|iniciar|O início da janela de tempo em que você deseja que os resultados sejam encontrados.|
|end|O fim da janela de tempo em que você deseja que os resultados sejam encontrados.|


**Resposta:**

```
	{
	  "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
	  "__metadata" : {
	    "resultType" : "raw",
	    "total" : 1455,
	    "top" : 150,
	    "StartTime" : "2015-02-11T21:09:07.0345815Z",
	    "Status" : "Successful",
	    "LastUpdated" : "2015-02-11T21:09:07.331463Z",
	    "CoreResponses" : [],
	    "sort" : [{
	      "name" : "TimeGenerated",
	      "order" : "desc"
	    }],
	    "requestTime" : 450
	  },
	  "value": [{
	    "SourceSystem" : "OpsManager",
	    "TimeGenerated" : "2015-02-07T14:07:33Z",
	    "Source" : "SideBySide",
	    "EventLog" : "Application",
	    "Computer" : "BAMBAM",
	    "EventCategory" : 0,
	    "EventLevel" : 1,
	    "EventLevelName" : "Error",
	    "UserName" : "N/A",
	    "EventID" : 78,
	    "MG" : "00000000-0000-0000-0000-000000000001",
	    "TimeCollected" : "2015-02-07T14:10:04.69Z",
	    "ManagementGroupName" : "AOI-5bf9a37f-e841-462b-80d2-1d19cd97dc40",
	    "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
	    "Type" : "Event",
	    "__metadata" : {
	      "Type" : "Event",
	      "TimeGenerated" : "2015-02-07T14:07:33Z",
	      "highlighting" : {
	      "EventLevelName" : ["{[hl]}Error{[/hl]}"]
	    }
	  }]
	],
	        "start" : "2015-02-04T21:03:29.231Z",
	        "end" : "2015-02-11T21:03:29.231Z"
	      }
	    }
	  }]
	}
```

### Pesquisa/{ID} - ação/leitura

**Solicite o conteúdo de uma pesquisa salva:**

```
	armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search/{SearchId}?api-version=2015-03-20
```

>[AZURE.NOTE] Se a pesquisa retornar com um status 'Pendente', a sondagem dos resultados atualizados poderá ser feita por essa API. Após seis minutos, o resultado da pesquisa será eliminado do cache e Http Gone será retornado. Se a solicitação inicial de pesquisa retornar um status 'Bem-sucedido' imediatamente, ela não será adicionada ao cache, fazendo essa API retornar Http Gone se consultada. O conteúdo de um resultado de Http 200 estará no mesmo formato que a solicitação inicial de pesquisa, apenas com valores atualizados.

### Pesquisas salvas - somente REST

**Solicite a lista de pesquisas salvas:**

```
	armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/savedSearches?api-version=2015-03-20
```

Métodos com suporte: GET, PUT e DELETE

Métodos de coleção com suporte: GET

A tabela a seguir descreve as propriedades que estão disponíveis.

|Propriedade|Descrição|
|---|---|
|ID|O identificador exclusivo.|
|Etag|**Obrigatório para o Patch**. Atualizado pelo servidor em cada gravação. O valor deve ser igual ao valor armazenado atual ou '*' para atualizar. 409 retornado para valores antigos ou inválidos.|
|properties.query|**Obrigatório**. A consulta de pesquisa.|
|properties.displayName|**Obrigatório**. O nome de exibição da consulta definido pelo usuário. Se modelado como um recurso do Azure, isso seria uma marca.|
|properties.category|**Obrigatório**. A categoria definida pelo usuário para a consulta. Se fosse modelada como um recurso do Azure, ela seria uma marca.|

>[AZURE.NOTE] A API de pesquisa do Log Analytics atualmente retorna pesquisas salvas criadas pelo usuário quando sondada para pesquisas salvas em um espaço de trabalho. A API não retornará pesquisas salvas fornecidas por soluções neste momento. Essa funcionalidade será adicionada em uma data posterior.

### Excluir pesquisas salvas

**Solicitação:**

```
	armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20
```

### Atualizar pesquisas salvas

 **Solicitação:**

```
	$savedSearchParametersJson = "{'etag': 'W/`"datetime\'2015-04-16T23%3A35%3A35.3182423Z\'`"', 'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
	armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### Metadados - somente JSON

Eis aqui uma maneira de ver os campos para todos os tipos de log para os dados coletados em seu espaço de trabalho. Por exemplo, se você quiser que saber se o tipo de Evento tem um campo chamado Computador, essa é uma maneira para pesquisar e confirmar.

**Solicitação por campos:**

```
	armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/schema?api-version=2015-03-20
```

**Resposta:**

```
	{
	  "__metadata" : {
	    "schema" : {
	      "name" : "Example Name",
	      "version" : 2
	    },
	    "resultType" : "schema",
	    "requestTime" : 35
	  },
	  "value" : [{
	      "name" : "MG",
	      "displayName" : "MG",
	      "type" : "Guid",
	      "facetable" : true,
	      "display" : false,
	      "ownerType" : ["PerfHourly", "ProtectionStatus", "Capacity_SMBUtilizationByHost", "Capacity_ArrayUtilization", "Capacity_SMBShareUtilization", "SQLAssessmentRecommendation", "Event", "ConfigurationChange", "ConfigurationAlert", "ADAssessmentRecommendation", "ConfigurationObject", "ConfigurationObjectProperty"]
	    }, {
	      "name" : "ManagementGroupName",
	      "displayName" : "ManagementGroupName",
	      "type" : "String",
	      "facetable" : true,
	      "display" : true,
	      "ownerType" : ["PerfHourly", "ProtectionStatus", "Event", "ConfigurationChange", "ConfigurationAlert", "W3CIISLog", "AlertHistory", "Recommendation", "Alert", "SecurityEvent", "UpdateAgent", "RequiredUpdate", "ConfigurationObject", "ConfigurationObjectProperty"]
	    }
	  ]
	}
```

A tabela a seguir descreve as propriedades que estão disponíveis.

|**Propriedade**|**Descrição**|
|---|---|
|name|Nome do campo.|
|displayName|O nome de exibição do campo.|
|type|O tipo do valor do campo.|
|facetable|Combinação das propriedades 'indexed', 'stored' e 'facet' atuais.|
|display|Propriedade 'display' atual. True se o campo está visível na pesquisa.|
|ownerType|Reduzido a somente aqueles tipos que pertencem a endereços IP incorporados.|


## Parâmetros opcionais
As informações a seguir descrevem os parâmetros opcionais disponíveis.

### Realce

O parâmetro "Highlight" é um parâmetro opcional que você pode usar para solicitar que o subsistema de pesquisa inclua um conjunto de marcadores em sua resposta.

Esses marcadores indicam o início e término do texto realçado que coincide com os termos fornecidos na consulta de pesquisa. Você pode especificar os marcadores de início e término que serão usados pela pesquisa para encapsular o termo realçado.

**Exemplo de consulta de pesquisa**

```
	$savedSearchParametersJson =
	{
	  "top":150,
	  "highlight":{
	    "pre":"{[hl]}",
	    "post":"{[/hl]}"
	  },
	  "query":"*",
	  "start":"2015-02-04T21:03:29.231Z",
	  "end":"2015-02-11T21:03:29.231Z"
	}
	armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```

**Resultado de exemplo:**

```
	{
	    "TimeGenerated":
	    "2015-05-18T23:55:59Z", "Source":
	    "EventLog": "Application",
	    "Computer": "smokedturkey.net",
	    "EventCategory": 0,
	    "EventLevel":1,
	    "EventLevelName":
	    "Error"
	    "Manager ", "__metadata":
	    {
	        "Type": "Event",
	        "TimeGenerated": "2015-05-18T23:55:59Z",
	        "highlighting": {
	            "EventLevelName":
	            ["{[hl]}Error{[/hl]}"]
	        }
	    }
	}
```

Observe que o resultado acima contém uma mensagem de erro que foi acrescida de um prefixo e anexada.

## Grupos de computadores

Grupos de computadores são pesquisas especiais salvas que retornam um conjunto de computadores. Você pode usar um grupo de computadores em outras consultas para limitar os resultados para os computadores no grupo. Um grupo de computadores é implementado como uma pesquisa salva com uma marca de Grupo com um valor de Computador.

A seguir está um exemplo de resposta para um grupo de computadores.

	"etag": "W/"datetime'2016-04-01T13%3A38%3A04.7763203Z'"",
	"properties": {
		"Category": "My Computer Groups",
		"DisplayName": "My Computer Group",
		"Query": "srv* | Distinct Computer",
		"Tags": [{
        	"Name": "Group",
        	"Value": "Computer"
      	}],
    "Version": 1
	}

### Recuperando grupos de computadores

Use o método Get com a ID do grupo para recuperar um grupo de computadores.

```
armclient get /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Group ID}`?api-version=2015-03-20
```

### Criar ou atualizar um grupo de computadores

Use o método Put com uma ID de pesquisa salva exclusiva para criar um novo grupo de computadores. Se você usar uma ID de grupo de computador existente, ele será modificado. Quando você cria um grupo de computadores no console do OMS, a ID é criada por meio do grupo e do nome.

A consulta usada para a definição de grupo deve retornar um conjunto de computadores para o grupo funcionar corretamente. É recomendável terminar sua consulta com *| Computador Distinto* para garantir que os dados corretos sejam retornados.

A definição da pesquisa salva deve incluir uma marca chamada Grupo com um valor de Computador para a pesquisa ser classificada como um grupo de computadores.

	$etag=get-date -f yyyy-MM-ddThh:mm:ss.msZ
	$groupName="My Computer Group"
	$groupQuery = "Computer=srv* | Distinct Computer"
	$groupCategory="My Computer Groups"
	$groupID = "My Computer Groups | My Computer Group"

	$groupJson = "{'etag': 'W/`"datetime\'" + $etag + "\'`"', 'properties': { 'Category': '" + $groupCategory + "', 'DisplayName':'"  + $groupName + "', 'Query':'" + $groupQuery + "', 'Tags': [{'Name': 'Group', 'Value': 'Computer'}], 'Version':'1'  }"

	armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20 $groupJson

### Excluindo grupos de computadores

Use o método Delete com a ID do grupo para excluir um grupo de computadores.

```
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20
```


## Próximas etapas

- Saiba mais sobre [pesquisas de log](log-analytics-log-searches.md) para criar consultas usando campos personalizados para os critérios.

<!---HONumber=AcomDC_0504_2016-->