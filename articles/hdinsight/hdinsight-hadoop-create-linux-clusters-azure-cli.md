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
   	ms.date="11/17/2015"
   	ms.author="larryfr"/>

#Criar clusters baseados em Linux no HDInsight usando o Azure CLI

[AZURE.INCLUDE [seletor](../../includes/hdinsight-create-linux-cluster-selector.md)]

A CLI do Azure é um utilitário de linha de comando entre plataformas que permite o gerenciamento dos Serviços do Azure. Ela pode ser usada, juntamente com os modelos de gerenciamento de recursos do Azure, para criar um cluster HDInsight, junto com as contas de armazenamento associadas e outros serviços.

Os modelos de gerenciamento de recursos do Azure são documentos JSON que descrevem um __grupo de recursos__ e todos os recursos que ele contém (por exemplo, HDInsight). Essa abordagem baseada em modelo permite a definição de todos os recursos necessários ao HDInsight em um modelo, e também gerenciar alterações no grupo todo por meio de __implantações__ que aplicam mudanças ao grupo.

As etapas neste documento fornecem orientação durante o processo de criação de um novo cluster HDInsight usando a CLI do Azure e um modelo.

> [AZURE.IMPORTANT]As etapas neste documento usam o número padrão de nós de trabalho (4) para um cluster HDInsight. Se você planeja ter mais de 32 nós de trabalho, seja na criação do cluster ou em seu dimensionamento após a criação, deverá selecionar um tamanho de nó de cabeçalho com pelo menos 8 núcleos e 14 GB de RAM.
>
> Para saber mais sobre tamanhos de nós e custos associados, consulte [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

##Pré-requisitos

- **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __CLI do Azure__. Para obter informações sobre como instalar a CLI, consulte [Instalar a CLI do Azure](../xplat-cli-install.md).

##Faça logon na sua assinatura do Azure

Siga as etapas documentadas em [Conectar a uma assinatura do Azure por meio da CLI do Azure (Interface de Linha de Comando do Azure)](../xplat-cli-connect.md) e conecte à sua assinatura usando o método de __logon__.

##Criar um cluster

As etapas a seguir devem ser executadas de um prompt de comando, shell ou a sessão terminal após instalar e configurar o CLI do Azure.

1. Use o seguinte comando para fazer logon em sua assinatura do Azure:

        azure login

    Você receberá uma solicitação para fornecer seu nome e senha. Se você tiver várias assinaturas do Azure, use `azure account set <subscriptionname>` para definir a assinatura que será usada pelos comandos da CLI do Azure.

3. Alterne para modo Gerenciador de Recursos do Azure usando o seguinte comando:

        azure config mode arm

4. Crie um modelo para seu cluster HDInsight. Estes são alguns exemplos de modelos básicos:

    * [Cluster baseado em Linux, usando uma chave SSH pública](https://github.com/Azure/azure-quickstart-templates/tree/master/hdinsight-linux-ssh-publickey)
    * [Cluster baseado em Linux, usando uma senha para a conta do SSH](https://github.com/Azure/azure-quickstart-templates/tree/master/hdinsight-linux-ssh-password)

    Os dois modelos também criam a Conta de Armazenamento do Azure padrão usada pelo HDInsight.

    Os arquivos necessários são o __azuredeploy.json__ e o __azuredeploy.parameters.json__. Copie esses arquivos localmente antes de continuar.

5. Abra o arquivo __azuredeploy.parameters.json__ em um editor e forneça valores para os itens na seção `parameters`:

    * __local__: o data center no qual os recursos serão criados. Você pode exibir a `location` seção no arquivo __azuredeploy.json__ para obter uma lista dos locais permitidos.
    * __clusterName__: o nome do cluster HDInsight. Esse nome deve ser exclusivo ou a implantação falhará.
    * __clusterStorageAccountName__: o nome da Conta de armazenamento do Azure que será criada para o cluster HDInsight. Esse nome deve ser exclusivo ou a implantação falhará.
    * __clusterLoginPassword__: a senha para o usuário administrador do cluster. Ela deve ser uma senha segura, pois é usada para acessar sites e serviços REST no cluster.
    * __sshUserName__: o nome do primeiro usuário SSH a ser criado para este cluster. SSH será usado para acessar remotamente o cluster usando essa conta.
    * __sshPublicKey__: se você estiver usando o modelo que exige uma chave pública SSH, adicione a chave pública a esta linha. Para saber mais sobre como gerar e trabalhar com chaves públicas, consulte os artigos a seguir:

        * [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
        * [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    * __sshPassword__: se você estiver usando o modelo que exige uma senha SSH, adicione uma senha a esta linha.

    Quando terminar, salve e feche o arquivo.

5. Use o seguinte para criar um grupo de recursos vazio. Substitua __RESOURCEGROUPNAME__ pelo nome que deseja usar para esse grupo. Substitua __LOCATION__ pelo data center no qual você quer criar o cluster HDInsight:

        azure group create RESOURCEGROUPNAME LOCATION
    
    > [AZURE.NOTE]Se o nome do local contiver espaços, coloque-o entre aspas duplas. Por exemplo, "Centro-Sul dos EUA".

6. Use o comando a seguir para criar a implantação inicial para esse grupo de recursos. Substitua __PATHTOTEMPLATE__ pelo caminho para o arquivo de modelo __azuredeploy.json__. Substitua __PATHTOPARAMETERSFILE__ pelo caminho para o arquivo __azuredeploy.parameters.json__. Substitua __RESOURCEGROUPNAME__ pelo nome do grupo criado na etapa anterior:

        azure group deployment create -f PATHTOTEMPLATE -e PATHTOPARAMETERSFILE -g RESOURCEGROUPNAME -n InitialDeployment

    Após a aceitação da implantação, você verá uma mensagem parecida com `group deployment create command ok`.

7. Pode levar algum tempo para a implantação ser concluída, cerca de 15 minutos. Exiba informações sobre a implantação usando o comando a seguir. Substitua __RESOURCEGROUPNAME__ pelo nome do grupo de recursos usado na etapa anterior:

        azure group log show -l RESOURCEGROUPNAME
    
    Depois que a implantação for concluída, o campo __Status__ conterá o valor __Bem-Sucedido__. Se ocorrer uma falha durante a implantação, será possível obter mais informações sobre ela usando o comando a seguir

        azure group log show -l -v RESOURCEGROUPNAME

##Próximas etapas

Agora que você criou com êxito um cluster HDInsight, use o seguinte para aprender a trabalhar com o seu cluster:

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

<!---HONumber=AcomDC_1210_2015-->