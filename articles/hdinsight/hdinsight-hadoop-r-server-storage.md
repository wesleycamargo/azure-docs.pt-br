<properties
   pageTitle="Opções do Armazenamento do Azure para o Servidor R no HDInsight (preview) | Azure"
   description="Conheça as diferentes opções de armazenamento disponíveis para usuários com o Servidor R no HDInsight (preview)"
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="paulettem"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="03/28/2016"
   ms.author="jeffstok"
/>

# Opções do Armazenamento do Azure para o Servidor R no HDInsight Premium (preview)

O Servidor R no HDInsight (preview) tem acesso ao Blob do Azure e, em breve, ao armazenamento do Azure Data Lake, como forma de persistir dados, código, objetos de resultado da análise, etc.

Ao criar um cluster Hadoop no HDInsight, especifique uma conta de armazenamento do Azure. Um contêiner de armazenamento de Blobs específico dessa conta foi desenvolvido para manter o sistema de arquivos do cluster que você cria, isto é, o HDFS (Sistema de Arquivos Distribuído Hadoop). Para fins de desempenho, o cluster HDInsight é criado no mesmo datacenter que a conta de armazenamento primária especificada. Para obter mais informações, consulte [Usar o Armazenamento de Blobs do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md "Usar o armazenamento de blobs do Azure com o HDInsight").


## Usar várias contas de Armazenamento de Blobs do Azure

Se necessário, será possível acessar várias contas de armazenamento ou contêineres do Azure com o cluster HDI. Para tanto, você precisará especificar as contas de armazenamento adicionais na interface de usuário na criação do cluster e seguir estas etapas para usá-las no R.

1.	Suponha que você crie um cluster HDInsight com um nome de conta de armazenamento de "storage1" com um contêiner padrão "container1". Você também especifica uma conta de armazenamento adicional "storage2".  
2.	Agora você copia um arquivo "mycsv.csv" no diretório "/share" e quer realizar uma análise nesse arquivo.  

    hadoop fs –mkdir /share hadoop fs –copyFromLocal myscsv.scv /share

3.	No código R, você define o nó do nome para "default" e define seu diretório e arquivo para processar

    myNameNode <- "default" myPort <- 0

  Local dos dados

    bigDataDirRoot <- "/share"  

  definir o contexto de computação do Spark

    mySparkCluster <- RxSpark(consoleOutput=TRUE)

  definir o contexto de computação

    rxSetComputeContext(mySparkCluster)

  definir o sistema de arquivos HDFS

    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

  especificar o arquivo de entrada no HDFS para análise

    inputFile <-file.path(bigDataDirRoot,"mycsv.csv")
 
Todas as referências de arquivo e diretório apontam para a conta de armazenamento wasb://container1@storage1.blob.core.windows.net, pois essa é a **conta de armazenamento padrão** associada ao cluster HDInsight.

Agora suponha que você queira processar um arquivo chamado "mySpecial.csv", localizado no diretório "/private" do contêiner "container2" no nome da conta de armazenamento "storage2".

No seu código R, altere a referência do nó do nome para a conta de armazenamento "storage2".

    myNameNode <- "wasb://container2@storage2.blob.core.windows.net"
    myPort <- 0

  Local dos dados

    bigDataDirRoot <- "/private"

  definir o contexto de computação do Spark

    mySparkCluster <- RxSpark(consoleOutput=TRUE)

  definir o contexto de computação

    rxSetComputeContext(mySparkCluster)

  definir o sistema de arquivos HDFS

    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

  especificar o arquivo de entrada no HDFS para análise

    inputFile <-file.path(bigDataDirRoot,"mySpecial.csv")
 
Todas as referências de diretório e arquivo agora apontam para a conta de armazenamento wasb://container2@storage2.blob.core.windows.net, pois esse é o **Nó do Nome** que você especificou.

Observe que você terá que configurar o diretório /user/RevoShare/<SSH username> na conta de armazenamento "storage2":

    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>


## Usar arquivos do Azure no nó de borda 

Também há uma opção conveniente de armazenamento de dados para uso no nó de borda chamado [Arquivos do Azure](../storage/storage-how-to-use-files-linux.md "Arquivos do Azure") que permite montar um compartilhamento de arquivos do Armazenamento do Azure para o sistema de arquivos Linux. Isso pode ser útil para armazenar arquivos de dados, scripts R e objetos de resultado que podem ser necessários posteriormente, quando fizer sentido usar o sistema de arquivos nativo no nó de borda em vez do HDFS. Uma grande vantagem de usar Arquivos do Azure é que os compartilhamentos de arquivos podem ser montados e usados por qualquer sistema com um sistema operacional compatível (Win, Linux), por exemplo, outro cluster HDInsight que você ou alguém da sua equipe tenha, uma VM do Azure ou até mesmo um sistema local.


## Próximas etapas

Agora que você sabe como criar um novo cluster HDInsight que inclui o Servidor R e tem as noções básicas sobre como usar o console R em uma sessão SSH, siga os links para descobrir outras maneiras de trabalhar com o Servidor R no HDInsight.

- [Overview of R Server on Hadoop](hdinsight-hadoop-r-server-overview.md) (Visão geral do servidor R no Hadoop)
- [Get started with R server on Hadoop](hdinsight-hadoop-r-server-get-started.md) (Introdução ao servidor R no Hadoop)
- [Add RStudio Server to HDInsight premium](hdinsight-hadoop-r-server-install-r-studio.md) (Adicionar o servidor RStudio ao HDInsight premium)
- [Compute context options for R Server on HDInsight premium](hdinsight-hadoop-r-server-compute-contexts.md) (Computar opções de contexto para o servidor R no HDInsight premium)

<!---HONumber=AcomDC_0420_2016-->