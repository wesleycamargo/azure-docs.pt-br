<properties
   	pageTitle="Provisionar clusters Hadoop em Linux no HDInsight | Microsoft Azure"
	description="Saiba como configurar clusters de Hadoop no Linux para o HDInsight usando o portal de gerenciamento, a linha de comando e o SDK do .NET."
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
	ms.date="08/07/2015"
	ms.author="nitinme"/>


#Provisionar cluster Hadoop Linux no HDInsight usando opções personalizadas (Preview)

Neste artigo, você aprenderá as diferentes maneiras de provisionar, de forma personalizada, um cluster Hadoop em Linux no Azure HDInsight, usando o portal do Azure, o PowerShell do Azure, a CLI do Azure ou o SDK do .NET do HDInsight.

## O que é um cluster HDInsight?

Você já se perguntou por que mencionamos clusters sempre que falamos de Hadoop ou Big Data? O motivo é que o Hadoop permite o processamento distribuído de grandes volumes de dados, espalhados em diferentes nós de um cluster. O cluster tem uma arquitetura mestre/de trabalho com um nó principal (também chamado de nó de cabeçalho ou nó de nome) e certo número de nós de trabalho (também chamados de nós de dados). Para obter mais informações, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>.

![Cluster do HDInsight][img-hdi-cluster]


Um cluster HDInsight resume os detalhes de implementação do Hadoop para que você não precise se preocupar com a forma de comunicação com diferentes nós de um cluster. Ao provisionar um cluster HDInsight, você provisiona recursos de computação do Azure que contêm o Hadoop e aplicativos relacionados. Para obter mais informações, consulte [Introdução ao Hadoop no HDInsight](hdinsight-hadoop-introduction.md). Os dados que sofrerem variação serão armazenados no armazenamento de Blob do Azure. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight](../hdinsight-use-blob-storage.md).


Este artigo fornece instruções sobre diferentes maneiras de provisionar um cluster. Se estiver procurando uma abordagem de início rápido para provisionar um cluster, consulte [Introdução ao Azure HDInsight no Linux](../hdinsight-hadoop-linux-get-started.md).

**Pré-requisitos**

Antes de começar este artigo, você deve ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Chaves SSH (Secure Shell)**. Se você quiser acesso remoto em um cluster do Linux usando o SSH com uma chave, em vez de uma senha. Usar uma chave é o método recomendado, pois é mais seguro. Para obter instruções sobre como gerar chaves SSH, consulte os seguintes artigos:
	-  Em um computador Linux: [Usar SSH com o HDInsight baseado em Linux (Hadoop) por meio de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
	-  Em um computador Windows: [Usar SSH com o HDInsight baseado em Linux (Hadoop) no Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

## <a id="configuration"></a>Opções de configuração

### Clusters no Linux

O HDInsight fornece a opção de provisionamento de clusters do Linux no Azure. Provisione um cluster Linux se você estiver familiarizado com Linux ou Unix, se estiver migrando de uma solução Hadoop baseada em Linux existente ou se quiser integração fácil com componentes do ecossistema Hadoop criados para Linux. Para obter mais informações sobre o Azure HDInsight no Linux, consulte [Introdução ao Hadoop no HDInsight](hdinsight-hadoop-introduction.md).


### Armazenamento adicional

Durante a configuração, você deve especificar uma conta de Armazenamento do Blob do Azure e um contêiner padrão. Eles são usados como o local de armazenamento padrão pelo cluster. Opcionalmente, você pode especificar blobs adicionais que também serão associados ao cluster.


Para obter mais informações sobre o uso de repositórios de blob secundários, consulte [Usando o armazenamento de Blob do Azure com o HDInsight](../hdinsight-use-blob-storage.md).


### Metastore

O metastore contém informações sobre tabelas Hive, partições, esquemas, colunas, etc. Essas informações são usadas pelo Hive para localizar onde os dados estão armazenados no HDFS (Sistema de Arquivos Distribuído do Hadoop) ou no armazenamento de Blob do Azure para HDInsight. Por padrão, o Hive usa um banco de dados integrado para armazenar essas informações.

Ao provisionar um cluster HDInsight, você pode especificar um banco de dados SQL para conter o metastore para Hive. Isso permite que as informações de metadados sejam preservadas quando você excluir um cluster, já que elas são armazenadas externamente no banco de dados SQL. Para obter instruções sobre como criar banco de dados SQL do Azure, consulte [Criar seu primeiro banco de dados SQL do Azure](sql-database-get-started.md).


## <a id="options"></a> Opções para provisionar um cluster HDInsight em Linux

Você pode provisionar um cluster HDInsight Hadoop em Linux tanto em um computador Linux, quanto em um computador baseado em Windows. A tabela a seguir fornece informações sobre as opções de provisionamento disponíveis dos diferentes sistemas operacionais e links para instruções de cada um.

Provisionar clusters Linux de um computador que executa este sistema operacional| Usando o portal do Azure | Usando o PowerShell do Azure | Usando o .NET SDK
-----------------| ------------------------| -------------------| ---------- | ---------
Linux| Clique [aqui](#portal) | Não aplicável | Não aplicável
Windows | Clique [aqui](#portal) | Clique [aqui](#powershell) | Clique [aqui](#sdk)

### <a id="portal"></a> Usando o portal do Azure

Clusters HDInsight usam um contêiner de Armazenamento de Blob do Azure como o sistema de arquivos padrão. Para poder criar um cluster HDInsight, você precisa de uma conta do Armazenamento do Azure localizada no mesmo datacenter. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight](../hdinsight-use-blob-storage.md). Para obter detalhes sobre como criar uma conta do Armazenamento do Azure, consulte [Como criar uma conta de armazenamento](../storage-create-storage-account.md).


> [AZURE.NOTE]Atualmente, apenas as regiões **Sudeste da Ásia**, **Norte da Europa**, **Leste dos EUA** e **Centro-Sul dos EUA** podem hospedar clusters HDInsight em Linux.

**Para criar um cluster HDInsight**

1. Entre no [Portal de Visualização do Azure](https://portal.azure.com).
2. Clique em **NOVO**, clique em **Análises de dados**, e, em seguida, clique em **HDInsight**.

    ![Criar um novo cluster no Portal de Visualização do Azure](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.1.png "Criar um novo cluster no Portal de Visualização do Azure")

3. Insira um **Nome de Cluster**, selecione **Hadoop** para o **Tipo de cluster** e na lista suspensa **Sistema operacional do cluster**, selecione **Ubuntu**. Uma marca de seleção verde aparecerá ao lado do nome do cluster, se disponível.

	![Digite o tipo e o nome do cluster](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.2.png "Digite o tipo e o nome do cluster")

4. Se você tiver mais de uma assinatura, clique na entrada **Assinatura** para selecionar a assinatura do Azure que será usada para o cluster.

5. Clique em **Grupo de recursos** para ver uma lista de grupos de recursos existente e, em seguida, selecione um onde será criado o cluster. Ou, você pode clicar em **Criar novo** e, em seguida, digitar o nome do novo grupo de recursos. Uma marca de seleção verde será exibida para indicar se o novo nome de grupo estiver disponível.

	> [AZURE.NOTE]A entrada padrão será um dos seus grupos de recursos existentes, se houver algum disponível.

6. Clique em **Credenciais** e, em seguida, digite uma senha para o usuário admin. Você também deve digitar um **Nome de usuário de SSH** e uma **SENHA** ou uma **CHAVE PÚBLICA**, que será usada para autenticar o usuário SSH. Usar uma chave pública é a abordagem recomendada. Clique em **Selecionar** na parte inferior para salvar a configuração de credenciais.

	![Forneça credenciais de cluster](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.3.png "Forneça credenciais de cluster")

	Para obter mais informações sobre como usar SSH com o HDInsight, consulte um dos seguintes artigos:

	* [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


7. Clique em **Fonte de dados** para escolher uma fonte de dados existente para o cluster ou criar um novo.

	![Folha de fonte de dados](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.4.png "Forneça a configuração da fonte de dados")

	No momento, você pode selecionar uma conta de armazenamento do Azure como a fonte de dados para um cluster HDInsight. Use o item a seguir para entender as entradas na folha **Fonte de Dados**.

	- **Método de seleção**: defina essa opção para **De todas as assinaturas** para habilitar a procura de contas de armazenamento de todas as suas assinaturas. Defina essa opção para **Tecla de Acesso** se você deseja inserir o **Nome de armazenamento** e **Tecla de Acesso** de uma conta de armazenamento existente.

	- **Selecionar conta de armazenamento / Criar nova**: clique em **Selecionar conta de armazenamento** para procurar e selecionar uma conta de armazenamento existente que você deseja associar com o cluster. Ou, clique em **Criar nova** para criar uma nova conta de armazenamento. Use o campo exibido para inserir o nome da conta de armazenamento. Uma marca de seleção verde será exibida se o nome estiver disponível.

	- **Escolher contêiner padrão**: use essa opção para inserir o nome do contêiner padrão a ser usado para o cluster. Embora você possa inserir qualquer nome aqui, é recomendável usar o mesmo nome que o cluster para que você possa reconhecer facilmente que o contêiner é usado para este cluster específico.

	- **Local**: a região geográfica na qual a conta de armazenamento está ou será criada.

		> [AZURE.IMPORTANT]Selecionar o local para a fonte de dados padrão também definirá o local do cluster do HDInsight. A fonte de dados padrão e o cluster devem estar localizados na mesma região.

	Clique em **Selecionar** para salvar a configuração de fonte de dados.

8. Clique em **Camadas de preços do nó** para exibir informações sobre os nós que serão criados para esse cluster. Defina o número de nós de trabalho que você precisa para o cluster. O custo estimado do cluster será mostrado na folha.

	![Folha de camadas de preços de nó](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.5.png "Especifique o número de nós de cluster")

	Clique em **Selecionar** para salvar a configuração de preço do nó.

9. Clique em **Configuração opcional** para selecionar a versão do cluster, bem como definir outras configurações opcionais, como adicionar uma **Rede Virtual**, definir a um **Metastore Externo** para armazenar dados de Hive e Oozie, usar as ações de Script para personalizar um cluster para instalar componentes personalizados ou usar contas de armazenamento adicionais com o cluster.

	* Clique na lista suspensa **Versão do HDInsight** e selecione a versão que você deseja usar para o cluster. Para obter mais informações, consulte [Versões de cluster do HDInsight](hdinsight-component-versioning.md).

	* Clique em **Metastores Externo** para especificar o banco de dados SQL que você deseja usar para salvar os metadados de Hive e Oozie associados com o cluster.

		![Personalize a folha de metastores](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.7.png "Especifique os metastores externos")

		Para **Usar um banco de dados SQL existente para metadados de Hive**, clique em **Sim**, selecione um banco de dados SQL e, em seguida, forneça o nome de usuário e senha para o banco de dados. Repita essas etapas se você quiser **Usar um banco de dados SQL existente para metadados de Oozie**. Clique em **Selecionar** até voltar para a folha **Configuração opcional**.

		>[AZURE.NOTE]O banco de dados SQL do Azure usado para o metastore deve permitir a conectividade com outros serviços do Azure, incluindo o Azure HDInsight. No painel do banco de dados SQL do Azure, no lado direito, clique no nome do servidor. Esse é o servidor no qual a instância do banco de dados SQL está sendo executada. Quando você estiver na exibição do servidor, clique em **Configurar** e depois em **Serviços do Azure**; clique em **Sim** e depois em **Salvar**.

	* Clique em **Chaves de armazenamento do Azure** para especificar contas de armazenamento adicionais para associar com o cluster. Na folha **Chaves de armazenamento do Azure**, clique em **Adicionar uma chave de armazenamento**, e, em seguida, selecione uma conta de armazenamento existente ou crie uma nova conta.

		![Folha de armazenamento adicional](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.9.png "Especifique as contas de armazenamento adicionais")

		Clique em **Selecionar** até voltar para a folha **Novo cluster HDInsight**.

10. Na folha **Novo cluster HDInsight**, certifique-se de que **Fixar no quadro inicial** está selecionado e, em seguida, clique em **Criar**. Isso criará o cluster e adicionará um bloco para o mesmo para o quadro inicial do seu Portal do Azure. O ícone indica que o cluster está provisionando e será alterado para exibir o ícone de HDInsight após a conclusão da configuração.

	| Durante o provisionamento | Provisionamento concluído |
	| ------------------ | --------------------- |
	| ![Indicador de provisionamento no quadro inicial](./media/hdinsight-hadoop-provision-linux-clusters/provisioning.png) | ![Bloco de cluster provisionado](./media/hdinsight-hadoop-provision-linux-clusters/provisioned.png) |

	> [AZURE.NOTE]Levará algum tempo para o cluster ser criado, geralmente cerca de 15 minutos. Use o bloco no Quadro Inicial ou a entrada **Notificações** à esquerda da página para verificar o processo de provisionamento.

11. Quando o provisionamento for concluído, clique no bloco para o cluster a partir do quadro inicial para iniciar a folha de cluster. A folha de cluster fornece informações essenciais sobre o cluster como o nome, o grupo de recursos ao qual ele pertence, o local, o sistema operacional, a URL para o painel do cluster, etc.

	![Folha de cluster](./media/hdinsight-hadoop-provision-linux-clusters/HDI.Cluster.Blade.png "Propriedades do cluster")

	Use o seguinte para entender os ícones na parte superior dessa folha e na seção **Essentials**:

	* **Configurações** e **Todas as Configurações**: exibem a folha **Configurações** do cluster, o que permite acessar informações de configuração detalhadas do cluster.

	* **Painel**, **Painel do cluster** e **URL**: essas são todas as maneiras de acessar o painel de cluster, que é um portal da Web para executar trabalhos no cluster.

	* **Secure Shell**: informações necessárias para acessar o cluster usando SSH.

	* **Excluir**: exclui o cluster HDInsight.

	* **Início Rápido** (![ícone de nuvem e raio = início rápido](./media/hdinsight-hadoop-provision-linux-clusters/quickstart.png)): exibe informações que o ajudarão a começar a usar o HDInsight.

	* **Usuários** (![ícone de usuários](./media/hdinsight-hadoop-provision-linux-clusters/users.png)): permite definir permissões para o _gerenciamento de portal_ do cluster para outros usuários em sua assinatura do Azure.

		> [AZURE.IMPORTANT]Isso _só_ afeta o acesso e as permissões para o cluster no portal de visualização do Azure e não afeta quem pode se conectar ao cluster HDInsight ou enviar trabalhos a ele.

	* **Marcas** (![ícone de marca](./media/hdinsight-hadoop-provision-linux-clusters/tags.png)): as marcas permitem definir pares de chave/valor para definir uma taxonomia personalizada dos serviços de nuvem. Por exemplo, você pode criar uma chave chamada __projeto__ e usar um valor comum para todos os serviços associados a um projeto específico.



### <a id="powershell"></a> Usando o PowerShell do Azure
O PowerShell do Azure é um ambiente de script poderoso que você pode usar para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Azure. Esta seção fornece instruções sobre como provisionar um cluster HDInsight usando o PowerShell do Azure. Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do Windows PowerShell do HDInsight, consulte [Instalar e configurar o PowerShell do Azure](../install-configure-powershell.md). Para obter mais informações sobre como usar o PowerShell do Azure com o HDInsight, consulte [Administrar o HDInsight usando o PowerShell](hdinsight-administer-use-powershell.md). Para obter a lista de cmdlets do Windows PowerShell do HDInsight, consulte [Referência de cmdlets do HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).


Os procedimentos a seguir são necessários para provisionar um cluster HDInsight usando o PowerShell do Azure:

- Criar um grupo de recursos do Azure
- Criar uma conta de Armazenamento do Azure
- Criar um contêiner de Blob do Azure
- Crie um cluster HDInsight

Os dois parâmetros mais importantes que devem ser definidos para provisionar que clusters Linux são aquelas em que você especifica o tipo de sistema operacional e os detalhes do usuário SSH:

- Garanta que você especificou o parâmetro **-OSType** como **Linux**.
- Para usar SSH para sessões remotas nos clusters, você pode especificar a senha do usuário SSH ou a chave pública SSH. Se você especificar tanto a senha do usuário SSH, quanto a chave pública SSH, a chave será ignorada. Se quiser usar a chave SSH para sessões remotas, você deverá especificar uma senha SSH em branco quando for solicitada. Para obter mais informações sobre como usar SSH com o HDInsight, consulte um dos seguintes artigos:

	* [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


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
			$sshCredentials = Get-Credential

			# The location of the HDInsight cluster. It must be in the same data center as the Storage account.
			$location = Get-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName | %{$_.Location}

			# Create a new HDInsight cluster
			New-AzureHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $credentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -OSType Linux -Version "3.2" -SshCredential $sshCredentials


>[AZURE.NOTE]Os valores especificados para **$clusterCredentials** são usados para criar a conta de usuário do Hadoop para o cluster. Você usará essa conta para se conectar ao cluster. Os valores especificados para **$sshCredentials** são usados para criar o usuário SSH para o cluster. Você pode usar essa conta para iniciar uma sessão SSH remota no cluster e executar trabalhos. Se você usar a opção Criação Rápida no portal do Azure para provisionar um cluster, o nome de usuário do Hadoop padrão será "admin" e o nome de usuário SSH padrão será "hdiuser".

Pode levar vários minutos para que o provisionamento do cluster seja concluído.

![HDI.CLI.Provision][image-hdi-ps-provision]

###<a id="sdk"></a> Usando o SDK do .NET do HDInsight
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
		        private const OSType NewClusterOsType = OSType.Linux;
		        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
		        private const string NewClusterVersion = "3.2";
		        private const string NewClusterUsername = "admin";
		        private const string NewClusterPassword = "<password>";
				private const string NewClusterSshUserName = "sshuser";
        		private const string NewClusterSshPublicKey = "<ssh public key>";

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
		                OSType = NewClusterOsType,
						SshUserName = NewClusterSshUserName,
                		SshPublicKey = NewClusterSshPublicKey
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
Neste artigo, você aprendeu várias maneiras de provisionar um cluster HDInsight Hadoop no Linux. Para saber mais, consulte os seguintes artigos:

- [Trabalhando com o HDInsight no Linux](hdinsight-hadoop-linux-information.md): conheça as nuances de trabalhar com um cluster HDInsight no Linux.
- [Gerenciar clusters HDInsight usando o Ambari](hdinsight-hadoop-manage-ambari.md): aprenda a monitorar e gerenciar seu Hadoop baseado em Linux no cluster HDInsight usando Web do Ambari ou a API REST do Ambari.
- [Usar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md): saiba mais sobre as diferentes maneiras de executar trabalhos do MapReduce em um cluster.
- [Usar o Hive com o HDInsight](hdinsight-use-hive.md): saiba mais sobre as diferentes maneiras de executar uma consulta Hive em um cluster.
- [Usar o Pig com o HDInsight](hdinsight-use-pig.md): saiba mais sobre as diferentes maneiras de executar um trabalho do Pig em um cluster.
- [Usar o armazenamento de Blob do Azure com o HDInsight](../hdinsight-use-blob-storage.md): saiba como o HDInsight usa o armazenamento de Blob do Azure para armazenar dados de clusters HDInsight.
- [Carregar dados no HDInsight](hdinsight-upload-data.md): aprenda a trabalhar com dados armazenados em um armazenamento de Blob do Azure para um cluster HDInsight.


[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx


[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/
[azure-create-storageaccount]: ../storage-create-storage-account/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png




[image-cli-account-download-import]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIListClusters.png "Listar e mostrar clusters"

[image-hdi-ps-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.provision.png
[image-hdi-ps-config-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.config.provision.png

[img-hdi-cluster]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.Cluster.png

  [89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Use o Sqoop com o HDInsight"

<!---HONumber=August15_HO8-->