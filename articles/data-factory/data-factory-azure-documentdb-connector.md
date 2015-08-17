<properties 
	pageTitle="Conector do Banco de Dados de Documentos - Mover dados de e para o Banco de Dados de Documentos" 
	description="Saiba mais sobre o Conector de Banco de Dados de Documentos do Azure para o serviço do Data Factory que permite mover dados de/para a coleção do Banco de Dados de Documentos do Azure" 
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
	ms.date="07/29/2015" 
	ms.author="spelluru"/>

# Conector do Banco de Dados de Documentos - Mover dados de e para o Banco de Dados de Documentos

Este artigo descreve como você pode usar a Atividade de Cópia em uma Azure Data Factory para mover dados para o Banco de Dados de Documentos do Azure de outro armazenamento de dados e mover dados de outro armazenamento de dados para o Banco de Dados de Documentos. Este artigo se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral de movimentação de dados com a atividade de cópia e combinações de armazenamento de dados com suporte.

## Exemplo: Copiar dados do Banco de Dados de Documentos para o Blob do Azure

O exemplo a seguir mostra:

1. Um serviço vinculado do tipo Banco de Dados de Documentos.
2. Um serviço vinculado do tipo AzureStorage. 
3. Um conjunto de dados de entrada do tipo DocumentDbCollection. 
4. Um conjunto de dados de saída do tipo AzureBlob.
4. O pipeline com Atividade de cópia que usa DocumentDbCollectionSource e BlobSink.

O exemplo copia dados de Banco de Dados de Documentos do Azure para Blobs do Azure. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço vinculado do Banco de Dados de Documentos do Azure:**

	{
	  "name": "DocumentDbLinkedService",
	  "properties": {
	    "type": "DocumentDb",
	    "typeProperties": {
	      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
	    }
	  }
	}

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

**Conjunto de dados de entrada do Banco de Dados de Documentos do Azure:**

O exemplo pressupõe que você tenha uma coleção chamada **Person** em um banco de dados do Banco de Dados de Documentos do Azure.
 
Definir “external”: ”true” e especificar a política externalData informa o serviço Azure Data Factory que essa é uma tabela externa à fábrica de dados e não é produzida por uma atividade nessa fábrica de dados.

	{
	  "name": "PersonDocumentDbTable",
	  "properties": {
	    "type": "DocumentDbCollection",
	    "linkedServiceName": "DocumentDbLinkedService",
	    "typeProperties": {
	      "collectionName": "Person"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}


**Conjunto de dados de saída de Blob do Azure:**

Dados são copiados para um novo blob a cada hora com o caminho para o blob que reflete a data e hora específicas com granularidade de hora.

	{
	  "name": "PersonBlobTableOut",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "docdb",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "nullValue": "NULL"
	      }
	    },
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

Documento JSON de exemplo na coleção Person em um banco de dados de Banco de Dados de Documentos:

	{
	  "PersonId": 2,
	  "Name": {
	    "First": "Jane",
	    "Middle": "",
	    "Last": "Doe"
	  }
	}

O Banco de Dados de Documentos dá suporte a consultas de documentos usando um SQL como sintaxe nos documentos JSON hierárquicos.

Exemplo: SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person

O pipeline a seguir copia dados da coleção Person no banco de dados Banco de Dados de Documentos para um blob do Azure. Como parte da atividade de cópia, os conjuntos de dados de entrada e saída foram especificados.
	
	{
	  "name": "DocDbToBlobPipeline",
	  "properties": {
	    "activities": [
	      {
	        "type": "Copy",
	        "typeProperties": {
	          "source": {
	            "type": "DocumentDbCollectionSource",
	            "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
	            "nestingSeparator": "."
	          },
	          "sink": {
	            "type": "BlobSink",
	            "blobWriterAddHeader": true,
	            "writeBatchSize": 1000,
	            "writeBatchTimeout": "00:00:59"
	          }
	        },
	        "inputs": [
	          {
	            "name": "PersonDocumentDbTable"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "PersonBlobTableOut"
	          }
	        ],
	        "policy": {
	          "concurrency": 1
	        },
	        "name": "CopyFromDocDbToBlob"
	      }
	    ],
	    "start": "2015-04-01T00:00:00Z",
	    "end": "2015-04-02T00:00:00Z"
	  }
	}

## Exemplo: Copiar dados do Blob do Azure para o Banco de Dados de Documentos do Azure

O exemplo a seguir mostra:

1. Um serviço vinculado do tipo Banco de Dados de Documentos.
2. Um serviço vinculado do tipo AzureStorage.
3. Um conjunto de dados de entrada do tipo AzureBlob.
4. Um conjunto de dados de saída do tipo DocumentDbCollection. 
4. O pipeline com a Atividade de cópia que usa BlobSource e DocumentDbCollectionSink.

O exemplo copia dados do blob do Azure para o Banco de Dados de Documentos do Azure. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

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

**Serviço vinculado do Banco de Dados de Documentos do Azure:**
	
	{
	  "name": "DocumentDbLinkedService",
	  "properties": {
	    "type": "DocumentDb",
	    "typeProperties": {
	      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
	    }
	  }
	}

**Conjunto de dados de entrada de Blob do Azure:**

	{
	  "name": "PersonBlobTableIn",
	  "properties": {
	    "structure": [
	      {
	        "name": "Id",
	        "type": "Int"
	      },
	      {
	        "name": "FirstName",
	        "type": "String"
	      },
	      {
	        "name": "MiddleName",
	        "type": "String"
	      },
	      {
	        "name": "LastName",
	        "type": "String"
	      }
	    ],
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "fileName": "input.csv",
	      "folderPath": "docdb",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "nullValue": "NULL"
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

**Conjunto de dados de saída do Banco de Dados de Documentos do Azure:**

O exemplo copia dados para uma coleção chamada "Person".

	{
	  "name": "PersonDocumentDbTableOut",
	  "properties": {
	    "structure": [
	      {
	        "name": "Id",
	        "type": "Int"
	      },
	      {
	        "name": "Name.First",
	        "type": "String"
	      },
	      {
	        "name": "Name.Middle",
	        "type": "String"
	      },
	      {
	        "name": "Name.Last",
	        "type": "String"
	      }
	    ],
	    "type": "DocumentDbCollection",
	    "linkedServiceName": "DocumentDbLinkedService",
	    "typeProperties": {
	      "collectionName": "Person"
	    },
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

O pipeline a seguir copia dados do Blobo do Azure para a coleção Person no Banco de Dados de Documentos. Como parte da atividade de cópia, os conjuntos de dados de entrada e saída foram especificados.
	
	{
	  "name": "BlobToDocDbPipeline",
	  "properties": {
	    "activities": [
	      {
	        "type": "Copy",
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource"
	          },
	          "sink": {
	            "type": "DocumentDbCollectionSink",
	            "nestingSeparator": ".",
	            "writeBatchSize": 2,
	            "writeBatchTimeout": "00:00:00"
	          }
	          "translator": {
	              "type": "TabularTranslator",
	              "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
	          }
	        },
	        "inputs": [
	          {
	            "name": "PersonBlobTableIn"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "PersonDocumentDbTableOut"
	          }
	        ],
	        "policy": {
	          "concurrency": 1
	        },
	        "name": "CopyFromBlobToDocDb"
	      }
	    ],
	    "start": "2015-04-14T00:00:00Z",
	    "end": "2015-04-15T00:00:00Z"
	  }
	}
 
Se a entrada de blob de exemplo for

	1,John,,Doe

Então, a saída JSON no Banco de Dados de Documentos será:

	{
	  "Id": 1,
	  "Name": {
	    "First": "John",
	    "Middle": null,
	    "Last": "Doe"
	  },
	  "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
	}
	
O Banco de Dados de Documentos é um repositório NoSQL para documentos JSON, em que estruturas aninhadas são permitidas. O Azure Data Factory permite que o usuário indique a hierarquia via **nestingSeparator**, que é "." neste exemplo. Com o separador, a atividade de cópia vai gerar o objeto "Name" com três elementos filhos First, Middle e Last, de acordo com "Name.First", "Name.Middle" e "Name.Last" na definição da tabela.

## Propriedades do Serviço vinculado do Banco de Dados de Documentos do Azure

A tabela a seguir fornece a descrição para elementos JSON específicas para o serviço de vinculado do Banco de Dados de Documentos do Azure.

| **Propriedade** | **Descrição** | **Obrigatório** |
| -------- | ----------- | --------- |
| type | A propriedade type deve ser definida como: **DocumentDb** | Sim |
| connectionString | Especifique as informações necessárias para se conectar ao banco de dados do Banco de Dados de Documentos do Azure. | Sim |

## Propriedades de tipo de conjunto de dados do Banco de Dados de Documentos do Azure

Para obter uma lista completa das seções e propriedades disponíveis para definição de conjuntos de dados, consulte o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). Seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).
 
A seção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção typeProperties para o conjunto de dados do tipo **DocumentDbCollection** tem as propriedades a seguir.

| **Propriedade** | **Descrição** | **Obrigatório** |
| -------- | ----------- | -------- |
| collectionName | Nome da coleção de documentos do Banco de Dados de Documentos. | Sim |


Exemplo:

	{
	  "name": "PersonDocumentDbTable",
	  "properties": {
	    "type": "DocumentDbCollection",
	    "linkedServiceName": "DocumentDbLinkedService",
	    "typeProperties": {
	      "collectionName": "Person"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

## Propriedades de tipo da atividade de cópia do Banco de Dados de Documentos do Azure

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, consulte o artigo [Criando pipelines](data-factory-create-pipelines.md). Propriedades, como nome, descrição, tabelas de entrada e saída, várias políticas, etc. estão disponíveis para todos os tipos de atividades.
 
**Observação:** a Atividade de cópia usa apenas uma entrada e produz apenas uma saída.

As propriedades disponíveis na seção typeProperties da atividade, por outro lado, variam de acordo com cada tipo de atividade e, no caso de Atividade de cópia, variam dependendo dos tipos de fontes e coletores.

No caso da Atividade de cópia, quando a fonte é do tipo **DocumentDbCollectionSource**, as seguintes propriedades estão disponíveis na seção **typeProperties**:

| **Propriedade** | **Descrição** | **Valores permitidos** | **Obrigatório** |
| ------------ | --------------- | ------------------ | ------------ |
| query | Especifique a consulta para ler dados. | Cadeia de caracteres de consulta com suporte pelo Banco de Dados de Documentos. <p>Exemplo: SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > "2009-01-01T00:00:00"</p> | Não. <p>Se não for especificada, a instrução SQL que é executada é: select <columns defined in structure> from mycollection </p>
| nestingSeparator | Caractere especial para indicar que o documento está aninhado | Qualquer caractere. <p>O Banco de Dados de Documentos é um repositório NoSQL para documentos JSON, em que estruturas aninhadas são permitidas. O Azure Data Factory permite que o usuário indique a hierarquia via nestingSeparator, que é "." neste exemplo. Com o separador, a atividade de cópia vai gerar o objeto "Name" com três elementos filhos First, Middle e Last, de acordo com "Name.First", "Name.Middle" e "Name.Last" na definição da tabela.</p> | Não

**DocumentDbCollectionSink** dá suporte às seguintes propriedades:

| **Propriedade** | **Descrição** | **Valores permitidos** | **Obrigatório** |
| -------- | ----------- | -------------- | -------- |
| nestingSeparator | Um caractere especial no nome da coluna de fonte para indicar que esse documento aninhado é necessário. <p>Exemplo acima: Name.First in the output table produces the following JSON structure in the DocumentDB document:</p><p>"Name": {<br/> "First": "John"<br/>},</p> | Caractere que é usado para separar os níveis de aninhamento.<p>O valor padrão é . (ponto).</p> | Caractere que é usado para separar os níveis de aninhamento. <p>O valor padrão é . (ponto).</p> | Não | 
| writeBatchSize | Número de solicitações paralelas para o serviço Banco de Dados de Documentos criar documentos.<p>Você pode ajustar o desempenho ao copiar dados para/do Banco de Dados de Documentos usando essa propriedade. Você pode esperar um melhor desempenho quando você aumenta writeBatchSize, pois mais solicitações paralelas para Banco de Dados de Documentos são enviadas. No entanto, será necessário evitar limitação que pode gerar a mensagem de erro: "Taxa de solicitação é grande".</p><p>A limitação é decidida por vários fatores, incluindo o tamanho dos documentos, o número de termos em documentos, indexação política da coleção de destino, etc. Para operações de cópia, você pode usar uma coleção melhor (por exemplo, S3) para ter mais taxa de transferência disponível (solicitação de 2.500 unidades/segundo).</p> | Valor inteiro | Não |
| writeBatchTimeout | Tempo de espera para a operação ser concluída antes de atingir o tempo limite. | (Unidade = timespan) Exemplo: "00:30:00" (30 minutos). | Não |
 
 

<!---HONumber=06-->