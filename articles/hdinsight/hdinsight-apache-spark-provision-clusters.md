<properties
   pageTitle="Provisionar os clusters do Apache Spark no HDInsight | Microsoft Azure"
   description="Saiba como provisionar clusters Spark para o Azure HDInsight usando o Portal Clássico do Azure, o Azure PowerShell, uma linha de comando ou o SDK do .NET do HDInsight"
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
    ms.date="11/07/2015"
    ms.author="nitinme"/>

# Provisionar clusters do Apache Spark no HDInsight usando opções personalizadas

Na maioria dos cenários, você pode provisionar um cluster do Spark usando o método de criação rápida, conforme descrito em [Introdução ao Apache Spark no HDInsight](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md). Em determinados cenários, você talvez queira provisionar um cluster personalizado. Por exemplo, você talvez queira anexar um repositório de metadados externos para manter os metadados de Hive persistentes além do tempo de vida de um cluster, ou você talvez queira usar armazenamento adicional com o cluster.

Para esses e outros cenários, este artigo fornece instruções sobre como usar o Portal Clássico do Azure, Azure PowerShell ou SDK do .NET do HDInsight para provisionar um cluster Spark personalizado no HDInsight.


**Pré-requisitos:**

Antes de começar a seguir as instruções neste artigo, você deve ter uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##<a id="configuration"></a>Quais são as opções de configuração diferentes?

###Armazenamento adicional

Durante a configuração, você deve especificar uma conta de Armazenamento do Blob do Azure e um contêiner padrão. Eles são usados como o local de armazenamento padrão pelo cluster. Opcionalmente, você pode especificar uma conta de armazenamento do Azure adicional que também será associada ao cluster.

>[AZURE.NOTE]Não compartilhe um contêiner de armazenamento de Blob entre múltiplos clusters. Não há suporte para isso.

Para obter mais informações sobre o uso de repositórios de blob secundários, consulte [Usando o Armazenamento de Blob do Azure com o HDInsight](hdinsight-use-blob-storage.md).

###Metastore

O Spark permite definir esquema e tabelas do Hive em dados brutos. Você pode salvar esses esquemas e metadados da tabela em metastores externos. Usar o metastore ajuda a manter seus metadados do Hive, para que você não precise recriar tabelas do Hive ao provisionar um novo cluster. Por padrão, o Hive usa um banco de dados integrado para armazenar essas informações. O banco de dados integrado não é capaz de preservar os metadados quando o cluster é excluído.

Para obter instruções sobre como criar banco de dados SQL do Azure, consulte [Criar seu primeiro banco de dados SQL do Azure](sql-database-get-started.md).

### Personalização do cluster

Você pode instalar componentes adicionais ou personalizar a configuração de cluster por meio de scripts durante o provisionamento. Esses scripts são chamados usando a **Ação de Script**, que é uma opção de configuração que pode ser usada no Portal Clássico do Azure, nos cmdlets do Windows PowerShell do HDInsight ou no SDK do .NET do HDInsight. Para obter mais informações, consulte [Personalizar cluster HDInsight usando a Ação de Script][hdinsight-customize-cluster].


###Rede Virtual

A [Rede Virtual do Azure](http://azure.microsoft.com/documentation/services/virtual-network/) permite que você crie uma rede segura e persistente contendo os recursos necessários para sua solução. Uma rede virtual permite que você:

* Conecte recursos da nuvem juntos em uma rede privada (somente nuvem).

	![diagrama da configuração somente nuvem](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-cloud-only.png)

* Conecte seus recursos de nuvem à sua rede do datacenter local (site a site ou ponto a site) usando uma Rede Virtual Privada (VPN).

	A configuração site a site permite que você conecte vários recursos de seu datacenter à Rede Virtual do Azure usando uma VPN de hardware ou o Serviço de Roteamento e Acesso Remoto.

	![diagrama da configuração site a site](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-site-to-site.png)

	A configuração ponto a site permite que você conecte um recurso específico à Rede Virtual do Azure usando uma VPN do software.

	![diagrama da configuração ponto a site](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-point-to-site.png)

Para obter mais informações sobre os recursos, benefícios e capacidades das redes virtuais, consulte a [Visão geral da rede virtual do Azure](../virtual-network/virtual-networks-overview.md).

> [AZURE.NOTE]Você deve criar a rede virtual do Azure antes de provisionar um cluster HDInsight. Para obter mais informações, consulte [Como criar uma Rede Virtual](virtual-networks-create-vnet.md).
>
> O Azure HDInsight dá suporte somente a redes virtuais baseadas no local, e não trabalha atualmente com redes virtuais baseadas em grupo de afinidade.
>
> É altamente recomendado designar uma única sub-rede para um cluster.

##Usar o Portal do Azure

Os clusters do Spark no HDInsight usam um contêiner de armazenamento de blob do Azure como o sistema de arquivos padrão. Uma conta de Armazenamento do Azure localizada no mesmo data center é necessária para poder criar um cluster HDInsight. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md). Para obter detalhes sobre como criar uma conta do Armazenamento do Azure, consulte [Como criar uma conta de armazenamento][azure-create-storageaccount].

**Para criar um cluster HDInsight usando a opção Criação Personalizada**

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Clique em **NOVO**, em **Análises de Dados** e em **HDInsight**.

    ![Criando um novo cluster no Portal do Azure](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.1.png "Criando um novo cluster no Portal do Azure")

3. Insira um **Nome de Cluster**, selecione **Spark** para o **Tipo de Cluster** e no menu suspenso **Sistema Operacional do Cluster**, selecione **Windows Server 2012 R2 Datacenter**. Uma marca de seleção verde aparecerá ao lado do nome do cluster, se disponível.

	![Digite o tipo e o nome do cluster](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.2.png "Digite o tipo e o nome do cluster")

4. Se você tiver mais de uma assinatura, clique na entrada **Assinatura** para selecionar a assinatura do Azure que será usada para o cluster.

5. Clique em **Grupo de Recursos** para ver uma lista de grupos de recursos existentes e selecione um em que será criado o cluster. Ou então, você pode clicar em **Criar Novo** e digitar o nome do novo grupo de recursos. Uma marca de seleção verde será exibida para indicar se o novo nome de grupo estiver disponível.

	> [AZURE.NOTE]A entrada padrão será um dos seus grupos de recursos existentes, se houver algum disponível.

6. Clique em **Credenciais** e digite um **Nome de Usuário de Logon do Cluster** e **Senha de Logon do Cluster**. Se você deseja habilitar a área de trabalho remota no nó do cluster, para **Habilitar a Área de Trabalho Remota**, clique em **Sim**. Selecione uma data de vencimento de acesso de área de trabalho remoto para o cluster e forneça o nome de usuário e senha do usuário de área de trabalho remota. Clique em **Selecionar** na parte inferior para salvar a configuração de credenciais.

	![Forneça credenciais de cluster](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.3.png "Forneça credenciais de cluster")

7. Clique em **Fonte de Dados** para escolher uma fonte de dados existente para o cluster ou criar uma nova.

	![Folha de fonte de dados](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.4.png "Forneça a configuração da fonte de dados")

	No momento, você pode selecionar uma Conta de Armazenamento do Azure como fonte de dados para um cluster HDInsight. Use o item a seguir para entender as entradas na folha **Fonte de Dados**.

	- **Método de Seleção**: defina essa opção para **De todas as assinaturas** para habilitar a procura de contas de armazenamento de todas as suas assinaturas. Defina essa opção para a **Chave de Acesso** se desejar inserir o **Nome de Armazenamento** e a **Chave de Acesso** de uma conta de armazenamento existente.

	- **Selecionar conta de armazenamento/Criar nova**: clique em **Selecionar conta de armazenamento** para procurar e selecionar uma conta de armazenamento existente que você deseja associar ao cluster. Ou então clique em **Criar Nova** para criar uma nova conta de armazenamento. Use o campo exibido para inserir o nome da conta de armazenamento. Uma marca de seleção verde será exibida se o nome estiver disponível.

	- **Escolher Contêiner Padrão**: use essa opção para inserir o nome do contêiner padrão a ser usado para o cluster. Embora você possa inserir qualquer nome aqui, é recomendável usar o mesmo nome que o cluster para que você possa reconhecer facilmente que o contêiner é usado para este cluster específico.

	- **Local**: a região geográfica na qual a conta de armazenamento está localizada ou será criada.

		> [AZURE.IMPORTANT]Selecionar o local para a fonte de dados padrão também definirá o local do cluster do HDInsight. A fonte de dados padrão e o cluster devem estar localizados na mesma região.

	Clique em **Selecionar** para salvar a configuração de fonte de dados.

8. Clique em **Tipos de Preço do Nó** para exibir informações sobre os nós que serão criados para esse cluster. Defina o número de nós de trabalho que você precisa para o cluster. O custo estimado do cluster será mostrado na folha.

	![Folha de camadas de preços de nó](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.5.png "Especifique o número de nós de cluster")

	Clique em **Selecionar** para salvar a configuração de preço do nó.

9. Clique em **Configuração Opcional** para selecionar a versão do cluster, bem como definir outras configurações opcionais, como ingressar em uma **Rede Virtual**, definir um **Metastore Externo** para armazenar dados do Hive e Oozie, usar as Ações de Script para personalizar um cluster para instalar componentes personalizados ou usar contas de armazenamento adicionais com o cluster.

	* Clique no menu suspenso **Versão do HDInsight** e selecione a versão que deseja usar para o cluster. Para obter mais informações, confira [Versões de cluster do HDInsight](hdinsight-component-versioning.md).

	* Clique em **Rede Virtual** para fornecer detalhes de configuração para configurar o cluster como parte de uma rede virtual. Na folha **Rede Virtual**, clique em **Rede Virtual** e em uma rede que você deseja usar. Da mesma forma, selecione uma **Sub-rede** para a rede e clique em **Selecionar** para salvar a configuração da rede virtual.

		![Folha de rede virtual](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.6.png "Especifique os detalhes da rede virtual")

	* Clique em **Metastores Externos** para especificar o banco de dados SQL que deseja usar para salvar os metadados do Hive e Oozie associados ao cluster.

		![Personalize a folha de metastores](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.7.png "Especifique os metastores externos")

		Para **Usar um banco de dados SQL existente para metadados do Hive**, clique em **Sim**, selecione um banco de dados SQL e, em seguida, forneça o nome de usuário e senha para o banco de dados. Repita essas etapas se você quiser **Usar um banco de dados SQL existente para metadados do Oozie**. Clique em **Selecionar** até voltar para a folha **Configuração Opcional**.

		>[AZURE.NOTE]O banco de dados SQL do Azure usado para o metastore deve permitir a conectividade com outros serviços do Azure, incluindo o Azure HDInsight. No painel do banco de dados SQL do Azure, no lado direito, clique no nome do servidor. Esse é o servidor no qual a instância do banco de dados SQL está sendo executada. Quando você estiver na exibição do servidor, clique em **Configurar** e depois em **Serviços do Azure**; clique em **Sim** e depois em **Salvar**.

	* Clique em **Ações de Script** se quiser usar um script personalizado para personalizar um cluster enquanto ele estiver sendo criado. Para obter mais informações sobre ações de script, veja [Personalizar clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster.md). Na folha Ações de Script forneça os detalhes como mostrado na captura de tela.

		![Folha de ação de script](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.8.png "Especifique a ação de script")

		Clique em **Selecionar** para salvar as alterações de configuração de ação de script.

	* Clique em **Chaves de Armazenamento do Azure** para especificar contas de armazenamento adicionais para associar ao cluster. Na folha **Chaves de Armazenamento do Azure**, clique em **Adicionar uma chave de armazenamento** e selecione uma conta de armazenamento existente ou crie uma nova conta.

		![Folha de armazenamento adicional](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.9.png "Especifique as contas de armazenamento adicionais")

		Clique em **Selecionar** até voltar para a folha **Novo cluster HDInsight**.

10. Na folha **Novo Cluster HDInsight**, certifique-se de que a opção **Fixar no Quadro Inicial** está marcada e clique em **Criar**. Isso criará o cluster e adicionará um bloco para ele ao quadro inicial do Portal Clássico do Azure. O ícone indica que o cluster está provisionando e será alterado para exibir o ícone de HDInsight após a conclusão da configuração.

	| Durante o provisionamento | Provisionamento concluído |
	| ------------------ | --------------------- |
	| ![Indicador de provisionamento no quadro inicial](./media/hdinsight-apache-spark-provision-clusters/provisioning.png) | ![Bloco de cluster provisionado](./media/hdinsight-apache-spark-provision-clusters/provisioned.png) |

	> [AZURE.NOTE]Levará algum tempo para que o cluster seja criado, geralmente, cerca de 15 minutos. Use o bloco no Quadro Inicial ou a entrada **Notificações** à esquerda da página para verificar o processo de provisionamento.

11. Quando o provisionamento for concluído, clique no bloco para o cluster a partir do quadro inicial para iniciar a folha de cluster. A folha de cluster fornece informações essenciais sobre o cluster como o nome, o grupo de recursos ao qual ele pertence, o local, o sistema operacional, a URL para o painel do cluster, etc.

	![Folha de cluster](./media/hdinsight-apache-spark-provision-clusters/HDI.Cluster.Blade.png "Propriedades do cluster")

	Use o seguinte para entender os ícones na parte superior dessa folha e nas seções **Informações gerais** e **Links rápidos**:

	* **Configurações** e **Todas as Configurações**: exibem a folha **Configurações** do cluster, o que permite acessar informações de configuração detalhadas do cluster.

	* **Painel** e **URL**: essas são todas as maneiras de acessar o painel do cluster, que é um portal da Web usado para executar trabalhos no cluster.

	* **Área de Trabalho Remota**: permite habilitar/desabilitar a área de trabalho remota nos nós do cluster.

	* **Cluster em Escala**: permite alterar o número de nós de trabalho deste cluster.

	* **Excluir**: exclui o cluster HDInsight.

	* **Início Rápido** (![ícone de nuvem e raio = início rápido](./media/hdinsight-apache-spark-provision-clusters/quickstart.png)): exibe informações que irão ajudá-lo a começar a usar o HDInsight.

	* **Usuários** (![ícone de usuários](./media/hdinsight-apache-spark-provision-clusters/users.png)): permite definir permissões para o _gerenciamento de portal_ deste cluster para outros usuários em sua assinatura do Azure.

		> [AZURE.IMPORTANT]Isso afeta _apenas_ o acesso e as permissões para esse cluster no Portal do Azure e não afeta quem pode se conectar ao cluster HDInsight ou enviar trabalhos a ele.

	* **Marcas** (![ícone de marca](./media/hdinsight-apache-spark-provision-clusters/tags.png)): as marcas permitem definir pares de chave/valor para definir uma taxonomia personalizada dos seus serviços de nuvem. Por exemplo, você pode criar uma chave chamada __projeto__ e usar um valor comum para todos os serviços associados a um projeto específico.

	* **Painel do Cluster**: inicia a folha Painel do Cluster a partir da qual você pode iniciar o próprio painel do cluster ou iniciar notebooks Zeppelin e Jupyter.


## Usar PowerShell do Azure

Veja [Criar clusters HDInsight](hdinsight-provision-clusters.md#create-using-azure-powershell).

Especifique a opção de tipo de cluster Spark no cmdlet New-AzureRmHDInsightCluster:

	-ClusterType Spark

## Use o SDK do .NET do HDInsight baseado em ARM

Veja [Criar clusters HDInsight](hdinsight-provision-clusters.md#create-using-the-hdinsight-net-sdk).

Especifique o tipo de cluster Spark:

	private const HDInsightClusterType NewClusterType = HDInsightClusterType.Spark;


## Próximas etapas

* Veja [Executar análises de dados interativas usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md) para saber como usar o Apache Spark no HDInsight com ferramentas de BI como Power BI e Tableau.
* Veja [Usar o Spark no HDInsight para compilar aplicativos de aprendizado de máquina](hdinsight-apache-spark-ipython-notebook-machine-learning.md) para saber como compilar aplicativos de aprendizado de máquina usando o Apache Spark no HDInsight.
* Veja [Usar o Spark no HDInsight para compilar aplicativos de transmissão em tempo real](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md) para saber como compilar aplicativos de transmissão usando o Apache Spark no HDInsight.
* Veja [Gerenciar os recursos do cluster Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md) para saber como usar o Gerenciador de Recursos para gerenciar os recursos alocados para o cluster Spark.


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

<!---HONumber=AcomDC_1203_2015-->