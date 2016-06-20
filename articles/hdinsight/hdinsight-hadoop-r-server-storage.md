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
   ms.date="06/01/2016"
   ms.author="jeffstok"
/>

# Opções do Armazenamento do Azure para o Servidor R no HDInsight Premium (preview)

O Servidor R no HDInsight (preview) tem acesso ao Blob do Azure e ao [Armazenamento do Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/), como forma de persistir dados, código, objetos de resultado da análise etc.

Ao criar um cluster Hadoop no HDInsight, especifique uma conta de armazenamento do Azure. Um contêiner de armazenamento de Blobs específico dessa conta foi desenvolvido para manter o sistema de arquivos do cluster que você cria, isto é, o HDFS (Sistema de Arquivos Distribuído Hadoop). Para fins de desempenho, o cluster HDInsight é criado no mesmo datacenter que a conta de armazenamento primária especificada. Para obter mais informações, consulte [Usar o Armazenamento de Blobs do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md "Usar o armazenamento de blobs do Azure com o HDInsight").


## Usar várias contas de Armazenamento de Blobs do Azure

Se necessário, será possível acessar várias contas de armazenamento ou contêineres do Azure com o cluster HDI. Para tanto, você precisará especificar as contas de armazenamento adicionais na interface de usuário na criação do cluster e seguir estas etapas para usá-las no R.

1.	Suponha que você crie um cluster HDInsight com um nome de conta de armazenamento de "storage1" com um contêiner padrão "container1". Você também especifica uma conta de armazenamento adicional "storage2".  
2.	Agora você copia um arquivo "mycsv.csv" no diretório "/share" e quer realizar uma análise nesse arquivo.  

````
hadoop fs –mkdir /share
hadoop fs –copyFromLocal myscsv.scv /share  
````

3.	No código R, você define o nó do nome para "default" e define seu diretório e arquivo para processar  

````
myNameNode <- "default"
myPort <- 0
````

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

Observe que você precisará configurar o diretório /user/RevoShare/<SSH username> na conta de armazenamento "storage2":

    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>

## Usando um Repositório Azure Data Lake

Para usar os Repositórios Azure Data Lake com sua conta do HDInsight, que você precisará fornecer seu acesso ao cluster para cada Repositório Azure Data Lake que gostaria de usar e, então, referenciar o repositório no script R de uma maneira semelhante ao uso de uma conta de armazenamento do Azure secundária descrita acima.

## Adicionando acesso de cluster aos seus Repositórios Azure Data Lake

O acesso a um Repositório Azure Data Lake é estabelecido por meio do uso de uma Entidade de Serviço AAD (Azure Active Directory) associada ao seu cluster HDInsight. Para adicionar uma Entidade de Serviço ao criar seu cluster HDInsight, clique na opção “Identidade AAD do Cluster” na guia Fonte de dados e clique em “Criar Nova” Entidade de Serviço. Depois de fornecer a ele um nome e uma senha, será aberta uma nova guia que permitirá que você associe a Entidade de Serviço aos Repositórios Azure Data Lake.

Observe que você também pode adicionar acesso a um Repositório Azure Data Lake posteriormente abrindo o Repositório Azure Data Lake no Portal do Azure e indo até “Explorer de Dados -> Acesso”. Aqui está uma caixa de diálogo de exemplo que mostra a criação de uma Entidade de Serviço e a associação dela ao Repositório Azure Data Lake “rkadl11”.

![Criar Entidade de Serviço do Repositório ADL 1](./media/hdinsight-hadoop-r-server-storage/hdinsight-hadoop-r-server-storage-adls-sp1.png)


![Criar Entidade de Serviço do Repositório ADL 2](./media/hdinsight-hadoop-r-server-storage/hdinsight-hadoop-r-server-storage-adls-sp2.png)

## Usando o Repositório Azure Data Lake com o Servidor R
Após fornecer acesso para um Repositório Azure Data Lake por meio do uso da Entidade de Serviço do cluster, você pode usá-lo no Servidor R no HDInsight da mesma maneira que uma conta de armazenamento do Azure secundária. A única diferença é que o prefixo wasb:// muda para adl://, por exemplo.

````
# point to the ADL store (e.g. ADLtest) 
myNameNode <- "adl://rkadl1.azuredatalakestore.net"
myPort <- 0

# Location of the data (assumes a /share directory on the ADL account) 
bigDataDirRoot <- "/share"  

# define Spark compute context
mySparkCluster <- RxSpark(consoleOutput=TRUE)

# set compute context
rxSetComputeContext(mySparkCluster)

# define HDFS file system
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# specify the input file in HDFS to analyze
inputFile <-file.path(bigDataDirRoot,"AirlineDemoSmall.csv")

# create Factors for days of the week
colInfo <- list(DayOfWeek = list(type = "factor",
               levels = c("Monday", "Tuesday", "Wednesday", "Thursday",
                          "Friday", "Saturday", "Sunday")))

# define the data source 
airDS <- RxTextData(file = inputFile, missingValueString = "M",
                    colInfo  = colInfo, fileSystem = hdfsFS)

# Run a linear regression
model <- rxLinMod(ArrDelay~CRSDepTime+DayOfWeek, data = airDS)
````

> [AZURE.NOTE] Aqui estão os comandos usados para configurar a conta de armazenamento do Azure Data Lake com o diretório RevoShare e adicionar o arquivo CSV de exemplo ao exemplo acima:

````
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user 
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare 
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/AirlineDemoSmall.csv adl://rkadl1.azuredatalakestore.net/share

hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share
````

## Usar arquivos do Azure no nó de borda 

Também há uma opção conveniente de armazenamento de dados para uso no nó de borda chamado [Arquivos do Azure](../storage/storage-how-to-use-files-linux.md "Arquivos do Azure") que permite montar um compartilhamento de arquivos do Armazenamento do Azure para o sistema de arquivos Linux. Isso pode ser útil para armazenar arquivos de dados, scripts R e objetos de resultado que podem ser necessários posteriormente, quando fizer sentido usar o sistema de arquivos nativo no nó de borda em vez do HDFS. Uma grande vantagem de usar Arquivos do Azure é que os compartilhamentos de arquivos podem ser montados e usados por qualquer sistema com um sistema operacional compatível (Win, Linux), por exemplo, outro cluster HDInsight que você ou alguém da sua equipe tenha, uma VM do Azure ou até mesmo um sistema local.


## Próximas etapas

Agora que você sabe como criar um novo cluster HDInsight que inclui o Servidor R e tem as noções básicas sobre como usar o console R em uma sessão SSH, siga os links para descobrir outras maneiras de trabalhar com o Servidor R no HDInsight.

- [Overview of R Server on Hadoop (Visão geral do servidor R no Hadoop)](hdinsight-hadoop-r-server-overview.md)
- [Get started with R server on Hadoop (Introdução ao servidor R no Hadoop)](hdinsight-hadoop-r-server-get-started.md)
- [Add RStudio Server to HDInsight premium (Adicionar o servidor RStudio ao HDInsight premium)](hdinsight-hadoop-r-server-install-r-studio.md)
- [Compute context options for R Server on HDInsight premium (Computar opções de contexto para o servidor R no HDInsight premium)](hdinsight-hadoop-r-server-compute-contexts.md)

<!---HONumber=AcomDC_0608_2016-->