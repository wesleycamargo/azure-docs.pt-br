---
title: Streaming do Apache Spark com Apache Kafka – Azure HDInsight
description: Saiba como usar o Apache Spark para transmitir dados dentro ou fora do Apache Kafka usando o DStreams. Neste exemplo, você deve transmitir dados usando um bloco de anotações do Jupyter do Spark no HDInsight.
keywords: exemplo de kafka, zookeeper kafka, streaming do kafka para Spark, exemplo de streaming do spark com o kafka
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: c0687ec94af60d3683d3f129eff2bad8fb97d786
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53165785"
---
# <a name="apache-spark-streaming-dstream-example-with-apache-kafka-on-hdinsight"></a>Exemplo de streaming do Apache Spark (DStream) com o Apache Kafka no HDInsight

Saiba como usar o [Apache Spark](https://spark.apache.org/) para transmitir dados para dentro ou fora do [Apache Kafka](https://kafka.apache.org/) no HDInsight usando [DStreams](https://spark.apache.org/docs/latest/api/java/org/apache/spark/streaming/dstream/DStream.html). Este exemplo usa uma [Jupyter Notebook](https://jupyter.org/) que é executado no cluster Spark.

> [!NOTE]
> As etapas neste documento criam um grupo de recursos do Azure que contém um Spark no HDInsight e um Kafka no cluster de HDInsight. Esses clusters são ambos localizados em uma Rede Virtual do Azure, que permite que o cluster Spark se comunique diretamente com o cluster Kafka.
>
> Quando você terminar as etapas neste documento, lembre-se de excluir os clusters para evitar cobranças em excesso.

> [!IMPORTANT]
> Este exemplo usa DStreams, que é uma tecnologia de streaming do Spark mais antiga. Para obter um exemplo que utiliza recursos de streaming do Spark mais recentes, consulte o documento [Streaming estruturado do Spark com Kafka](hdinsight-apache-kafka-spark-structured-streaming.md).

## <a name="create-the-clusters"></a>Criar os clusters

Apache Kafka no HDInsight não fornece acesso para Agentes de Kafka pela internet pública. Qualquer coisa que se comunique com Kafka deve estar na mesma rede virtual do Azure que os nós no cluster Kafka. Para este exemplo, clusters de Spark e Kafka estão localizados em uma rede virtual do Azure. O diagrama a seguir mostra como a comunicação flui entre os clusters:

![Diagrama de clusters Spark e Kafka em uma rede virtual do Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Embora Kafka em si esteja limitado a comunicação na rede virtual, outros serviços do cluster, como o SSH e Ambari podem ser acessados pela Internet. Para obter mais informações sobre as portas públicas disponíveis com o HDInsight, consulte [portas e URIs usados pelo HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Enquanto você pode criar uma rede virtual do Azure, Kafka e clusters de Spark manualmente, é mais fácil usar um modelo do Azure Resource Manager. Use as seguintes etapas para implantar uma rede virtual do Azure, Kafka e clusters de Spark para sua assinatura do Azure.

1. Use o botão a seguir para entrar no Azure e abra o modelo do Gerenciador de Recursos no portal do Azure.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    O modelo do Azure Resource Manager está localizado em **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json**.

    > [!WARNING]
    > Para garantir a disponibilidade do Kafka no HDInsight, o cluster deve conter pelo menos três nós de trabalho. Este modelo cria um cluster de Kafka que contém três nós de trabalho.

    Este modelo cria um cluster HDInsight 3.6 para Kafka e Spark.

2. Use as seguintes informações para preencher as entradas na seção **Implantação personalizada**:
   
    ![Implantação personalizada do HDInsight](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **Grupo de recursos**: Crie um grupo ou selecione um existente. Esse grupo contém o cluster HDInsight.

    * **Local**: Escolha um local geograficamente perto de você.

    * **Nome do Cluster de Base**: Esse valor é usado como o nome de base dos clusters Spark e Kafka. Por exemplo, a inserção de **hdistreaming** cria um cluster Spark chamado __spark-hdistreaming__ e um cluster Kafka chamado **kafka-hdistreaming**.

    * **Nome de Usuário de Logon do Cluster**: O nome do usuário administrador dos clusters Spark e Kafka.

    * **Senha de Logon do Cluster**: A senha do usuário administrador dos clusters Spark e Kafka.

    * **Nome de Usuário SSH**: O usuário SSH para criar para os clusters Spark e Kafka.

    * **Senha SSH**: A senha para o usuário SSH dos clusters Kafka e Spark.

3. Leia **Termos e Condições**, e depois selecione **Concordo com os termos e condições declarados acima**.

4. Por fim, selecione **Comprar**. Demora cerca de 20 minutos para criar os clusters.

Depois que os recursos tiverem sido criados, uma página de resumo será exibida.

![Resumo de grupo de recursos para a rede virtual e clusters](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]
> Observe que os nomes dos clusters HDInsight são **spark-BASENAME** e **kafka-BASENAME**, em que BASENAME é o nome fornecido para o modelo. Você usa esses nomes em etapas posteriores ao se conectar aos clusters.

## <a name="use-the-notebooks"></a>Usar os blocos de anotações

O código do exemplo descrito neste documento está disponível em [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka).

Siga as etapas no arquivo `README.md` para concluir este exemplo.

## <a name="delete-the-cluster"></a>Excluir o cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Como as etapas neste documento criam ambos os clusters no mesmo grupo de recursos do Azure, você pode excluir o grupo de recursos no portal do Azure. Excluir o grupo remove todos os recursos criados por este documento, a rede Virtual do Azure e a conta de armazenamento usada pelos clusters.

## <a name="next-steps"></a>Próximas etapas

Neste exemplo, você aprendeu a usar o Spark para ler e gravar em Kafka. Use os links a seguir para descobrir outras maneiras de trabalhar com Kafka:

* [Introdução ao Apache Kafka no HDInsight](kafka/apache-kafka-get-started.md)
* [Use MirrorMaker para criar uma réplica de Apache Kafka no HDInsight](kafka/apache-kafka-mirroring.md)
* [Use o Apache Storm com o Apache Kafka no HDInsight](hdinsight-apache-storm-with-kafka.md)

