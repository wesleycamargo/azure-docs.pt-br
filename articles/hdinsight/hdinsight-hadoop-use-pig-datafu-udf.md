<properties
pageTitle="Usar DataFu com Pig no HDInsight"
description="DataFu é uma coleção de bibliotecas para uso com Hadoop. Saiba como usar DataFu com Pig no cluster do HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="08/23/2016"
ms.author="larryfr"/>

#Usar DataFu com pig no HDInsight

DataFu é uma coleção de bibliotecas de software livre para uso com o Hadoop. Neste documento, você aprenderá como usar DataFu no cluster do HDInsight e como usar as Funções Definidas de Usuário DataFu (UDF) com Pig.

##Pré-requisitos

* Uma assinatura do Azure.

* Um cluster do HDInsight do Azure (com base no Linux ou Windows)

* Familiaridade básica com [usando o Pig no HDInsight](hdinsight-use-pig.md)

##Instalar DataFu no HDInsight baseados em Linux

> [AZURE.NOTE] DataFu é instalado em clusters baseados em Linux, da versão 3.3 e superior, e em clusters baseados no Windows. Ele não é instalado em clusters baseados em Linux anteriores à versão 3.3.
>
> Se você estiver usando um cluster baseado em Linux da versão 3.3 ou superior, ou um cluster baseado no Windows, ignore esta seção.

DataFu pode ser baixado e instalado no repositório Maven. Use as etapas a seguir para adicionar DataFu ao cluster HDInsight:

1. Utilize SSH para se conectar ao cluster do HDInsight para Linux. Para obter mais informações sobre como usar SSH com o HDInsight, consulte um dos seguintes documentos:

    * [Usar SSH com Hadoop baseado em Linux no HDInsight do Linux, OS X e Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-unix.md)
    
2. Use o seguinte comando para baixar o arquivo jar do DataFu usando o utilitário wget, ou copie e cole o link no seu navegador para iniciar o download.

        wget http://central.maven.org/maven2/com/linkedin/datafu/datafu/1.2.0/datafu-1.2.0.jar

3. Em seguida, carregue o arquivo no armazenamento padrão para o cluster HDInsight. Isso torna o arquivo disponível a todos os nós no cluster e o arquivo permanecerá no armazenamento, mesmo se você excluir e recria o cluster.

        hdfs dfs -put datafu-1.2.0.jar /example/jars
    
    > [AZURE.NOTE] O exemplo anterior armazena o jar em `wasbs:///example/jars` como este diretório já existe no armazenamento de cluster. Você pode usar qualquer local que desejar no armazenamento de cluster HDInsight.

##Usar DataFu com Pig

As etapas nesta seção pressupõem que você esteja familiarizado com o uso de Pig no HDInsight e fornecem apenas as instruções de Pig Latin, não são etapas sobre como usá-los com o cluster. Para obter mais informações sobre como usar o Pig com o HDInsight, consulte [Usar o Pig com o HDInsight](hdinsight-use-pig.md).

> [AZURE.IMPORTANT] Ao usar DataFu de Pig em um cluster HDInsight baseados em Linux, você deve primeiro registrar o arquivo jar usando a seguinte instrução de Pig Latin:
>
> ```register wasbs:///example/jars/datafu-1.2.0.jar```
>
> DataFu é registrado por padrão em clusters HDInsight baseados no Windows.

Normalmente, você vai definir um alias para funções DataFu. Por exemplo:

    DEFINE SHA datafu.pig.hash.SHA();
    
Isso define um alias chamado `SHA` para o função de hash de SHA. Em seguida, você pode usar isso em um script de Pig Latin para gerar um hash para os dados de entrada. Por exemplo, o código a seguir substitui os nomes nos dados de entrada com um valor de hash:

    raw = LOAD '/data/raw/' USING PigStorage(',') AS  
        (name:chararray, 
        int1:int, 
        int2:int,
        int3:int); 
    mask = FOREACH raw GENERATE SHA(name), int1, int2, int3; 
    DUMP mask;

Se isso for usado com os seguintes dados de entrada:

    Lana Zemljaric,5,9,1
    Qiong Zhong,9,3,6
    Sandor Harsanyi,0,7,3
    Roko Petkovic,2,6,2
    Tibor Rozsa,8,0,0
    Lea Hrastovsek,6,3,6
    Regina Toth,2,1,2
    Eva Makay,8,9,2
    Shi Liao,4,6,0
    Tjasa Zemljaric,0,2,5
    
Gerará a seguinte saída:

    (c1a743b0f34d349cfc2ce00ef98369bdc3dba1565fec92b4159a9cd5de186347,5,9,1)
    (713d030d621ab69aa3737c8ea37a2c7c724a01cd0657a370e103d8cdecac6f99,9,3,6)
    (7a5f5abdd281f68168199319d98a1a662535f988d1443b3a3c497010937bac89,0,7,3)
    (a94818e93807e12079c4b35f8f3c8c8ef8e8acd1954e7f0476bc1a3a86fc96a9,2,6,2)
    (894ead4f48af91df7e088241218a23157bede7c52115272e417e95c046d48902,8,0,0)
    (6f99f163af3448fda672087db306f363e27a98a9e49c1f274a0860e303f8aec4,6,3,6)
    (a03de92a28be3c6a984c7a153fa9ed81c0413f76a9401955b5f7e04a5dd0ab9f,2,1,2)
    (6ceab977c8fb48d9ad0dc413e6bc646cabd89f22e7ab97a6b0133f3d225c6013,8,9,2)
    (fa9c436469096ff1bd297e182831f460501b826272ae97e921f5f6e3f54747e8,4,6,0)
    (bc22db7c238b86c37af79a62c78f61a304b35143f6087eb99c34040325865654,0,2,5)

##Próximas etapas

Para obter mais informações sobre DataFu ou Pig, consulte os seguintes documentos:

* [Guia Apache Pig DataFu](http://datafu.incubator.apache.org/docs/datafu/guide.html).

* [Usar o Pig com o HDInsight](hdinsight-use-pig.md)

<!---HONumber=AcomDC_0824_2016-->