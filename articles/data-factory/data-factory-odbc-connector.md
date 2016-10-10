<properties 
	pageTitle="Mover dados de armazenamentos de dados ODBC | Azure Data Factory" 
	description="Saiba mais sobre como mover dados de armazenamentos de dados ODBC usando o Azure Data Factory." 
	services="data-factory" 
	documentationCenter="" 
	authors="linda33wj" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/12/2016" 
	ms.author="jingwang"/>

# Mover dados de armazenamentos de dados ODBC usando o Azure Data Factory
Este artigo descreve como você pode usar a atividade de cópia em uma data factory do Azure para mover dados de um armazenamento de dados ODBC local para outro armazenamento de dados. Este artigo se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral de movimentação de dados com a atividade de cópia e combinações de armazenamento de dados com suporte.

Atualmente, o data factory dá suporte apenas à movimentação de dados de um repositório de dados ODBC local para outros repositórios de dados. Ele não oferece suporte à movimentação de dados de outros repositórios de dados para um repositório de dados ODBC local.


## Habilitando a conectividade
O serviço Data Factory dá suporte à conexão com fontes ODBC locais usando o Gateway de Gerenciamento de Dados. Consulte o artigo [movendo dados entre pontos locais e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) para saber mais sobre o Gateway de gerenciamento de dados e obter instruções passo a passo de como configurar o gateway. Use o gateway para se conectar a um repositório de dados ODBC, mesmo se ele estiver hospedado em uma VM IaaS do Azure.

Você pode instalar o gateway no mesmo computador local ou na VM do Azure como o repositório de dados ODBC. No entanto, recomendamos que você instale o gateway em uma máquina separada/VM IaaS Azure para evitar a contenção de recursos e para melhorar o desempenho. Quando você instalar o gateway em um computador separado, o computador deverá ser capaz de acessar o computador com o armazenamento de dados ODBC.

Além do Gateway de Gerenciamento de Dados, você também precisa instalar o driver ODBC para o armazenamento de dados no computador do gateway.

> [AZURE.NOTE] Consulte [Solucionar problemas de gateway](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) para ver dicas sobre como solucionar os problemas relacionados à conexão/gateway.

## Assistente de cópia de dados
A maneira mais fácil de criar um pipeline que copia dados de uma fonte ODBC é usar o Assistente para cópia de dados. Confira [Tutorial: Criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente de cópia de dados.

Os exemplos a seguir fornecem amostras de definições de JSON que você pode usar para criar um pipeline usando o [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados de uma fonte ODBC para um Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a Atividade de Cópia no Azure Data Factory.


## Exemplo: Copiar dados do armazenamento de dados ODBC para o Blob do Azure

Este exemplo mostra como copiar dados de um repositório de dados ODBC para o Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** para qualquer uma das fontes declaradas [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a atividade de cópia no Azure Data Factory.
 
O exemplo tem as seguintes entidades de data factory:

1.	Um serviço vinculado do tipo [OnPremisesOdbc](#odbc-linked-service-properties).
2.	Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [RelationalTable](#odbc-dataset-type-properties).
4.	Um [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [RelationalSource](#odbc-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia dados de um resultado de consulta em um armazenamento de dados ODBC para um blob a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

Como uma primeira etapa, configure o gateway de gerenciamento de dados. As instruções estão no artigo [Mover dados entre fontes locais e a nuvem](data-factory-move-data-between-onprem-and-cloud.md).

**Serviço vinculado a ODBC** Esse exemplo usa a autenticação Básica. Veja a seção [Serviço vinculado a ODBC](#odbc-linked-service-properties) para ver os diferentes tipos de autenticação que você pode usar.

	{
	    "name": "OnPremOdbcLinkedService",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
	            "authenticationType": "Basic",
	            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
	            "userName": "username",
	            "password": "password",
	            "gatewayName": "mygateway"
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

**Conjunto de dados de entrada do ODBC**

O exemplo supõe que você tenha criado uma tabela "MyTable" no armazenamento de dados ODBC e que ela contenha uma coluna chamada "timestampcolumn" para dados de série temporal.

Configurar “external”: “true” informa ao serviço Data Factory que o conjunto de dados é externo ao Data Factory e não é produzido por uma atividade no Data Factory.
	
	{
	    "name": "ODBCDataSet",
	    "properties": {
	        "published": false,
	        "type": "RelationalTable",
	        "linkedServiceName": "OnPremOdbcLinkedService",
	        "typeProperties": {},
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
			"external": true,
	        "policy": {
	            "externalData": {
	                "retryInterval": "00:01:00",
	                "retryTimeout": "00:10:00",
	                "maximumRetry": 3
	            }
	        }
	    }
	}



**Conjunto de dados de saída de Blob do Azure**

Os dados são gravados em um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

	{
	    "name": "AzureBlobOdbcDataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/odbc/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
	                        "format": "MM"
	                    }
	                },
	                {
	                    "name": "Day",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "dd"
	                    }
	                },
	                {
	                    "name": "Hour",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "HH"
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

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **RelationalSource** e o tipo **sink** está definido como **BlobSink**. A consulta SQL especificada para a propriedade **query** seleciona os dados na última hora para copiar.
	
	{
	    "name": "CopyODBCToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "OdbcDataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobOdbcDataSet"
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
	                "name": "OdbcToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}



## Propriedades do Serviço Vinculado do ODBC

A tabela a seguir fornece a descrição para elementos JSON específicos do serviço vinculado do ODBC.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- | 
| type | A propriedade type deve ser definida como: **OnPremisesOdbc** | Sim |
| connectionString | A parte da credencial que não está relacionada ao acesso da cadeia de conexão e uma credencial criptografada opcional. Veja os exemplos nas seções a seguir. | Sim
| credencial | A parte da credencial de acesso da cadeia de conexão especificada no formato propriedade-valor específico do driver. Exemplo: "Uid=<ID usuário>;Pwd=<senha>;RefreshToken=<token de atualização de senha>;". | Não
| authenticationType | Tipo de autenticação usado para se conectar ao armazenamento de dados ODBC. Os valores possíveis são: Anonymous e Basic. | Sim | 
| Nome de Usuário | Especifique o nome de usuário se você estiver usando a autenticação Básica. | Não | 
| Senha | Especifique a senha da conta de usuário que você especificou para o nome de usuário. | Não | 
| gatewayName | O nome do gateway que o serviço Data Factory deve usar para se conectar ao armazenamento de dados ODBC. | Sim |


Confira [Configurando credenciais e segurança](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) para obter detalhes sobre como definir credenciais para um armazenamento de dados ODBC local.

### Usando a autenticação Básica

	{
	    "name": "odbc",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
	            "authenticationType": "Basic",
	            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
	            "userName": "username",
	            "password": "password",
	            "gatewayName": "mygateway"
	        }
	    }
	}

### Usando a autenticação Básica com credenciais criptografadas
Você pode criptografar as credenciais usando o cmdlet [New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) (versão 1.0 do Azure PowerShell) ou [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx) (versão 0.9 ou anterior do Azure PowerShell).

	{
	    "name": "odbc",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
	            "authenticationType": "Basic",
	            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
	            "gatewayName": "mygateway"
	        }
	    }
	}


### Usando a autenticação anônima

	{
	    "name": "odbc",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
	            "authenticationType": "Anonymous",
	            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
	            "credential": "UID={uid};PWD={pwd}",
	            "gatewayName": "mygateway"
	        }
	    }
	}



## Propriedades de tipo Conjunto de Dados do ODBC

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). As seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção typeProperties do conjunto de dados do tipo **RelationalTable** (que inclui o conjunto de dados do ODBC) tem as propriedades a seguir

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| tableName | Nome da tabela no repositório de dados ODBC. | Sim | 

## Propriedades do tipo da Atividade de Cópia do ODBC

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, consulte o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades, como nome, descrição, tabelas de entrada e saída, e políticas, estão disponíveis para todos os tipos de atividade.

As propriedades disponíveis na seção **typeProperties** da atividade, por outro lado, variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fonte e coletor.

Na atividade de cópia, quando a fonte for do tipo **RelationalSource** (que inclui o ODBC), as seguintes propriedades estarão disponíveis na seção typeProperties:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------------- | -------- |
| query | Utiliza a consulta personalizada para ler os dados. | Cadeia de caracteres de consulta SQL. Por exemplo: select * from MyTable. | Sim | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Mapeamento de tipos para ODBC

Conforme mencionado no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md), a Atividade de cópia executa conversões automáticas de tipo de fonte para tipos de coletor, com a abordagem em duas etapas descritas a seguir:

1. Converter de tipos de fonte nativos para o tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados de repositórios de dados ODBC, os tipos de dados ODBC são mapeados para tipos .NET, como mencionado no tópico [Mapeamentos de tipo de dados ODBC](https://msdn.microsoft.com/library/cc668763.aspx).


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## Repositório GE Historian
Você cria um serviço vinculado de ODBC para vincular um armazenamento de dados [GE Proficy Historian (agora GE Historian)](http://www.geautomation.com/products/proficy-historian) a um Azure Data Factory, como mostrado no seguinte exemplo:

	{
	    "name": "HistorianLinkedService",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
			    "connectionString": "DSN=<name of the GE Historian store>",
			    "gatewayName": "<gateway name>",
			    "authenticationType": "Basic",
			    "userName": "<user name>",
			    "password": "<password>"
	        }
	    }
	}

Instale o Gateway de Gerenciamento de Dados em um computador local e registre o gateway no portal. O gateway instalado no computador local usa o driver ODBC para GE Historian para se conectar ao repositório de dados GE Historian. Portanto, instale o driver, se ele ainda não estiver instalado no computador do gateway. Veja a seção [Habilitando a conectividade](#enabling-connectivity) para obter detalhes.

Antes de usar o repositório GE Historian em uma solução de Data Factory, verifique se o gateway pode se conectar ao armazenamento de dados usando instruções na próxima seção.

Leia o artigo desde o início para uma visão geral detalhada do uso de dados ODBC armazena como armazenamentos de dados de origem em uma operação de cópia.

## Solucionar problemas de conectividade
Para solucionar problemas de conexão, use a guia **Diagnósticos** do **Gerenciador de Configuração de Gateway de Gerenciamento de Dados**.

1. Iniciar o **Gerenciador de Configuração de Gateway de Gerenciamento de Dados**. Você pode executar "C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared\\ConfigManager.exe" diretamente (ou) pesquisar por **Gateway** para encontrar um link para o aplicativo **Gateway de Gerenciamento de Dados da Microsoft**, conforme mostrado na imagem a seguir.

	![Gateway de pesquisa](./media/data-factory-odbc-connector/search-gateway.png)
2. Alterne para a guia **Diagnóstico**.

	![Diagnóstico de gateway](./media/data-factory-odbc-connector/data-factory-gateway-diagnostics.png)
3. Selecione o **tipo** de armazenamento de dados (serviço vinculado).
4. Especifique a **autenticação** e digite as **credenciais** (ou) insira a **cadeia de conexão** usada para se conectar ao repositório de dados.
5. Clique em **Testar Conexão** para testar a conexão com o armazenamento de dados.

## Desempenho e Ajuste  
Veja o [Guia de Desempenho e Ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho e a movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.

<!---HONumber=AcomDC_0928_2016-->