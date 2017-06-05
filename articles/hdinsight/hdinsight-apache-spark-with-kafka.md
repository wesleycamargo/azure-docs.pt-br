---
title: "Streaming do Apache Spark com Kafka – Azure HDInsight | Microsoft Docs"
description: "Aprenda a usar o Apache Spark no HDInsight para ler e gravar dados no Apache Kafka no HDInsight. Este exemplo usa Scala em um bloco de anotações do Jupyter para gravar dados no Kafka no HDInsight, em seguida, lê-lo usando o streaming do Spark."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: dd8f53c1-bdee-4921-b683-3be4c46c2039
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/15/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: ceff0df193b3356ed2a23f381ea65369063957b1
ms.contentlocale: pt-br
ms.lasthandoff: 05/17/2017

---
# <a name="use-apache-spark-with-kafka-preview-on-hdinsight"></a>Use Apache Spark com Kafka (visualização) no HDInsight

Saiba como usar o Apache Spark para transmitir dados dentro ou fora do Apache Kafka. Neste documento, saiba como transmitir dados dentro e fora de Kafka usando um Notebook Jupyter do Spark no HDInsight.

> [!NOTE]
> As etapas neste documento criam um grupo de recursos do Azure que contém um Spark no HDInsight e um Kafka no cluster de HDInsight. Esses clusters são ambos localizados em uma Rede Virtual do Azure, que permite que o cluster Spark se comunique diretamente com o cluster Kafka.
>
> Quando você terminar as etapas neste documento, lembre-se de excluir os clusters para evitar cobranças em excesso.

## <a name="create-the-clusters"></a>Criar os clusters

Apache Kafka no HDInsight não fornece acesso para Agentes de Kafka pela internet pública. Qualquer coisa que se comunique com Kafka deve estar na mesma rede virtual do Azure que os nós no cluster Kafka. Para este exemplo, clusters de Spark e Kafka estão localizados em uma rede virtual do Azure. O diagrama a seguir mostra como a comunicação flui entre os clusters:

![Diagrama de clusters Spark e Kafka em uma rede virtual do Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Embora Kafka em si esteja limitado a comunicação na rede virtual, outros serviços do cluster, como o SSH e Ambari podem ser acessados pela Internet. Para obter mais informações sobre as portas públicas disponíveis com o HDInsight, consulte [portas e URIs usados pelo HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Enquanto você pode criar uma rede virtual do Azure, Kafka e clusters de Spark manualmente, é mais fácil usar um modelo do Azure Resource Manager. Use as seguintes etapas para implantar uma rede virtual do Azure, Kafka e clusters de Spark para sua assinatura do Azure.

1. Use o botão a seguir para entrar no Azure e abra o modelo do Gerenciador de Recursos no portal do Azure.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v2.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    O modelo do Azure Resource Manager está localizado em **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v2.json**.

2. Use as seguintes informações para preencher as entradas na folha de **Implantação personalizada** :
   
    ![Implantação personalizada do HDInsight](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **Grupo de recursos**: Crie um grupo ou selecione um existente. Esse grupo contém o cluster HDInsight.

    * **Local**: escolha um local geograficamente perto de você.

    * **Nome do Cluster de base**: esse valor é usado como o nome de base para os clusters Spark e Kafka. Por exemplo, inserir **hdi** cria um cluster Spark chamado hdi__ spark e um cluster Kafka chamado **kafka hdi**.

    * **Nome de usuário de logon do cluster**: O nome de usuário do administrador para os clusters Spark e Kafka.

    * **Senha de logon do cluster**: A senha de usuário do administrador para os clusters Spark e Kafka.

    * **Nome de usuário SSH**: O usuário SSH para criar para os clusters Spark e Kafka.

    * **Senha SSH**: a senha para o usuário SSH dos clusters Kafka e Spark.

3. Leia **Termos e Condições**, e depois selecione **Concordo com os termos e condições declarados acima**.

4. Por fim, marque **Fixar no painel** e selecione **Comprar**. Demora cerca de 20 minutos para criar os clusters.

Quando os recursos tiverem sido criados, você será redirecionado para uma folha do grupo de recursos que contém os clusters e o painel da Web.

![Folha do grupo de recursos para rede virtual e clusters](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]
> Observe que os nomes dos clusters HDInsight são **spark-BASENAME** e **kafka-BASENAME**, em que BASENAME é o nome fornecido para o modelo. Você usa esses nomes em etapas posteriores ao se conectar aos clusters.

## <a name="get-the-code"></a>Obter código

O código de exemplo descrito neste documento está disponível em [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka).

## <a name="understand-the-code"></a>Compreender o código

Este exemplo usa um aplicativo Scala em um Notebook Jupyter. O código no notebook depende das seguintes partes de dados:

* __Agentes Kafka__: O processo do agente é executado em cada nó de trabalho no cluster Kafka. A lista de agentes é exigida pelo componente do produtor, que grava dados no Kafka.

* __Hosts do zookeeper__: os hosts do Zookeeper para o cluster Kafka são usados durante o consumo de dados (leitura) do Kafka.

* __Nome do tópico__: O nome do tópico em que os dados são gravados e lidos. Esse exemplo espera um tópico chamado `sparktest`.

Consulte a seção [informações do host Kafka](#kafkahosts) para obter informações sobre como obter o Agente de Kafka e informações de host do Zookeeper.

O código no notebook executa as seguintes tarefas:

* Cria um consumidor que lê dados de um tópico Kafka chamado `sparktest`, conta cada palavra nos dados e armazena a palavra e a contagem em uma tabela temporária denominada `wordcounts`.

* Cria um produtor que grava sentenças aleatórias para o tópico Kafka chamado `sparktest`.

* Seleciona dados da tabela `wordcounts` para exibir as contagens.

Cada célula no projeto contêm comentários ou uma seção de texto que explica o que o código faz.

## <a id="kafkahosts"></a>Informações do host Kafka

A primeira coisa que você deve fazer ao criar um aplicativo que funciona com Kafka no HDInsight é obter o Agente de Kafka e as informações de host Zookeeper para o cluster Kafka. Isso é usado por aplicativos cliente para se comunicarem com Kafka.

> [!NOTE]
> O agente de Kafka e os hosts Zookeeper não são diretamente acessíveis pela Internet. Qualquer aplicativo que use Kafka deve ser executado no cluster Kafka ou dentro da mesma rede Virtual do Azure que o cluster Kafka. Nesse caso, o exemplo executa um Spark no cluster HDInsight na mesma rede virtual.

No ambiente de desenvolvimento, use os seguintes comandos para recuperar as informações do Zookeeper e do agente:

* Para obter as informações do __Agente de Kafka__:

    ```bash
    curl -u admin:$PASSWORD -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")'
    ```

    > [!NOTE]
    > Substitua `$PASSWORD` pela senha de logon (admin) que você usou ao criar o cluster. Substitua `$CLUSTERNAME` pelo nome de base que você usou ao criar o cluster.

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $brokerHosts = $respObj.host_components.HostRoles.host_name
    ($brokerHosts -join ":9092,") + ":9092"
    ```

    > [!NOTE]
    > Defina `$cluterName` como o nome do cluster do HDInsight. Quando solicitado, insira a senha para a conta de logon do cluster (admin).

* Para obter as informações do __host Zookeeper__:

    ```bash
    curl -u admin:$PASSWORD -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")'
    ```

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $zookeeperHosts = $respObj.host_components.HostRoles.host_name
    ($zookeeperHosts -join ":2181,") + ":2181"
    ```

Esse comando retornará informações semelhantes ao seguinte texto:

* __Agentes de Kafka__: `wn0-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092,wn1-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092`

* __Hosts do Zookeeper__: `zk0-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk1-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk2-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181`

> [!IMPORTANT]
> Salve essas informações conforme são usadas em várias etapas neste documento.

## <a name="use-the-jupyter-notebook"></a>Execute o Notebook Jupyter

Para usar o Notebook Jupyter de exemplo, você deve carregá-lo ao servidor do Notebook Jupyter no cluster Spark. Use as etapas a seguir para carregar o notebook:

1. No navegador da Web, use a seguinte URL para se conectar ao servidor do Notebook Jupyter no cluster Spark. Substitua `CLUSTERNAME` pelo nome do cluster do Spark.

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Quando solicitado, insira o nome de usuário e senha que você inseriu ao criar o cluster.

2. No canto superior direito da página, use o botão __Carregar__ para carregar o `KafkaStreaming.ipynb` arquivo. Selecione o arquivo na caixa de diálogo de navegador de arquivo e selecione __Abrir__.

    ![Use o botão Carregar para selecionar e carregar um notebook](./media/hdinsight-apache-spark-with-kafka/upload-button.png)

    ![Selecione o arquivo KafkaStreaming.ipynb](./media/hdinsight-apache-spark-with-kafka/select-notebook.png)

3. Encontre a entrada __KafkaStreaming.ipynb__ na lista de blocos de anotações e selecione o botão __carregar__ ao lado dela.

    ![Use o botão Carregar ao lado da entrada KafkaStreaming.ipynb para carregá-la para o servidor de notebook](./media/hdinsight-apache-spark-with-kafka/upload-notebook.png)

4. Depois que o arquivo foi carregado, selecione a entrada __KafkaStreaming.ipynb__ para abrir o notebook. Para concluir este exemplo, siga as instruções no notebook.

## <a name="delete-the-cluster"></a>Excluir o cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Como as etapas neste documento criam ambos os clusters no mesmo grupo de recursos do Azure, você pode excluir o grupo de recursos no portal do Azure. Excluir o grupo remove todos os recursos criados por este documento, a rede Virtual do Azure e a conta de armazenamento usada pelos clusters.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a usar o Spark para ler e gravar em Kafka. Use os links a seguir para descobrir outras maneiras de trabalhar com Kafka:

* [Introdução ao Apache Kafka no HDInsight](hdinsight-apache-kafka-get-started.md)
* [Use MirrorMaker para criar uma réplica de Kafka no HDInsight](hdinsight-apache-kafka-mirroring.md)
* [Use o Apache Storm com Kafka no HDInsight](hdinsight-apache-storm-with-kafka.md)


