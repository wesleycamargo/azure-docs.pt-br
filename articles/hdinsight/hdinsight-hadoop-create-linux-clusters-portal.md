<properties
   	pageTitle="Criar Hadoop, HBase ou clusters Storm no Linux no HDInsight usando o portal | Microsoft Azure"
   	description="Aprenda a criar Hadoop, HBase ou clusters Storm em Linux para HDInsight usando um navegador da Web e o portal de visualização do Azure."
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
   	ms.date="10/14/2015"
   	ms.author="nitinme"/>


#Criar clusters baseados em Linux no HDInsight usando o portal de visualização do Azure

[AZURE.INCLUDE [seletor](../../includes/hdinsight-create-linux-cluster-selector.md)]

O portal de visualização do Azure é uma ferramenta de gerenciamento baseado na web para serviços e recursos hospedados na nuvem do Microsoft Azure. Use as informações neste documento para criar um novo cluster HDInsight baseados em Linux usando o portal de visualização e o navegador da Web

##Pré-requisitos

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __Um navegador da Web__. O portal de visualização do Azure usa o HTML5 e o Javascript e pode não funcionar corretamente em navegadores mais antigos.

##Criar um cluster

1. Entre no [Portal de Visualização do Azure](https://portal.azure.com).

2. Clique em **NOVO**, clique em **Análises de dados**, e, em seguida, clique em **HDInsight**.

    ![Criar um novo cluster no Portal de Visualização do Azure](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.1.png "Criar um novo cluster no Portal de Visualização do Azure")

3. Insira um **Nome de Cluster**, selecione **Hadoop** para o **Tipo de Cluster** e, na lista suspensa **Sistema Operacional do Cluster**, selecione **Ubuntu**. Uma marca de seleção verde aparecerá ao lado do nome do cluster, se disponível.

    > [AZURE.NOTE]Para provisionar cluster HBase ou Storm, selecione o valor apropriado para o menu suspenso **Tipo de Cluster**.

    ![Digite o tipo e o nome do cluster](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.2.png "Digite o tipo e o nome do cluster")

4. Se você tiver mais de uma assinatura, clique na entrada **Assinatura** para selecionar a assinatura do Azure que será usada para o cluster.

5. Clique em **Grupo de recursos** para ver uma lista de grupos de recursos existente e, em seguida, selecione um no qual será criado o cluster. Ou, você pode clicar em **Criar novo** e, em seguida, digitar o nome do novo grupo de recursos. Uma marca de seleção verde será exibida para indicar se o novo nome de grupo estiver disponível.

	> [AZURE.NOTE]A entrada padrão será um dos seus grupos de recursos existentes, se houver algum disponível.

6. Clique em **Credenciais** e insira uma senha para o usuário administrador. Você também deve inserir um **Nome de Usuário de SSH** e uma **SENHA** ou uma **CHAVE PÚBLICA**, que será usada para autenticar o usuário SSH. Usar uma chave pública é a abordagem recomendada. Clique em **Selecionar** na parte inferior para salvar a configuração de credenciais.

	![Forneça credenciais de cluster](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.3.png "Forneça credenciais de cluster")

	Para obter mais informações sobre como usar SSH com o HDInsight, consulte um dos seguintes artigos:

	* [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


7. Clique em **Fonte de dados** para escolher uma fonte de dados existente para o cluster ou criar um novo.

	![Folha de fonte de dados](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.4.png "Forneça a configuração da fonte de dados")

	No momento, você pode selecionar uma Conta de Armazenamento do Azure como fonte de dados para um cluster HDInsight. Use o item a seguir para entender as entradas na folha **Fonte de Dados**.

	- **Método de seleção**: defina essa opção para **De todas as assinaturas** para habilitar a procura de contas de armazenamento de todas as suas assinaturas. Defina essa opção para **Tecla de Acesso** se você deseja inserir o **Nome de armazenamento** e **Tecla de Acesso** de uma conta de armazenamento existente.

	- **Selecionar conta de armazenamento / Criar nova**: clique em **Selecionar conta de armazenamento** para procurar e selecionar uma conta de armazenamento existente que você deseja associar com o cluster. Ou, clique em **Criar nova** para criar uma nova conta de armazenamento. Use o campo exibido para inserir o nome da conta de armazenamento. Uma marca de seleção verde será exibida se o nome estiver disponível.

	- **Escolher Contêiner Padrão**: use essa opção para inserir o nome do contêiner padrão a ser usado para o cluster. Embora você possa inserir qualquer nome aqui, é recomendável usar o mesmo nome que o cluster para que você possa reconhecer facilmente que o contêiner é usado para este cluster específico.

	- **Local**: a região geográfica na qual a conta de armazenamento está ou será criada.

		> [AZURE.IMPORTANT]Selecionar o local para a fonte de dados padrão também definirá o local do cluster do HDInsight. A fonte de dados padrão e o cluster devem estar localizados na mesma região.

	Clique em **Selecionar** para salvar a configuração de fonte de dados.

8. Clique em **Camadas de preços do nó** para exibir informações sobre os nós que serão criados para esse cluster. Defina o número de nós de trabalho que você precisa para o cluster. O custo estimado do cluster será mostrado na folha.

	![Folha de camadas de preços de nó](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.5.png "Especifique o número de nós de cluster")

	Clique em **Selecionar** para salvar a configuração de preço do nó.

9. Clique em **Configuração opcional** para selecionar a versão do cluster, bem como definir outras configurações opcionais, como adicionar uma **Rede Virtual**, definir a um **Metastore Externo** para armazenar dados de Hive e Oozie, usar as ações de Script para personalizar um cluster para instalar componentes personalizados ou usar contas de armazenamento adicionais com o cluster.

	* Clique na lista suspensa **Versão do HDInsight** e selecione a versão que você deseja usar para o cluster. Para obter mais informações, consulte [Versões de cluster do HDInsight](hdinsight-component-versioning.md).

	* **Rede virtual**: selecione uma rede virtual do Azure e a sub-rede, se você deseja colocar o cluster em uma rede virtual.

		![Folha de rede virtual](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.6.png "Especifique os detalhes da rede virtual")

    	>[AZURE.NOTE]O cluster HDInsight baseado em Windows só pode ser implantado em uma rede virtual clássica.

	* Clique em **Metastores Externo** para especificar o banco de dados SQL que você deseja usar para salvar os metadados de Hive e Oozie associados com o cluster.

		![Personalize a folha de metastores](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.7.png "Especifique os metastores externos")

		Para **Usar um banco de dados SQL existente para metadados de Hive**, clique em **Sim**, selecione um banco de dados SQL e, em seguida, forneça o nome de usuário e senha para o banco de dados. Repita essas etapas se você quiser **Usar um banco de dados SQL existente para metadados de Oozie**. Clique em **Selecionar** até voltar para a folha **Configuração opcional**.

		>[AZURE.NOTE]O banco de dados SQL do Azure usado para o metastore deve permitir a conectividade com outros serviços do Azure, incluindo o Azure HDInsight. No painel do banco de dados SQL do Azure, no lado direito, clique no nome do servidor. Esse é o servidor no qual a instância do banco de dados SQL está sendo executada. Quando você estiver na exibição do servidor, clique em **Configurar** e depois em **Serviços do Azure**; clique em **Sim** e depois em **Salvar**.

	* **Ações de Script** se você quiser usar um script personalizado para personalizar um cluster enquanto o mesmo estiver sendo criado. Para obter mais informações sobre ações de script, consulte [Personalizar clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md). Na folha Ações de Script forneça os detalhes como mostrado na captura de tela.

		![Folha de ação de script](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.8.png "Especifique a ação de script")

	* Clique em **Chaves de armazenamento do Azure** para especificar contas de armazenamento adicionais para associar com o cluster. Na folha **Chaves de armazenamento do Azure**, clique em **Adicionar uma chave de armazenamento**, e, em seguida, selecione uma conta de armazenamento existente ou crie uma nova conta.

		![Folha de armazenamento adicional](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.9.png "Especifique as contas de armazenamento adicionais")

		Clique em **Selecionar** até voltar para a folha **Novo cluster HDInsight**.

10. Na folha **Novo cluster HDInsight**, certifique-se de que **Fixar no quadro inicial** está selecionado e, em seguida, clique em **Criar**. Isso criará o cluster e adicionará um bloco para o mesmo para o quadro inicial do seu Portal do Azure. O ícone indica que o cluster está provisionando e será alterado para exibir o ícone de HDInsight após a conclusão da configuração.

	| Durante o provisionamento | Provisionamento concluído |
	| ------------------ | --------------------- |
	| ![Indicador de provisionamento no quadro inicial](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioning.png) | ![Bloco de cluster provisionado](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioned.png) |

	> [AZURE.NOTE]Levará algum tempo para que o cluster seja criado, geralmente, cerca de 15 minutos. Use o bloco no Quadro Inicial ou a entrada **Notificações** à esquerda da página para verificar o processo de provisionamento.

11. Quando o provisionamento for concluído, clique no bloco para o cluster a partir do quadro inicial para iniciar a folha de cluster. A folha de cluster fornece informações essenciais sobre o cluster como o nome, o grupo de recursos ao qual ele pertence, o local, o sistema operacional, a URL para o painel do cluster, etc.

	![Folha de cluster](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.Cluster.Blade.png "Propriedades do cluster")

	Use as seguintes opções para entender os ícones na parte superior desta folha e na seção **Noções básicas**:

	* **Configurações** e **Todas as Configurações**: exibem a folha **Configurações** do cluster, o que permite acessar informações de configuração detalhadas do cluster.

	* **Painel**, **Painel do Cluster** e **URL**: essas são todas as maneiras de acessar o painel do cluster, que é um portal da Web para executar trabalhos no cluster.

	* **Secure Shell**: informações necessárias para acessar o cluster usando SSH.

	* **Excluir**: exclui o cluster HDInsight.

	* **Início Rápido** (![ícone de nuvem e raio = início rápido](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)): exibe informações que o ajudarão a começar a usar o HDInsight.

	* **Usuários** (![ícone de usuários](./media/hdinsight-hadoop-create-linux-cluster-portal/users.png)): permite definir permissões para o _gerenciamento de portal_ do cluster para outros usuários em sua assinatura do Azure.

		> [AZURE.IMPORTANT]Isso _só_ afeta o acesso e as permissões para o cluster no portal de visualização do Azure e não afeta quem pode se conectar ao cluster HDInsight ou enviar trabalhos a ele.

	* **Marcas** (![ícone de marca](./media/hdinsight-hadoop-create-linux-cluster-portal/tags.png)): as marcas permitem definir pares de chave/valor para definir uma taxonomia personalizada dos serviços de nuvem. Por exemplo, você pode criar uma chave chamada __projeto__ e usar um valor comum para todos os serviços associados a um projeto específico.

##Próximas etapas

Agora que você criou com êxito um cluster HDInsight, use o seguinte para aprender a trabalhar com o seu cluster:

###Clusters do Hadoop

* [Usar o Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o Pig com o HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

###Clusters do HBase

* [Introdução ao HBase no HDInsight](hdinsight-hbase-tutorial-get-stared-linux.md)
* [Desenvolvimento de aplicativos Java para HBase no HDInsight](hdinsight-hbase-build-java-maven-linux)

###Clusters Storm

* [Desenvolver topologias Java para Storm no HDInsight](hdinsight-storm-develop-java-topology.md)
* [Usar componentes de Python no Storm no HDInsight](hdinsight-storm-develop-python.md)
* [Implantar e monitorar topologias com o Storm no HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

<!---HONumber=Oct15_HO3-->