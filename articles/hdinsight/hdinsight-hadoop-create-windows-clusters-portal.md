<properties
   pageTitle="Criar clusters Hadoop no HDInsight | Microsoft Azure"
   	description="Aprenda a criar clusters do Azure HDInsight usando o Portal do Azure."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="06/10/2016"
   ms.author="jgao"/>

# Crie clusters Hadoop baseados no Windows no HDInsight usando o Portal do Azure

[AZURE.INCLUDE [seletor](../../includes/hdinsight-selector-create-clusters.md)]

Saiba como criar um cluster Hadoop no HDInsight usando o Portal do Azure. O Portal do Microsoft [Azure](../azure-portal-overview.md) é um local central no qual você pode provisionar e gerenciar os recursos do Azure. O Portal do Azure é uma das ferramentas que você pode usar para criar cluster do Hadoop baseado em Linux ou Windows no HDInsight. Para outros recursos e ferramentas de criação de cluster, clique na guia Selecionar na parte superior dessa página ou consulte [Métodos de criação de cluster](hdinsight-provision-clusters.md#cluster-creation-methods).

##Pré-requisitos:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Antes de começar a seguir as instruções neste artigo, você deve ter o seguinte:

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## Criar clusters


**Para criar um cluster HDInsight**

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Clique em **NOVO**, em **Análises de Dados** e em **HDInsight**.

    ![Criando um novo cluster no Portal do Azure](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Criando um novo cluster no Portal do Azure")

3. Digite ou selecione os valores a seguir:

  * **Nome do Cluster**: insira um nome para o cluster. Uma marca de seleção verde aparecerá ao lado do nome do cluster, se o nome estiver disponível.
  * **Tipo de Cluster**: selecione **Hadoop**. Outras opções incluem **HBase**, **Storm** e **Spark**.
  * **Sistema Operacional do Cluster** - selecione **Windows**. Para criar um cluster baseado em Linux, selecione **Linux**.
  * **Versão**: consulte [versões do HDInsight](hdinsight-component-versioning.md).
  * **Assinatura**: selecione a assinatura do Azure que será usada para criar esse cluster.
  * **Grupo de Recursos**: escolha um grupo de recursos existente ou crie um novo. A entrada padrão será um dos seus grupos de recursos existentes, se houver algum disponível.
  * **Credenciais**: configure o nome de usuário e a senha do usuário do Hadoop (usuário HTTP). Se você habilitar a área de trabalho remota para o cluster, você precisará configurar uma data de vencimento de conta e senha e o nome de usuário do usuário de área de trabalho remota. Clique em **Selecionar** na parte inferior para salvar as alterações.

	   	![Provide cluster credentials](./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "Provide cluster credentials")

  * **Fonte de Dados**: crie uma nova conta de armazenamento do Azure ou escolha uma conta existente que será usada como o sistema de arquivos padrão do cluster.

   		![Data source blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "Provide data source configuration")

  		* **Selection Method**: Set this to **From all subscriptions** to enable browsing of storage accounts from all your subscriptions. Set this to **Access Key** if you want to enter the **Storage Name** and **Access Key** of an existing storage account.
  		* **Select storage account / Create New**: Click **Select storage account** to browse and select an existing storage account you want to associate with the cluster. Or, click **Create New** to create a new storage account. Use the field that appears to enter the name of the storage account. A green check will appear if the name is available.
  		* **Choose Default Container**: Use this to enter the name of the default container to use for the cluster. While you can enter any name here, we recommend using the same name as the cluster so that you can easily recognize that the container is used for this specific cluster.
  		* **Location**: The geographic region that the storage account is in, or will be created in. This location will determine the cluster location.  The cluster and its default storage account must co-locate in the same Azure data center.
  	
  * **Tipos de Preço do Nó**: defina o número de nós de trabalho que você precisa para o cluster. O custo estimado do cluster será mostrado na folha.
  

		![Node pricing tiers blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "Specify number of cluster nodes")


  * **Configuração Opcional**: para selecionar a versão do cluster, bem como definir outras configurações opcionais, como adicionar uma **Rede Virtual**, definir um **Metastore Externo** para armazenar dados do Hive e do Oozie, use as Ações de Script para personalizar um cluster para instalar componentes personalizados ou use contas de armazenamento adicionais com o cluster.

  		* **HDInsight Version**: Select the version you want to use for the cluster. For more information, see [HDInsight cluster versions](hdinsight-component-versioning.md).
  		* **Virtual Network**: Select an Azure virtual network and the subnet if you want to place the cluster into a virtual network.  

			![Virtual network blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "Specify virtual network details")

			For information on using HDInsight with a Virtual Network, including specific configuration requirements for the Virtual Network, see [Extend HDInsight capbilities by using an Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md).
  

  		
		* **External Metastores**: Specify an Azure SQL database to store Hive and Oozie metadata associated with the cluster.
 
            > [AZURE.NOTE] Metastore configuration is not available for HBase cluster types.

			![Custom metastores blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "Specify external metastores")


			For **Use an existing SQL DB for Hive** metadata, click **Yes**, select a SQL database, and then provide the username/password for the database. Repeat these steps if you want to **Use an existing SQL DB for Oozie metadata**. Click **Select** till you are back on the **Optional Configuration** blade.


			>[AZURE.NOTE] The Azure SQL database used for the metastore must allow connectivity to other Azure services, including Azure HDInsight. On the Azure SQL database dashboard, on the right side, click the server name. This is the server on which the SQL database instance is running. Once you are on the server view, click **Configure**, and then for **Azure Services**, click **Yes**, and then click **Save**.
		
  		* **Script Actions** if you want to use a custom script to customize a cluster, as the cluster is being created. For more information about script actions, see [Customize HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster.md). On the Script Actions blade provide the details as shown in the screen capture.
  	

			![Script action blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "Specify script action")


    	* **Azure Storage Keys**: Specify additional storage accounts to associate with the cluster. In the **Azure Storage Keys** blade, click **Add a storage key**, and then select an existing storage account or create a new account.
    

			![Additional storage blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "Specify additional storage accounts")


4. Clique em **Criar**. Escolher **Fixar no Quadro Inicial** adicionará um bloco para o cluster ao Quadro Inicial do Portal. O ícone indica que o cluster está sendo criado e será alterado para exibir o ícone de HDInsight após a conclusão da criação.
	
    Levará algum tempo para que o cluster seja criado, geralmente, cerca de 15 minutos. Use o bloco no Quadro Inicial ou a entrada **Notificações** à esquerda da página para verificar o processo de provisionamento.
	

5. Quando a criação for concluída, clique no bloco para o cluster no quadro inicial para iniciar a folha de cluster. A folha de cluster fornece informações essenciais sobre o cluster como o nome, o grupo de recursos ao qual ele pertence, o local, o sistema operacional, a URL para o painel do cluster, etc.


	![Folha de cluster](./media/hdinsight-provision-clusters/HDI.Cluster.Blade.png "Propriedades do cluster")


	Use o seguinte para entender os ícones na parte superior desta folha e na seção **Informações gerais**:


	* **Configurações** e **Todas as Configurações**: exibem a folha **Configurações** do cluster, que permite acessar informações de configuração detalhadas do cluster.
	* **Painel**, **Painel do Cluster** e **URL**: essas são as maneiras de acessar o painel do cluster, que é um portal da Web para executar trabalhos no cluster.
	* **Área de Trabalho Remota**: permite habilitar/desabilitar a área de trabalho remota nos nós do cluster.
	* **Cluster em Escala**: permite alterar o número de nós de trabalho deste cluster.
	* **Excluir**: exclui o cluster HDInsight.
	* **Início Rápido** (![ícone de nuvem e raio = início rápido](./media/hdinsight-provision-clusters/quickstart.png)): exibe informações que ajudarão você a começar a usar o HDInsight.
	* **Usuários** (![ícone de usuários](./media/hdinsight-provision-clusters/users.png)): permite definir permissões para o _gerenciamento do portal_ deste cluster para outros usuários em sua assinatura do Azure.
	

		> [AZURE.IMPORTANT] Isso afeta _apenas_ o acesso e as permissões para este cluster no Portal e não quem pode se conectar ao cluster HDInsight ou enviar trabalhos para ele.
		
	* **Marcas** (![ícone de marca](./media/hdinsight-provision-clusters/tags.png)): as marcas permitem estabelecer pares de chave/valor para definir uma taxonomia personalizada dos serviços de nuvem. Por exemplo, você pode criar uma chave chamada __projeto__ e usar um valor comum para todos os serviços associados a um projeto específico.

##Personalizar clusters

- Consulte [Personalizar clusters do HDInsight usando a Inicialização](hdinsight-hadoop-customize-cluster-bootstrap.md).
- Consulte [Personalizar clusters do HDInsight baseados em Windows usando a Ação de Script](hdinsight-hadoop-customize-cluster.md).

##Próximas etapas
Neste artigo, você aprendeu várias maneiras de criar um cluster HDInsight. Para saber mais, consulte os seguintes artigos:

* [Introdução ao Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) - aprenda como começar a trabalhar com seu cluster HDInsight
* [Enviar trabalhos do Hadoop de forma programática](hdinsight-submit-hadoop-jobs-programmatically.md) - aprenda como enviar trabalhos ao HDInsight de forma programática
* [Gerenciar clusters Hadoop no HDInsight usando o Portal do Azure](hdinsight-administer-use-management-portal.md)

<!---HONumber=AcomDC_0615_2016-->