
<properties
   pageTitle="Opções do Armazenamento do Azure para o Servidor R no HDInsight (visualização) | Microsoft Azure"
   description="Conheça as diferentes opções de armazenamento disponíveis para usuários com o Servidor R no HDInsight (visualização)"
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
   ms.date="09/01/2016"
   ms.author="jeffstok"
/>

# Opções do Armazenamento do Azure para o Servidor R no HDInsight Premium (preview)

O Microsoft Servidor R no HDInsight (visualização) tem acesso ao Blob do Azure e ao [Armazenamento do Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/), como forma de persistir dados, código, objetos de resultado da análise e assim por diante.

Ao criar um cluster Hadoop no HDInsight, especifique uma conta de armazenamento do Azure. Um contêiner de armazenamento de Blobs específico dessa conta mantém o sistema de arquivos do cluster que você cria (por exemplo, o Sistema de Arquivos Distribuído Hadoop). Para fins de desempenho, o cluster HDInsight é criado no mesmo datacenter que a conta de armazenamento primária especificada. Para obter mais informações, consulte [Usar o Armazenamento de Blobs do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md "Usar o armazenamento de blobs do Azure com o HDInsight").


## Usar várias contas de armazenamento de Blobs do Azure

Se necessário, você pode acessar várias contas de armazenamento ou contêineres do Azure com o cluster HDI. Para isso, você precisa especificar as contas de armazenamento adicionais a interface do usuário ao criar o cluster e, depois, seguir estas etapas para usá-las no R.

1.	Crie um cluster HDInsight com um nome de conta de armazenamento de **storage1** e um contêiner padrão chamado **container1**.
2. Especifique uma conta de armazenamento adicional chamada **storage2**.
3. Copie o arquivo mycsv.csv para o diretório /share e execute a análise nesse arquivo.

    ````
    hadoop fs –mkdir /share
    hadoop fs –copyFromLocal myscsv.scv /share  
    ````

3.	No código R, defina o nó do nome como **default** e defina o diretório e arquivo a serem processados.

    ````
    myNameNode <- "default"
    myPort <- 0
    ````

  Local dos dados:

    bigDataDirRoot <- "/share"  

  Definir contexto de computação do Spark:

    mySparkCluster <- RxSpark(consoleOutput=TRUE)

  Definir contexto de computação:

    rxSetComputeContext(mySparkCluster)

  Defina o sistema de arquivos HDFS (Sistema de Arquivos Distribuído do Hadoop):

    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

  Especificar arquivo de entrada para análise no HDFS:

    inputFile <-file.path(bigDataDirRoot,"mycsv.csv")

Todas as referências de diretório e arquivo apontam para a conta de armazenamento wasbs://container1@storage1.blob.core.windows.net. Essa é a **conta de armazenamento padrão** associada ao cluster do HDInsight.

Agora, suponha que você queira processar um arquivo chamado mySpecial.csv, localizado no diretório /private de **container2** em **storage2**.

No código R, aponte a referência de nó de nome para a conta de armazenamento **storage2**.

    myNameNode <- "wasbs://container2@storage2.blob.core.windows.net"
    myPort <- 0

  Local dos dados:

    bigDataDirRoot <- "/private"

  Definir contexto de computação do Spark:

    mySparkCluster <- RxSpark(consoleOutput=TRUE)

  Definir contexto de computação:

    rxSetComputeContext(mySparkCluster)

  Definir sistema de arquivos HDFS:

    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

  Especificar arquivo de entrada para análise no HDFS:

    inputFile <-file.path(bigDataDirRoot,"mySpecial.csv")

Todas as referências de diretório e arquivo agora apontam para a conta de armazenamento wasbs://container2@storage2.blob.core.windows.net. Esse é o **Nome de Nó** que você especificou.

Observe que você precisa configurar o diretório /user/RevoShare/<nome de usuário do SSH> em **storage2** da seguinte maneira:

    hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>

## Usar um repositório Azure Data Lake

Para usar repositórios Data Lake com sua conta do HDInsight, você precisa dar ao cluster acesso a cada repositório do Azure Data Lake que deseja usar. Você usa o repositório no script R de forma semelhante a uma conta de armazenamento secundária (conforme descrito no procedimento anterior).

## Adicionar acesso de cluster aos repositórios Azure Data Lake

Você pode acessar um repositório Data Lake usando uma Entidade de Serviço do Azure AD (Azure Active Directory) que está associada ao cluster do HDInsight.

### Para adicionar uma entidade de serviço
1. Ao criar o cluster do HDInsight, selecione **Identidade do Cluster AAD** na guia **Fonte de Dados**.
2. Na caixa de diálogo **Identidade de Cluster AAD**, em **Selecionar Entidade de Serviço do AD**, selecione **Criar novo**.

Depois que você der um nome à entidade de serviço e criar uma senha para ela, será aberta uma nova guia, em que você pode associar a entidade de serviço aos repositórios Data Lake.

Observe que você também pode adicionar acesso a um Data Lake Store mais tarde abrindo o Data Lake Store no Portal do Azure e indo para **Data Explorer** > **Acesso**. Aqui está um exemplo de caixa de diálogo que mostra a criação de uma Entidade de Serviço e sua associação ao Repositório Azure Data Lake "rkadl11".

![Princípio 1 para criar Serviço do repositório Data Lake](./media/hdinsight-hadoop-r-server-storage/hdinsight-hadoop-r-server-storage-adls-sp1.png)


![Princípio 2 para criar Serviço do repositório Data Lake](./media/hdinsight-hadoop-r-server-storage/hdinsight-hadoop-r-server-storage-adls-sp2.png)

## Usar o repositório Data Lake com o Servidor R
Após obter acesso a um repositório Data Lake, você pode usar o repositório no Servidor R no HDInsight da forma como faria com uma conta de armazenamento do Azure secundária. A única diferença é que o prefixo **wasb://** muda para **adl://**, da seguinte maneira:

````
# Point to the ADL store (e.g. ADLtest)
myNameNode <- "adl://rkadl1.azuredatalakestore.net"
myPort <- 0

# Location of the data (assumes a /share directory on the ADL account)
bigDataDirRoot <- "/share"  

# Define Spark compute context
mySparkCluster <- RxSpark(consoleOutput=TRUE)

# Set compute context
rxSetComputeContext(mySparkCluster)

# Define HDFS file system
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# Specify the input file in HDFS to analyze
inputFile <-file.path(bigDataDirRoot,"AirlineDemoSmall.csv")

# Create factors for days of the week
colInfo <- list(DayOfWeek = list(type = "factor",
               levels = c("Monday", "Tuesday", "Wednesday", "Thursday",
                          "Friday", "Saturday", "Sunday")))

# Define the data source
airDS <- RxTextData(file = inputFile, missingValueString = "M",
                    colInfo  = colInfo, fileSystem = hdfsFS)

# Run a linear regression
model <- rxLinMod(ArrDelay~CRSDepTime+DayOfWeek, data = airDS)
````

Estes são os comandos que são usados para configurar a conta do repositório Data Lake com o diretório RevoShare e adicionar o arquivo .csv de exemplo do exemplo anterior:

````
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/AirlineDemoSmall.csv adl://rkadl1.azuredatalakestore.net/share

hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share
````

## Usar arquivos do Azure no nó de borda

Também há uma opção de armazenamento de dados conveniente para uso no nó de borda, chamada [Arquivos do Azure](../storage/storage-how-to-use-files-linux.md "Arquivos do Azure"). Esse recurso permite montar um compartilhamento de arquivos do Armazenamento do Azure para o sistema de arquivos do Linux. Isso pode ser útil para armazenar arquivos de dados, scripts R e objetos de resultado que podem ser necessários posteriormente, quando fizer sentido usar o sistema de arquivos nativo no nó de borda em vez do HDFS.

Uma grande vantagem dos Arquivos do Azure é que os compartilhamentos de arquivos podem ser montados e usados por qualquer sistema que tenha um sistema operacional com suporte, como Windows ou Linux. Por exemplo, ele pode ser usado por outro cluster do HDInsight que você ou alguém em sua equipe tenha, por uma VM do Azure ou até mesmo por um sistema local.


## Próximas etapas

Agora que você aprendeu os fundamentos para usar o console do R em uma sessão de SSH e criar um novo cluster do HDInsight que inclui o Servidor R, use os links a seguir para descobrir outras maneiras de trabalhar com o Servidor R no HDInsight.

- [Visão geral do Servidor R no HDInsight](hdinsight-hadoop-r-server-overview.md)
- [Get started with R server on Hadoop (Introdução ao servidor R no Hadoop)](hdinsight-hadoop-r-server-get-started.md)
- [Add RStudio Server to HDInsight premium (Adicionar o servidor RStudio ao HDInsight premium)](hdinsight-hadoop-r-server-install-r-studio.md)
- [Opções de contexto de computação para o Servidor R no HDInsight](hdinsight-hadoop-r-server-compute-contexts.md)

<!---HONumber=AcomDC_0831_2016-->