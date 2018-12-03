---
title: 'Tutorial: Fluxo estruturado do Apache Spark com Apache Kafka – Azure HDInsight '
description: Saiba como usar o streaming do Apache Spark para transmitir dados para dentro ou fora do Apache Kafka. Neste tutorial, você deve transmitir dados usando um bloco de anotações do Jupyter do Spark no HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 5f59f14f93b5bfc79c07b096a07ac109be0bedb6
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52499087"
---
# <a name="tutorial-use-apache-spark-structured-streaming-with-apache-kafka-on-hdinsight"></a>Tutorial: Use o fluxo estruturado do Apache Spark com Apache Kafka no Azure HDInsight

Este tutorial demonstra como usar o [fluxo estruturado do Apache Spark](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) para ler e gravar dados com o [Apache Kafka](https://kafka.apache.org/) no Azure HDInsight.

O streaming estruturado do Spark é um mecanismo de processamento de fluxo baseado no Spark SQL. Ele permite expressar cálculos de streaming do mesmo modo que cálculos de lote em dados estáticos. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Fluxo estruturado com Kafka
> * Criar clusters Kafka e Spark
> * Carregar o bloco de anotações para Spark
> * Use o bloco de anotações
> * Limpar recursos

Quando você terminar as etapas neste documento, lembre-se de excluir os clusters para evitar cobranças em excesso.

## <a name="prerequisites"></a>Pré-requisitos

* Familiaridade com o uso de [Jupyter Notebook](https://jupyter.org/) com Spark no HDInsight. Para saber mais, confira o documento [Carregar dados e executar consultas com o Apache Spark no HDInsight](spark/apache-spark-load-data-run-query.md).

* Familiaridade com a linguagem de programação [Scala](https://www.scala-lang.org/). O código usado neste tutorial é gravado em Scala.

* Familiaridade com a criação de tópicos Kafka. Para saber mais, confira o documento [Início rápido do Apache Kafka no HDInsight](kafka/apache-kafka-get-started.md).

> [!IMPORTANT]
> As etapas neste documento requerem um grupo de recursos do Azure que contém um Spark no HDInsight e um Kafka no cluster de HDInsight. Esses clusters são ambos localizados em uma Rede Virtual do Azure, que permite que o cluster Spark se comunique diretamente com o cluster Kafka.
> 
> Para sua conveniência, este documento direciona para um modelo que pode criar todos os recursos necessários do Azure. 
>
> Para obter mais informações sobre como usar o HDInsight em uma rede virtual, confira o documento [Estender o HDInsight usando uma rede virtual](hdinsight-extend-hadoop-virtual-network.md).

## <a name="structured-streaming-with-apache-kafka"></a>Fluxo estruturado com Apache Kafka

O fluxo estruturado do Spark é um mecanismo de processamento de fluxo baseado no mecanismo SQL do Spark. Ao usar fluxo estruturado, você pode gravar consultas de fluxo da mesma maneira que você grava consultas de lote.

Os snippets de código a seguir demonstram a leitura do Kafka e o armazenamento de arquivo. A primeira é uma operação em lote, enquanto a segunda é uma operação de fluxo:

```scala
// Read a batch from Kafka
val kafkaDF = spark.read.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()
// Select data and write to file
kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .write
                .format("parquet")
                .option("path","/example/batchtripdata")
                .option("checkpointLocation", "/batchcheckpoint")
                .save()
```

```scala
// Stream from Kafka
val kafkaStreamDF = spark.readStream.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()
// Select data from the stream and write to file
kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .writeStream
                .format("parquet")
                .option("path","/example/streamingtripdata")
                .option("checkpointLocation", "/streamcheckpoint")
                .start.awaitTermination(30000)
```

Em ambos os snippets de código, os dados são lidos do Kafka e gravados no arquivo. As diferenças entre os exemplos são:

| Lote | Fluxo |
| --- | --- |
| `read` | `readStream` |
| `write` | `writeStream` |
| `save` | `start` |

A operação de fluxo também usa `awaitTermination(30000)`, que interrompe o fluxo após 30000 ms. 

Para usar o fluxo estruturado com Kafka, seu projeto deve ter uma dependência no pacote `org.apache.spark : spark-sql-kafka-0-10_2.11`. A versão deste pacote deve corresponder à versão do Spark no HDInsight. Para Spark 2.2.0 (disponível no HDInsight 3.6), você pode encontrar as informações de dependência para diferentes tipos de projeto em [https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar](https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar).

Para o Jupyter Notebook fornecido com este tutorial, a célula a seguir carrega essa dependência do pacote:

```
%%configure -f
{
    "conf": {
        "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
        "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11"
    }
}
```

## <a name="create-the-clusters"></a>Criar os clusters

Apache Kafka no HDInsight não fornece acesso para Agentes de Kafka pela internet pública. Qualquer coisa que usa Kafka deve estar na mesma rede virtual do Azure. Neste tutorial, os clusters de Spark e Kafka estão localizados na mesma rede virtual do Azure. 

O diagrama a seguir mostra como a comunicação flui entre o Spark e o Kafka:

![Diagrama de clusters Spark e Kafka em uma rede virtual do Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> O serviço Kafka é limitado a comunicação dentro da rede virtual. Outros serviços no cluster, como SSH e Ambari, podem ser acessados pela Internet. Para obter mais informações sobre as portas públicas disponíveis com o HDInsight, consulte [portas e URIs usados pelo HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Para criar uma Rede Virtual do Azure e, em seguida, criar os clusters Kafka e Spark dentro dela, use as seguintes etapas:

1. Use o botão a seguir para entrar no Azure e abra o modelo do Gerenciador de Recursos no portal do Azure.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    O modelo do Azure Resource Manager está localizado em **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json**.

    Este modelo cria os seguintes recursos:

    * Um cluster Kafka no HDInsight 3.6.
    * Um Spark 2.2.0 no cluster HDInsight 3.6.
    * Uma Rede Virtual do Azure, que contém os clusters HDInsight.

    > [!IMPORTANT]
    > O bloco de anotações de fluxo estruturado usado neste exemplo requer o Spark 2.2.0 no HDInsight 3.6. Se você usar uma versão anterior do Spark no HDInsight, você receberá erros ao usar o bloco de anotações.

2. Use as informações a seguir para preencher as entradas na seção **Modelo personalizado**:

    | Configuração | Valor |
    | --- | --- |
    | Assinatura | Sua assinatura do Azure |
    | Grupo de recursos | O grupo de recursos que contém os recursos. |
    | Local padrão | A região do Azure em que os recursos são criados. |
    | Nome do cluster do Spark | O nome do cluster do Spark. Os primeiro de seis caracteres devem ser diferentes do nome de cluster Kafka. |
    | Nome do cluster do Kafka | O nome do cluster do Kafka. Os primeiro de seis caracteres devem ser diferentes do nome de cluster Spark. |
    | Nome de usuário de logon do cluster | O nome de usuário administrador para os clusters. |
    | Senha de logon do cluster | A senha de usuário administrador para os clusters. |
    | Nome de usuário do SSH | O usuário do SSH para criar os clusters. |
    | Senha SSH | A senha para o usuário do SSH. |
   
    ![Captura de tela do modelo personalizado](./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png)

3. Leia os **Termos e Condições** e, em seguida, selecione **Eu concordo com os termos e condições declarados acima**

4. Por fim, marque **Fixar no painel** e selecione **Comprar**. 

> [!NOTE]
> A criação de clusters pode levar até 20 minutos.

## <a name="upload-the-notebook"></a>Carregar o bloco de anotações

Para carregar o bloco de anotações do projeto para o Spark no cluster HDInsight, use as etapas a seguir:

1. Faça o download do projeto de [https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming](https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming).

1. No navegador da Web, conecte-se ao bloco de anotações do Jupyter em seu cluster Spark. Na URL a seguir, substitua `CLUSTERNAME` pelo nome do cluster __Spark__:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Quando solicitado, insira o nome de usuário e senha que você inseriu ao criar o cluster.

2. No canto superior direito da página, use o botão __Carregar__ para carregar o arquivo __spark-structured-streaming-kafka.ipynb__ para o cluster. Selecione __Abrir__ para iniciar o upload.

    ![Use o botão Carregar para selecionar e carregar um notebook](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-button.png)

    ![Selecione o arquivo KafkaStreaming.ipynb](./media/hdinsight-apache-kafka-spark-structured-streaming/select-notebook.png)

3. Encontre a entrada __spark-structured-streaming-kafka.ipynb__ na lista de blocos de anotações e selecione o botão __Carregar__ ao lado dela.

    ![Para carregar o notebook, use o botão carregar da entrada KafkaStreaming.ipynb](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-notebook.png)


## <a name="use-the-notebook"></a>Use o bloco de anotações

Depois que os arquivos forem carregados, selecione a entrada __spark-structured-streaming-kafka.ipynb__ para abrir o bloco de anotações. Para saber como usar o streaming estruturado do Spark com Kafka no HDInsight, siga as instruções no bloco de anotações.

## <a name="clean-up-resources"></a>Limpar recursos

Para limpar os recursos criados por este tutorial, você pode excluir o grupo de recursos. A exclusão do grupo de recursos também exclui o cluster HDInsight associado e todos os outros recursos associados ao grupo de recursos.

Para remover o grupo de recursos usando o portal do Azure:

1. No portal do Azure, expanda o menu à esquerda para abrir o menu de serviços e escolha __Grupo de Recursos__ para exibir a lista dos seus grupos de recursos.
2. Localize o grupo de recursos a ser excluído e clique com o botão direito do mouse no botão __Mais__ (...) do lado direito da lista.
3. Selecione __Excluir grupo de recursos__ e confirme.

> [!WARNING]
> A cobrança do cluster HDInsight começa quando um cluster é criado e para quando o cluster é excluído. A cobrança ocorre por minuto, portanto, sempre exclua o cluster quando ele não estiver mais sendo usado.
> 
> Excluir um Kafka no cluster HDInsight exclui todos os dados armazenados no Kafka.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar o [Fluxo Estruturado do Apache Spark](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) para gravar e ler dados do [Apache Kafka](https://kafka.apache.org/) no HDInsight. Use o link a seguir para aprender a usar o [Apache Storm](https://storm.apache.org/) com o Kafka.

> [!div class="nextstepaction"]
> [Usar o Apache Storm com o Apache Kafka](hdinsight-apache-storm-with-kafka.md)
