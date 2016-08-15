<properties
   pageTitle="Integrando o Repositório Data Lake com outros Serviços do Azure | Azure"
   description="Noções básicas sobre como o Repositório Data Lake é integrado com outros serviços do Azure"
   documentationCenter=""
   services="data-lake-store"
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/02/2016"
   ms.author="nitinme"/>

# Integrando o Repositório Data Lake com outros Serviços do Azure

O Repositório Azure Data Lake pode ser usado em conjunto com outros serviços do Azure para habilitar uma maior variedade de cenários. O artigo a seguir lista os serviços com os quais o Repositório Data Lake pode ser integrado.

## Usar o Repositório Data Lake com o Azure HDInsight

É possível provisionar um cluster [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) que usa o Repositório do Data Lake como o armazenamento compatível com HDFS. Para esta versão, para clusters Hadoop e Storm no Windows e Linux, é possível usar o Repositório Data Lake somente como um armazenamento adicional. Esses clusters ainda usam o Armazenamento do Azure (WASB) como o armazenamento padrão. No entanto, para clusters HBase no Windows e Linux, você pode usar o Repositório Data Lake como o armazenamento padrão ou adicional, ou ambos.

Para obter instruções sobre como provisionar um cluster HDInsight com o Repositório Data Lake, veja:

* [Provisionar um cluster HDInsight com o Repositório do Data Lake usando o Portal do Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Provisionar um cluster HDInsight com o Repositório Data Lake usando o Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

**Prefere vídeos?** Siga os links abaixo para assistir a vídeos com instruções sobre como usar o Repositório Data Lake com clusters HDInsight.

* [Criar um cluster HDInsight com acesso ao Repositório Data Lake](https://mix.office.com/watch/l93xri2yhtp2)
* Depois que o cluster for configurado, [Acesse dados no Repositório Data Lake usando scripts do Hive e Pig](https://mix.office.com/watch/1n9g5w0fiqv1q)


## Usar o Repositório Data Lake com a Análise Azure Data Lake

A [Análise Azure Data Lake](../data-lake-analytics/data-lake-analytics-overview.md) permite que você trabalhe com Big Data em escala na nuvem. Ela provisiona recursos de maneira dinâmica e permite fazer a análise de terabytes ou até mesmo de exabytes de dados que podem ser armazenados em várias fontes de dados com suporte, sendo uma delas o Repositório Data Lake. A Análise Data Lake é especialmente otimizada para funcionar com o Repositório Azure Data Lake - fornecendo o mais alto nível de desempenho, taxa de transferência e paralelização para suas cargas de trabalho de Big Data.

Para obter instruções sobre como usar a Análise Data Lake com o Repositório Data Lake, veja [Introdução à Análise Data Lake usando o Repositório Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

**Prefere vídeos?** Siga os links abaixo para assistir a vídeos com instruções sobre como usar o Repositório Data Lake com clusters HDInsight.

* [Conectar a Análise Azure Data Lake ao Repositório Azure Data Lake](https://mix.office.com/watch/qwji0dc9rx9k)
* [Acessar o Repositório Data Lake por meio da Análise Data Lake](https://mix.office.com/watch/1n0s45up381a8)


## Usar o Repositório Data Lake com o Azure Data Factory

É possível usar o [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) para receber dados de tabelas do Azure, Banco de Dados SQL do Azure, SQL Data Warehouse do Azure, Blobs de Armazenamento do Azure e bancos de dados locais. Como cidadão de primeira classe no ecossistema do Azure, o Azure Data Factory pode ser usado para orquestrar a ingestão de dados dessas fontes no Repositório Azure Data Lake.

Para obter instruções sobre como usar o Azure Data Factory com o Repositório Data Lake , veja [Mover dados para e do Repositório Data Lake usando o Data Factory](../data-factory/data-factory-azure-datalake-connector.md).

**Vídeos novamente!** Confira [Orquestração de dados usando o Azure Data Factory para o Repositório Azure Data Lake](https://mix.office.com/watch/1oa7le7t2u4ka).

## Copiar dados de Blobs do Armazenamento do Azure para o Repositório Data Lake

O Repositório Data Lake do Azure fornece uma ferramenta de linha de comando, AdlCopy, que permite copiar dados do Armazenamento de Blobs do Azure para o Repositório Data Lake. Para saber mais, confira [Copiar dados de Blobs do Armazenamento do Azure para o Repositório Data Lake](data-lake-store-copy-data-azure-storage-blob.md).

## Copiar dados entre o Banco de Dados SQL do Azure e o Repositório Data Lake

Você pode usar o Apache Sqoop para importar e exportar dados entre o Banco de Dados SQL do Azure e o Repositório Data Lake. Para saber mais, confira [Copiar dados entre o Repositório Data Lake e o Banco de Dados SQL do Azure usando o Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

**Assista a este vídeo** sobre [Uso do Apache Sqoop para mover dados entre fontes relacionais e o Repositório Azure Data Lake](https://mix.office.com/watch/1butcdjxmu114).

## Usar o Repositório Data Lake com o Stream Analytics

Você pode usar o Repositório Data Lake como uma das saídas para armazenar dados transmitidos usando o Stream Analytics do Azure. Para saber mais, confira [Transmitir dados do Blob de Armazenamento do Azure para o Repositório Data Lake usando o Stream Analytics do Azure](data-lake-store-stream-analytics.md).

## Usar o Repositório Data Lake com o Power BI

Você pode usar o Power BI para importar dados de uma conta do Repositório Data Lake para analisar e visualizar os dados. Para saber mais, confira [Analisar dados no Repositório Data Lake usando o Power BI](data-lake-store-power-bi.md).

## Usar o Repositório Data Lake com o Catálogo de Dados

Você pode registrar dados do Repositório Data Lake no Catálogo de Dados do Azure para tornar os dados detectáveis em toda a organização. Para saber mais, confira [Registrar dados do Repositório Data Lake no Catálogo de Dados do Azure](data-lake-store-with-data-catalog.md).


## Consulte também

- [Visão geral do Repositório Azure Data Lake](data-lake-store-overview.md)
- [Introdução ao Repositório Data Lake usando o Portal](data-lake-store-get-started-portal.md)
- [Introdução ao Repositório Data Lake usando o PowerShell](data-lake-store-get-started-powershell.md)

<!---HONumber=AcomDC_0803_2016-->