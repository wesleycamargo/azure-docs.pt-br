<properties 
	pageTitle="Mover dados do MongoDB usando o Data Factory | Microsoft Azure" 
	description="Saiba mais sobre como mover dados do banco de dados MongoDB usando o Azure Data Factory." 
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
	ms.date="08/04/2016" 
	ms.author="spelluru"/>

# Mover dados do MongoDB usando o Azure Data Factory

Este artigo descreve como você pode usar a atividade de cópia em uma fábrica de dados do Azure para mover dados de um armazenamento de dados MongoDB local para outro armazenamento de dados. Este artigo se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral da movimentação de dados com a atividade de cópia e as combinações de armazenamento de dados de origem/do coletor para as quais a atividade de cópia oferece suporte.

O serviço Data Factory dá suporte à conexão com fontes MongoDB locais usando o Gateway de Gerenciamento de Dados. Veja o artigo [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md) para saber mais sobre o Gateway de Gerenciamento de Dados e o artigo [Mover dados do local para a nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter instruções passo a passo sobre como configurar o gateway de um pipeline de dados para mover dados.

> [AZURE.NOTE] É necessário utilizar o gateway para se conectar ao MongoDB, mesmo se ele estiver hospedado em VMs do Azure IaaS. Se estiver tentando se conectar a uma instância do MongoDB hospedada na nuvem, você também pode instalar a instância do gateway na VM do IaaS.

Atualmente, o Data Factory dá suporte apenas para a movimentação de dados do MongoDB para outros armazenamentos de dados, mas não para a movimentação de dados de outros armazenamentos de dados para o MongoDB.

## Pré-requisitos
Para o serviço Azure Data Factory poder se conectar ao banco de dados MongoDB no local, você deve instalar o seguinte:

- Gateway de Gerenciamento de Dados 2.0 ou superior no mesmo computador que hospeda o banco de dados ou em um computador separado para evitar disputa por recursos com o banco de dados. O Gateway de Gerenciamento de Dados é um software que conecta fontes de dados locais a serviços de nuvem de maneira segura e gerenciada. Confira o artigo [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md) para obter todos os detalhes sobre o Gateway de Gerenciamento de Dados.
  
	Quando você instala o gateway, ele instala automaticamente um driver ODBC do Microsoft MongoDB usado para se conectar ao banco de dados MongoDB.

## Assistente para Copiar Dados
A maneira mais fácil de criar um pipeline que copie dados de um banco de dados Cassandra para qualquer um dos repositórios de dados compatíveis é usar o Assistente de cópia de dados. Confira [Tutorial: Criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente de cópia de dados.

O exemplo a seguir fornece as definições de JSON de exemplo que você pode usar para criar um pipeline usando o [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

## Exemplo: copiar dados do MongoDB para o Blob do Azure
Este exemplo mostra como copiar dados de um banco de dados MongoDB local para um Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** para qualquer uma das fontes declaradas [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a atividade de cópia no Azure Data Factory.
 
O exemplo tem as seguintes entidades de data factory:

1.	Um serviço vinculado do tipo [OnPremisesMongoDb](#linked-service-properties).
2.	Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [MongoDbCollection](#dataset-type-properties).
4.	Um [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	Um [pipeline](data-factory-create-pipelines.md) com a Atividade de Cópia que usa [MongoDbSource](#copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia dados de um resultado de consulta no banco de dados MongoDB para um blob a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

Como uma primeira etapa, configure o gateway de gerenciamento de dados de acordo com as instruções no artigo [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md).

**Serviço vinculado ao MongoDB**

	{ 
	    "name": "OnPremisesMongoDbLinkedService", 
	    "properties": 
	    { 
	        "type": "OnPremisesMongoDb", 
	        "typeProperties": 
	        { 
	            "authenticationType": "<Basic or Anonymous>", 
	            "server": "< The IP address or host name of the MongoDB server >",  
				"port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>", 
	            "username": "<username>", 
	            "password": "<password>",
	           "authSource": "< The database that you want to use to check your credentials for authentication. >",
	            "databaseName": "<database name>",
	            "gatewayName": "<mygateway>"
	        } 
		}
	} 


**Serviço vinculado de armazenamento do Azure**

	{
	  "name": "AzureStorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Conjunto de dados de entrada do MongoDB** Configurar “external” como “true” informa ao serviço Data Factory que a tabela é externa ao Data Factory e não é produzido por uma atividade no Data Factory.
	
	{
	     "name":  "MongoDbInputDataset",
	    "properties": { 
	        "type": "MongoDbCollection", 
	        "linkedServiceName": "OnPremisesMongoDbLinkedService", 
	        "typeProperties": { 
	            "collectionName": "<Collection name>"	
	        }, 
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
			"external": true
	    } 
	}



**Conjunto de dados de saída de Blob do Azure**

Os dados são gravados em um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

	{
	    "name": "AzureBlobOutputDataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/frommongodb/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	            "format": {
	                "type": "TextFormat",
	                "rowDelimiter": "\n",
	                "columnDelimiter": "\t"
	            },
	            "partitionedBy": [
	                {
	                    "name": "Year",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "yyyy"
	                    }
	                },
	                {
	                    "name": "Month",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%M"
	                    }
	                },
	                {
	                    "name": "Day",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%d"
	                    }
	                },
	                {
	                    "name": "Hour",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%H"
	                    }
	                }
	            ]
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}



**Pipeline com Atividade de cópia**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída acima e agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **MongoDbSource** e o tipo **sink** está definido como **BlobSink**. A consulta SQL especificada para a propriedade **query** seleciona os dados na última hora para copiar.
	
	{
	    "name": "CopyMongoDBToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "MongoDbSource",
	                        "query": "$$Text.Format('select * from  MyTable where LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "MongoDbInputDataset"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobOutputDataSet"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1
	                },
	                "scheduler": {
	                    "frequency": "Hour",
	                    "interval": 1
	                },
	                "name": "MongoDBToAzureBlob"
	            }
	        ],
	        "start": "2016-06-01T18:00:00Z",
	        "end": "2016-06-01T19:00:00Z"
	    }
	}


## Propriedades do serviço vinculado

A tabela a seguir fornece a descrição para elementos JSON específicos do serviço vinculado **OnPremisesMongoDB**.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- | 
| type | A propriedade do tipo deve ser definida como: **OnPremisesMongoDb** | Sim |
| server | Endereço IP ou nome do host do servidor MongoDB. | Sim | 
| porta | A porta TCP usada pelo servidor MongoDB para ouvir conexões de cliente. | Opcional, valor padrão: 27017 |
| authenticationType | Básica ou Anônima. | Sim | 
| Nome de Usuário | Conta de usuário para acessar o MongoDB. | Sim (se a autenticação básica for usada).|
| Senha | Senha do usuário. |	Sim (se a autenticação básica for usada). | 
| authSource | Nome do banco de dados MongoDB que você deseja usar para verificar suas credenciais para autenticação. | Opcional (se a autenticação básica for usada). Padrão: usa a conta de administrador e o banco de dados especificado usando a propriedade databaseName. |  
| databaseName | Nome do banco de dados MongoDB que você deseja acessar. | Sim |
| gatewayName | Nome do gateway que acessa o armazenamento de dados. | Sim | 
| encryptedCredential | Credencial criptografada pelo gateway. | Opcional |


Veja [Definir credenciais e segurança](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) para obter detalhes sobre como definir credenciais para uma fonte de dados MongoDB local.

## Propriedades do tipo de conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). Seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção typeProperties para o conjunto de dados do tipo **MongoDbCollection** tem as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| collectionName | Nome da coleção no banco de dados MongoDB. | Sim | 

## Propriedades do tipo da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, consulte o artigo [Criando pipelines](data-factory-create-pipelines.md). Propriedades como nome, descrição, tabelas de entrada e saída, diversas políticas, etc. estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis na seção **typeProperties** da atividade, por outro lado, variam de acordo com cada tipo de atividade e, no caso de Atividade de cópia, variam dependendo dos tipos de fontes e coletores.

No caso da Atividade de Cópia, quando a fonte é do tipo **MongoDbSource**, as seguintes propriedades estão disponíveis na seção typeProperties:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------------- | -------- |
| query | Utiliza a consulta personalizada para ler os dados. | Cadeia de consulta SQL-92. Por exemplo: select * from MyTable. | Não (se **collectionName** de **dataset** for especificado) | 

## Esquema do Data Factory
O serviço Azure Data Factory infere o esquema de uma coleção do MongoDB usando os 100 documentos mais recentes na coleção. Se esses 100 documentos não contiverem o esquema completo, algumas colunas poderão ser ignoradas durante a operação de cópia.

## Mapeamento de tipo para o MongoDB

Conforme mencionado no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md), a Atividade de cópia executa conversões automáticas de tipo de fonte para tipos de coletor, com a abordagem em duas etapas descritas a seguir:

1. Converter de tipos de fonte nativos para o tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados para o MongoDB, os seguintes mapeamentos serão usados dos tipos do MongoDB para os tipos do .NET.

| Tipo do MongoDB | Tipo .NET Framework |
| ------------------- | ------------------- | 
| Binário | Byte |
| Booliano | Booliano |
| Data | DateTime |
| NumberDouble | Duplo |
| NumberInt | Int32 |
| NumberLong | Int64 |
| ObjectID | Cadeia de caracteres |
| Cadeia de caracteres | Cadeia de caracteres |
| UUID | Guid | 
| Objeto | Renormalizado para colunas simples com “\_” como separador aninhado |

> [AZURE.NOTE]  
Para saber mais sobre o suporte para matrizes usando tabelas virtuais, consulte a seção [Suporte para tipos complexos usando tabelas virtuais](#support-for-complex-types-using-virtual-tables) abaixo.

Os tipos de dados do MongoDB não têm suporte neste momento: DBPointer, JavaScript, Max/Min key, Regular Expression, Symbol, Timestamp, Undefined

## Suporte para tipos complexos usando tabelas virtuais
O Azure Data Factory usa um driver ODBC interno para se conectar ao banco de dados MongoDB e copiar dados dele. Para tipos complexos, como matrizes ou objetos com tipos diferentes nos documentos, o driver renormalizará os dados em tabelas virtuais correspondentes. Especificamente, se uma tabela contiver tais colunas, o driver vai gerar as seguintes tabelas virtuais:

-	Uma **tabela base**, que contém os mesmos dados da tabela real, exceto nas colunas de tipo complexo. A tabela base usa o mesmo nome da tabela real que ela representa.
-	Uma **tabela virtual** para cada coluna de tipo complexo, que expande os dados aninhados. As tabelas virtuais ão nomeadas usando o nome da tabela real, um separador “\_” e o nome da matriz ou objeto.

As tabelas virtuais se referem aos dados na tabela real, permitindo que o driver acesse dados desordenados. Veja a seção Exemplo abaixo para obter detalhes. Você pode acessar o conteúdo das matrizes do MongoDB consultando e unindo as tabelas virtuais.

Você pode aproveitar o [Assistente de Cópia](data-factory-data-movement-activities.md#data-factory-copy-wizard) para exibir intuitivamente a lista de tabelas no banco de dados MongoDB, incluindo as tabelas virtuais, e visualizar os dados internos. Também é possível construir uma consulta no Assistente de Cópia e validar para ver o resultado.

### Exemplo

Por exemplo, "TabelaDeExemplo" abaixo é uma tabela do MongoDB com uma coluna com uma matriz de Objetos em cada célula – Faturas, e uma coluna com uma matriz de tipos escalares – Classificações.

\_id | Nome do Cliente | Faturas | Nível de Serviço | Classificações
--- | ------------- | -------- | ------------- | -------
1111 | ABC | [{invoice\_id:”123”, item:”torradeira”, price:”456”, discount:”0,2”}, {invoice\_id:”124”, item:”forno”,price: ”1235”,discount: ”0,2”}] | Silver | [5,6]
2222 | XYZ | [{invoice\_id:”135”, item:”geladeira”,price: ”12543”,discount: ”0,0”}] | Gold | [1,2]

O driver geraria várias tabelas virtuais para representar essa tabela única. A primeira tabela virtual é a tabela base chamada "ExampleTable", mostra abaixo. A tabela base contém todos os dados da tabela original, mas os dados das matrizes foram omitidos e serão expandidos nas tabelas virtuais.

\_id | Nome do Cliente | Nível de Serviço
--- | ------------- | -------------
1111 | ABC | Silver
2222 | XYZ | Gold

As tabelas a seguir mostram as tabelas virtuais que representam as matrizes originais no exemplo. Cada uma dessas tabelas contém os seguintes

- Uma referência à coluna de chave primária original correspondente para a linha da matriz original (por meio da coluna \_id)
- Uma indica da posição dos dados dentro da matriz original
- Os dados expandidos para cada elemento da matriz

Tabela "TabelaDeExemplo\_Faturas":

\_id | TabelaDeExemplo\_Faturas\_dim1\_idx | invoice\_id | item | preço | Desconto
--- | -------------- | ---------- | ---- | ----- | --------
1111 | 0 | 123 | torradeira | 456 | 0,2
1111 | 1 | 124 | forno | 1235 | 0,2
2222 | 0 | 135 | geladeira | 12543 | 0,0

Tabela "TabelaDeExemplo\_Classificações":

\_id | TabelaDeExemplo\_Classificações\_dim1\_idx | TabelaDeExemplo\_Classificações
--- | ------------- | -------------
1111 | 0 | 5
1111 | 1 | 6
2222 | 0 | 1
2222 | 1 | 2

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## Desempenho e Ajuste  
Veja o [Guia de Desempenho e Ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho e a movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.

## Próximas etapas
Veja o artigo [Mover dados entre locais e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter instruções passo a passo para criar um pipeline de dados que move dados de um armazenamento de dados local para um armazenamento de dados do Azure.

<!---HONumber=AcomDC_0810_2016-->