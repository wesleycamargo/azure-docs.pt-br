<properties
	pageTitle="Mover dados do Salesforce usando o Data Factory | Microsoft Azure"
	description="Saiba mais sobre como mover os dados do Salesforce usando o Azure Data Factory."
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
	ms.date="07/07/2016"
	ms.author="spelluru"/>

# Mover dados do Salesforce usando o Azure Data Factory
Este artigo descreve como você pode usar a Atividade de Cópia no Azure Data Factory para copiar os dados do Salesforce para qualquer armazenamento de dados listado na coluna Coletores tabela de [fontes de dados e coletores com suporte](data-factory-data-movement-activities.md#supported-data-stores). Este artigo se baseia no artigo de [atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral de movimentação de dados com a Atividade de Cópia e combinações de armazenamentos de dados com suporte.

Atualmente, o Azure Data Factory suporta apenas mover os dados do Salesforce para os [armazenamentos de dados do coletor com suporte]((data-factory-data-movement-activities.md#supported-data-stores), mas não oferece suporte para mover os dados de outros armazenamentos de dados para o Salesforce.

## Pré-requisitos
- Você deve usar uma das edições a seguir do Salesforce: Developer Edition, Professional Edition, Enterprise Edition, ou Unlimited Edition.
- A permissão de API deve estar habilitada. Consulte [Como habilito o acesso à API no Salesforce por conjunto de permissões?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)
- Para copiar os dados do Salesforce para os armazenamentos de dados locais, você deve ter, pelo menos, o Gateway de Gerenciamento de Dados 2.0 instalado no ambiente local.

## Assistente para Copiar Dados
A maneira mais fácil de criar um pipeline que copia os dados do Salesforce para qualquer um dos armazenamentos de dados do coletor suportados é usar o Assistente para Copiar Dados. Consulte [Tutorial: criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente para Copiar Dados.

O exemplo a seguir fornece as definições JSON de exemplo que você pode utilizar para criar um pipeline usando o [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados do Salesforce para o Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a Atividade de Cópia no Azure Data Factory.

## Exemplo: copiar dados do Salesforce para um blob do Azure
O exemplo copia os dados do Salesforce para um blob do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos. Você pode copiar os dados diretamente para qualquer um dos coletores listados no artigo de [atividades de movimentação de dados](data-factory-data-movement-activities.md#supported-data-stores) usando a Atividade de Cópia no Azure Data Factory.

Aqui estão os artefatos Data Factory que você precisará criar para implementar o cenário. As seções depois da lista fornecem detalhes sobre essas etapas.

- Um serviço vinculado do tipo [Salesforce](#salesforce-linked-service-properties)
- Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)
- Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [RelationalTable](#salesforce-dataset-properties)
- Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)
- Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [RelationalSource](#relationalsource-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)

**Serviço vinculado Salesforce**

Este exemplo usa o serviço vinculado **Salesforce**. Veja a seção [Serviço vinculado do Salesforce](#salesforce-linked-service-properties) para ver as propriedades com suporte para esse serviço vinculado. Veja [Obter token de segurança](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) para ver instruções sobre como redefinir/obter o token de segurança.

	{
		"name": "SalesforceLinkedService",
		"properties":
		{
			"type": "Salesforce",
			"typeProperties":
			{
				"username": "<user name>",
				"password": "<password>",
				"securityToken": "<security token>"
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

**Conjunto de dados de entrada do Salesforce**

	{
		"name": "SalesforceInput",
		"properties": {
			"linkedServiceName": "SalesforceLinkedService",
			"type": "RelationalTable",
			"typeProperties": {
				"tableName": "AllDataType__c"  
			},
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

Configurar **external** como **true** informa ao serviço Data Factory que o conjunto de dados é externo ao Data Factory e não é produzido por uma atividade no Data Factory.

> [AZURE.IMPORTANT] A parte "\_\_c" do Nome da API é necessária para qualquer objeto personalizado.

![Data Factory — conexão Salesforce — nome da API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**Conjunto de dados de saída do blob do Azure**

Os dados são gravados em um novo blob a cada hora (frequência: hora, intervalo: 1).

	{
	    "name": "AzureBlobOutput",
	    "properties":
	    {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties":
	        {
	            "folderPath": "adfgetstarted/alltypes_c"
	        },
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}


**Pipeline com Atividade de cópia**

O pipeline contém uma Atividade de Cópia, que está configurada para usar os conjuntos de dados de entrada e saída acima, e está agendado para ser executado a cada hora. Na definição JSON do pipeline, o tipo **origem** está definido para **RelationalSource** e o tipo **coletor** está definido para **BlobSink**.

Veja [Propriedades do tipo RelationalSource](#relationalsource-type-properties) para obter a lista de propriedades com suporte para o RelationalSource.

	{  
		"name":"SamplePipeline",
		"properties":{  
			"start":"2016-06-01T18:00:00",
			"end":"2016-06-01T19:00:00",
			"description":"pipeline with copy activity",
			"activities":[  
			{
				"name": "SalesforceToAzureBlob",
				"description": "Copy from Salesforce to an Azure blob",
				"type": "Copy",
				"inputs": [
				{
					"name": "SalesforceInput"
				}
				],
				"outputs": [
				{
					"name": "AzureBlobOutput"
				}
				],
				"typeProperties": {
					"source": {
						"type": "RelationalSource",
						"query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"				
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

> [AZURE.IMPORTANT] A parte "\_\_c" do Nome da API é necessária para qualquer objeto personalizado.

![Data Factory — conexão Salesforce — nome da API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## Propriedades do serviço vinculado Salesforce

A tabela a seguir fornece descrições dos elementos JSON específicos para o serviço vinculado Salesforce.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| type | A propriedade type deve ser definida para: **Salesforce**. | Sim |
| Nome de Usuário |Especifique um nome de usuário para a conta de usuário. | Sim |
| Senha | Especifique um senha para a conta de usuário. | Sim |
| securityToken | Especifique um token de segurança para a conta de usuário. Veja [Obter token de segurança](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) para ver instruções sobre como redefinir/obter o token de segurança. Para saber mais sobre os tokens de segurança em geral, veja [Segurança e a API](https://developer.salesforce.com/docs/atlas.pt-BR.api.meta/api/sforce_api_concepts_security.htm). | Sim |

## Propriedades do conjunto de dados Salesforce

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, veja o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). As seções como a estrutura, disponibilidade e política de um JSON do conjunto de dados são parecidas para todos os tipos de conjuntos de dados (SQL do Azure, blob do Azure, tabela do Azure etc.).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção typeProperties para um conjunto de dados do tipo **RelationalTable** tem as propriedades a seguir:

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| tableName | Nome da tabela no Salesforce. | Não (se uma **consulta** de **RelationalSource** for especificada) |

> [AZURE.IMPORTANT]  A parte "\_\_c" do Nome da API é necessária para qualquer objeto personalizado.

![Data Factory — conexão Salesforce — nome da API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## Propriedades do tipo RelationalSource
Para obter uma lista completa das seções e propriedades disponíveis para definir as atividades, veja o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades como o nome, descrição, tabelas de entrada e saída, e várias políticas estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis na seção typeProperties da atividade, por outro lado, variam com cada tipo de atividade e, no caso da Atividade de Cópia, variam dependendo dos tipos de origens e coletores.

No caso da Atividade de Cópia, quando a origem for do tipo **RelationalSource** (que inclui Salesforce), as seguintes propriedades estarão disponíveis na seção typeProperties:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------------- | -------- |
| query | Utiliza a consulta personalizada para ler os dados. | Uma consulta SQL-92 ou uma consulta [SOQL (Salesforce Object Query Language)](https://developer.salesforce.com/docs/atlas.pt-BR.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm). Por exemplo: select * from MyTable\_\_c. | Não (se **tableName** do **conjunto de dados** for especificado) |

> [AZURE.IMPORTANT] A parte "\_\_c" do Nome da API é necessária para qualquer objeto personalizado.<br> Quando você especificar uma consulta que inclui a cláusula **where** na coluna DateTime, use SOQL. Por exemplo: $$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd) ou a consulta SQL, por exemplo, $$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts'{0:yyyy-MM-dd HH:mm:ss}'}} AND LastModifiedDate < {{ts'{1:yyyy-MM-dd HH:mm:ss}'}}', WindowStart, WindowEnd).

![Data Factory — conexão Salesforce — nome da API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## Recuperando dados do relatório do Salesforce
É possível recuperar dados dos relatórios do Salesforce especificando a consulta como {call "<nome do relatório>"}, por exemplo, "query": "{call "RelatórioDeTeste"}".

## Limites da solicitação Salesforce
O Salesforce tem limites para o total de solicitações de API e as solicitações simultâneas de API. Veja a seção "Limites de Solicitações da API" no artigo [Limites do Desenvolvedor Salesforce](http://resources.docs.salesforce.com/200/20/pt-BR/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) para obter detalhes.

Se o número de solicitações simultâneas exceder o limite, a limitação será atingida e você verá falhas aleatórias. Se o número total de solicitações exceder o limite, a conta do Salesforce será bloqueada por 24 horas. Você também pode receber o erro "REQUEST\_LIMIT\_EXCEEDED" em ambos os cenários.


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Mapeamento de tipo para Salesforce
Tipo Salesforce | Tipo baseado no .NET
--------------- | ---------------
Numeração automática | Cadeia de caracteres
Caixa de seleção | Booliano
Moeda | Duplo
Data | DateTime
Data/hora | DateTime
Email | Cadeia de caracteres
ID | Cadeia de caracteres
Relação de pesquisa | Cadeia de caracteres
Lista de seleção múltipla | Cadeia de caracteres
Número | Duplo
Porcentagem | Duplo
Telefone | Cadeia de caracteres
Lista de seleção | Cadeia de caracteres
Texto | Cadeia de caracteres
Área de texto | Cadeia de caracteres
Área de texto (longo) | Cadeia de caracteres
Área de texto (Rich) | Cadeia de caracteres
Texto (criptografado) | Cadeia de caracteres
URL | Cadeia de caracteres

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]
[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## Desempenho e ajuste  
Veja o [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho e a movimentação dos dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar.

<!---HONumber=AcomDC_0817_2016-->