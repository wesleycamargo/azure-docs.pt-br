<properties 
	pageTitle="Conjuntos de dados no Azure Data Factory | Microsoft Azure" 
	description="Entenda os conjuntos de dados do Azure Data Factory e aprenda como criá-los." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/08/2016" 
	ms.author="spelluru"/>

# Conjuntos de dados no Azure Data Factory
Conjuntos de dados no Azure Data Factory são referências/ponteiros nomeados para os dados que você deseja usar como uma entrada ou uma saída de uma atividade em um pipeline. Conjuntos de dados identificam dados em armazenamentos de dados diferentes, inclusive tabelas, arquivos, pastas e documentos.

Um **serviço vinculado** define as informações necessárias para o Azure Data Factory se **conectar** a um **armazenamento de dados** (como a Conta de Armazenamento do Azure e o Banco de Dados SQL do Azure) ou a uma **computação** (como o HDInsight do Azure e o Lote do Azure). O serviço vinculado que define o mecanismo (endereço, protocolo, esquema de autenticação, etc...) para acessar o armazenamento de dados ou computação.

Para obter uma lista do armazenamento de dados com suporte de serviços vinculados, confira [Fontes de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores). Clique em uma fonte de dados na tabela para ir até o tópico que fornece detalhes sobre como criar/configurar um serviço vinculado para esse armazenamento de dados.

Para obter uma lista de computação com os serviços vinculados com suporte, confira [Serviços vinculados de computação](data-factory-compute-linked-services.md). Para entender as atividades que usam esses serviços vinculados, confira [Data Transformation Activities](data-factory-data-transformation-activities.md) (Atividades de transformação de dados).

Um **conjunto de dados** no Data Factory representa estruturas de dados em um armazenamento de dados representadas por um **serviço vinculado de armazenamento de dados**, por exemplo, um contêiner de blob em uma conta de armazenamento do Azure, uma tabela em um Banco de Dados SQL do Azure). Ele pode ser usado como entrada ou saída de uma atividade em um pipeline. Depois de criar os conjuntos de dados, você pode usá-los com atividades no pipeline. Por exemplo, você pode ter um conjunto de dados como um conjunto de dados de entrada/saída de uma atividade de Cópia/HDInsightHive.

> [AZURE.NOTE] Se você for novo no Azure Data Factory, confira [Introdução ao Azure Data Factory](data-factory-introduction.md) para ter uma visão geral do serviço Azure Data Factory e [Compilar sua primeira data factory](data-factory-build-your-first-pipeline.md) para ver um tutorial para criar sua primeira data factory. Esses dois artigos fornecem as informações básicas necessárias para entender melhor este artigo.


## Definir conjuntos de dados
Um conjunto de dados no Azure Data Factory é definido da seguinte maneira:


	{
	    "name": "<name of dataset>",
	    "properties": {
	        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
			"external": <boolean flag to indicate external data. only for input datasets>,
	        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
	        "structure": [
	            {
	                "name": "<Name of the column>",
	                "type": "<Name of the type>"
	            }
	        ],
	        "typeProperties": {
	            "<type specific property>": "<value>",
				"<type specific property 2>": "<value 2>",
	        },
	        "availability": {
	            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
	            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
	        },
	       "policy": 
	        {      
	        }
	    }
	}

A tabela a seguir descreve as propriedades no JSON acima:

| Propriedade | Descrição | Obrigatório | Padrão |
| -------- | ----------- | -------- | ------- |
| name | Nome do conjunto de dados. Confira [Azure Data Factory - Regras de Nomenclatura](data-factory-naming-rules.md) para ver as regras de nomenclatura. | Sim | ND |
| type | Tipo de conjunto de dados. Especifique um dos tipos com suporte no Azure Data Factory (por exemplo: AzureBlob, AzureSqlTable). <br/><br/>Confira [Tipo de conjunto de dados](#Type) para obter detalhes. | Sim | ND |
| estrutura | Esquema do conjunto de dados<br/><br/>Confira a seção [Estrutura do conjunto de dados](#Structure) para obter mais detalhes | Nº | ND |
| typeProperties | Propriedades que correspondem ao tipo selecionado. Confira a seção [Tipo de conjunto de dados](#Type) para obter detalhes sobre os tipos com suporte e suas propriedades. | Sim | ND |
| externo | Sinalizador booliano para especificar se um conjunto de dados é explicitamente produzido por um pipeline de data factory ou não. | Não | false | 
| disponibilidade | Define a janela de processamento ou o modelo de fatiamento para a produção de conjunto de dados. <br/><br/>Confira o tópico [Disponibilidade do conjunto de dados](#Availability) para obter mais detalhes<br/><br/>Confira o artigo [Agendamento e execução](data-factory-scheduling-and-execution.md) para obter mais detalhes sobre o modelo de fatiamento do conjunto de dados | Sim | ND
| policy | Define os critérios ou a condição que as fatias de conjunto de dados devem atender. <br/><br/>Confira o tópico [Política de conjunto de dados](#Policy) para obter mais detalhes | Não | ND |

### Exemplo

Abaixo está um exemplo de um conjunto de dados que representa uma tabela chamada **MyTable** no **Banco de Dados SQL do Azure**.

	{
	    "name": "DatasetSample",
	    "properties": {
	        "type": "AzureSqlTable",
	        "linkedServiceName": "AzureSqlLinkedService",
	        "typeProperties": 
	        {
	            "tableName": "MyTable"
	        },
	        "availability": 
	        {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

Observe o seguinte:

- O tipo foi definido como AzureSqlTable.
- A propriedade de tipo tableName (específica do tipo AzureSqlTable) foi definida como MyTable.
- linkedServiceName refere-se a um serviço vinculado do tipo AzureSqlDatabase. Consulte a definição do serviço vinculado abaixo. 
- A frequência de disponibilidade foi definida como Dia e o intervalo foi definido como 1, o que significa que a fatia é produzida diariamente.  

AzureSqlLinkedService é definido da seguinte maneira:

	{
	    "name": "AzureSqlLinkedService",
	    "properties": {
	        "type": "AzureSqlDatabase",
	        "description": "",
	        "typeProperties": {
	            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
	        }
	    }
	}

No JSON acima:

- O tipo foi definido como AzureSqlDatabase
- A propriedade de tipo connectionString especifica informações para se conectar a um banco de dados SQL do Azure.  


Como você pode ver, o serviço vinculado define como se conectar a um banco de dados SQL do Azure e o conjunto de dados define qual tabela é usada como uma entrada/saída na data factory. A seção de atividade no JSON do seu [pipeline](data-factory-create-pipelines.md) especifica se o conjunto de dados é usado como um conjunto de dados de entrada ou saída.


> [AZURE.IMPORTANT] A menos que um conjunto de dados seja produzido pelo Azure Data Factory, ele deverá ser marcado como **externo**. Isso geralmente se aplica à(s) entrada(s) da primeira atividade em um pipeline.

## <a name="Type"></a> Tipo de conjunto de dados
As fontes de dados com suporte e os tipos de conjunto de dados são alinhados. Confira os tópicos referenciados no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md#supported-data-stores) para obter informações sobre os tipos e a configuração de conjuntos de dados. Por exemplo, se você estiver usando dados de um Banco de Dados SQL do Azure, clique em Banco de Dados SQL do Azure na lista de repositórios de dados com suporte para ver informações detalhadas sobre como usar o Banco de Dados SQL do Azure como um armazenamento de dados de origem ou coletor.

## <a name="Structure"></a>Estrutura do conjunto de dados
A seção **estrutura** declara o esquema do conjunto de dados. Ela contém uma coleção de nomes e tipos de dados das colunas. No exemplo a seguir, o conjunto de dados tem três colunas: slicetimestamp, projectname e pageviews, sendo dos seguintes tipos: String, String e Decimal, respectivamente.

	structure:  
	[ 
	    { "name": "slicetimestamp", "type": "String"},
	    { "name": "projectname", "type": "String"},
	    { "name": "pageviews", "type": "Decimal"}
	]

## <a name="Availability"></a> Disponibilidade do conjunto de dados
A seção **disponibilidade** em um conjunto de dados define a janela de processamento (horário, diário, semanal, etc.) ou o modelo de divisão para o conjunto de dados. Consulte o artigo [Cronograma e Execução](data-factory-scheduling-and-execution.md) para obter mais detalhes sobre o modelo de divisão e dependência de conjunto de dados.

A seção de disponibilidade abaixo especifica que o conjunto de dados seja produzido por hora no caso de um conjunto de dados de saída (ou) disponível por hora no caso do conjunto de dados de entrada.

	"availability":	
	{	
		"frequency": "Hour",		
		"interval": 1	
	}

A tabela a seguir descreve as propriedades que você pode usar na seção de disponibilidade.

| Propriedade | Descrição | Obrigatório | Padrão |
| -------- | ----------- | -------- | ------- |
| frequência | Especifica a unidade de tempo para a produção da fatia de conjunto de dados.<br/><br/>**Frequência com suporte**: Minuto, Hora, Dia, Semana, Mês | Sim | ND |
| intervalo | Especifica um multiplicador para frequência<br/><br/>"Intervalo x da frequência" determina a frequência com que a fatia é produzida.<br/><br/>Se você precisar que o conjunto de dados seja dividido por hora, defina **Frequência** como **Hora** e o **Intervalo** como **1**.<br/><br/>**Observação:** caso você especifique a frequência como minuto, recomendamos que defina o intervalo como não inferior a 15 | Sim | ND |
| estilo | Especifica se a fatia deve ser produzida no início/término do intervalo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Se a frequência for definida como Mês e o estilo como EndOfInterval, a fatia será produzida no último dia do mês. Se o estilo for definido como StartOfInterval, a fatia será produzida no primeiro dia do mês.<br/><br/>Se a Frequência for definida como Dia e o estilo como EndOfInterval, a fatia será gerada na última hora do dia.<br/><br/>Se a Frequência for definida como Hora e o estilo como EndOfInterval, a fatia será produzida ao final da hora. Por exemplo, para uma fatia de período 13h – 14h, a fatia é produzida às 14h. | Não | EndOfInterval |
| anchorDateTime | Define a posição absoluta no tempo usado pelo agendador para computar limites de fatia do conjunto de dados.<br/><br/>**Observação:** se o AnchorDateTime tiver partes de data mais granulares do que a frequência, as partes mais granulares serão ignoradas. <br/><br/>Por exemplo, se o **intervalo** for **por hora** (frequência: hora e intervalo: 1) e o **AnchorDateTime** contiver **minutos e segundos**, as partes **minutos e segundos** do AnchorDateTime serão ignoradas. | Não | 01/01/0001 |
| deslocamento | O intervalo de tempo pelo qual o início e término de todas as fatias de conjunto de dados são deslocados. <br/><br/>**Observação:** se anchorDateTime e o deslocamento forem especificados, o resultado será a mudança combinada. | Não | ND |

### exemplo de deslocamento

Divisões diárias que iniciam às 6h, em vez da meia-noite do padrão.

	"availability":
	{
		"frequency": "Day",
		"interval": 1,
		"offset": "06:00:00"
	}

A **frequência** é definida como **Mês** e o **intervalo** é definido como **1** (uma vez por mês): se você deseja que a fatia a ser produzida no dia 9 de cada mês seja às 6h, defina o deslocamento como "09.06:00:00". Lembre-se de que o fuso é UTC.

Para uma agenda de 12 meses (frequência = mês; intervalo = 12), o deslocamento: 60.00:00:00 significa cada ano em 1º ou 2 de março (60 dias desde o início do ano se estilo = StartOfInterval), dependendo do ano ser ano bissexto ou não.

### Exemplo de anchorDateTime

**Exemplo:** fatias de conjuntos de dados de 23 horas que começam em 2007-04-19T08:00:00

	"availability":	
	{	
		"frequency": "Hour",		
		"interval": 23,	
		"anchorDateTime":"2007-04-19T08:00:00"	
	}

### Exemplo de deslocamento/estilo

Se precisar de um conjunto de dados mensalmente em uma data e hora específicas (digamos que seja no dia 3 de cada mês às 8h), você poderá usar a marcação **deslocamento** para definir a data e a hora de execução.

	{
	  "name": "MyDataset",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "availability": {
	      "frequency": "Month",
	      "interval": 1,
	      "offset": "3.08:10:00",
	      "style": "StartOfInterval"
	    }
	  }
	}


## <a name="Policy"></a>Política de conjunto de dados

A seção **política** na definição do conjunto de dados define os critérios ou a condição que as divisões de conjunto de dados devem atender.

### Políticas de validação

| Nome da política | Descrição | Aplicado a | Obrigatório | Padrão |
| ----------- | ----------- | ---------- | -------- | ------- |
| minimumSizeMB | Valida se os dados em um **blob do Azure** atenderem aos requisitos de tamanho mínimo (em megabytes). | Blob do Azure | Não | ND |
|minimumRows | Valida se os dados em um **Banco de Dados SQL do Azure** ou uma **tabela do Azure** contiverem o número mínimo de linhas. | <ul><li>Banco de Dados SQL Azure</li><li>Tabela do Azure</li></ul> | Não | ND

#### Exemplos

**minimumSizeMB:**

	"policy":
	
	{
	    "validation":
	    {
	        "minimumSizeMB": 10.0
	    }
	}

**minimumRows**

	"policy":
	{
		"validation":
		{
			"minimumRows": 100
		}
	}

### Conjuntos de dados externos

Conjuntos de dados externos são aqueles que não são produzidos por um pipeline em execução na data factory. Se o conjunto de dados estiver marcado como **externo**, a política **ExternalData** poderá ser definida para influenciar o comportamento da disponibilidade da divisão do conjunto de dados.

A menos que um conjunto de dados seja produzido pelo Azure Data Factory, ele deverá ser marcado como **externo**. Geralmente, isso se aplica a entradas de primeira atividade em um pipeline, a menos que uma atividade ou encadeamento de pipeline seja utilizado.

| Nome | Descrição | Obrigatório | Valor Padrão |
| ---- | ----------- | -------- | -------------- |
| dataDelay | Tempo para esperar a verificação na disponibilidade dos dados externos de uma determinada divisão. Por exemplo, se os dados tiverem que estar disponíveis por hora, a verificação para confirmar se os dados externos estão realmente disponíveis e se a fatia correspondente está Pronta poderá ser atrasada por dataDelay.<br/><br/>Só se aplica à hora atual. Por exemplo, se agora for 13h e esse valor for 10 minutos, a validação será iniciada às 13h10.<br/><br/>Essa configuração não afeta as fatias no passado (fatias com a Hora de Término da Fatia + dataDelay < agora serão processadas sem atraso).<br/><br/>A hora acima de 23 horas e 59 minutos terá que ser especificada usando o formato dia.horas:minutos:segundos. Por exemplo, para especificar 24 horas, não use 24:00:00; em vez disso, use 1.00:00:00. Se você usar 24:00:00, esse valor será tratado como 24 dias (24.00:00:00). Para um dia e quatro horas, especifique 1.04:00:00. | Não | 0 |
| retryInterval | O tempo de espera entre uma falha e a próxima tentativa de repetição. Aplica-se a hora atual. Se o anterior falhou, podemos esperar muito tempo após a última tentativa. <br/><br/>Se agora for 13h, iniciaremos a primeira tentativa. Se a duração para concluir a primeira verificação de validação for 1 minuto e a operação tiver falhado, a próxima repetição será 1:00 + 1 min (duração) + 1min (intervalo de repetição) = 13h02. <br/><br/>Para fatias no passado, não haverá nenhum atraso. A repetição acontecerá imediatamente. | Não | 00:01:00 (1 minuto) | 
| retryTimeout | O tempo limite para cada tentativa de repetição.<br/><br/>Se for definido como 10 minutos, a validação deverá ser concluída em 10 minutos. Se demorar mais de 10 minutos para executar a validação, a repetição atingirá o tempo limite.<br/><br/>Se todas as tentativas para a validação excederem o tempo limite, a fatia será marcada como TimedOut. | Não | 00:10:00 (10 minutos) |
| maximumRetry | Número de vezes para verificar a disponibilidade dos dados externos. O valor máximo permitido é 10. | Não | 3 | 

## Conjuntos de dados com escopo
Você pode criar conjuntos de dados que estão no escopo de um pipeline usando a propriedade **datasets**. Esses conjuntos de dados podem ser apenas usado por atividades dentro deste pipeline, não por atividades em outros pipelines. O exemplo a seguir define um pipeline com dois conjuntos de dados - InputDataset-rdc and OutputDataset-rdc - a serem usados dentro do pipeline.

> [AZURE.IMPORTANT] Há suporte apenas para conjuntos de dados com escopo com pipelines avulsos (**pipelineMode** definido como **OneTime**). Confira [Pipeline avulso](data-factory-scheduling-and-execution.md#onetime-pipeline) para obter detalhes.

	{
	    "name": "CopyPipeline-rdc",
	    "properties": {
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "BlobSource",
	                        "recursive": false
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "InputDataset-rdc"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "OutputDataset-rdc"
	                    }
	                ],
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1,
	                    "style": "StartOfInterval"
	                },
	                "name": "CopyActivity-0"
	            }
	        ],
	        "start": "2016-02-28T00:00:00Z",
	        "end": "2016-02-28T00:00:00Z",
	        "isPaused": false,
	        "pipelineMode": "OneTime",
	        "expirationTime": "15.00:00:00",
	        "datasets": [
	            {
	                "name": "InputDataset-rdc",
	                "properties": {
	                    "type": "AzureBlob",
	                    "linkedServiceName": "InputLinkedService-rdc",
	                    "typeProperties": {
	                        "fileName": "emp.txt",
	                        "folderPath": "adftutorial/input",
	                        "format": {
	                            "type": "TextFormat",
	                            "rowDelimiter": "\n",
	                            "columnDelimiter": ","
	                        }
	                    },
	                    "availability": {
	                        "frequency": "Day",
	                        "interval": 1
	                    },
	                    "external": true,
	                    "policy": {}
	                }
	            },
	            {
	                "name": "OutputDataset-rdc",
	                "properties": {
	                    "type": "AzureBlob",
	                    "linkedServiceName": "OutputLinkedService-rdc",
	                    "typeProperties": {
	                        "fileName": "emp.txt",
	                        "folderPath": "adftutorial/output",
	                        "format": {
	                            "type": "TextFormat",
	                            "rowDelimiter": "\n",
	                            "columnDelimiter": ","
	                        }
	                    },
	                    "availability": {
	                        "frequency": "Day",
	                        "interval": 1
	                    },
	                    "external": false,
	                    "policy": {}
	                }
	            }
	        ]
	    }
	}

<!---HONumber=AcomDC_0608_2016-->