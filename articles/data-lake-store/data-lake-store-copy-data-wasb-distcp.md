<properties
   pageTitle="Copie dados de e para o WASB no Repositório do Data Lake usando Distcp| Microsoft Azure"
   description="Use a ferramenta Distcp para copiar dados de e para os Blobs de Armazenamento do Azure para o Repositório Data Lake"
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
   ms.date="05/11/2016"
   ms.author="nitinme"/>

# Use Distcp para copiar dados entre o Repositório do Data Lake e os Blobs de Armazenamento do Azure

Depois de criar um cluster do HDInsight que tem acesso a uma conta de Repositório do Data Lake, você pode usar ferramentas do ecossistema Hadoop, como Distcp, para copiar dados **do e para** um armazenamento de cluster do HDInsight (WASB) em uma conta do Repositório do Data Lake. Esse artigo fornece instruções sobre como fazer isso.

##Pré-requisitos

Antes de começar este artigo, você deve ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Habilite sua assinatura do Azure** para a visualização pública do Repositório Data Lake. Veja [instruções](data-lake-store-get-started-portal.md#signup).
- **Cluster Azure HDInsight** com acesso a uma conta do Repositório do Data Lake. Consulte [Criar um cluster do HDInsight com o Repositório do Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de habilitar a área de trabalho remota para o cluster.

## Você aprende rapidamente com vídeos?

[Assista a este vídeo](https://mix.office.com/watch/1liuojvdx6sie) sobre como copiar dados entre o Repositório do Data Lake e os Blobs de Armazenamento do Azure usando o DistCp.

## Use Distcp da área de trabalho remota (cluster do Windows) ou o SSH (cluster do Linux)

Um cluster do HDInsight é fornecido com o utilitário Distcp, que pode ser usado para copiar dados de fontes diferentes em um cluster do HDInsight. Se você tiver configurado o cluster do HDInsight para usar o Repositório do Data Lake como um armazenamento adicional, o utilitário Distcp pode ser usado prontamente para copiar dados de e para uma conta do Repositório do Data Lake. Nesta seção, vamos examinar como usar o utilitário Distcp.

1. Se você tiver um cluster do Windows, faça com que ele seja remoto em um cluster do HDInsight que tenha acesso a uma conta de Repositório do Data Lake. Para instruções, confira [Connect to clusters using RDP](../hdinsight/hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp) (Conectar-se aos clusters usando RDP). No cluster de área de trabalho, abra a linha de comando do Hadoop.

	Se você tiver um cluster do Linux, use SSH para se conectar ao cluster. Confira [Conectar-se a um cluster HDInsight baseado em Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster). Execute os comandos do prompt de SSH.

3. Verifique se você pode acessar os WASB (Blobs de Armazenamento do Azure). Execute o comando a seguir:

		hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

	Isso deve fornecer uma lista de conteúdo no blob de armazenamento.

4. Da mesma forma, verifique se você pode acessar a conta de Repositório do Data Lake do cluster. Execute o comando a seguir:

		hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net:443/

	Isso deve fornecer uma lista de arquivos/pastas na conta de Repositório do Data Lake.

5. Use Distcp para copiar dados do WASB para uma conta de Repositório do Data Lake.

		hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder

	Isso copiará o conteúdo da pasta **/example/data/gutenberg/** no WASB para **/myfolder** na conta de Repositório do Data Lake.

6. De modo semelhante, use Distcp para copiar dados da conta de Repositório do Data Lake para o WASB.

		hadoop distcp adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

	Isso copiará o conteúdo de **/myfolder** na conta de Repositório do Data Lake para a pasta **/example/data/gutenberg/** no WASB.

## Consulte também

- [Copiar dados de Blobs do Armazenamento do Azure para o Repositório Data Lake](data-lake-store-copy-data-azure-storage-blob.md)
- [Proteger dados no Repositório Data Lake](data-lake-store-secure-data.md)
- [Usar a Análise Data Lake do Azure com o Repositório Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usar o Azure HDInsight com o Repositório Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0518_2016-->