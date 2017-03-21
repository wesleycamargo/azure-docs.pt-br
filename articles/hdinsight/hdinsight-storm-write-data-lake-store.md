---
title: Usar o Armazenamento do Azure Data Lake com o Apache Storm no Azure HDInsight
description: Aprenda a gravar dados no Armazenamento do Azure Data Lake a partir de uma topologia do Apache Storm no HDInsight. Este documento e o exemplo associado demonstram como o componente HdfsBolt pode ser usado para gravar dados no Armazenamento do Data Lake.
services: hdinsight
documentationcenter: na
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 1df98653-a6c8-4662-a8c6-5d288fc4f3a6
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/03/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 376415d34592d18de00513ee9142512eb716e426
ms.lasthandoff: 03/04/2017


---
# <a name="use-azure-data-lake-store-with-apache-storm-with-hdinsight-java"></a>Usar o Azure Data Lake Store com o Apache Storm com HDInsight (Java)

O Armazenamento do Azure Data Lake é um serviço de armazenamento de nuvem compatível com HDFS que fornece confiabilidade, disponibilidade, durabilidade e alta taxa de transferência de dados. Neste documento, você aprende a usar uma topologia Storm baseada em Java para gravar dados no Azure Data Lake Store. As etapas descritas neste documento usam o componente [HdfsBolt](http://storm.apache.org/releases/1.0.2/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html), que é fornecido como parte do Apache Storm.

> [!IMPORTANT]
> A topologia de exemplo usada neste documento depende dos componentes incluídos no Storm em clusters HDInsight e pode exigir modificações para funcionar com o Armazenamento do Azure Data Lake quando usado com outros clusters do Apache Storm.

## <a name="how-to-work-with-azure-data-lake-store"></a>Como trabalhar com o Azure Data Lake Store

O Data Lake Store aparece para o HDInsight como um sistema de arquivos compatível com HDFS, de modo que você pode usar o bolt HDFS do Storm para gravar nele. Ao trabalhar com o Azure Data Lake no HDInsight, é possível usar um esquema de arquivo `adl://`.

* Se o Armazenamento do Data Lake for o armazenamento primário do cluster, use `adl:///`. Esta é a raiz do armazenamento de cluster no Azure Data Lake. Isso pode ser convertido em um caminho /clusters/CLUSTERNAME na conta de Armazenamento do Data Lake.
* Se o Armazenamento do Data Lake for o armazenamento secundário do cluster, use `adl://DATALAKEACCOUNT.azuredatalakestore.net/`. Esse URI especifica a conta de Armazenamento do Data Lake na qual os dados são gravados. Os dados são gravados com início na raiz do Data Lake Store.

    > [!NOTE]
    > Também é possível usar esse formato de URI para salvar dados na conta do Data Lake Store que contém o armazenamento primário do cluster. Isso permite salvar os dados fora do caminho de diretório que contém o HDInsight.

O código Java a seguir demonstra como é possível usar o bolt HDFS do Storm para gravar dados em um diretório chamado `/stormdata` em uma conta do Data Lake Store chamada `MYDATALAKE`.

```java
// 1. Create sync and rotation policies to control when data is synched
//    (written) to the file system and when to roll over into a new file.
SyncPolicy syncPolicy = new CountSyncPolicy(1000);
FileRotationPolicy rotationPolicy = new FileSizeRotationPolicy(0.5f, Units.KB);
// 2. Set the format. In this case, comma delimited
RecordFormat recordFormat = new DelimitedRecordFormat().withFieldDelimiter(",");
// 3. Set the directory name. In this case, '/stormdata/'
FileNameFormat fileNameFormat = new DefaultFileNameFormat().withPath("/stormdata/");
// 4. Create the bolt using the previously created settings,
//    and also tell it the base URL to your Data Lake Store.
// NOTE! Replace 'MYDATALAKE' below with the name of your data lake store.
HdfsBolt adlsBolt = new HdfsBolt()
    .withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")
        .withRecordFormat(recordFormat)
        .withFileNameFormat(fileNameFormat)
        .withRotationPolicy(rotationPolicy)
        .withSyncPolicy(syncPolicy);
// 4. Give it a name and wire it up to the bolt it accepts data
//    from. NOTE: The name used here is also used as part of the
//    file name for the files written to Data Lake Store.
builder.setBolt("ADLStoreBolt", adlsBolt, 1)
    .globalGrouping("finalcount");
```

O YAML a seguir demonstra como usar o bolt HDFS do Storm por meio da estrutura do Flux:

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - KB

  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

  - id: "rotationAction"
    className: "org.apache.storm.hdfs.common.rotation.MoveFileAction"
    configMethods:
      - name: "toDestination"
        args: ["${hdfs.dest.dir}"]

# bolt definitions
bolts:
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
    parallelism: 1
```

> [!NOTE]
> Os exemplos neste documento usam a estrutura do Flux.

## <a name="prerequisites"></a>Pré-requisitos

* [Java JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) ou superior. O HDInsight 3.5 requer Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

* Um cluster Storm no HDInsight versão 3.5. Para criar um novo Storm no cluster HDInsight, use as etapas descritas no documento [Usar o HDInsight com o Repositório Data Lake usando o Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md) .

### <a name="configure-environment-variables"></a>Configurar variáveis de ambiente

As seguintes variáveis de ambiente podem ser definidas quando você instala o Java e o JDK em sua estação de trabalho de desenvolvimento. No entanto, você deve verificar se elas existem e se contêm os valores corretos para o seu sistema.

* **JAVA_HOME** - deve apontar para o diretório onde o JRE (Java runtime environment) está instalado. Por exemplo, em uma distribuição Unix ou Linux, ele deve ter um valor semelhante a `/usr/lib/jvm/java-8-oracle`. No Windows, ele teria um valor semelhante a `c:\Program Files (x86)\Java\jre1.8`.
* **PATH** - deve conter os seguintes caminhos:
  
  * **JAVA\_INÍCIO** (ou o caminho equivalente)
  * **JAVA\_HOME\bin** (ou o caminho equivalente)
  * O diretório onde o Maven está instalado

## <a name="topology-implementation"></a>Implementação da topologia

O exemplo usado neste documento é escrito em Java e usa os seguintes componentes:

* **TickSpout**: gera os dados usados por outros componentes na topologia.
* **PartialCount**: conta os eventos gerados por TickSpout.
* **FinalCount**: agrega a contagem de dados de PartialCount.
* **ADLStoreBolt**: grava os dados no Azure Data Lake Store usando o componente [HdfsBolt](http://storm.apache.org/releases/1.0.2/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html).

O projeto que contém essa topologia está disponível como um download em [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store).

## <a name="build-and-package-the-topology"></a>Compilar e criar o pacote da topologia

1. Baixe o projeto de exemplo em [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) em seu ambiente de desenvolvimento.

2. Abra o arquivo `StormToDataLake\src\main\java\com\microsoft\example\StormToDataLakeStore.java` em um editor e encontre a linha que contém `.withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")`. Altere **MYDATALAKE** para o nome do Azure Data Lake Store usado ao criar seu servidor HDInsight.

3. Em um prompt de comando, terminal ou sessão do shell, altere os diretórios para a raiz do projeto baixado e execute os seguintes comandos para compilar e criar o pacote da topologia.
   
        mvn compile
        mvn package
   
    Após a conclusão do build e do empacotamento, haverá um novo diretório chamado `target`, que contém um arquivo denominado `StormToDataLakeStore-1.0-SNAPSHOT.jar`. Isto contém a topologia compilada.

## <a name="deploy-and-run-the-topology"></a>Implantar e executar a topologia

1. Use o seguinte comando para copiar a topologia para o cluster HDInsight. Substitua**USER** pelo nome de usuário SSH usado ao criar o cluster. Substitua **CLUSTERNAME** pelo nome do cluster.
   
        scp target\StormToDataLakeStore-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToDataLakeStore-1.0-SNAPSHOT.jar
   
    Quando solicitado, digite a senha usada ao criar o usuário SSH para o cluster. Se você usou uma chave pública em vez de uma senha, talvez precise usar o parâmetro `-i` para especificar o caminho para a chave privada correspondente.
   
   > [!NOTE]
   > Caso esteja usando um cliente Windows para o desenvolvimento, talvez não haja um comando `scp`. Se estiver, você poderá usar o `pscp`, que está disponível em [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

2. Quando o carregamento for concluído, use o seguinte para se conectar ao cluster HDInsight usando o SSH. Substitua**USER** pelo nome de usuário SSH usado ao criar o cluster. Substitua **CLUSTERNAME** pelo nome do cluster.
   
        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net
   
    Quando solicitado, digite a senha usada ao criar o usuário SSH para o cluster. Se você usou uma chave pública em vez de uma senha, talvez precise usar o parâmetro `-i` para especificar o caminho para a chave privada correspondente.
   
   > [!NOTE]
   > Se estiver usando um cliente Windows para o desenvolvimento, siga as informações em [Connect to Linux-based HDInsight with SSH from Windows](hdinsight-hadoop-linux-use-ssh-windows.md) (Conectar ao HDInsight baseado em Linux com o SSH do Windows) para obter informações sobre como usar o cliente PuTTY para se conectar ao cluster.

3. Depois de conectado, use o seguinte comando para criar um arquivo chamado `dev.properties`:

        nano dev.properties

4. Use o seguinte texto como o conteúdo do arquivo `dev.properties`:

        hdfs.write.dir: /stormdata
        hdfs.url: adl:///
    
    Para salvar o arquivo, use __Ctrl + X__, em seguida, __Y__ e, por fim, __Enter__. Os valores desse arquivo definem a URL do Data Lake Store e o nome do diretório no qual os dados são gravados.

3. Use o seguinte comando para iniciar a topologia:
   
        storm jar StormToDataLakeStore-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /datalakewriter.yaml --filter dev.properties

    Esse comando inicia a topologia usando a estrutura do Flux. A topologia é definida pelo arquivo `datalakewriter.yaml` incluído no jar. O arquivo `dev.properties` é passado como um filtro e os valores contidos no arquivo são lidos pela topologia.

## <a name="view-output-data"></a>Exibir dados de saída

Para exibir os dados, use o seguinte comando:

    hdfs dfs -ls /stormdata/

Isso exibe uma lista de arquivos que foram criados pela topologia.

Se o Data Lake Store não for o armazenamento padrão do cluster, use o seguinte comando para exibir os dados:

    hdfs dfs -ls adl://MYDATALAKE.azuredatalakestore.net/stormdata/

No comando anterior, substitua __MYDATALAKE__ pela conta do Data Lake Store.

A seguinte lista é um exemplo dos dados retornados pelos comandos anteriores:

    Found 30 items
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-0-1488568403092.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-1-1488568404567.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-10-1488568408678.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-11-1488568411636.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-12-1488568411884.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-13-1488568412603.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-14-1488568415055.txt

## <a name="stop-the-topology"></a>Parar a topologia

As topologias do Storm serão executadas até ser interrompidas ou o cluster ser excluído. Para interromper as topologias, use as informações a seguir.

**Para o HDInsight baseado no Linux**:

Em uma sessão SSH para o cluster, use o seguinte comando:

    storm kill datalakewriter

## <a name="delete-your-cluster"></a>Excluir o cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar o Storm para gravar no Repositório Azure Data Lake, descubra outros [exemplos do Storm para HDInsight](hdinsight-storm-example-topology.md).


