<properties 
   pageTitle="Cenários de dados envolvendo o Repositório Data Lake | Microsoft Azure" 
   description="Entenda os diferentes cenários e as ferramentas usando quais dados podem ser ingeridos, processados, baixados e visualizados em um Repositório Data Lake" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="03/09/2016"
   ms.author="nitinme"/>

# Cenários de dados envolvendo o repositório Azure Data Lake

Há quatro estágios principais no processamento de big data:

* Ingestão de grandes quantidades de dados em um repositório de dados, em tempo real ou em lotes
* Processamento dos dados
* Download dos dados
* Visualização dos dados


Neste artigo, analisamos esses estágios com relação ao Repositório Azure Data Lake para entender as opções e as ferramentas disponíveis para atender às suas necessidades de big data.

## Ingerir dados no Repositório Data Lake

Esta seção destaca as diferentes fontes de dados e as diferentes maneiras que os dados podem ser ingeridos em uma conta do Repositório Data Lake.

![Ingerir dados no Repositório Data Lake](./media/data-lake-store-data-scenarios/ingest-data.png "Ingerir dados no Repositório Data Lake")

### Dados ad hoc

Representam conjuntos de dados menores que são usados para criar protótipos de um aplicativo de big data. Há diferentes maneiras de ingerir dados ad hoc, dependendo da fonte dos dados.

| Fonte de dados | Ingeri-la usando |
|--------------------|----------------------------------------------------------------------------------------|
| Computador local | <ul> <li>[Portal do Azure](/data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[CLI entre plataformas do Azure](data-lake-store-get-started-cli.md)</li> <li>[Usando as Ferramentas do Data Lake para o Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md#upload-source-data-files) </li></ul> |
| Blob de Armazenamento do Azure | <ul> <li>[Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store)</li> <li>[Ferramenta AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li> </ul> |

 
### Dados transmitidos

Representam os dados que podem ser gerados por várias fontes, como aplicativos, dispositivos, sensores, etc. Esses dados podem ser ingeridos em um Repositório Data Lake por uma variedade de ferramentas. Essas ferramentas geralmente capturam e processam os dados em um evento em tempo real e gravam os eventos em lotes no Repositório Data Lake para que depois eles possam ser processados.

Veja as ferramentas que você pode usar:
 
* [Stream Analytics do Azure](../stream-analytics-data-lake-output) – Eventos incluídos nos Hubs de Eventos podem ser gravados no Azure Data Lake usando uma saída do Repositório Azure Data Lake.
* [Azure HDInsight Storm](../hdinsight/hdinsight-storm-write-data-lake-store.md) – É possível gravar dados diretamente do cluster Storm no Repositório Data Lake.
* [EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost) – É possível receber eventos dos Hubs de Eventos e gravá-los no Repositório Data Lake usando o [SDK do .NET do Repositório Data Lake](data-lake-store-get-started-net-sdk.md).

### Dados relacionais

Você também pode originar dados nos bancos de dados relacionais. Durante um período, os bancos de dados relacionais coletam grandes volumes de dados que podem fornecer informações importantes se processados por meio de um pipeline de big data. É possível usar as ferramentas a seguir para mover tais dados para o Repositório Data Lake.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/data-factory-data-movement-activities.md)

### Dados de log do servidor Web (carregar usando aplicativos personalizados)

Esse tipo de conjunto de dados é especificamente chamado porque a análise dos dados do log do servidor Web é um caso de uso comum para aplicativos de big data e requer que grandes volumes de arquivos de log sejam carregados no Repositório Data Lake. Você pode usar qualquer uma das ferramentas a seguir para escrever seus próprios scripts ou aplicativos para carregar esses dados.

* [CLI entre plataformas do Azure](data-lake-store-get-started-cli.md)
* [PowerShell do Azure](data-lake-store-get-started-powershell.md)
* [SDK .NET do Repositório Azure Data Lake](data-lake-store-get-started-net-sdk.md)
* [Azure Data Factory](../data-factory/data-factory-data-movement-activities.md)

Para carregar dados de log do servidor Web, e também para carregar outros tipos de dados (por exemplo, dados de sentimentos sociais), é uma boa abordagem escrever seus próprios scripts/aplicativos personalizados, pois eles proporcionam a flexibilidade para incluir seus dados carregando o componente como parte do aplicativo maior de big data. Em alguns casos, esse código pode assumir a forma de um script ou um utilitário simples de linha de comando. Em outros casos, o código pode ser usado para integrar o processamento de big data em um aplicativo ou uma solução de negócios.


### Dados associados aos clusters Azure HDInsight

A maioria dos tipos de cluster HDInsight (Hadoop, HBase, Storm) é compatível com o Repositório Data Lake como um repositório de armazenamento de dados. Os clusters HDInsight acessam dados dos WASB (Blobs de Armazenamento do Azure). Para obter melhor desempenho, você pode copiar os dados do WASB em uma conta do Repositório Data Lake associada ao cluster. Você pode usar as ferramentas a seguir para copiar os dados.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [Serviço AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store)

### Conjuntos de dados realmente grandes

Carregar conjuntos de dados que incluem vários terabytes usando os métodos descritos acima, às vezes, pode ser uma tarefa lenta e onerosa. Nesses casos, você pode usar as opções a seguir.

* **Carregamento de dados “offline”**. É possível usar o [serviço de Importação/Exportação do Azure](../storage/storage-import-export-service.md) para enviar unidades de disco rígido com seus dados para um datacenter do Azure; em seguida, os dados são carregados em um Blob de Armazenamento do Azure. Em seguida, é possível usar o [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store) ou a [ferramenta AdlCopy](data-lake-store-copy-data-azure-storage-blob.md) para mover dados dos Blobs de Armazenamento do Azure para o Repositório Data Lake.

	>[AZURE.NOTE] Ao usar o serviço Importar/Exportar, os tamanhos dos arquivos nos discos que você envia ao datacenter do Azure não devem ultrapassar 200 GB.

* **Usando a Rota Expressa do Azure**. A Rota Expressa do Azure permite criar conexões privadas entre os datacenters do Azure e a infraestrutura presente em seu local. Isso proporciona uma opção confiável para transferir grandes quantidades de dados. Para obter mais informações, confira a [documentação da Rota Expressa do Azure](../expressroute/expressroute-introduction.md).

## Processar dados armazenados no Repositório Data Lake

Depois que os dados estiverem disponíveis no Repositório Data Lake, você poderá fazer uma análise nesses dados usando os aplicativos de big data compatíveis. Atualmente, é possível usar o Azure HDInsight e a Análise do Azure Data Lake para executar trabalhos de análise de dados nos dados armazenados em um Repositório Data Lake.

![Analisar dados no Repositório Data Lake](./media/data-lake-store-data-scenarios/analyze-data.png "Analisar dados no Repositório Data Lake")

Você pode analisar os exemplos a seguir.

* [Criar um cluster HDInsight com o Repositório Data Lake como armazenamento](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Usar a Análise Data Lake do Azure com o Repositório Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)



## Baixar dados do Repositório Data Lake

Você também pode querer baixar ou mover dados do Repositório Azure Data Lake para cenários como:

* Mover dados para outros repositórios para fazer interface com os pipelines de processamento de dados existentes. Por exemplo, você pode querer mover os dados do Repositório Data Lake para o Banco de Dados SQL do Azure ou SQL Server local.
* Baixar dados no computador local para processamento em ambientes IDE durante a criação de protótipos de aplicativo.

![Gerar dados do Repositório Data Lake](./media/data-lake-store-data-scenarios/egress-data.png "Gerar dados do Repositório Data Lake")

Nesses casos, você pode usar qualquer uma das opções a seguir:

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/data-factory-data-movement-activities.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

Você também pode usar os métodos a seguir para escrever seu próprio script/aplicativo e baixar dados do Repositório Data Lake.

* [CLI entre plataformas do Azure](data-lake-store-get-started-cli.md)
* [PowerShell do Azure](data-lake-store-get-started-powershell.md)
* [SDK .NET do Repositório Azure Data Lake](data-lake-store-get-started-net-sdk.md)

## Visualizar dados no Repositório Data Lake

Você pode usar uma combinação de serviços para criar representações visuais de dados armazenados no Repositório Data Lake.

![Visualizar dados no Repositório Data Lake](./media/data-lake-store-data-scenarios/visualize-data.png "Visualizar dados no Repositório Data Lake")

* É possível começar usando o [Azure Data Factory para mover dados do Repositório Data Lake para um SQL Data Warehouse do Azure](../data-factory/data-factory-data-movement-activities.md#supported-data-stores)
* Depois disso, você pode [integrar o Power BI ao SQL Data Warehouse do Azure](../sql-data-warehouse/sql-data-warehouse-integrate-power-bi) a fim de criar a representação visual dos dados.

<!---HONumber=AcomDC_0323_2016-->