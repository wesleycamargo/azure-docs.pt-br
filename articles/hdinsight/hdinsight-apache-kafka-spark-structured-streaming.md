---
title: Streaming Estruturado do Apache Spark com Kafka – Azure HDInsight | Microsoft Docs
description: Saiba como usar o streaming do Apache Spark (DStream) para transmitir dados para dentro ou fora do Apache Kafka. Neste exemplo, você deve transmitir dados usando um bloco de anotações do Jupyter do Spark no HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/04/2018
ms.author: larryfr
ms.openlocfilehash: 49c13bbea537d7de60ecf509bc28675191c0b34d
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="use-spark-structured-streaming-with-kafka-on-hdinsight"></a>Use fluxo estruturado do Spark com o Kafka no HDInsight

Saiba como usar o Streaming Estruturado do Spark para ler dados do Apache Kafka no Azure HDInsight.

O streaming estruturado do Spark é um mecanismo de processamento de fluxo baseado no Spark SQL. Ele permite expressar cálculos de streaming do mesmo modo que cálculos de lote em dados estáticos. Para obter mais informações sobre Streaming Estruturado, consulte o [Guia de Programação de Streaming Estruturado [Versão Alfa]](http://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html) em Apache.org.

> [!IMPORTANT]
> Este exemplo usa o Spark 2.2 no HDInsight 3.6.
>
> As etapas neste documento criam um grupo de recursos do Azure que contém um Spark no HDInsight e um Kafka no cluster de HDInsight. Esses clusters são ambos localizados em uma Rede Virtual do Azure, que permite que o cluster Spark se comunique diretamente com o cluster Kafka.
>
> Quando você terminar as etapas neste documento, lembre-se de excluir os clusters para evitar cobranças em excesso.

## <a name="create-the-clusters"></a>Criar os clusters

Apache Kafka no HDInsight não fornece acesso para Agentes de Kafka pela internet pública. Qualquer coisa que usa Kafka deve estar na mesma rede virtual do Azure. Neste tutorial, os clusters de Spark e Kafka estão localizados na mesma rede virtual do Azure. 

O diagrama a seguir mostra como a comunicação flui entre o Spark e o Kafka:

![Diagrama de clusters Spark e Kafka em uma rede virtual do Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> O serviço Kafka é limitado a comunicação dentro da rede virtual. Outros serviços no cluster, como SSH e Ambari, podem ser acessados pela Internet. Para obter mais informações sobre as portas públicas disponíveis com o HDInsight, consulte [portas e URIs usados pelo HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Para sua conveniência, as etapas a seguir usam um modelo do Azure Resource Manager para criar clusters Kafka e Spark dentro de uma rede virtual.

1. Use o botão a seguir para entrar no Azure e abra o modelo do Gerenciador de Recursos no portal do Azure.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    O modelo do Azure Resource Manager está localizado em **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json**.

    Este modelo cria os seguintes recursos:

    * Um cluster Kafka no HDInsight 3.6.
    * Um Spark 2.2.0 no cluster HDInsight 3.6.
    * Uma Rede Virtual do Azure, que contém os clusters HDInsight.

    > [!IMPORTANT]
    > O bloco de anotações de streaming estruturado usado neste exemplo requer o Spark no HDInsight 3.6. Se você usar uma versão anterior do Spark no HDInsight, você receberá erros ao usar o bloco de anotações.

2. Use as informações a seguir para preencher as entradas na seção **Modelo personalizado**:

    | Configuração | Valor |
    | --- | --- |
    | Assinatura | Sua assinatura do Azure |
    | Grupo de recursos | O grupo de recursos que contém os recursos. |
    | Local padrão | A região do Azure em que os recursos são criados. |
    | Nome do cluster do Spark | O nome do cluster do Spark. |
    | Nome do cluster do Kafka | O nome do cluster do Kafka. |
    | Nome de usuário de logon do cluster | O nome de usuário administrador para os clusters. |
    | Senha de logon do cluster | A senha de usuário administrador para os clusters. |
    | Nome de usuário do SSH | O usuário do SSH para criar os clusters. |
    | Senha SSH | A senha para o usuário do SSH. |
   
    ![Captura de tela do modelo personalizado](./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png)

4. Por fim, marque **Fixar no painel** e selecione **Comprar**. 

> [!NOTE]
> Pode levar até 20 minutos para criar os clusters.

## <a name="get-the-notebook"></a>Obter o bloco de anotações

O código do exemplo descrito neste documento está disponível em [https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming](https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming).

## <a name="upload-the-notebooks"></a>Carregar os blocos de anotações

Para carregar o bloco de anotação do projeto para seu Spark no cluster HDInsight, use as etapas a seguir:

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

Agora que você aprendeu a usar o Streaming Estruturado do Spark, consulte os documentos a seguir para saber mais sobre como trabalhar com Spark e Kafka:

* [Como usar o streaming do Spark (DStream) com Kafka](hdinsight-apache-spark-with-kafka.md).
* [Iniciar com o bloco de anotações do Jupyter e Spark no HDInsight](spark/apache-spark-jupyter-spark-sql.md)