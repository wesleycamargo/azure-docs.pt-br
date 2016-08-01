<properties 
	pageTitle="Mover dados para e do Sistema de Arquivos | Azure Data Factory" 
	description="Aprenda como mover dados para/do sistema de arquivos local usando o Azure Data Factory" 
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
	ms.date="07/13/2016" 
	ms.author="spelluru"/>

# Mover dados para e do sistema de arquivos local usando o Azure Data Factory

Este artigo descreve como você pode usar a atividade de cópia da data factory para mover dados para e do sistema de arquivos local. Confira [Supported sources and sinks](data-factory-data-movement-activities.md#supported-data-stores) (Fontes e coletores com suporte) para obter uma lista de armazenamentos de dados que podem ser usados como fontes ou coletores com o sistema de arquivos local. Este artigo se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral de movimentação de dados com a atividade de cópia e combinações de armazenamento de dados com suporte.

A data factory dá suporte à conexão com e do sistema de arquivos local por meio do Gateway de Gerenciamento de Dados. Consulte o artigo sobre [movimentação de dados entre pontos locais e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) para saber mais sobre o Gateway de Gerenciamento de Dados e obter instruções passo a passo de como configurar o gateway.

> [AZURE.NOTE] 
Com exceção do Gateway de Gerenciamento de Dados, não é necessário instalar nenhum outro binário para se comunicar de e para o Sistema de Arquivos local.
> 
> Confira [Gateway Troubleshooting](data-factory-move-data-between-onprem-and-cloud.md#gateway-troubleshooting) (Solução de problemas de Gateway) para ver dicas sobre como solucionar problemas de conexão/gateway.

## Compartilhamento de arquivos do Linux 

Execute as duas etapas a seguir para usar um compartilhamento de arquivos do Linux com o serviço vinculado do servidor de arquivos:

- Instale o [Samba](https://www.samba.org/) no servidor Linux.
- Instale e configure o Gateway de Gerenciamento de Dados em um servidor Windows. Não há suporte para a instalação do gateway em um servidor Linux.
 
## Exemplo: copiar dados de um sistema de arquivos local para um Blob do Azure

Este exemplo mostra como copiar dados de um sistema de arquivos local para um Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** para qualquer uma das fontes declaradas [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a Atividade de Cópia no Azure Data Factory.
 
O exemplo tem as seguintes entidades de data factory:

- Um serviço vinculado do tipo [OnPremisesFileServer](data-factory-onprem-file-system-connector.md#onpremisesfileserver-linked-service-properties).
- Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
- Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [FileShare](data-factory-onprem-file-system-connector.md#on-premises-file-system-dataset-type-properties).
- Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- O [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que usa [FileSystemSource](data-factory-onprem-file-system-connector.md#file-share-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo a seguir copia dados pertencentes a uma série temporal do sistema de arquivos local para o blob do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

Como uma primeira etapa, configure o gateway de gerenciamento de dados de acordo com as instruções no artigo sobre [movimentação de dados entre pontos locais e na nuvem](data-factory-move-data-between-onprem-and-cloud.md).

**Serviço vinculado de servidor de arquivos local:**

	{
	  "Name": "OnPremisesFileServerLinkedService",
	  "properties": {
	    "type": "OnPremisesFileServer",
	    "typeProperties": {
	      "host": "\\\Contosogame-Asia.<region>.corp.<company>.com",
	      "userid": "Admin",
	      "password": "123456",
	      "gatewayName": "mygateway"
	    }
	  }
	}

Para o host, você pode especificar **Local** ou **localhost** se o compartilhamento de arquivos estiver no próprio computador do gateway. Além disso, é recomendável usar a propriedade **encryptedCredential** em vez de usar as propriedades **userid** e **password**. Consulte [Serviço Vinculado do Sistema de Arquivos](#onpremisesfileserver-linked-service-properties) para obter detalhes sobre esse serviço vinculado.

**Serviço vinculado do armazenamento de Blob do Azure:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Conjunto de dados de entrada do sistema de arquivos local:**

Os dados são obtidos de um novo arquivo a cada hora com o caminho e nome do arquivo refletindo a data e hora específicas com granularidade de hora.

Configurar “external”: “true” e especificar a política externalData informa ao serviço Azure Data Factory que essa a tabela é externa à data factory e não é produzida por uma atividade na data factory.

	{
	  "name": "OnpremisesFileSystemInput",
	  "properties": {
	    "type": " FileShare",
	    "linkedServiceName": " OnPremisesFileServerLinkedService ",
	    "typeProperties": {
	      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
	      "fileName": "{Hour}.csv",
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
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

**Conjunto de dados de saída de Blob do Azure:**

Os dados são gravados em um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
	            "format": "%HH"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": "\t",
	        "rowDelimiter": "\n"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**Atividade de cópia:**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída acima e agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo de **source** está definido como **FileSystemSource** e o tipo de **sink** está definido como **BlobSink**.
	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2015-06-01T18:00:00",
	    "end":"2015-06-01T19:00:00",
	    "description":"Pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "OnpremisesFileSystemtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "OnpremisesFileSystemInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "FileSystemSource"
	          },
	          "sink": {
	            "type": "BlobSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	     ]
	   }
	}

##Exemplo: copiar dados do SQL do Azure pra o sistema de arquivos local 

O exemplo a seguir mostra:

- Um serviço vinculado do tipo AzureSqlDatabase.
- Um serviço vinculado do tipo OnPremisesFileServer.
- Um conjunto de dados de entrada do tipo AzureSqlTable.
- Um conjunto de dados de saída do tipo FileShare.
- Um pipeline com a atividade de cópia que usa SqlSource e FileSystemSink.

O exemplo copia a cada hora dados pertencentes a uma série temporal de uma tabela no banco de dados SQL do Azure para um sistema de arquivos local. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço vinculado do SQL Azure:**

	{
	  "name": "AzureSqlLinkedService",
	  "properties": {
	    "type": "AzureSqlDatabase",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Serviço vinculado de servidor de arquivos local:**

	{
	  "Name": "OnPremisesFileServerLinkedService",
	  "properties": {
	    "type": "OnPremisesFileServer",
	    "typeProperties": {
	      "host": "\\\Contosogame-Asia.<region>.corp.<company>.com",
	      "userid": "Admin",
	      "password": "123456",
	      "gatewayName": "mygateway"
	    }
	  }
	}

Para o host, você pode especificar **Local** ou **localhost** se o compartilhamento de arquivos estiver no próprio computador do gateway. Além disso, é recomendável usar a propriedade **encryptedCredential** em vez de usar as propriedades **userid** e **password**. Consulte [Serviço Vinculado do Sistema de Arquivos](#onpremisesfileserver-linked-service-properties) para obter detalhes sobre esse serviço vinculado.

**Conjunto de dados de entrada do SQL Azure:**

O exemplo supõe que você criou uma tabela "MyTable" no SQL Azure e que ela contém uma coluna chamada "timestampcolumn" para dados de série temporal.

Configurar “external”: “true” e especificar a política externalData informa ao serviço Data Factory que essa a tabela é externa à data factory e não é produzida por uma atividade na data factory.

	{
	  "name": "AzureSqlInput",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

**Conjunto de dados de saída do sistema de arquivos local:**

Os dados são copiados para um novo arquivo a cada hora com o caminho do blob refletindo a data e hora específicas com granularidade de hora.

	{
	  "name": "OnpremisesFileSystemOutput",
	  "properties": {
	    "type": "FileShare",
	    "linkedServiceName": " OnPremisesFileServerLinkedService ",
	    "typeProperties": {
	      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
	      "fileName": "{Hour}.csv",
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
	            "format": "%HH"
	          }
	        }
	      ]
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

**Pipeline com uma atividade de cópia:** o pipeline contém uma atividade de cópia que está configurada para usar os conjuntos de dados de entrada e saída acima e agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo de **source** está definido como **SqlSource** e o tipo de **sink** está definido como **FileSystemSink**. A consulta SQL especificada para a propriedade **SqlReaderQuery** seleciona os dados da última hora a serem copiados.

	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2015-06-01T18:00:00",
	    "end":"2015-06-01T20:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLtoOnPremisesFile",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureSQLInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "OnpremisesFileSystemOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "SqlSource",
	            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
	          },
	          "sink": {
	            "type": "FileSystemSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 3,
	          "timeout": "01:00:00"
	        }
	      }
	     ]
	   }
	}

## Propriedades do serviço vinculado OnPremisesFileServer

Você pode vincular um sistema de arquivos local a uma Azure Data Factory com o serviço vinculado de servidor de arquivos local. A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado do servidor de arquivos local.

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
type | A propriedade type deve ser definida como **OnPremisesFileServer** | Sim 
host | Caminho raiz da pasta que você deseja copiar. Use o caractere de escape ' \\ ' para caracteres especiais na cadeia de caracteres. Confira [Definições de conjunto de dados e serviço vinculado de exemplo](#sample-linked-service-and-dataset-definitions) para obter exemplos. | Sim
userid | Especifique a ID do usuário que tem acesso ao servidor | Não (se você escolher encryptedcredential)
Senha | Especifique a senha para o usuário (userid) | Não (se você escolher encryptedcredential 
encryptedCredential | Especifique as credenciais criptografadas que você pode obter executando o cmdlet New-AzureRmDataFactoryEncryptValue<br/><br/>**Observação:** você deve usar o Azure PowerShell versão 0.8.14 ou superior para usar cmdlets como o New-AzureRmDataFactoryEncryptValue com o parâmetro de tipo definido como OnPremisesFileSystemLinkedService | Não (se você optar por especificar userid e password em texto sem formatação)
gatewayName | O nome do gateway que o serviço Data Factory deve usar para se conectar ao servidor de arquivos local | Sim

Consulte [Definir credenciais e segurança](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) para obter detalhes sobre como definir credenciais para uma fonte de dados do Sistema de Arquivos local.

### Definições de conjunto de dados e de serviço vinculado de exemplo 
Cenário | Host em definição de serviço vinculado | folderPath em definição de conjunto de dados
-------- | --------------------------------- | --------------------- |
Pasta local no computador do Gateway de Gerenciamento de Dados <br/><br/>por exemplo, D:\\* ou D:\\pasta\\subpasta\\* | D:\\\ (para gateway versão 2.0 e superior) <br/><br/> localhost (para gateway versão inferior a 2.0) | .\\\ ou pasta\\\subpasta (para gateway versão 2.0 e superior) <br/><br/>D:\\\ ou D:\\\pasta\\\subpasta (para gateway versão inferior a 2.0)
Pasta compartilhada remota: <br/><br/>por exemplo, \\\meuservidor\\compartilhamento\\* ou \\\meuservidor\\compartilhamento\\pasta\\subpasta\\* | \\\\\\meuservidor\\\compartilhamento | .\\\ ou pasta\\\subpasta

Você pode encontrar a **versão** do gateway instalado iniciando o [Gerenciador de configuração do Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md#data-management-gateway-configuration-manager) em seu computador e alternando para a guia **Ajuda**.

> [AZURE.NOTE] Para o cenário de pasta local, ao especificar a propriedade "host" como "localhost", a execução da atividade de cópia ainda funcionará com qualquer versão do gateway, mas você não poderá usar o Assistente de Cópia para configurar a cópia. É sugerido que você [atualize o gateway para a versão 2.0 ou superior](data-factory-data-management-gateway.md#update-data-management-gateway). Assim, será possível usar novas configurações por meio do JSON e do Assistente de Cópia para que o cenário funcione.

**Exemplo: usando username e password em texto sem formatação**
	
	{
	  "Name": "OnPremisesFileServerLinkedService",
	  "properties": {
	    "type": "OnPremisesFileServer",
	    "typeProperties": {
	      "host": "\\\Contosogame-Asia",
	      "userid": "Admin",
	      "password": "123456",
	      "gatewayName": "mygateway"
	    }
	  }
	}
	
**Exemplo: usando encryptedcredential**

	{
	  "Name": " OnPremisesFileServerLinkedService ",
	  "properties": {
	    "type": "OnPremisesFileServer",
	    "typeProperties": {
	      "host": "localhost",
	      "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
	      "gatewayName": "mygateway"
	    }
	  }
	}

## Propriedades type do conjunto de dados de sistema de arquivos local

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). Seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL do Azure, Blob do Azure, tabela do Azure, sistema de arquivos local etc.).

A seção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local, formato etc. de dados no armazenamento de dados. A seção typeProperties para o conjunto de dados do tipo **FileShare** tem as seguintes propriedades.

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
folderPath | Subcaminho para a pasta. Use o caractere de escape ' \\ ' para caracteres especiais na cadeia de caracteres. Confira [Definições de conjunto de dados e serviço vinculado de exemplo](#sample-linked-service-and-dataset-definitions) para obter exemplos.<br/><br/>Você pode combinar isso com **partitionBy** para ter caminhos de pastas com base na fatia de data/hora de início/término. | Sim
fileName | Especifique o nome do arquivo no **folderPath** se quiser que a tabela se refira a um arquivo específico na pasta. Se você não especificar algum valor para essa propriedade, a tabela apontará para todos os arquivos na pasta.<br/><br/>Quando fileName não for especificado para um conjunto de dados de saída, o nome do arquivo gerado estará no seguinte formato:<br/><br/>Data.<Guid>.txt (por exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) | Não
partitionedBy | partitionedBy pode utilizado para especificar um filename, folderPath dinâmico para dados de série temporal. Por exemplo, folderPathparametrizado para cada hora dos dados. | Não
Formatar | Os seguintes tipos de formato têm suporte: **TextFormat**, **AvroFormat**, **JsonFormat** e **OrcFormat**. É necessário definir a propriedade **type** sob formato como um desses valores. Confira as seções [Especificando TextFormat](#specifying-textformat), [Especificando AvroFormat](#specifying-avroformat), Especificando [JsonFormat](#specifying-jsonformat) e [Especificando OrcFormat](#specifying-orcformat) para obter detalhes. Se você quiser copiar arquivos no estado em que se encontram entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída. | Não
fileFilter | Especifique um filtro a ser usado para selecionar um subconjunto de arquivos no folderPath em vez de todos os arquivos. <br/><br/>Os valores permitidos são: * (vários caracteres) e ? (um único caractere).<br/><br/>Exemplo 1: "fileFilter": "*.log"<br/>Exemplo 2: "fileFilter": 2014-1-?.txt"<br/><br/>**Observação**: fileFilter é aplicável a um conjunto de dados FileShare de entrada | Não
| compactação | Especifique o tipo e o nível de compactação para os dados. Os tipos com suporte são: **GZip**, **Deflate** e **BZip2** e os níveis com suporte são: **Melhor** e **Mais rápido**. Observe que as configurações de compactação de dados em **AvroFormat** ou **OrcFormat** não têm suporte neste momento. Consulte a seção [Suporte à compactação](#compression-support) para obter mais detalhes. | Não |

> [AZURE.NOTE] filename e fileFilter não podem ser usados simultaneamente.

### Utilizando a propriedade partionedBy

Conforme mencionado acima, você pode especificar um filename, folderPath dinâmico para dados de série temporal com partitionedBy. Você pode fazer isso com as macros de Data Factory e as variáveis de sistema SliceStart e SliceEnd que indicam o período de tempo lógico para uma determinada fatia de dados.

Confira os artigos [Criando conjuntos de dados](data-factory-create-datasets.md), [Agendamento e execução](data-factory-scheduling-and-execution.md) e [Criando pipelines](data-factory-create-pipelines.md) para obter mais detalhes sobre conjuntos de dados de série temporal, agendamentos e fatias.

#### Exemplo 1:

	"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
	"partitionedBy": 
	[
	    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
	],

No exemplo acima, {Slice} é substituído pelo valor da variável de sistema SliceStart da Data Factory no formato (AAAAMMDDHH) especificado. O SliceStart refere-se à hora de início da fatia. O folderPath é diferente para cada fatia. Por exemplo: wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104.

#### Exemplo 2:

	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
	"fileName": "{Hour}.csv",
	"partitionedBy": 
	 [
	    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
	    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
	    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
	    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
	],

No exemplo acima, ano, mês, dia e hora do SliceStart são extraídos em variáveis separadas que são usadas pelas propriedades folderPath e fileName.

[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]  
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## Propriedades de tipo da atividade de cópia do compartilhamento de arquivos

**FileSystemSource** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------------- | -------- |
| recursiva | Indica se os dados são lidos recursivamente a partir das subpastas ou somente da pasta especificada. | True, False (padrão)| Não | 

**FileSystemSink** dá suporte às seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------------- | -------- |
| copyBehavior | Define o comportamento de cópia quando a origem é BlobSource ou FileSystem. | **PreserveHierarchy:** preserva a hierarquia de arquivos na pasta de destino, ou seja, o caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><br/>**FlattenHierarchy:** todos os arquivos da pasta de origem estarão no primeiro nível da pasta de destino. Os arquivos de destino terão o nome gerado automaticamente.<br/><br/>**MergeFiles:** mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo/blob for especificado, o nome do arquivo mesclado será o nome especificado; caso contrário, será o nome de arquivo gerado automaticamente. | Não |

### exemplos de recursive e copyBehavior
Esta seção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores recursive e copyBehavior.

recursive | copyBehavior | Comportamento resultante
--------- | ------------ | --------
verdadeiro | preserveHierarchy | Para uma pasta de origem Pasta1 com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta de destino Pasta1 terá a mesma estrutura de acordo com a origem<br/><br/>>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.  
verdadeiro | flattenHierarchy | Para uma pasta de origem Pasta1 com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta de destino Pasta1 terá a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo5
verdadeiro | mergeFiles | Para uma pasta de origem Pasta1 com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta de destino Pasta1 terá a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;o conteúdo de Arquivo1 + Arquivo2 + Arquivo3 + Arquivo4 + Arquivo5 será mesclado em um nome de arquivo gerado automaticamente<
false | preserveHierarchy | Para uma pasta de origem Pasta1 com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta de destino Pasta1 terá a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/>Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não são selecionados.
false | flattenHierarchy | Para uma pasta de origem Pasta1 com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta de destino Pasta1 terá a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para Arquivo2<br/><br/>Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não são selecionados.<
false | mergeFiles | Para uma pasta de origem Pasta1 com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5<br/><br/>a pasta de destino Pasta1 terá a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;o conteúdo dos Arquivo1 + Arquivo2 será mesclado em um arquivo com o nome de arquivo gerado automaticamente. Nome gerado automaticamente para o Arquivo1<br/><br/>A Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não será selecionada.


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## Desempenho e Ajuste  
Confira o [Guia de desempenho e ajuste da atividade de cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.






 

<!---HONumber=AcomDC_0720_2016-->