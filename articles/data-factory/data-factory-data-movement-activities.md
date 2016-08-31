<properties 
	pageTitle="Mover dados usando a Atividade de Cópia | Microsoft Azure" 
	description="Saiba mais sobre a movimentação de dados em pipelines do Data Factory: migração de dados entre armazenamentos em nuvem, entre os locais e a nuvem. Usar a Atividade de Cópia." 
	keywords="copiar dados, movimentação de dados, migração de dados, transferir dados"
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
	ms.date="08/08/2016" 
	ms.author="spelluru"/>

# Mover dados usando Atividade de Cópia

## Visão geral
O Azure Data Factory permite que você use a Atividade de Cópia para copiar dados de formas diferentes de uma variedade de locais e na nuvem fontes de dados no Azure, para que eles possam ser transformados e analisados ainda mais. A Atividade de Cópia também pode ser usada para publicar resultados de análise e transformação para Business Intelligence (BI) e consumo de aplicativos.

![Função da atividade de cópia](media/data-factory-data-movement-activities/copy-activity.png)

A Atividade de Cópia é alimentada por um [serviço disponível globalmente](#global) seguro, confiável e escalonável. Este artigo fornece detalhes sobre a movimentação de dados no Data Factory e na Atividade de Cópia. Primeiro, vejamos como a migração de dados ocorre entre dois armazenamentos de dados de nuvem, entre um armazenamento de dados local e um armazenamento de dados em nuvem

> [AZURE.NOTE] Para saber mais sobre como definir as atividades em geral, confira o artigo [Noções básicas de pipelines e atividades](data-factory-create-pipelines.md).

### Copiando dados entre dois armazenamentos de dados em nuvem
Quando os armazenamentos de dados de origem e coletores (destino) residem na nuvem, a atividade de cópia passa pelas fases a seguir para copiar/mover os dados da origem para o coletor. O serviço que capacita a Atividade de Cópia executa o seguinte:

1. Lê dados do armazenamento de dados de origem
2. Executa a serialização/desserialização, a compactação/descompactação, o mapeamento de coluna e a conversão de tipo baseado nas configurações de conjuntos de dados de entrada e de saída e em Atividade de Cópia
3.	Grava dados no armazenamento de dados de destino

O serviço escolhe automaticamente a região ideal para executar a movimentação de dados, geralmente a região mais próxima do armazenamento de dados do coletor.

![cópia de nuvem para nuvem](./media/data-factory-data-movement-activities/cloud-to-cloud.png)


### Copiar dados entre um armazenamento de dados local e um armazenamento de dados em nuvem
Para mover com segurança dados entre armazenamentos de dados locais dentro do seu firewall corporativo armazenamento de dados de nuvem, você precisará instalar o Gateway de Gerenciamento de Dados, que é um agente que permite a movimentação e o processamento de dados híbridos em seu computador local. O gateway de gerenciamento de dados pode ser instalado no mesmo computador do próprio armazenamento de dados ou em computador separado que tenha acesso ao armazenamento de dados.. Neste cenário, a serialização/desserialização, a compactação/descompactação, o mapeamento de coluna e a conversão de tipo são executadas pelo gateway de gerenciamento de dados. Os dados não fluem pelo serviço Azure Data Factory nesse caso. O gateway de gerenciamento de dados grava diretamente os dados no repositório de destino.

![cópia local para nuvem](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

Consulte o artigo [Mover dados entre armazenamentos de dados locais e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter uma introdução e um passo a passo e o artigo [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md) artigo para obter informações detalhadas sobre o Gateway de Gerenciamento de Dados.

Você também pode mover dados de/para armazenamentos de dados com suporte hospedados nas VMs do Azure IaaS (máquinas virtuais de infraestrutura como serviço) usando o gateway de gerenciamento de dados. Nesse caso, o gateway de gerenciamento de dados pode ser instalado na mesma VM do Azure em que está o armazenamento de dados ou em uma VM separada que tenha acesso ao armazenamento de dados.

## Fontes de dados e formatos com suporte
A atividade de cópia copia os dados de um armazenamento de dados de **origem** para um armazenamento de dados **coletor**. A fábrica de dados oferece suporte aos seguintes armazenamentos de dados e os **dados de qualquer fonte podem ser gravados em qualquer coletor**. Clique em um armazenamento de dados para saber como copiar dados de/para ele.

Categoria | Armazenamento de dados | Tem suporte como origem | Tem suporte como coletor
:------- | :--------- | :------------------ | :-----------------
As tabelas | [Blob do Azure](data-factory-azure-blob-connector.md) <br/> [Azure Data Lake Store](data-factory-azure-datalake-connector.md) <br/> [Banco de Dados SQL do Azure](data-factory-azure-sql-connector.md) <br/> [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md) <br/> [Tabela do Azure](data-factory-azure-table-connector.md) <br/> [Banco de Dados de Documentos do Azure](data-factory-azure-documentdb-connector.md) <br/> | ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ | ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ 
Bancos de dados | [SQL Server](data-factory-sqlserver-connector.md)* <br/> [Oracle](data-factory-onprem-oracle-connector.md)* <br/> [MySQL](data-factory-onprem-mysql-connector.md)* <br/> [DB2](data-factory-onprem-db2-connector.md)* <br/> [Teradata](data-factory-onprem-teradata-connector.md)* <br/> [PostgreSQL](data-factory-onprem-postgresql-connector.md)* <br/> [Sybase](data-factory-onprem-sybase-connector.md)* <br/>[Cassandra](data-factory-onprem-cassandra-connector.md)* <br/>[MongoDB](data-factory-on-premises-mongodb-connector.md)* | ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓<br/> ✓ <br/> ✓ <br/> ✓ | ✓ <br/> ✓ <br/> &nbsp; <br/> &nbsp; <br/> &nbsp; <br/> &nbsp;<br/> &nbsp;<br/> &nbsp;<br/> &nbsp; 
Arquivo | [Sistema de Arquivos](data-factory-onprem-file-system-connector.md)* <br/> [Sistema de Arquivos Distribuídos Hadoop (HDFS)](data-factory-hdfs-connector.md)* | ✓ <br/> ✓ <br/> | ✓ <br/> &nbsp; 
Outros | [Salesforce](data-factory-salesforce-connector.md)<br/> [ODBC genérico](data-factory-odbc-connector.md)* <br/> [OData genérico](data-factory-odata-connector.md) <br/> [Tabela da Web (tabela do HTML)](data-factory-web-table-connector.md) <br/> [GE Historian](data-factory-odbc-connector.md#ge-historian-store)* | ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ | &nbsp; <br/> &nbsp; <br/> &nbsp; <br/> &nbsp;<br/> &nbsp;<br/> &nbsp;

> [AZURE.NOTE] Os armazenamentos de dados com * podem estar no local ou no Azure IaaS e exigem a instalação do [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md) em um computador Azure IaaS/local.

Se você precisar mover os dados para/a partir de um armazenamento de dados sem suporte para a **Atividade de Cópia**, poderá usar a **atividade personalizada** no Data Factory com sua própria lógica para copiar/mover os dados. Confira o artigo [Usar atividades personalizadas em um pipeline do Azure Data Factory](data-factory-use-custom-activities.md) para obter detalhes sobre como criar e usar uma atividade personalizada.

### Formatos de arquivo com suporte
A Atividade de Cópia pode copiar os arquivos como estão entre dois armazenamentos de dados como Blob do Azure, o Sistema de Arquivos e o Sistema de Arquivos Distribuídos Hadoop (HDFS). Para fazer isso, você pode ignorar a [seção de formatação](data-factory-create-datasets.md) nas definições de conjunto de dados de entrada e de saída e os dados serão copiados com eficiência sem qualquer serialização/desserialização.

A Atividade de Cópia também lê e grava em arquivos em formatos especificados: texto, Avro, ORC e JSON. Aqui estão alguns exemplos de atividades de cópia que você pode obter:

-	Copiar dados em formato de texto (CSV) do Blob do Azure e gravá-los no SQL Azure
-	Copiar arquivos no formato de texto (CSV) do Sistema de Arquivos local e gravá-los no Blob do Azure no formato Avro
-	Copiar os dados no Banco de Dados SQL do Azure e gravá-los no HDFS local no formato ORC



## <a name="global"></a>Movimentação de dados globalmente disponível
O serviço que capacita a Atividade de Cópia está disponível globalmente nas regiões e nas regiões geográficas a seguir, mesmo que o próprio Azure Data Factory esteja disponível somente nas regiões oeste dos EUA, leste dos EUA e na Europa Setentrional. A topologia globalmente disponível garante a movimentação de dados eficiente, evitando saltos usualmente entre regiões. Veja [Serviços por região](https://azure.microsoft.com/regions/#services) para obter a disponibilidade do serviço Data Factory e o Movimento de Dados em uma região.

### Copiar dados entre armazenamentos de dados em nuvem
Quando os armazenamentos de dados de origem e de coletor residem na nuvem, o Azure Data Factory usa uma implantação de serviço na região que está mais perto do local do coletor na mesma região geográfica para realizar a movimentação de dados. Consulte a tabela a seguir para ver o mapeamento:

Região do armazenamento de dados de destino | Região usada para movimentação de dados
:----------------------------------- | :----------------------------
Leste dos EUA | Leste dos EUA
Leste dos EUA 2 | Leste dos EUA 2
Centro dos EUA | Centro dos EUA
Oeste dos EUA | Oeste dos EUA
Centro-Norte dos EUA | Centro-Norte dos EUA
Centro-Sul dos Estados Unidos | Centro-Sul dos Estados Unidos
Norte da Europa | Norte da Europa
Europa Ocidental | Europa Ocidental
Sudeste Asiático | Sudeste da Ásia
Ásia Oriental | Sudeste da Ásia
Leste do Japão | Leste do Japão
Oeste do Japão | Leste do Japão
Sul do Brasil | Sul do Brasil
Leste da Austrália | Leste da Austrália
Sudeste da Austrália | Sudeste da Austrália
Índia Central | Índia Central
Sul da Índia | Índia Central
Índia Ocidental | Índia Central


> [AZURE.NOTE] Se a região do repositório de dados de destino não estiver na lista acima, a Atividade de Cópia falhará em vez de passar por uma região alternativa.

### Copiar dados entre um armazenamento de dados local e um armazenamento de dados em nuvem
Quando os dados estiverem sendo copiados entre locais (ou uma VM do Azure IaaS) e a nuvem, a movimentação de dados será realizada pelo [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md) em um computador local ou em uma VM do Azure IaaS. Os dados não fluirão pelo serviço na nuvem, a menos que você use o recurso [cópia preparada](data-factory-copy-activity-performance.md#staged-copy), em que os fluxos de dados de caso por meio do armazenamento de Blobs do Azure de preparo antes de serem gravados no armazenamento de dados do coletor.


## Criar pipeline com atividade de cópia 
Você pode criar um pipeline com uma atividade de cópia de duas maneiras:

### Usando o Assistente de Cópia
O **Assistente de Cópia do Data Factory** permite que você crie um pipeline com uma atividade de cópia para copiar dados de fontes compatíveis para destinos **sem gravar as definições de JSON** para serviços vinculados, conjuntos de dados e pipelines. Veja o tutorial [Assistente de cópia do Data Factory](data-factory-copy-wizard.md) para obter detalhes sobre o assistente.

### Usando scripts JSON
Você pode usar o Editor do Data Factory no Portal do Azure (ou) no Visual Studio (ou) no Azure PowerShell para criar uma definição de JSON para um pipeline com atividade de cópia e implantá-lo para criar o pipeline no Data Factory. Veja [Tutorial: Usar a Atividade de Cópia em um pipeline do Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter um tutorial com instruções passo a passo.

As propriedades JSON, como nome, descrição, tabelas de entrada e saída, políticas etc., estão disponíveis para todos os tipos de atividades. As propriedades disponíveis na seção **typeProperties** da atividade, por outro lado, variam de acordo com cada tipo de atividade.

No caso da Atividade de Cópia, a seção **typeProperties** varia de acordo com os tipos de fonte e coletor. Clique em uma fonte/um coletor na seção [Fontes/Coletores com suporte](#supported-data-stores) para aprender sobre as propriedades de tipo compatíveis com a Atividade de Cópia para esse repositório de dados.

Veja um exemplo de definição de JSON:

	{
	  "name": "ADFTutorialPipeline",
	  "properties": {
	    "description": "Copy data from Azure blob to Azure SQL table",
	    "activities": [
	      {
	        "name": "CopyFromBlobToSQL",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "InputBlobTable"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "OutputSQLTable"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource"
	          },
	          "sink": {
	            "type": "SqlSink",
	            "writeBatchSize": 10000,
	            "writeBatchTimeout": "60:00:00"
	          }
	        },
	        "Policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	    ],
	    "start": "2016-07-12T00:00:00Z",
	    "end": "2016-07-13T00:00:00Z"
	  }
	} 

A agenda definida no conjunto de dados de saída determina quando a atividade é executada (por exemplo, **diária**: frequência: dia e intervalo: 1). A atividade copia dados de um conjunto de dados de entrada (**origem**) para um conjunto de dados de saída (**coletor**). Você pode especificar mais de um conjunto de dados de entrada para a atividade de cópia e eles serão usados para verificar as dependências antes que a atividade seja executada, mas somente os dados do primeiro conjunto de dados serão copiados para o conjunto de dados de destino. Confira [Agendamento e execução](data-factory-scheduling-and-execution.md) para mais detalhes.

## Desempenho e ajuste 
Confira o artigo [Guia de Desempenho e Ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) que descreve os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory. Ele também lista o desempenho observado durante os testes internos e discute várias maneiras de otimizar o desempenho da Atividade de Cópia.

## Agendamento e cópia sequencial
Confira o artigo [Agendamento e execução](data-factory-scheduling-and-execution.md) para obter informações detalhadas sobre como funcionam o agendamento e a execução no Data Factory.

É possível executar várias operações de cópia, uma após a outra de maneira sequencial/ordenada. Veja a seção [Cópia solicitada](data-factory-scheduling-and-execution.md#ordered-copy) no artigo.

## Conversões de tipo 
Armazenamentos de dados diferentes têm sistemas de tipo nativo diferentes. A atividade de cópia executa conversões de tipo automáticas dos tipos de fonte para os tipos de coletor a abordagem de duas etapas a seguir:

1. Converter de tipos de fonte nativos para o tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Você pode encontrar o mapeamento para um determinado sistema de tipo nativo para o .NET para o armazenamento de dados nos artigos específicos do respectivo armazenamento de dados (clique no respectivo link na tabela [Armazenamentos de dados com suporte](#supported-data-stores)). Você pode usar esses mapeamentos para determinar os tipos apropriados ao criar tabelas para que as conversões certas sejam executadas durante a atividade de cópia.

 
## Próximas etapas
- Confira [Copiar dados do Blob do Azure para o SQL Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para saber mais sobre como usar a Atividade de Cópia para mover dados de um repositório de dados de origem para um repositório de dados de coletor em geral.
- Confira [Mover dados entre repositórios de dados locais e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) para saber mais sobre como mover dados de um repositório de dados local para um repositório de dados na nuvem.
 

<!---HONumber=AcomDC_0817_2016-->