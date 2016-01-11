<properties
   pageTitle="Provisionar os clusters do Apache Spark no HDInsight | Microsoft Azure"
   description="Saiba como provisionar clusters do Spark para o Azure HDInsight usando o portal do Azure, Azure PowerShell, uma linha de comando ou o SDK HDInsight .NET"
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

# Criar clusters do Apache Spark no HDInsight usando opções personalizadas (Windows)

> [AZURE.NOTE]O HDInsight agora fornece clusters Spark no Linux. Para obter informações sobre a criação personalizada de um cluster Spark no HDInsight Linux, confira [Criar clusters baseados em Linux no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

Na maioria dos cenários, você pode criar um cluster Spark usando o método de criação rápida, conforme descrito em [Introdução ao Apache Spark no HDInsight](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md). Em determinados cenários, você talvez queira criar um cluster personalizado. Por exemplo, você talvez queira anexar um repositório de metadados externos para manter os metadados de Hive persistentes além do tempo de vida de um cluster, ou você talvez queira usar armazenamento adicional com o cluster.

Para esses e outros cenários, este artigo fornece instruções sobre como usar o Portal do Azure, Azure PowerShell ou SDK do HDInsight .NET para criar um cluster Spark personalizado no HDInsight.


**Pré-requisitos:**

Antes de começar a seguir as instruções neste artigo, você deve ter uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##<a id="configuration"></a>Quais são as opções de configuração diferentes?

### Armazenamento adicional

Durante a configuração, você deve especificar uma conta de Armazenamento do Blobs do Azure e um contêiner padrão. Eles são usados como o local de armazenamento padrão pelo cluster. Opcionalmente, você pode especificar uma conta de armazenamento do Azure adicional que também será associada ao cluster.

>[AZURE.NOTE]Não compartilhe um contêiner de armazenamento de Blobs entre múltiplos clusters. Não há suporte para isso.

Para obter mais informações sobre o uso de repositórios de blob secundários, consulte [Usando o Armazenamento de Blobs do Azure com o HDInsight](hdinsight-use-blob-storage.md).

### Metastore

O Spark permite definir esquema e tabelas do Hive em dados brutos. Você pode salvar esses esquemas e metadados da tabela em metastores externos. Usar o metastore ajuda a manter seus metadados do Hive para que você não precise recriar tabelas do Hive ao criar um novo cluster. Por padrão, o Hive usa um banco de dados integrado para armazenar essas informações. O banco de dados integrado não é capaz de preservar os metadados quando o cluster é excluído.

Para obter instruções sobre como criar um banco de dados SQL do Azure, confira [Create your first Azure SQL Database](sql-database-get-started.md).

### Personalização do cluster

Você pode instalar componentes adicionais ou personalizar a configuração de cluster por meio de scripts durante a criação. Tais scripts são chamados usando a **Ação de Script**, que é uma opção de configuração que pode ser usada no portal do Azure, cmdlets do Windows PowerShell do HDInsight ou SDK do .NET do HDInsight. Para obter mais informações, consulte [Personalizar cluster HDInsight usando a Ação de Script][hdinsight-customize-cluster].


### Rede Virtual

A [Rede Virtual do Azure](http://azure.microsoft.com/documentation/services/virtual-network/) permite que você crie uma rede segura e persistente contendo os recursos necessários para sua solução. Uma rede virtual permite que você:

* Conecte recursos da nuvem juntos em uma rede privada (somente nuvem).

	![diagrama da configuração somente nuvem](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-cloud-only.png)

* Conecte seus recursos de nuvem à sua rede do datacenter local (site a site ou ponto a site) usando uma Rede Virtual Privada (VPN).

	A configuração site a site permite que você conecte vários recursos de seu datacenter à Rede Virtual do Azure usando uma VPN de hardware ou o Serviço de Roteamento e Acesso Remoto.

	![diagrama da configuração site a site](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-site-to-site.png)

	A configuração ponto a site permite que você conecte um recurso específico à Rede Virtual do Azure usando uma VPN do software.

	![diagrama da configuração ponto a site](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-point-to-site.png)

Para obter mais informações sobre os recursos, benefícios e capacidades das redes virtuais, consulte a [Visão geral da rede virtual do Azure](../virtual-network/virtual-networks-overview.md).

> [AZURE.NOTE]Você deve criar a rede virtual do Azure antes de criar um cluster. Para obter mais informações, consulte [Como criar uma rede virtual](virtual-networks-create-vnet.md).
>
> O Azure HDInsight dá suporte somente a redes virtuais baseadas no local, e não trabalha atualmente com redes virtuais baseadas em grupo de afinidade.
>
> É altamente recomendado designar uma única sub-rede para um cluster.

##<a id="portal"></a>Usando o Portal de Visualização do Azure

Os clusters do Spark no HDInsight usam um contêiner de armazenamento de blobs do Azure como o sistema de arquivos padrão. Uma conta de Armazenamento do Azure localizada no mesmo data center é necessária para poder criar um cluster HDInsight. Para obter mais informações, consulte [Usar o Armazenamento de Blobs do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md). Para obter detalhes sobre como criar uma conta do Armazenamento do Azure, consulte [Como criar uma conta de armazenamento][azure-create-storageaccount].

**Para criar um cluster HDInsight usando a opção Criação Personalizada**

1. Entre no [Portal de Visualização do Azure](https://portal.azure.com).
2. Clique em **NOVO**, em **Análises de Dados** e em **HDInsight**.

    ![Criar um novo cluster no Portal de Visualização do Azure](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.1.png "Criar um novo cluster no Portal de Visualização do Azure")

3. Insira um **Nome de Cluster**, escolha **Spark** para o **Tipo de Cluster** e, no menu suspenso **Sistema Operacional do Cluster**, escolha **Windows Server 2012 R2 Datacenter**. Uma marca de seleção verde aparecerá ao lado do nome do cluster, se disponível.

	![Digite o tipo e o nome do cluster](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.2.png "Digite o tipo e o nome do cluster")

4. Se você tiver mais de uma assinatura, clique na entrada **Assinatura** para escolher a assinatura do Azure que será usada para o cluster.

5. Clique em **Grupo de Recursos** para ver uma lista de grupos de recursos existentes e escolha aquele no qual o cluster deve ser criado. Ou então, você pode clicar em **Criar Novo** e digitar o nome do novo grupo de recursos. Uma marca de seleção verde será exibida para indicar se o novo nome de grupo estiver disponível.

	> [AZURE.NOTE]A entrada padrão será um dos seus grupos de recursos existentes, se houver algum disponível.

6. Clique em **Credenciais** e insira um **Nome de Usuário de Logon do Cluster** e **Senha de Logon do Cluster**. Se você deseja habilitar a área de trabalho remota no nó do cluster, para **Habilitar a Área de Trabalho Remota**, clique em **Sim**. Selecione uma data de vencimento de acesso de área de trabalho remoto para o cluster e forneça o nome de usuário e senha do usuário de área de trabalho remota. Clique em **Selecionar** na parte inferior para salvar a configuração de credenciais.

	![Forneça credenciais de cluster](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.3.png "Forneça credenciais de cluster")

7. Clique em **Fonte de Dados** para escolher uma fonte de dados existente para o cluster ou criar uma nova.

	![Folha de fonte de dados](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.4.png "Forneça a configuração da fonte de dados")

	No momento, você pode selecionar uma Conta de Armazenamento do Azure como fonte de dados para um cluster HDInsight. Use os itens a seguir para entender as entradas na folha **Fonte de Dados**.

	- **Método de Seleção**: defina essa opção como **De todas as assinaturas** para habilitar a procura de contas de armazenamento em todas as suas assinaturas. Defina essa opção como **Chave de Acesso** se desejar inserir o **Nome de Armazenamento** e a **Chave de Acesso** de uma conta de armazenamento existente.

	- **Selecionar conta de armazenamento/Criar Nova**: clique em **Selecionar conta de armazenamento** para procurar e escolher uma conta de armazenamento existente que deseja associar ao cluster. Ou clique em **Criar Nova** para criar uma nova conta de armazenamento. Use o campo exibido para inserir o nome da conta de armazenamento. Uma marca de seleção verde será exibida se o nome estiver disponível.

	- **Escolher Contêiner Padrão**: use essa opção para inserir o nome do contêiner padrão a ser usado para o cluster. Embora você possa inserir qualquer nome aqui, é recomendável usar o mesmo nome que o cluster para que você possa reconhecer facilmente que o contêiner é usado para este cluster específico.

	- **Local**: a região geográfica na qual a conta de armazenamento está ou será criada.

		> [AZURE.IMPORTANT]Selecionar o local para a fonte de dados padrão também definirá o local do cluster do HDInsight. O cluster e a fonte de dados padrão devem estar localizados na mesma região.

	Clique em **Selecionar** para salvar a configuração de fonte de dados.

8. Clique em **Tipos de Preço do Nó** para exibir informações sobre os nós que serão criados para esse cluster. Defina o número de nós de trabalho que você precisa para o cluster. O custo estimado do cluster será mostrado na folha.

	![Folha de camadas de preços de nó](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.5.png "Especifique o número de nós de cluster")

	Clique em **Selecionar** para salvar a configuração de preços do nó.

9. Clique em **Configuração Opcional** para escolher a versão do cluster, bem como definir outras configurações opcionais, como ingressar em uma **Rede Virtual**, definir um **Metastore Externo** para armazenar dados de Hive e Oozie, usar as ações de script para personalizar um cluster a fim de instalar componentes personalizados ou usar contas de armazenamento adicionais com o cluster.

	* Clique na lista suspensa **Versão do HDInsight** e escolha a versão que deseja usar para o cluster. Para obter mais informações, confira [Versões de cluster do HDInsight](hdinsight-component-versioning.md).

	* Clique em **Rede Virtual** para fornecer detalhes de configuração para definir o cluster como parte de uma rede virtual. Na folha **Rede Virtual**, clique em **Rede Virtual** e em uma rede que deseja usar. Da mesma forma, escolha uma **Sub-rede** para a rede e clique em **Selecionar** para salvar a configuração da rede virtual.

		![Folha de rede virtual](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.6.png "Especifique os detalhes da rede virtual")

	* Clique em **Metastores Externos** para especificar o banco de dados SQL que deseja usar para salvar os metadados de Hive e Oozie associados ao cluster.

		![Personalize a folha de metastores](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.7.png "Especifique os metastores externos")

		Para **Usar um BD SQL existente para os metadados Hive**, clique em **Sim**, escolha um banco de dados SQL e forneça o nome de usuário e a senha para o banco de dados. Repita essas etapas se desejar **Usar um DB SQL existente para os metadados Oozie**. Clique em **Selecionar** até voltar para a folha **Configuração Opcional**.

		>[AZURE.NOTE]O banco de dados SQL do Azure usado para o metastore deve permitir a conectividade com outros serviços do Azure, incluindo o Azure HDInsight. No painel do banco de dados SQL do Azure, no lado direito, clique no nome do servidor. Esse é o servidor no qual a instância do banco de dados SQL está sendo executada. Quando você estiver na exibição do servidor, clique em **Configurar** e depois em **Serviços do Azure**; clique em **Sim** e depois em **Salvar**.

	* Clique em **Ações de Script** se quiser usar um script personalizado para personalizar um cluster enquanto ele estiver sendo criado. Para obter mais informações sobre ações de script, confira [Personalizar os clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster.md). Na folha Ações de Script forneça os detalhes como mostrado na captura de tela.

		![Folha de ação de script](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.8.png "Especifique a ação de script")

		Clique em **Selecionar** para salvar as alterações de configuração de ação de script.

	* Clique em **Chaves de Armazenamento do Azure** para especificar outras contas de armazenamento para associar ao cluster. Na folha **Chaves de Armazenamento do Azure**, clique em **Adicionar uma chave de armazenamento** e selecione uma conta de armazenamento existente ou crie uma nova conta.

		![Folha de armazenamento adicional](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.9.png "Especifique as contas de armazenamento adicionais")

		Clique em **Selecionar** até voltar para a folha **Novo cluster HDInsight**.

10. Na folha **Novo Cluster HDInsight**, verifique se a opção **Fixar no Quadro Inicial** está marcada e clique em **Criar**. Isso criará o cluster e adicionará um bloco para o mesmo para o quadro inicial do seu Portal do Azure. O ícone indica que o cluster está sendo criado e que será alterado para exibir o ícone do HDInsight após a conclusão da criação.

	| Durante a criação | Criação concluída |
	| ------------------ | --------------------- |
	| ![Criando um indicador no quadro inicial](./media/hdinsight-apache-spark-provision-clusters/provisioning.png) | ![Bloco de cluster criado](./media/hdinsight-apache-spark-provision-clusters/provisioned.png) |

	> [AZURE.NOTE]Levará algum tempo para que o cluster seja criado, geralmente, cerca de 15 minutos. Use o bloco no Quadro Inicial ou a entrada **Notificações** à esquerda da página para verificar o processo de criação.

11. Quando a criação for concluída, clique no bloco para o cluster no quadro inicial para iniciar a folha de cluster. A folha de cluster fornece informações essenciais sobre o cluster como o nome, o grupo de recursos ao qual ele pertence, o local, o sistema operacional, a URL para o painel do cluster, etc.

	![Folha de cluster](./media/hdinsight-apache-spark-provision-clusters/hdispark.cluster.blade.png "Propriedades do cluster")

	Use os seguintes itens para entender os ícones na parte superior desta folha e nas seções **Noções Básicas** e **Links Rápidos**:

	* **Configurações** e **Todas as Configurações**: exibem a folha **Configurações** do cluster, que permite acessar informações de configuração detalhadas do cluster.

	* **Painel** e **URL**: essas são as maneiras de acessar o painel do cluster, que é um portal da Web usado para executar trabalhos no cluster.

	* **Área de Trabalho Remota**: permite habilitar/desabilitar a área de trabalho remota nos nós do cluster.

	* **Dimensionar Cluster**: permite alterar o número de nós de trabalho do cluster.

	* **Excluir**: exclui o cluster HDInsight.

	* **Início Rápido** (![ícone de nuvem e raio = início rápido](./media/hdinsight-apache-spark-provision-clusters/quickstart.png)): exibe informações que ajudarão você a começar a usar o HDInsight.

	* **Usuários** (![ícone de usuários](./media/hdinsight-apache-spark-provision-clusters/users.png)): permite definir permissões para o _gerenciamento do portal_ deste cluster para outros usuários em sua assinatura do Azure.

		> [AZURE.IMPORTANT]Isso afeta _apenas_ o acesso e as permissões para o cluster no Portal de Visualização do Azure e não quem pode se conectar ao cluster HDInsight ou enviar trabalhos a ele.

	* **Marcas** (![ícone de marca](./media/hdinsight-apache-spark-provision-clusters/tags.png)): as marcas permitem estabelecer pares de chave/valor para definir uma taxonomia personalizada dos serviços de nuvem. Por exemplo, você pode criar uma chave chamada __projeto__ e usar um valor comum para todos os serviços associados a um projeto específico.

	* **Painel do Cluster**: inicia a folha Painel do Cluster, no qual você pode iniciar o próprio painel do cluster ou iniciar blocos de anotações do Zeppelin e Jupyter.


##<a id="powershell"></a>Usar o Azure PowerShell

Confira [Criar clusters HDInsight](hdinsight-provision-clusters.md#create-using-azure-powershell).

Especifique a opção de tipo de cluster Spark no cmdlet New-AzureRmHDInsightCluster:

	-ClusterType Spark


## Use o SDK do .NET do HDInsight baseado em ARM

Confira [Criar clusters HDInsight](hdinsight-provision-clusters.md#create-using-the-hdinsight-net-sdk).

Especifique o tipo de cluster Spark:

	private const HDInsightClusterType NewClusterType = HDInsightClusterType.Spark;


##<a id="nextsteps"></a> Próximas etapas

* Confira [Perform interactive data analysis using Spark in HDInsight with BI tools](hdinsight-apache-spark-use-bi-tools-v1.md) para saber como usar o Apache Spark no HDInsight com ferramentas de BI, como Power BI e Tableau.
* Confira [Use Spark in HDInsight for building machine learning applications](hdinsight-apache-spark-ipython-notebook-machine-learning-v1.md) para saber como criar aplicativos de aprendizado de máquina usando o Apache Spark no HDInsight.
* Confira [Use Spark in HDInsight for building real-time streaming applications](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md) para saber como criar aplicativos de streaming usando o Apache Spark no HDInsight.
* Confira [Manage resources for the Apache Spark cluster in Azure HDInsight](hdinsight-apache-spark-resource-manager-v1.md) para saber como usar o Gerenciador de Recursos para gerenciar os recursos alocados para o cluster Spark.


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/
[hadoop-hdinsight-intro]: ../hdinsight-hadoop-introduction/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx
[hdinsight-storm-get-started]: ../hdinsight-storm-getting-started/

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/

[azure-create-storageaccount]: ../storage-create-storage-account/

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[powershell-install-configure]: ../install-configure-powershell/


[azure-preview-portal]: https://portal.azure.com

[89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Use o Sqoop com o HDInsight"

<!---HONumber=AcomDC_1223_2015-->