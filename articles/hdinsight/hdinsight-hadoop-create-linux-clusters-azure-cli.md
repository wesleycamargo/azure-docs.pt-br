<properties
   	pageTitle="Criar clusters Hadoop, HBase ou Storm no Linux em HDInsight usando o Azure CLI entre plataformas| Microsoft Azure"
   	description="Aprenda a criar clusters HDInsight baseados em Linux usando o Azure CLI entre plataformas, modelos do Gerenciador de Recursos do Azure e a API REST do Azure. Você pode especificar o tipo de cluster (Hadoop, HBase ou Storm) ou usar scripts para instalar componentes personalizados."
   	services="hdinsight"
   	documentationCenter=""
   	authors="Blackmist"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="06/13/2016"
   	ms.author="larryfr"/>

#Criar clusters baseados em Linux no HDInsight usando o Azure CLI

[AZURE.INCLUDE [seletor](../../includes/hdinsight-selector-create-clusters.md)]

A CLI do Azure é um utilitário de linha de comando entre plataformas que permite o gerenciamento dos Serviços do Azure. Ela pode ser usada, juntamente com os modelos de gerenciamento de recursos do Azure, para criar um cluster HDInsight, junto com as contas de armazenamento associadas e outros serviços.

Os modelos de gerenciamento de recursos do Azure são documentos JSON que descrevem um __grupo de recursos__ e todos os recursos que ele contém (por exemplo, HDInsight). Essa abordagem baseada em modelo permite a definição de todos os recursos necessários ao HDInsight em um modelo, e também gerenciar alterações no grupo todo por meio de __implantações__ que aplicam mudanças ao grupo.

As etapas neste documento fornecem orientação durante o processo de criação de um novo cluster HDInsight usando a CLI do Azure e um modelo.

> [AZURE.IMPORTANT] As etapas neste documento usam o número padrão de nós de trabalho (4) para um cluster HDInsight. Se você planeja ter mais de 32 nós de trabalho, seja na criação do cluster ou em seu dimensionamento após a criação, deverá selecionar um tamanho de nó de cabeçalho com pelo menos 8 núcleos e 14 GB de RAM.
>
> Para saber mais sobre tamanhos de nós e custos associados, consulte [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

##Pré-requisitos

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- __CLI do Azure__. As etapas neste documento foram testadas pela última vez com a versão 0.10.1 da CLI do Azure.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##Faça logon na sua assinatura do Azure

Siga as etapas documentadas em [Conectar a uma assinatura do Azure por meio da CLI do Azure (Interface de Linha de Comando do Azure)](../xplat-cli-connect.md) e conecte à sua assinatura usando o método de __logon__.

##Criar um cluster

As etapas a seguir devem ser executadas de um prompt de comando, shell ou a sessão terminal após instalar e configurar o CLI do Azure.

1. Use o seguinte comando para fazer logon em sua assinatura do Azure:

        azure login

    Você receberá uma solicitação para fornecer seu nome e senha. Se você tiver várias assinaturas do Azure, use `azure account set <subscriptionname>` para definir a assinatura que será usada pelos comandos da CLI do Azure.

3. Alterne para modo Gerenciador de Recursos do Azure usando o seguinte comando:

        azure config mode arm

4. Criar um novo grupo de recursos. Ele conterá o cluster HDInsight e a conta de armazenamento associada.

        azure group create groupname location
        
    * Substitua __groupname__ por um nome exclusivo para o grupo. 
    * Substitua __local__ pela região geográfica em que você deseja criar um grupo. 
    
        Para obter uma lista de locais válidos, use o comando `azure locations list` e use um dos locais da coluna __Nome__.

5. Criar uma nova conta de armazenamento. Ela será usada como o armazenamento padrão do cluster HDInsight.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename
        
     * Substitua __groupname__ pelo nome do grupo criado na etapa anterior:
     * Substitua __location__ pelo mesmo local usado na etapa anterior. 
     * Substitua __storagename__ por um nome exclusivo da conta de armazenamento.
     
     > [AZURE.NOTE] Para saber mais sobre os parâmetros usados nesse comando, use `azure storage account create -h` para exibir a ajuda deste comando.

5. Recupere a chave usada para acessar a conta de armazenamento.

        azure storage account keys list -g groupname storagename
        
    * Substitua __groupname__ pelo nome do grupo de recursos.
    * Substitua __storagename__ pelo nome da conta de armazenamento.
    
    Nos dados retornados, salve o valor __key__ para __key1__.

6. Crie um novo cluster HDInsight.

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 2 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * Substitua __groupname__ pelo nome do grupo de recursos.
    * Substitua __location__ pelo mesmo local usado nas etapas anteriores.
    * Substitua __storagename__ pelo nome da conta de armazenamento:
    * Substitua __storagekey__ pela chave obtida na etapa anterior. 
    * Como parâmetro `--defaultStorageContainer`, use o mesmo nome que você está usando para o cluster.
    * Substitua __admin__ e __httppassword__ pelo nome e pela senha que você deseja usar quando acessar o cluster por meio de HTTPS.
    * Substitua __sshuser__ e __sshuserpassword__ pelo nome de usuário e pela senha que deseja usar quando acessar o cluster usando SSH

    Pode levar vários minutos para que o processo de criação de cluster seja concluído. Geralmente, cerca de 15 minutos.

##Próximas etapas

Agora que você criou com êxito um cluster HDInsight usando a CLI do Azure, use o seguinte para aprender a trabalhar com o seu cluster:

###Clusters do Hadoop

* [Usar o Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o Pig com o HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

###Clusters do HBase

* [Introdução ao HBase no HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Desenvolvimento de aplicativos Java para HBase no HDInsight](hdinsight-hbase-build-java-maven-linux.md)

###Clusters Storm

* [Desenvolver topologias Java para Storm no HDInsight](hdinsight-storm-develop-java-topology.md)
* [Usar componentes de Python no Storm no HDInsight](hdinsight-storm-develop-python-topology.md)
* [Implantar e monitorar topologias com o Storm no HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

<!---HONumber=AcomDC_0615_2016-->