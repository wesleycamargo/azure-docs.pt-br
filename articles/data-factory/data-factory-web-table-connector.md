<properties 
	pageTitle="Mover dados da Tabela da Web | Azure Data Factory" 
	description="Saiba mais sobre como mover dados de uma tabela em uma página da Web usando o Azure Data Factory" 
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
	ms.date="05/02/2016" 
	ms.author="spelluru"/>

# Mover dados de uma fonte de tabela da Web usando o Azure Data Factory
Este artigo descreve como você pode usar a atividade de cópia em um data factory do Azure para copiar dados de uma tabela em uma página da Web para outro armazenamento de dados. Este artigo se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral de movimentação de dados com a atividade de cópia e combinações de armazenamento de dados com suporte.

Atualmente, o data factory dá suporte apenas para a movimentação de dados de uma tabela da Web para outros armazenamentos de dados, mas não para a movimentação de dados de outros armazenamentos de dados para uma tabela da Web.

## Exemplo: copiar dados de uma tabela da Web para o Blob do Azure

O exemplo a seguir mostra:

1.	Um serviço vinculado do tipo [Web](#web-linked-service-properties).
2.	Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [WebTable](#WebTable-dataset-properties).
4.	Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	O [pipeline](data-factory-create-pipelines.md) com a Atividade de Cópia que usa [WebSource](#websource-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia dados de uma tabela da Web para um blob do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

O exemplo a seguir mostra como copiar dados de uma tabela da Web para um blob do Azure. No entanto, os dados podem ser copiados diretamente para qualquer um dos coletores declarados no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md) usando a atividade de cópia no Azure Data Factory.

**Serviço vinculado à Web** Este exemplo usa o serviço vinculado à Web com autenticação anônima. Confira a seção [Serviço vinculado à Web](#web-linked-service-properties) para ver os diferentes tipos de autenticação que você pode usar.

	{
	    "name": "WebLinkedService",
	    "properties":
	    {
	        "type": "Web",
	        "typeProperties":
	        {
	            "authenticationType": "Anonymous",
	            "url" : "http://finance.yahoo.com/q/"
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

O **conjunto de dados de entrada de WebTable** definindo **external** como **true** e especificando a política **externalData** (opcional) informa ao serviço Data Factory que essa é uma tabela externa ao data factory e não é produzida por uma atividade desse data factory.

> [AZURE.NOTE] Confira a seção [Obter índice de uma tabela em uma página HTML](#get-index-of-a-table-in-an-html-page) a fim de ver as etapas para obter o índice de uma tabela em uma página HTML.

	
	{
	    "name": "WebTableInput",
	    "properties": {
	        "type": "WebTable",
	        "linkedServiceName": "WebLinkedService",
	        "typeProperties": {
	            "index": 4,
	            "path": "hp?s=MSFT+Historical+Prices"
	        },
	        "external": true,
	        "availability": {
	            "frequency": "Hour",
	            "interval":  1
	        }
	    }
	}



**Conjunto de dados de saída de Blob do Azure**

Os dados são gravados em um novo blob a cada hora (frequência: hora, intervalo: 1).

	{
	    "name": "AzureBlobOutput",
	    "properties":
	    {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties":
	        {
	            "folderPath": "MSFTHistoricalPrices"
	        },
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}




**Pipeline com Atividade de cópia**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída acima e agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **WebSource** e o tipo **sink** está definido como **BlobSink**.

Confira [Propriedades do tipo WebSource](#websource-copy-activity-type-properties) para obter a lista de propriedades com suporte da WebSource.
	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "WebTableToAzureBlob",
	        "description": "Copy from a Web table to an Azure blob",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "WebTableInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "WebSource"
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


## Propriedades do serviço vinculado à Web

A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado à Web.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- | 
| type | A propriedade type deve ser definida como: **Web** | Sim | 
| Url | URL para a origem da Web | Sim |
| userName | Nome de usuário para autenticação básica. | Sim (para autenticação básica)
| Senha | Senha de autenticação básica. | Sim (para autenticação básica)
| authenticationType | Anônima, Básica ou API da Web. | Sim |
| apiKey | ApiKey para autenticação de API da Web. | Sim (para autenticação de API da Web)|   

### Usando a autenticação anônima

	{
	    "name": "web",
	    "properties":
	    {
	        "type": "Web",
	        "typeProperties":
	        {
	            "authenticationType": "Anonymous",
	            "url" : "http://finance.yahoo.com/q/"
	        }
	    }
	}


### Usando a autenticação Básica
	
	{
	    "name": "web",
	    "properties":
	    {
	        "type": "Web",
	        "typeProperties":
	        {
	            "authenticationType": "basic",
	            "url" : "http://myit.mycompany.com/",
	            "userName": "Administrator",
	            "password": "password"
	        }
	    }
	}


## Propriedades de conjunto de dados WebTable

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). Seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção typeProperties para o conjunto de dados do tipo **WebTable** tem as propriedades a seguir

Propriedade | Descrição | Obrigatório
:-------- | :----------- | :--------
type | tipo do conjunto de dados. deve ser definido como **WebTable** | Sim
path | Uma URL relativa para o recurso que contém a tabela. | Não. Quando o caminho não for especificado, apenas a URL especificada na definição do serviço vinculado será usada. 
índice | O índice da tabela no recurso. Confira a seção [Obter índice de uma tabela em uma página HTML](#get-index-of-a-table-in-an-html-page) a fim de ver as etapas para obter o índice de uma tabela em uma página HTML. | Sim


**Exemplo:**

	{
	    "name": "WebTableInput",
	    "properties": {
	        "type": "WebTable",
	        "linkedServiceName": "WebLinkedService",
	        "typeProperties": {
	            "index": 4,
	            "path": "hp?s=MSFT+Historical+Prices"
	        },
	        "external": true,
	        "availability": {
	            "frequency": "Hour",
	            "interval":  1
	        }
	    }
	}

## WebSource – propriedades de tipo de atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, consulte o artigo [Criando pipelines](data-factory-create-pipelines.md). Propriedades como nome, descrição, tabelas de entrada e saída, diversas políticas, etc. estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis na seção typeProperties da atividade, por outro lado, variam de acordo com cada tipo de atividade e, no caso de Atividade de cópia, variam dependendo dos tipos de fontes e coletores.

No caso da atividade de cópia, quando a fonte é do tipo **WebSource**, não há suporte a propriedades adicionais no momento.

## Obter o índice de uma tabela em uma página HTML

1. Inicie o **Excel 2016** e alterne para a guia **Dados**.  
2. Clique em **Nova Consulta** na barra de ferramentas, aponte para **De Outras Fontes** e clique em **Da Web**.
	
	![Menu do Power Query](./media/data-factory-web-table-connector/PowerQuery-Menu.png) 
3. Na caixa de diálogo **Da Web**, digite a **URL** que você usaria no JSON de serviço vinculado (por exemplo: https://en.wikipedia.org/wiki/) juntamente com o caminho que você especificaria para o conjunto de dados (por exemplo: AFI 27s\_100\_Years de %... 100\_Movies) e clique em **OK**. 

	![Do diálogo da Web](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

	URL usada neste exemplo: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies 
4.  Se você vir a caixa de diálogo **Acessar conteúdo da Web**, selecione a **autenticação** de **URL** correta e clique em **Conectar**. 

	![Acessar caixa de diálogo de conteúdo da Web](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5.  Clique em um item de **tabela** na exibição de árvore para ver o conteúdo da tabela e clique em **Editar** na parte inferior.  

	![Diálogo de navegador](./media/data-factory-web-table-connector/Navigator-DialogBox.png)

5. Na janela **Editor de Consultas**, clique no botão **Editor Avançado** na barra de ferramentas.

	![Botão Editor Avançado](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)

6. Na caixa de diálogo Editor Avançado, o número ao lado de "Origem" é o índice.

	![Editor Avançado – índice](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)


Se você estiver usando o Excel 2013, use [Microsoft Power Query para Excel](https://www.microsoft.com/download/details.aspx?id=39379) a fim de obter o índice. Confira o artigo [Conectar-se a uma página da Web](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) para obter detalhes. As etapas são semelhantes se você estiver usando o [Microsoft Power BI para Desktop](https://powerbi.microsoft.com/desktop/).

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## Desempenho e Ajuste  
Veja o [Guia de Desempenho e Ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho e a movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.

<!---HONumber=AcomDC_0504_2016-->