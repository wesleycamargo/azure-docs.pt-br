<properties
   	pageTitle="Criar clusters de Hadoop, HBase, Storm ou Spark em Linux no HDInsight usando o portal | Microsoft Azure"
   	description="Aprenda a criar clusters Hadoop, HBase, Storm ou Spark em Linux para HDInsight usando um navegador da Web e o portal de visualização do Azure."
   	services="hdinsight"
   	documentationCenter=""
   	authors="nitinme"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="12/08/2015"
   	ms.author="nitinme"/>


#Criar clusters baseados em Linux no HDInsight usando o portal de visualização do Azure

[AZURE.INCLUDE [seletor](../../includes/hdinsight-selector-create-clusters.md)]

O portal de visualização do Azure é uma ferramenta de gerenciamento baseado na web para serviços e recursos hospedados na nuvem do Microsoft Azure. Use as informações neste documento para criar um novo cluster HDInsight baseados em Linux usando o portal de visualização e o navegador da Web

##Pré-requisitos

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __Um navegador da Web__. O portal de visualização do Azure usa o HTML5 e o Javascript e pode não funcionar corretamente em navegadores mais antigos.

##Criar clusters

1. Entre no [Portal de Visualização do Azure](https://portal.azure.com).

2. Clique em **NOVO**, clique em **Análises de dados**, e, em seguida, clique em **HDInsight**.

    ![Criar um novo cluster no Portal de Visualização do Azure](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.1.png "Criar um novo cluster no Portal de Visualização do Azure")

3. Insira um **Nome de Cluster**, selecione **Hadoop** para o **Tipo de Cluster** e, na lista suspensa **Sistema Operacional do Cluster**, selecione **Ubuntu**. Uma marca de seleção verde aparecerá ao lado do nome do cluster, se disponível.

    > [AZURE.NOTE] Para provisionar cluster HBase ou Storm, selecione o valor apropriado para o menu suspenso **Tipo de Cluster**.

    ![Digite o tipo e o nome do cluster](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.2.png "Digite o tipo e o nome do cluster")

4. Se você tiver mais de uma assinatura, clique na entrada **Assinatura** para selecionar a assinatura do Azure que será usada para o cluster.

5. Clique em **Grupo de recursos** para ver uma lista de grupos de recursos existente e, em seguida, selecione um em que será criado o cluster. Ou, você pode clicar em **Criar novo** e, em seguida, digitar o nome do novo grupo de recursos. Uma marca de seleção verde será exibida para indicar se o novo nome de grupo estiver disponível.

	> [AZURE.NOTE] A entrada padrão será um dos seus grupos de recursos existentes, se houver algum disponível.

6. Clique em **Credenciais** e, em seguida, digite uma senha para o usuário admin. Você também deve inserir um **Nome de Usuário de SSH** e uma **SENHA** ou uma **CHAVE PÚBLICA**, que será usada para autenticar o usuário SSH. Usar uma chave pública é a abordagem recomendada. Clique em **Selecionar** na parte inferior para salvar a configuração de credenciais.

	![Forneça credenciais de cluster](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.3.png "Forneça credenciais de cluster")

	Para obter mais informações sobre como usar SSH com o HDInsight, consulte um dos seguintes artigos:

	* [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


7. Clique em **Fonte de dados** para escolher uma fonte de dados existente para o cluster ou criar um novo.

	![Folha de fonte de dados](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.4.png "Forneça a configuração da fonte de dados")

	No momento, você pode selecionar uma Conta de Armazenamento do Azure como fonte de dados para um cluster HDInsight. Use o item a seguir para entender as entradas na folha **Fonte de Dados**.

	- **Método de Seleção**: defina essa opção como **De todas as assinaturas** para habilitar a procura de contas de armazenamento em todas as suas assinaturas. Defina essa opção como **Tecla de Acesso** se você desejar inserir o **Nome de Armazenamento** e a **Tecla de Acesso** de uma conta de armazenamento existente.

	- **Selecionar conta de armazenamento/Criar Nova**: clique em **Selecionar conta de armazenamento** para procurar e escolher uma conta de armazenamento existente que deseja associar ao cluster. Ou, clique em **Criar nova** para criar uma nova conta de armazenamento. Use o campo exibido para inserir o nome da conta de armazenamento. Uma marca de seleção verde será exibida se o nome estiver disponível.

	- **Escolher Contêiner Padrão**: use essa opção para inserir o nome do contêiner padrão a ser usado para o cluster. Embora você possa inserir qualquer nome aqui, é recomendável usar o mesmo nome que o cluster para que você possa reconhecer facilmente que o contêiner é usado para este cluster específico.

	- **Local**: a região geográfica na qual a conta de armazenamento está ou será criada.

		> [AZURE.IMPORTANT] Selecionar o local para a fonte de dados padrão também definirá o local do cluster do HDInsight. A fonte de dados padrão e o cluster devem estar localizados na mesma região.

	Clique em **Selecionar** para salvar a configuração de fonte de dados.

8. Clique em **Camadas de preços do nó** para exibir informações sobre os nós que serão criados para esse cluster. Defina o número de nós de trabalho que você precisa para o cluster. O custo estimado do cluster será mostrado na folha.

	![Folha de camadas de preços de nó](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.5.png "Especifique o número de nós de cluster")
    
    > [AZURE.IMPORTANT] Se você planeja ter mais de 32 nós de trabalho, seja na criação do cluster ou em seu dimensionamento após a criação, deverá selecionar um tamanho de nó de cabeçalho com pelo menos 8 núcleos e 14 GB de RAM.
    >
    > Para saber mais sobre tamanhos de nós e custos associados, consulte [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

	Clique em **Selecionar** para salvar a configuração de preços do nó.

9. Clique em **Configuração Opcional** para escolher a versão do cluster, bem como definir outras configurações opcionais, como ingressar em uma **Rede Virtual**, definir um **Metastore Externo** para armazenar dados de Hive e Oozie, usar as ações de script para personalizar um cluster a fim de instalar componentes personalizados ou usar contas de armazenamento adicionais com o cluster.

	* Clique no menu suspenso **Versão do HDInsight** e selecione a versão que deseja usar para o cluster. Para obter mais informações, consulte [Versões de cluster do HDInsight](hdinsight-component-versioning.md).

	* **Rede virtual**: selecione uma rede virtual do Azure e a sub-rede, se você deseja colocar o cluster em uma rede virtual.

		![Folha de rede virtual](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.6.png "Especifique os detalhes da rede virtual")

    	Para obter informações sobre como usar o HDInsight com uma Rede Virtual, incluindo requisitos de configuração específicos para a Rede Virtual, consulte [Estender recursos do HDInsight usando uma Rede Virtual do Azure](hdinsight-extend-hadoop-virtual-network.md).

	* Clique em **Metastores Externo** para especificar o banco de dados SQL que você deseja usar para salvar os metadados de Hive e Oozie associados ao cluster.
    
        > [AZURE.NOTE] A configuração de Metastore não está disponível para tipos de cluster HBase.

		![Personalize a folha de metastores](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.7.png "Especifique os metastores externos")

		Para **Usar um banco de dados SQL existente para metadados do Hive**, clique em **Sim**, selecione um banco de dados SQL e forneça o nome de usuário e a senha para o banco de dados. Repita essas etapas se você quiser **Usar um banco de dados SQL existente para metadados de Oozie**. Clique em **Selecionar** até voltar para a folha **Configuração Opcional**.

		>[AZURE.NOTE] O banco de dados SQL do Azure usado para o metastore deve permitir a conectividade com outros serviços do Azure, incluindo o Azure HDInsight. No painel do banco de dados SQL do Azure, no lado direito, clique no nome do servidor. Esse é o servidor no qual a instância do banco de dados SQL está sendo executada. Quando você estiver na exibição do servidor, clique em **Configurar** e depois em **Serviços do Azure**; clique em **Sim** e depois em **Salvar**.

	* **Ações de Script** se você quiser usar um script personalizado para personalizar um cluster enquanto o mesmo estiver sendo criado. Para obter mais informações sobre ações de script, consulte [Personalizar clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md). Na folha Ações de Script forneça os detalhes como mostrado na captura de tela.

		![Folha de ação de script](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.8.png "Especifique a ação de script")

	* Clique em **Chaves de Armazenamento do Azure** para especificar outras contas de armazenamento para associar ao cluster. Na folha **Chaves de Armazenamento do Azure**, clique em **Adicionar uma chave de armazenamento** e selecione uma conta de armazenamento existente ou crie uma nova conta.

		![Folha de armazenamento adicional](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.9.png "Especifique as contas de armazenamento adicionais")

		Clique em **Selecionar** até voltar para a folha **Novo cluster HDInsight**.

10. Na folha **Novo Cluster HDInsight**, certifique-se de que a opção **Fixar no Quadro Inicial** está marcada e clique em **Criar**. Isso criará o cluster e adicionará um bloco para o mesmo para o quadro inicial do seu Portal do Azure. O ícone indica que o cluster está provisionando e será alterado para exibir o ícone de HDInsight após a conclusão da configuração.

	| Durante o provisionamento | Provisionamento concluído |
	| ------------------ | --------------------- |
	| ![Indicador de provisionamento no quadro inicial](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioning.png) | ![Bloco de cluster provisionado](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioned.png) |

	> [AZURE.NOTE] Levará algum tempo para que o cluster seja criado, geralmente, cerca de 15 minutos. Use o bloco no Quadro Inicial ou a entrada **Notificações** à esquerda da página para verificar o processo de provisionamento.

11. Quando o provisionamento for concluído, clique no bloco para o cluster a partir do quadro inicial para iniciar a folha de cluster. A folha de cluster fornece informações essenciais sobre o cluster como o nome, o grupo de recursos ao qual ele pertence, o local, o sistema operacional, a URL para o painel do cluster, etc.

	![Folha de cluster](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.Cluster.Blade.png "Propriedades do cluster")

	Use o seguinte para entender os ícones na parte superior desta folha e na seção **Informações gerais**:

	* **Configurações** e **Todas as Configurações**: exibem a folha **Configurações** do cluster, que permite acessar informações de configuração detalhadas do cluster.

	* **Painel**, **Painel do Cluster** e **URL**: essas são todas as maneiras de acessar o painel do cluster, que é um portal da Web para executar trabalhos no cluster.

	* **Secure Shell**: informações necessárias para acessar o cluster usando o SSH.

	* **Excluir**: exclui o cluster HDInsight.

	* **Início Rápido** (![ícone de nuvem e raio = início rápido](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)): exibe informações que ajudarão você a começar a usar o HDInsight.

	* **Usuários** (![ícone de usuários](./media/hdinsight-hadoop-create-linux-cluster-portal/users.png)): permite definir permissões para o _gerenciamento do portal_ deste cluster para outros usuários em sua assinatura do Azure.

		> [AZURE.IMPORTANT] Isso afeta _apenas_ o acesso e as permissões para o cluster no Portal de Visualização do Azure e não quem pode se conectar ao cluster HDInsight ou enviar trabalhos a ele.

	* **Marcas** (![ícone de marca](./media/hdinsight-hadoop-create-linux-cluster-portal/tags.png)): as marcas permitem estabelecer pares de chave/valor para definir uma taxonomia personalizada dos serviços de nuvem. Por exemplo, você pode criar uma chave chamada __projeto__ e usar um valor comum para todos os serviços associados a um projeto específico.

##Personalizar clusters

- Consulte [Personalizar clusters do HDInsight usando a Inicialização](hdinsight-hadoop-customize-cluster-bootstrap.md).
- Consulte [Personalizar clusters do HDInsight baseados em Windows usando a Ação de Script](hdinsight-hadoop-customize-cluster.md).

##Próximas etapas

Agora que você criou com êxito um cluster HDInsight, use o seguinte para aprender a trabalhar com o seu cluster:

###Clusters do Hadoop

* [Usar o Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o Pig com o HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

###Clusters do HBase

* [Introdução ao HBase no HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Desenvolvimento de aplicativos Java para HBase no HDInsight](hdinsight-hbase-build-java-maven-linux)

###Clusters Storm

* [Desenvolver topologias Java para Storm no HDInsight](hdinsight-storm-develop-java-topology.md)
* [Usar componentes de Python no Storm no HDInsight](hdinsight-storm-develop-python-topology.md)
* [Implantar e monitorar topologias com o Storm no HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

###Clusters do Spark

* [Criar um aplicativo autônomo usando Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster do Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)
* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usar o Spark no HDInsight para a criação de aplicativos streaming em tempo real](hdinsight-apache-spark-eventhub-streaming.md)

<!---HONumber=AcomDC_0224_2016-->