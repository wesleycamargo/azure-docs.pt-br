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
	ms.date="08/17/2015"
	ms.author="nitinme"/>

# Provisionar clusters do Apache Spark no HDInsight usando opções personalizadas

Na maioria dos cenários, você pode provisionar um cluster do Spark usando o método de criação rápida, conforme descrito em [Introdução ao Apache Spark no HDInsight](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md). Em determinados cenários, você talvez queira provisionar um cluster personalizado. Por exemplo, você talvez queira anexar um repositório de metadados externos para manter os metadados de Hive persistentes além do tempo de vida de um cluster, ou você talvez queira usar armazenamento adicional com o cluster.

Para esses e outros cenários, este artigo fornece instruções sobre como usar o portal do Azure, Azure PowerShell ou o SDK do HDInsight .NET para provisionar um cluster do Spark personalizado no HDInsight.


**Pré-requisitos:**

Antes de começar a seguir as instruções neste artigo, você deve ter uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##<a id="configuration"></a>Quais são as opções de configuração diferentes?

###Armazenamento adicional

Durante a configuração, você deve especificar uma conta de Armazenamento do Blob do Azure e um contêiner padrão. Eles são usados como o local de armazenamento padrão pelo cluster. Opcionalmente, você pode especificar uma conta de armazenamento do Azure adicional que também será associada ao cluster.

>[AZURE.NOTE] Não compartilhe um contêiner de armazenamento de Blob entre múltiplos clusters. Não há suporte para isso.

Para obter mais informações sobre o uso de repositórios de blob secundários, consulte [Usando o Armazenamento de Blob do Azure com o HDInsight](hdinsight-use-blob-storage.md).

###Metastore

O Spark permite definir esquema e tabelas do Hive em dados brutos. Você pode salvar esses esquemas e metadados da tabela em metastores externos. Usar o metastore ajuda a manter seus metadados do Hive, para que você não precise recriar tabelas do Hive ao provisionar um novo cluster. Por padrão, o Hive usa um banco de dados integrado para armazenar essas informações. O banco de dados integrado não é capaz de preservar os metadados quando o cluster é excluído.

Para obter instruções sobre como criar banco de dados SQL do Azure, consulte [Criar seu primeiro banco de dados SQL do Azure](sql-database-get-started.md).

### Personalização do cluster

Você pode instalar componentes adicionais ou personalizar a configuração de cluster por meio de scripts durante o provisionamento. Tais scripts são chamados usando a **Ação de Script**, que é uma opção de configuração que pode ser usada no portal do Azure, cmdlets do Windows PowerShell do HDInsight ou SDK do .NET do HDInsight. Para obter mais informações, consulte [Personalizar cluster HDInsight usando a Ação de Script][hdinsight-customize-cluster].


###Rede Virtual

A [Rede Virtual do Azure](http://azure.microsoft.com/documentation/services/virtual-network/) permite que você crie uma rede segura e persistente contendo os recursos necessários para sua solução. Uma rede virtual permite que você:

* Conecte recursos da nuvem juntos em uma rede privada (somente nuvem).

	![diagrama da configuração somente nuvem](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-cloud-only.png)

* Conecte seus recursos de nuvem à sua rede do datacenter local (site a site ou ponto a site) usando uma Rede Virtual Privada (VPN).

	A configuração site a site permite que você conecte vários recursos de seu datacenter à Rede Virtual do Azure usando uma VPN de hardware ou o Serviço de Roteamento e Acesso Remoto.

	![diagrama da configuração site a site](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-site-to-site.png)

	A configuração ponto a site permite que você conecte um recurso específico à Rede Virtual do Azure usando uma VPN do software.

	![diagrama da configuração ponto a site](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-point-to-site.png)

Para obter mais informações sobre os recursos, benefícios e capacidades das redes virtuais, consulte a [Visão geral da rede virtual do Azure](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [AZURE.NOTE] Você deve criar a rede virtual do Azure antes de provisionar um cluster HDInsight. Para obter mais informações, consulte [Tarefas de configuração de rede virtual](http://msdn.microsoft.com/library/azure/jj156206.aspx).
>
> O Azure HDInsight dá suporte somente a redes virtuais baseadas no local, e não trabalha atualmente com redes virtuais baseadas em grupo de afinidade.
>
> É altamente recomendado designar uma única sub-rede para um cluster.

##<a id="portal"></a> Usando o portal de visualização do Azure

Os clusters do Spark no HDInsight usam um contêiner de armazenamento de blob do Azure como o sistema de arquivos padrão. Uma conta de Armazenamento do Azure localizada no mesmo data center é necessária para poder criar um cluster HDInsight. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md). Para obter detalhes sobre como criar uma conta do Armazenamento do Azure, consulte [Como criar uma conta de armazenamento][azure-create-storageaccount].

**Para criar um cluster HDInsight usando a opção Criação Personalizada**

1. Entre no [Portal de Visualização do Azure](https://portal.azure.com).
2. Clique em **NOVO**, clique em **Análises de dados**, e, em seguida, clique em **HDInsight**.

    ![Criar um novo cluster no Portal de Visualização do Azure](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.1.png "Criar um novo cluster no Portal de Visualização do Azure")

3. Insira um **Nome de Cluster**, selecione **Hadoop** para o **Tipo de cluster** e na lista suspensa **Sistema operacional do cluster**, selecione **Windows Server 2012 R2 Datacenter**. Uma marca de seleção verde aparecerá ao lado do nome do cluster, se disponível.

	![Digite o tipo e o nome do cluster](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.2.png "Digite o tipo e o nome do cluster")

4. Se você tiver mais de uma assinatura, clique na entrada **Assinatura** para selecionar a assinatura do Azure que será usada para o cluster.

5. Clique em **Grupo de recursos** para ver uma lista de grupos de recursos existente e, em seguida, selecione um onde será criado o cluster. Ou, você pode clicar em **Criar novo** e, em seguida, digitar o nome do novo grupo de recursos. Uma marca de seleção verde será exibida para indicar se o novo nome de grupo estiver disponível.

	> [AZURE.NOTE]A entrada padrão será um dos seus grupos de recursos existentes, se houver algum disponível.

6. Clique em **Credenciais**, em seguida, digite um **Nome de usuário de logon de cluster** e **Senha de logon do Cluster**. Se você deseja habilitar a área de trabalho remota no nó do cluster, para **Habilitar área de trabalho remota**, clique em **Sim**. Selecione uma data de vencimento de acesso de área de trabalho remoto para o cluster e forneça o nome de usuário e senha do usuário de área de trabalho remota. Clique em **Selecionar** na parte inferior para salvar a configuração de credenciais.

	![Forneça credenciais de cluster](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.3.png "Forneça credenciais de cluster")

7. Clique em **Fonte de dados** para escolher uma fonte de dados existente para o cluster ou criar um novo.

	![Folha de fonte de dados](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.4.png "Forneça a configuração da fonte de dados")

	No momento, você pode selecionar uma Conta de Armazenamento do Azure como fonte de dados para um cluster HDInsight. Use o item a seguir para entender as entradas na folha **Fonte de dados**.

	- **Método de seleção**: defina essa opção para **De todas as assinaturas** para habilitar a procura de contas de armazenamento de todas as suas assinaturas. Defina essa opção para **Tecla de Acesso** se você deseja inserir o **Nome de armazenamento** e **Tecla de Acesso** de uma conta de armazenamento existente.

	- **Selecionar conta de armazenamento / Criar nova**: clique em **Selecionar conta de armazenamento** para procurar e selecionar uma conta de armazenamento existente que você deseja associar com o cluster. Ou, clique em **Criar nova** para criar uma nova conta de armazenamento. Use o campo exibido para inserir o nome da conta de armazenamento. Uma marca de seleção verde será exibida se o nome estiver disponível.

	- **Escolher contêiner padrão**: use essa opção para inserir o nome do contêiner padrão a ser usado para o cluster. Embora você possa inserir qualquer nome aqui, é recomendável usar o mesmo nome que o cluster para que você possa reconhecer facilmente que o contêiner é usado para este cluster específico.

	- **Local**: a região geográfica na qual a conta de armazenamento está ou será criada.

		> [AZURE.IMPORTANT]Selecionar o local para a fonte de dados padrão também definirá o local do cluster do HDInsight. A fonte de dados padrão e o cluster devem estar localizados na mesma região.

	Clique em **Selecionar** para salvar a configuração de fonte de dados.

8. Clique em **Camadas de preços do nó** para exibir informações sobre os nós que serão criados para esse cluster. Defina o número de nós de trabalho que você precisa para o cluster. O custo estimado do cluster será mostrado na folha.

	![Folha de camadas de preços de nó](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.5.png "Especifique o número de nós de cluster")

	Clique em **Selecionar** para salvar a configuração de preço do nó.

9. Clique em **Configuração opcional** para selecionar a versão do cluster, bem como definir outras configurações opcionais, como adicionar uma **Rede Virtual**, definir a um **Metastore Externo** para armazenar dados de Hive e Oozie, usar as ações de Script para personalizar um cluster para instalar componentes personalizados ou usar contas de armazenamento adicionais com o cluster.

	* Clique na lista suspensa **Versão do HDInsight** e selecione a versão que você deseja usar para o cluster. Para obter mais informações, consulte [Versões de cluster do HDInsight](hdinsight-component-versioning.md).

	* Clique em **Rede Virtual** para fornecer detalhes de configuração para configurar o cluster como parte de uma rede virtual. Na folha **Rede Virtual**, clique em **Rede Virtual** e, em seguida, clique em uma rede que você deseja usar. Da mesma forma, selecione uma **sub-rede** para a rede e clique em **Selecionar** para salvar a configuração da rede virtual.

		![Folha de rede virtual](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.6.png "Especifique os detalhes da rede virtual")

	* Clique em **Metastores Externo** para especificar o banco de dados SQL que você deseja usar para salvar os metadados de Hive e Oozie associados com o cluster.

		![Personalize a folha de metastores](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.7.png "Especifique os metastores externos")

		Para **Usar um banco de dados SQL existente para metadados de Hive**, clique em **Sim**, selecione um banco de dados SQL e, em seguida, forneça o nome de usuário e senha para o banco de dados. Repita essas etapas se você quiser **Usar um banco de dados SQL existente para metadados de Oozie**. Clique em **Selecionar** até voltar para a folha **Configuração opcional**.

    >[AZURE.NOTE] O banco de dados SQL do Azure usado para o metastore deve permitir a conectividade com outros serviços do Azure, incluindo o Azure HDInsight. No painel do banco de dados SQL do Azure, no lado direito, clique no nome do servidor. Esse é o servidor no qual a instância do banco de dados SQL está sendo executada. Quando você estiver na exibição do servidor, clique em **Configurar** e depois em **Serviços do Azure**; clique em **Sim** e depois em **Salvar**.

	* Clique em **Ações de Script** se você quiser usar um script personalizado para personalizar um cluster enquanto o mesmo estiver sendo criado. Para obter mais informações sobre ações de script, consulte [Personalizar clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster.md). Na folha Ações de Script forneça os detalhes como mostrado na captura de tela.

		![Folha de ação de script](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.8.png "Especifique a ação de script")

		Clique em **Selecionar** para salvar as alterações de configuração de ação de script.

	* Clique em **Chaves de armazenamento do Azure** para especificar contas de armazenamento adicionais para associar com o cluster. Na folha **Chaves de armazenamento do Azure**, clique em **Adicionar uma chave de armazenamento**, e, em seguida, selecione uma conta de armazenamento existente ou crie uma nova conta.

		![Folha de armazenamento adicional](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.9.png "Especifique as contas de armazenamento adicionais")

		Clique em **Selecionar** até voltar para a folha **Novo cluster HDInsight**.

10. Na folha **Novo cluster HDInsight**, certifique-se de que **Fixar no quadro inicial** está selecionado e, em seguida, clique em **Criar**. Isso criará o cluster e adicionará um bloco para o mesmo para o quadro inicial do seu Portal do Azure. O ícone indica que o cluster está provisionando e será alterado para exibir o ícone de HDInsight após a conclusão da configuração.

	| Durante o provisionamento | Provisionamento concluído |
	| ------------------ | --------------------- |
	| ![Indicador de provisionamento no quadro inicial](./media/hdinsight-apache-spark-provision-clusters/provisioning.png) | ![Bloco de cluster provisionado](./media/hdinsight-apache-spark-provision-clusters/provisioned.png) |

	> [AZURE.NOTE]Levará algum tempo para que o cluster seja criado, geralmente, cerca de 15 minutos. Use o bloco no Quadro Inicial ou a entrada **Notificações** à esquerda da página para verificar o processo de provisionamento.

11. Quando o provisionamento for concluído, clique no bloco para o cluster a partir do quadro inicial para iniciar a folha de cluster. A folha de cluster fornece informações essenciais sobre o cluster como o nome, o grupo de recursos ao qual ele pertence, o local, o sistema operacional, a URL para o painel do cluster, etc.

	![Folha de cluster](./media/hdinsight-apache-spark-provision-clusters/HDI.Cluster.Blade.png "Propriedades do cluster")

	Use o seguinte para entender os ícones na parte superior dessa folha e nas seções **Essentials** e **Links rápidos**:

	* **Configurações** e **Todas as Configurações**: exibem a folha **Configurações** do cluster, o que permite acessar informações de configuração detalhadas do cluster.

	* **Painel** e **URL**: essas são todas as maneiras de acessar o painel de cluster, que é um portal da Web para executar trabalhos no cluster.

	* **Área de trabalho remota**: permite habilitar/desabilitar a área de trabalho remota em nós de cluster.

	* **Cluster em escala**: permite alterar o número de nós de trabalho para o cluster.

	* **Excluir**: exclui o cluster HDInsight.

	* **Início Rápido** (![ícone de nuvem e raio = início rápido](./media/hdinsight-apache-spark-provision-clusters/quickstart.png)): exibe informações que o ajudarão a começar a usar o HDInsight.

	* **Usuários** (![ícone de usuários](./media/hdinsight-apache-spark-provision-clusters/users.png)): permite definir permissões para o _gerenciamento de portal_ do cluster para outros usuários em sua assinatura do Azure.

		> [AZURE.IMPORTANT]Isso _só_ afeta o acesso e as permissões para o cluster no portal de visualização do Azure e não afeta quem pode se conectar ao cluster HDInsight ou enviar trabalhos a ele.

	* **Marcas** (![ícone de marca](./media/hdinsight-apache-spark-provision-clusters/tags.png)): as marcas permitem definir pares de chave/valor para definir uma taxonomia personalizada dos serviços de nuvem. Por exemplo, você pode criar uma chave chamada __projeto__ e usar um valor comum para todos os serviços associados a um projeto específico.

	* **Painel de cluster**: inicia a folha do painel do cluster a partir do qual você pode iniciar o painel de cluster em si ou iniciar notebooks Zeppelin e Jupyter.


##<a id="powershell"></a> Usando o PowerShell do Azure

O PowerShell do Azure é um ambiente de script poderoso que você pode usar para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Azure. Esta seção fornece instruções sobre como provisionar um cluster HDInsight usando o PowerShell do Azure. Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do Windows PowerShell do HDInsight, consulte [Instalar e configurar o PowerShell do Azure](../install-configure-powershell.md). Para obter mais informações sobre como usar o PowerShell do Azure com o HDInsight, consulte [Administrar o HDInsight usando o PowerShell](hdinsight-administer-use-powershell.md). Para obter a lista de cmdlets do Windows PowerShell do HDInsight, consulte [Referência de cmdlets do HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).


Os procedimentos a seguir são necessários para provisionar um cluster HDInsight usando o PowerShell do Azure:

- Criar um grupo de recursos do Azure
- Criar uma conta de Armazenamento do Azure
- Criar um contêiner de Blob do Azure
- Crie um cluster HDInsight


		# Use the new Azure Resource Manager mode
		Switch-AzureMode AzureResourceManager

		###########################################
		# Create required items, if none exist
		###########################################

		# Sign in
		Add-AzureAccount

		# Select the subscription to use
		$subscriptionName = "<SubscriptionName>"        # Provide your Subscription Name
		Select-AzureSubscription -SubscriptionName $subscriptionName

		# Register your subscription to use HDInsight
		Register-AzureProvider -ProviderNamespace "Microsoft.HDInsight" -Force

		# Create an Azure Resource Group
		$resourceGroupName = "<ResourceGroupName>"      # Provide a Resource Group name
		$location = "<Location>"                        # For example, "West US"
		New-AzureResourceGroup -Name $resourceGroupName -Location $location

		# Create an Azure Storage account
		$storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name
		New-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

		# Create an Azure Blob Storage container
		$containerName = "<ContainerName>"              # Provide a container name
		$storageAccountKey = Get-AzureStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		New-AzureStorageContainer -Name $containerName -Context $destContext

		###########################################
		# Create an HDInsight Cluster
		###########################################

		# Skip these variables if you just created them
		$resourceGroupName = "<ResourceGroupName>"      # Provide the Resource Group name
		$storageAccountName = "<StorageAcccountName>"   # Provide the Storage account name
		$containerName = "<ContainerName>"              # Provide the container name
		$storageAccountKey = Get-AzureStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }

		# Set these variables
		$clusterName = $containerName           		# As a best practice, have the same name for the cluster and container
		$clusterNodes = <ClusterSizeInNodes>    		# The number of nodes in the HDInsight cluster
		$credentials = Get-Credential

		# The location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$location = Get-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName | %{$_.Location}

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $credentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Spark


	![HDI.CLI.Provision](./media/hdinsight-apache-spark-provision-clusters/HDI.ps.provision.png)


## Usando o SDK do .NET do HDInsight baseado em ARM
O SDK do .NET do HDInsight fornece bibliotecas de cliente .NET que facilitam o trabalho com o HDInsight em um aplicativo .NET Framework. Siga as instruções abaixo para criar um aplicativo de console do Visual Studio e cole o código para criar um cluster.

**Para criar um aplicativo de console do Visual Studio**

1. Abra o Visual Studio 2013.

2. No menu **Arquivo**, clique em **Novo** e em **Projeto**.

3. Em **Novo Projeto**, digite ou selecione os seguintes valores:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Propriedade</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Valor</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Categoria</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Modelos/Visual C#/Windows</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Modelo</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Aplicativo de console</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateHDICluster</td></tr>
</table>

4. Clique em **OK** para criar o projeto.

5. No menu **Ferramentas**, clique em **Gerenciador de pacotes NuGet** e, em seguida, clique em **Gerenciar pacotes NuGet para soluções**. Na caixa de texto de pesquisa dentro da caixa de diálogo, procure **HDInsight**. A partir dos resultados que são exibidos, instale:

	 * Microsoft.Azure.Management.HDInsight
	 * Microsoft.Azure.Management.HDInsight.Job

	Pesquise Autenticação do Azure e a partir dos resultados exibidos, instale **Microsoft.Azure.Common.Authentication**.

6. No Solution Explorer, clique duas vezes em **Program.cs** para abri-lo, cole o seguinte código e forneça valores para as variáveis:


        using System;
		using System.Collections.Generic;
		using System.Diagnostics;
		using System.Linq;
		using System.Security;
		using System.Text;
		using System.Threading.Tasks;
		using Hyak.Common;
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Microsoft.Azure.Management.HDInsight.Models;
		using Newtonsoft.Json;


		namespace CreateHDICluster
		{
		    internal class Program
		    {
		        private static ProfileClient _profileClient;
		        private static SubscriptionCloudCredentials _cloudCredentials;
		        private static HDInsightManagementClient _hdiManagementClient;

		        private static Guid SubscriptionId = new Guid("<SubscriptionID>");
		        private const string ResourceGroupName = "<ResourceGroupName>";
		        private const string ExistingStorageName = "<storageaccountname>.blob.core.windows.net";
		        private const string ExistingStorageKey = "<account key>";
		        private const string ExistingContainer = "<container name>";
		        private const string NewClusterName = "<cluster name>";
		        private const int NewClusterNumNodes = <number of nodes>;
		        private const string NewClusterLocation = "<location>";		//should be same as the storage account
		        private const OSType NewClusterOsType = OSType.Windows;
		        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Spark;
		        private const string NewClusterVersion = "3.2";
		        private const string NewClusterUsername = "admin";
		        private const string NewClusterPassword = "<password>";

		        private static void Main(string[] args)
		        {
		            System.Console.WriteLine("Start cluster provisioning");

		            _profileClient = GetProfile();
		            _cloudCredentials = GetCloudCredentials();
		            _hdiManagementClient = new HDInsightManagementClient(_cloudCredentials);

		            System.Console.WriteLine(String.Format("Creating the cluster {0}...", NewClusterName));
		            CreateCluster();
		            System.Console.WriteLine("Done. Press any key to continue.");
		            System.Console.ReadKey(true);
		        }

		        private static void CreateCluster()
		        {
		            var parameters = new ClusterCreateParameters
		            {
		                ClusterSizeInNodes = NewClusterNumNodes,
		                UserName = NewClusterUsername,
		                Password = NewClusterPassword,
		                Location = NewClusterLocation,
		                DefaultStorageAccountName = ExistingStorageName,
		                DefaultStorageAccountKey = ExistingStorageKey,
		                DefaultStorageContainer = ExistingContainer,
		                ClusterType = NewClusterType,
		                OSType = NewClusterOsType
		            };

		            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
		        }

		        private static ProfileClient GetProfile(string username = null, SecureString password = null)
		        {
		            var profileClient = new ProfileClient(new AzureProfile());
		            var env = profileClient.GetEnvironmentOrDefault(EnvironmentName.AzureCloud);
		            var acct = new AzureAccount { Type = AzureAccount.AccountType.User };

		            if (username != null && password != null)
		                acct.Id = username;

		            profileClient.AddAccountAndLoadSubscriptions(acct, env, password);

		            return profileClient;
		        }

		        private static SubscriptionCloudCredentials GetCloudCredentials()
		        {
		            var sub = _profileClient.Profile.Subscriptions.Values.FirstOrDefault(s => s.Id.Equals(SubscriptionId));

		            Debug.Assert(sub != null, "subscription != null");
		            _profileClient.SetSubscriptionAsDefault(sub.Id, sub.Account);

		            return AzureSession.AuthenticationFactory.GetSubscriptionCloudCredentials(_profileClient.Profile.Context);
		        }

		    }
		}

7. Pressione **F5** para executar o aplicativo. Uma janela de console deve ser aberta e exibir o status do aplicativo. Você também será solicitado a inserir suas credenciais de conta do Azure. Pode levar vários minutos para criar um cluster HDInsight.


##<a id="nextsteps"></a> Próximas etapas

* Consulte [Executar análises de dados interativas usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md) para aprender a usar o Apache Spark no HDInsight com ferramentas de BI como Power BI e Tableau.
* Consulte [Usar o Spark no HDInsight para a criação de aplicativos de aprendizado de máquina](hdinsight-apache-spark-ipython-notebook-machine-learning.md) para aprender a criar aplicativos de aprendizado de máquina usando o Apache Spark no HDInsight.
* Consulte [Usar o Spark no HDInsight para a criação de aplicativos de streaming em tempo real](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md) para aprender a criar aplicativos de streaming usando o Apache Spark no HDInsight.
* Consulte [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md) para aprender a usar o Gerenciador de Recursos para gerenciar os recursos alocados para o cluster do Spark.


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




[89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Use o Sqoop com o HDInsight"

<!---HONumber=August15_HO9-->