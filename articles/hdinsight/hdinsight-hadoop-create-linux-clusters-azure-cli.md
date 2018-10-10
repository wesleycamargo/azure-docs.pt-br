---
title: Criar clusters do Hadoop usando a CLI clássica do Azure – Azure HDInsight
description: Saiba como criar clusters do HDInsight usando a CLI clássica do Azure multiplataforma.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: jasonh
ms.openlocfilehash: 84b352fea0c5b9c98cd3b4e814e448cf8b706402
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992806"
---
# <a name="create-hdinsight-clusters-using-the-azure-classic-cli"></a>Criar clusters do HDInsight usando a CLI clássica do Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

As etapas deste documento são um passo a passo para a criação de um cluster HDInsight 3.5 usando a CLI clássica do Azure.

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **CLI clássica do Azure**. As etapas neste documento foram testadas pela última vez com a versão 0.10.14 da CLI clássica do Azure.

## <a name="log-in-to-your-azure-subscription"></a>Entre na sua assinatura do Azure

Siga as etapas documentadas em [Conectar a uma assinatura do Azure por meio da Interface de Linha de Comando do Azure](/cli/azure/authenticate-azure-cli) e conecte à sua assinatura usando o método de **logon**.

## <a name="create-a-cluster"></a>Criar um cluster

As etapas a seguir devem ser executadas de uma linha de comando, como o PowerShell ou o Bash.

1. Use o seguinte comando para fazer logon em sua assinatura do Azure:

        azure login

    Você receberá uma solicitação para fornecer seu nome e senha. Se você tiver várias assinaturas do Azure, use `azure account set <subscriptionname>` para definir a assinatura que será usada pelos comandos da CLI clássica do Azure.

2. Alterne para modo Gerenciador de Recursos do Azure usando o seguinte comando:

        azure config mode arm

3. Crie um grupos de recursos. Esse grupo de recursos contém o cluster HDInsight e a conta de armazenamento associada.

        azure group create groupname location

    * Substitua `groupname` por um nome exclusivo para o grupo.

    * Substitua `location` pela região geográfica na qual você deseja criar um grupo.

       Para obter uma lista de locais válidos, use o comando `azure location list` e, então, utilize um dos locais da coluna `Name`.

4. Criar uma conta de armazenamento. Essa conta de armazenamento é usada como o armazenamento padrão do cluster HDInsight.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename

    * Substitua `groupname` pelo nome do grupo criado na etapa anterior.

    * Substitua `location` pelo mesmo local usado na etapa anterior.

    * Substitua `storagename` por um nome exclusivo para a conta de armazenamento.

        > [!NOTE]
        > Para saber mais sobre os parâmetros usados nesse comando, use `azure storage account create -h` para exibir a ajuda deste comando.

5. Recupere a chave usada para acessar a conta de armazenamento.

        azure storage account keys list -g groupname storagename

    * Substitua `groupname` pelo nome do grupo de recursos.
    * Substitua `storagename` pelo nome da conta de armazenamento.

     Nos dados retornados, salve o valor `key` para `key1`.

6. Crie um cluster HDInsight.

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 3 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * Substitua `groupname` pelo nome do grupo de recursos.

    * Substitua `Hadoop` pelo tipo de cluster que você deseja criar. Por exemplo, `Hadoop`, `HBase`, `Kafka`, `Spark` ou `Storm`.

     > [!IMPORTANT]
     > Clusters HDInsight são fornecidos em uma variedade de tipos que correspondem à carga de trabalho ou à tecnologia para a qual o cluster está ajustado. Não há nenhum método com suporte para criar um cluster que combina vários tipos, como o Storm e HBase em um cluster.

    * Substitua `location` pelo mesmo local usado nas etapas anteriores.

    * Substitua `storagename` pelo nome da conta de armazenamento.

    * Substitua `storagekey` pela chave obtida na etapa anterior.

    * Como parâmetro `--defaultStorageContainer` , use o mesmo nome que você está usando para o cluster.

    * Substitua `admin` e `httppassword` pelo nome e pela senha que você deseja usar quando acessar o cluster por meio de HTTPS.

    * Substitua `sshuser` e `sshuserpassword` pelo nome de usuário e senha que você deseja usar quando acessar o cluster usando SSH

    > [!IMPORTANT]
    > Este exemplo cria um cluster com dois nós de trabalho. Você também pode alterar o número de nós de trabalho após a criação do cluster realizando operações de colocação em escala. Se você planeja usar mais de 32 nós de trabalho, será necessário selecionar um tamanho de nó de cabeçalho com pelo menos oito núcleos e 14 GB de RAM. Você pode definir o tamanho do nó principal usando o parâmetro `--headNodeSize` durante a criação do cluster.
    >
    > Para saber mais sobre tamanhos de nós e custos associados, consulte [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

    Pode levar vários minutos para que o processo de criação de cluster seja concluído. Geralmente, cerca de 15 minutos.

## <a name="troubleshoot"></a>Solucionar problemas

Se você tiver problemas com a criação de clusters HDInsight, confira os [requisitos de controle de acesso](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Próximas etapas

Agora que você criou com êxito um cluster HDInsight usando a CLI clássica, use o seguinte para aprender a trabalhar com o seu cluster:

### <a name="hadoop-clusters"></a>Clusters do Hadoop

* [Usar o Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Usar o Pig com o HDInsight](hadoop/hdinsight-use-pig.md)
* [Usar o MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Clusters do HBase

* [Introdução ao HBase no HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Desenvolvimento de aplicativos Java para HBase no HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Clusters Storm

* [Desenvolvimento de aplicativos Java para Storm no HDInsight](storm/apache-storm-develop-java-topology.md)
* [Usar componentes de Python no Storm no HDInsight](storm/apache-storm-develop-python-topology.md)
* [Implantar e monitorar topologias com o Storm no HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
