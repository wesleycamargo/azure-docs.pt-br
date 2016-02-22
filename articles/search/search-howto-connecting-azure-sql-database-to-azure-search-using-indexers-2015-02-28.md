<properties 
	pageTitle="Como conectar o Banco de Dados SQL do Azure à Pesquisa do Azure usando indexadores | Microsoft Azure | Serviço de pesquisa de nuvem hospedado" 
	description="Saiba como extrair dados do Banco de Dados SQL do Azure para um índice de Pesquisa do Azure usando indexadores." 
	services="search" 
	documentationCenter="" 
	authors="chaosrealm" 
	manager="pablocas" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="02/08/2016" 
	ms.author="eugenesh"/>

#Conectando o Banco de Dados do SQL do Azure à Pesquisa do Azure usando indexadores

O serviço da Pesquisa do Azure é um serviço de pesquisa de nuvem hospedado que facilita o fornecimento de uma excelente experiência de pesquisa. Antes de pesquisar, você precisa popular um índice da Pesquisa do Azure com seus dados. Se os dados estiverem em um Banco de Dados SQL do Azure, o novo **indexador da Pesquisa do Azure para o Banco de Dados SQL do Azure** (ou **indexador do SQL Azure** para resumir) na Pesquisa do Azure poderá automatizar o processo de indexação. Isso significa menos código para escrever e menos infraestrutura para se preocupar.

Atualmente, os indexadores funcionam apenas com o Banco de Dados SQL do Azure, SQL Server em VMs do Azure e [Banco de Dados de Documentos do Azure](../documentdb/documentdb-search-indexer.md). Neste artigo, vamos nos concentrar nos indexadores que funcionam com o Banco de Dados SQL do Azure. Se você quiser suporte para outras fontes de dados, forneça seus comentários no [Fórum de comentários da Pesquisa do Azure](https://feedback.azure.com/forums/263029-azure-search/).

Este artigo abordará a mecânica do uso de indexadores, mas também exploraremos recursos e comportamentos disponíveis apenas com os bancos de dados SQL (por exemplo, controle integrado de alterações).

## Indexadores e fontes de dados

Para instalar e configurar um indexador do SQL Azure, você pode chamar a [API REST da Pesquisa do Azure](http://go.microsoft.com/fwlink/p/?LinkID=528173) para criar e gerenciar **indexadores** e **fontes de dados**.

Também é possível usar a [classe Indexer](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.indexer.aspx) no [SDK do .NET](https://msdn.microsoft.com/library/azure/dn951165.aspx) ou o assistente de Importação de Dados no [Portal clássico do Azure](https://portal.azure.com) para criar e agendar um indexador.

Uma **fonte de dados** especifica quais dados indexar, as credenciais necessárias para acessar os dados e as políticas que permitem à Pesquisa do Azure identificar com eficiência as alterações nos dados (linhas novas, modificadas ou excluídas). Ela é definida como um recurso independente para que possa ser usada por vários indexadores.

Um **indexador** é um recurso que conecta fontes de dados a índices de pesquisa de destino. Um indexador é usado para as seguintes finalidades:
 
- Executar uma cópia única dos dados para preenchimento de um índice.
- Atualizar um índice com as alterações feitas na fonte de dados com base em uma agenda.
- Executar sob demanda para atualização de um índice, conforme necessário. 

## Quando usar o indexador do SQL Azure

O uso do indexador do SQL Azure pode ou não ser apropriado dependendo de vários fatores relacionados aos seus dados. Se os dados atenderem aos requisitos a seguir, você poderá usar o indexador do SQL Azure:

- Todos os dados são provenientes de uma única tabela ou exibição
	- Se os dados estiverem espalhados em várias tabelas, você pode criar uma exibição e usar essa exibição com o indexador. No entanto, saiba que se você usar uma exibição, não será possível usar a detecção integrada de alterações do SQL Server. Confira esta seção para saber mais. 
- Os tipos de dados usados na fonte de dados têm suporte do indexador. A maioria dos tipos de SQL tem suporte, mas não todos. Para mais detalhes, confira [Mapeamento dos tipos de dados na Pesquisa do Azure](http://go.microsoft.com/fwlink/p/?LinkID=528105). 
- Você não precisa de atualizações quase em tempo real no índice quando uma linha é alterada. 
	- O indexador pode reindexar sua tabela no máximo a cada 5 minutos. Se os dados forem alterados com frequência e as alterações precisarem ser refletidas no índice em questão de segundos ou minutos, recomendamos o uso direto da [API do Índice de Pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn798930.aspx). 
- Se você tiver um conjunto grande de dados e planeja executar o indexador com base em uma agenda, o esquema permitirá a identificação eficiente de linhas alteradas (e excluídas, se for aplicável). Para mais detalhes, confira "Captura de linhas alteradas e excluídas" abaixo. 
- O tamanho dos campos indexados em uma linha não excede o tamanho máximo de uma solicitação de indexação de Pesquisa do Azure, que é de 16 MB. 

## Criar e usar um indexador do SQL Azure

Primeiro, crie a fonte de dados:

	POST https://myservice.search.windows.net/datasources?api-version=2015-02-28 
	Content-Type: application/json
	api-key: admin-key
	
	{ 
	    "name" : "myazuresqldatasource",
	    "type" : "azuresql",
	    "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
	    "container" : { "name" : "name of the table or view that you want to index" }
	}


Você pode conseguir a cadeia de conexão no [Portal clássico do Azure](https://portal.azure.com); use a opção `ADO.NET connection string`.

Em seguida, crie um índice de destino da Pesquisa do Azure, caso ainda não tenha criado um. É possível fazer isso por meio da [UI do portal](https://portal.azure.com) ou usando a [API de Criação de Índices](https://msdn.microsoft.com/library/azure/dn798941.aspx). Certifique-se de que o esquema de seu índice de destino seja compatível com o esquema da tabela de origem. Confira a tabela a seguir para conhecer o mapeamento entre os tipos de dados de SQL e da Pesquisa do Azure.

****Mapeamento entre tipos de dados SQL e tipos de dados da Pesquisa do Azure

|Tipo de dados SQL | Tipos de campos de índice de destino permitidos |Observações 
|------|-----|----|
|bit|Edm.Boolean, Edm.String| |
|int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String| |
| bigint | Edm.Int64, Edm.String | |
| real, float |Edm.Double, Edm.String | |
| smallmoney, numérico decimal dinheiro | Edm.String| O Azure Search não dá suporte à conversão de tipos decimais em Edm.Double porque isso causaria a perda de precisão |
| char, nchar, varchar, nvarchar | Edm.String<br/>Collection(Edm.String)|Transformar uma coluna de cadeia de caracteres em Collection(Edm.String) requer o uso de uma API Versão 2015-02-28-visualização prévia. Confira [este artigo](search-api-indexers-2015-02-28-Preview.md#create-indexer) para obter detalhes| 
|smalldatetime, datetime, datetime2, date, datetimeoffset |Edm.DateTimeOffset, Edm.String| |
|uniqueidentifer | Edm.String | |
|geografia | Edm.GeographyPoint | Há suporte apenas para instâncias de Geografia do tipo POINT com SRID 4326 (que é o padrão) | | 
|rowversion| N/D |Colunas de linha-versão não podem ser armazenadas no índice de pesquisa, mas podem ser usadas para o controle de alterações | |
| tempo, timespan, binário, varbinário, imagem, xml, geometria, tipos CLR | N/D |Sem suporte |

Por fim, crie o indexador dando um nome a ele e fazendo referência à fonte de dados e ao índice de destino:

	POST https://myservice.search.windows.net/indexers?api-version=2015-02-28 
	Content-Type: application/json
	api-key: admin-key
	
	{ 
	    "name" : "myindexer",
	    "dataSourceName" : "myazuresqldatasource",
	    "targetIndexName" : "target index name"
	}

Um indexador criado dessa maneira não tem um agenda. Ele é executado automaticamente assim que é criado. Você pode executá-lo novamente a qualquer momento usando uma solicitação **executar indexador**:

	POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2015-02-28 
	api-key: admin-key
 
Talvez seja necessário permitir a conexão dos serviços do Azure ao banco de dados. Confira [Conexão a partir do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) para obter instruções sobre como fazer isso.

Para monitorar o status do indexador e o histórico de execução (número de itens indexados, falhas etc.), use uma solicitação **status do indexador**:

	GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2015-02-28 
	api-key: admin-key

A resposta deve parecer com a seguinte:

	{
		"@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
		"status":"running",
		"lastResult": {
			"status":"success",
			"errorMessage":null,
			"startTime":"2015-02-21T00:23:24.957Z",
			"endTime":"2015-02-21T00:36:47.752Z",
			"errors":[],
			"itemsProcessed":1599501,
			"itemsFailed":0,
			"initialTrackingState":null,
			"finalTrackingState":null 
        },
		"executionHistory":
		[
			{
				"status":"success",
				"errorMessage":null,
				"startTime":"2015-02-21T00:23:24.957Z",
				"endTime":"2015-02-21T00:36:47.752Z",
				"errors":[],
				"itemsProcessed":1599501,
				"itemsFailed":0,
				"initialTrackingState":null,
				"finalTrackingState":null 
			},
			... earlier history items 
		]
	}

O histórico de execução contém até 50 execuções mais recentes, classificadas em ordem cronológica inversa (de modo que a execução mais recente apareça em primeiro lugar na resposta). Informações adicionais sobre a resposta podem ser encontradas em [Obter o status do indexador](http://go.microsoft.com/fwlink/p/?LinkId=528198)

## Executar indexadores de acordo com uma agenda

Você também pode organizar o indexador para que execute periodicamente com base em uma agenda. Para fazer isso, basta adicionar a propriedade **schedule** ao criar ou atualizar o indexador. O exemplo a seguir mostra uma solicitação PUT para atualização do indexador:

	PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2015-02-28 
	Content-Type: application/json
	api-key: admin-key 

	{ 
	    "dataSourceName" : "myazuresqldatasource",
	    "targetIndexName" : "target index name",
	    "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
	}

O parâmetro **interval** é necessário. O intervalo refere-se ao tempo entre o início de duas execuções consecutivas do indexador. O menor intervalo permitido é de cinco minutos, e o maior é de um dia. Ele deve ser formatado como um valor XSD de "dayTimeDuration" (um subconjunto restrito de um [valor de duração ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)). O padrão para isso é: `P(nD)(T(nH)(nM))`. Exemplos: `PT15M` para cada 15 minutos, `PT2H` para cada 2 horas.

O **startTime** opcional indica quando as execuções agendadas devem começar; se for omitido, a hora UTC atual será usada. Esse tempo pode estar no passado, nesse caso, a primeira execução será agendada como se o indexador estivesse em execução contínua desde o startTime.

Só é possível ocorrer uma execução de um determinado indexador por vez. Se um indexador já estiver em execução no momento em que o próximo precisar iniciar, a execução será ignorada e retomada no próximo intervalo, supondo que nenhum outro trabalho de indexador esteja em execução.

Vamos considerar um exemplo para tornar isso mais concreto. Vamos supor que o seguinte agendamento por hora está configurado:

	"schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

Veja o que acontece:

1. A primeira execução do indexador começa em, ou em torno de, 1º de março de 2015, meia-noite. UTC.
1. Vamos supor que essa execução demore 20 minutos (ou menos de uma hora).
1. A segunda execução começa em, ou em torno de, 1º de março de 2015, 01:00. 
1. Agora vamos supor que essa execução demore mais de uma hora (para isso realmente ocorrer seria necessário ter uma grande quantidade de documentos, mas é uma ilustração útil), por exemplo, 70 minutos, para que seja concluída aproximadamente às 2h10.
1. Agora são 2:00, hora da terceira execução começar. No entanto, como a segunda execução, a que começou 1h, ainda está ocorrendo, a terceira execução será ignorada. A terceira execução começa às 3h.

Você pode adicionar, alterar ou excluir uma agenda de um indexador existente usando uma solicitação de **indexador PUT**.

## Captura de linhas novas, alteradas e excluídas

Se você estiver usando uma agenda e a tabela contiver um número incomum de linhas, use uma política de detecção de alteração de dados para que o indexador possa recuperar com eficiência apenas as linhas novas ou alteradas sem precisar reindexar a tabela inteira.

### Política de controle integrado de alterações do SQL

Se o banco de dados SQL oferece suporte ao [controle de alterações](https://msdn.microsoft.com/library/bb933875.aspx), recomendamos o uso da **Política de controle integrado de alterações do SQL**. Essa política permite um controle de alterações mais eficiente e também permite que a Pesquisa do Azure identifique linhas excluídas sem precisar adicionar uma coluna explícita de "exclusão reversível" à tabela.

O controle integrado de alterações tem suporte a partir das seguintes versões de banco de dados do SQL Server:
 
- SQL Server 2008 R2 e posterior, se você estiver usando o SQL Server em VMs do Azure. 
- Banco de Dados SQL do Azure V12, se você estiver usando o Banco de Dados SQL do Azure.

Ao usar a política de controle integrado de alterações do SQL, não especifique uma política separada de detecção de exclusão de dados, pois essa política tem suporte interno para identificação de linhas excluídas.

Essa política só pode ser usada com tabelas; ela não pode ser usada com exibições. Você precisa habilitar o controle de alterações para a tabela que está usando antes de poder usar essa política. Confira [Habilitar e desabilitar o controle de alterações](https://msdn.microsoft.com/library/bb964713.aspx) para obter instruções.

Para usar essa política, crie ou atualize a fonte de dados da seguinte maneira:
 
	{ 
	    "name" : "myazuresqldatasource",
	    "type" : "azuresql",
	    "credentials" : { "connectionString" : "connection string" },
	    "container" : { "name" : "table or view name" }, 
	    "dataChangeDetectionPolicy" : {
	       "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
	  }
	}

### Política de detecção de alteração de marca d’água alta

Embora a política de controle integrado de alterações do SQL seja recomendada, não será possível usá-la se os dados estiverem em uma exibição, ou se você estiver usando uma versão mais antiga do Banco de Dados SQL do Azure. Nesse caso, considere o uso da política de marca d'água alta. Essa política pode ser usada se a tabela contiver uma coluna que atenda aos seguintes critérios:

- Todas as inserções especificam um valor para a coluna. 
- Todas as atualizações de um item também alteram o valor da coluna. 
- O valor dessa coluna aumenta com cada alteração.
- As consultas que usam uma cláusula `WHERE` semelhante à `WHERE [High Water Mark Column] > [Current High Water Mark Value]` podem ser executadas com eficiência.

Por exemplo, uma coluna **rowversion** indexada é uma candidata ideal para a coluna de marca d'água alta. Para usar essa política, crie ou atualize a fonte de dados da seguinte maneira:

	{ 
	    "name" : "myazuresqldatasource",
	    "type" : "azuresql",
	    "credentials" : { "connectionString" : "connection string" },
	    "container" : { "name" : "table or view name" }, 
	    "dataChangeDetectionPolicy" : {
	       "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
	       "highWaterMarkColumnName" : "[a row version or last_updated column name]" 
	  }
	}

### Política de detecção de exclusão de coluna por exclusão reversível

Quando as linhas são excluídas da tabela de origem, provavelmente você deseja excluí-las também do índice de pesquisa. Se você usar a política de controle integrado de alterações do SQL, isso será resolvido para você. No entanto, a política de controle de alterações de marca d'água alta não ajuda você com relação às linhas excluídas. O que fazer?

Se as linhas forem fisicamente removidas da tabela, você deu azar, já que não é possível inferir a presença de registros que não existem mais. No entanto, você pode usar a técnica de "exclusão reversível" para marcar linhas como excluídas logicamente sem removê-las da tabela. Faça isso adicionando uma coluna e marcando as linhas como excluídas usando um valor de marcador nessa coluna.

Ao usar a técnica de exclusão reversível, você pode especificar a política de exclusão reversível da seguinte maneira ao criar ou atualizar a fonte de dados:

	{ 
	    …, 
	    "dataDeletionDetectionPolicy" : { 
	       "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
	       "softDeleteColumnName" : "[a column name]", 
	       "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]" 
	    }
	}

Observe que **softDeleteMarkerValue** deve ser uma cadeia de caracteres – use a representação de cadeia de caracteres de seu valor real. Por exemplo, se você tiver uma coluna de inteiros na qual as linhas excluídas são marcadas com o valor 1, use `"1"`; se você tiver uma coluna BIT na qual as linhas excluídas são marcadas com o valor booliano real, use `"True"`.

## Personalizar o indexador do SQL Azure
 
Você pode personalizar certos aspectos do comportamento do indexador (por exemplo, tamanho do lote, número de documentos que podem ser ignorados antes que uma execução de indexador falhe, entre outros). Para obter mais detalhes, veja [Personalização de indexador do Pesquisa do Azure](search-indexers-customization.md).

## Perguntas frequentes

**P:** Posso usar o indexador do SQL Azure com bancos de dados SQL em execução em VMs de IaaS no Azure?

R: Sim, desde que você permita que os serviços do Azure se conectem ao banco de dados abrindo as portas apropriadas.

**P:** Posso usar o indexador do SQL Azure com bancos de dados SQL em execução no local?

R: Não recomendamos ou damos suporte a isso, pois isso exigiria a abertura de seus bancos de dados ao tráfego da Internet.

**P:** Posso usar o indexador do SQL Azure com outros bancos de dados que não o SQL Server em execução em IaaS no Azure?

R: Não damos suporte a essa situação, pois não testamos o indexador com qualquer outro banco de dados que não o SQL Server.

**P:** Posso criar vários indexadores em execução com base em uma agenda?

R: Sim. No entanto, somente um indexador pode ser executado por vez em um nó. Se você precisar de vários indexadores em execução simultaneamente, considere o aumento de seu serviço de pesquisa para mais de uma unidade de pesquisa.

**P:** executar um indexador afeta minha carga de trabalho de consulta?

R: Sim. O indexador é executado em um dos nós em seu serviço de pesquisa, e os recursos do nó são compartilhados entre a indexação e o atendimento ao tráfego de consultas e outras solicitações da API. Se você executar cargas de trabalho com indexação e consultas intensas e receber uma taxa alta de erros 503 ou tempos de resposta maiores, considere o aumento de seu serviço de pesquisa.

<!---HONumber=AcomDC_0211_2016-->