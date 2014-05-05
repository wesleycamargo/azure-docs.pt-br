<properties linkid="manage-services-hdinsight-provision-hdinsight-clusters" urlDisplayName="Administração do HDInsight" pageTitle="Provisionar cluster HDInsight | Azure" metaKeywords="hdinsight, administração do hdinsight, azure administração do hdinsight" description="Saiba como provisionar clusters para o Azure HDInsight usando o Portal de Gerenciamento, o PowerShell ou a linha de comando." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Provisionar clusters HDInsight" authors="jgao" />

#Provisionar clusters HDInsight

Neste artigo, você aprenderá diferentes maneiras de provisionar um cluster HDInsight.

**Pré-requisitos:**

Antes de começar este artigo, você deve ter o seguinte:

- Uma assinatura do Azure. O Azure é uma plataforma baseada em assinatura. Os cmdlets do PowerShell do HDInsight executam as tarefas com sua assinatura. Para obter mais informações sobre como adquirir uma assinatura, consulte [Opções de compra][azure-purchase-options], [Ofertas para membro][azure-member-offers] ou [Avaliação gratuita][azure-free-trial].

##Neste artigo

* [Usando o Portal de Gerenciamento do Azure](#portal)
* [Usando o PowerShell do Azure](#powershell)
* [Usando linha de comando entre plataformas](#cli)
* [Usando o SDK do .NET do HDInsight](#sdk)
* [Próximas etapas](#nextsteps)

##<a id="portal"></a> Usando o Portal de Gerenciamento do Azure

O cluster HDInsight usa um contêiner de Armazenamento de Blob do Azure como o sistema de arquivos padrão. Uma conta de Armazenamento do Azure localizada no mesmo data center é necessária para poder criar um cluster HDInsight. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][hdinsight-storage]. Para obter detalhes sobre como criar uma nova conta de Armazenamento do Azure, consulte [Como criar uma conta de armazenamento][azure-create-storageaccount].


> [WACOM.NOTE] Atualmente, apenas as regiões Sudeste da Ásia, Norte da Europa, Europa Ocidental, Leste dos Estados Unidos e Oeste dos Estados Unidos podem hospedar clusters HDInsight.

Esta sessão descreve o procedimento de criação de um cluster HDInsight usando a opção de criação personalizada.  Para obter informações sobre como usar a opção de criação rápida, consulte [Introdução ao Azure HDInsight][hdinsight-getting-started].


**Para criar um cluster HDInsight usando a opção de criação personalizada**

1. Entre no [Portal de Gerenciamento do Azure][azure-management-portal].
2. Clique em **+ NOVO** na parte inferior da página, clique em **SERVIÇOS DE DADOS**, clique em **HDINSIGHT** e, em seguida, clique em **CRIAÇÃO PERSONALIZADA**.
3. Na página Detalhes do Cluster, digite ou escolha os valores a seguir:

	<table border='1'>
		<tr><th>Propriedade</th><th>Valor</th></tr>
		<tr><td>NOME DO CLUSTER</td>
			<td><p>Nomeie o cluster. </p>
				<ul>
				<li>O nome DNS deve iniciar e terminar com alfa numéricos e pode conter traços.</li>
				<li>O campo deve ser uma cadeia de caracteres com 3 a 63 caracteres.</li>
				</ul></td></tr>
		<tr><td>NÓS DE DADOS</td>
			<td>Especifique o número de nós no cluster. O valor padrão é quatro.</td></tr>
		<tr><td>VERSÃO DO HDINSIGHT</td>
			<td>Escolha a versão. O padrão é 2.0 executando clusters Hadoop 1.2.  A versão 3.0 usa clusters Hadoop 2.2. Para obter mais informações, consulte <a href="http://www.windowsazure.com/pt-br/manage/services/hdinsight/versioning-in-hdinsight/">Qual versão do Hadoop está no Azure HDInsight? (a página pode estar em inglês)</a>.</td></tr>
		<tr><td>REGIÃO</td>
			<td>Especifique o data center onde o cluster está instalado. O local deve ser o mesmo que o do Armazenamento de Blob do Azure que será usado como o sistema de arquivos padrão. Atualmente, você só pode escolher *Sudeste da Ásia*, *Norte da Europa*, *Europa Ocidental*, *Leste dos EUA* ou *Oeste dos EUA*.</td>
		</tr>
	</table>

	![HDI.CustomProvision.Page1][image-customprovision-page1]

4. Clique na seta para a direita no canto inferior direito da página.
5. Na página Configurar Usuários de Cluster, digite ou escolha o seguinte valor:

	<table border='1'>
		<tr><th>Propriedade</th><th>Valor</th></tr>
		<tr><td>NOME DE USUÁRIO</td>
			<td>Especifique o nome do usuário do cluster HDInsight.</td></tr>
		<tr><td><p>SENHA</p><p>CONFIRMAR SENHA</p></td>
			<td>Especifique a senha do usuário do cluster HDInsight.</td></tr>
		<tr><td>Inserir Hive/Oozie Metastore</td>
			<td>Especifique um Banco de Dados SQL no mesmo data center a ser usado como o metastore do Hive/Oozie.</td></tr>
		<tr><td>HIVE META/BANCO DE DADOS OOZIESTORE</td>
			<td>Especifique o Banco de Dados SQL Azure que será usado como o metastore para Hive/OOzie. Esse Banco de Dados SQL deve estar no mesmo data center que o cluster HDInsight. A caixa de listagem lista apenas os bancos de dados SQL no mesmo data center conforme sua especificação na página Detalhes do Cluster.</td></tr>
		<tr><td>USUÁRIO DO BANCO DE DADOS</td>
			<td>Especifique o usuário do Banco de Dados SQL que será usado para a conexão ao banco de dados.</td></tr>
		<tr><td>SENHA DO USUÁRIO DO BANCO DE DADOS</td>
			<td>Especifique a senha do usuário do Banco de Dados SQL.</td></tr>
	</table>

	Para o cluster HDInsight versão 2.0, as credenciais fornecidas aqui só podem acessar os serviços no cluster. A Área de Trabalho Remota pode ser ativada após a criação do cluster.

	![HDI.CustomProvision.Page2][image-customprovision-page2]


6. Clique na seta para a direita no canto inferior direito da página.
7. Na página Conta de Armazenamento, digite ou escolha o seguinte valor:

	<table border='1'>
		<tr><th>Propriedade</th><th>Valor</th></tr>
		<tr><td>CONTA DE ARMAZENAMENTO</td>
			<td>Especifique a conta de Armazenamento do Azure que será usada como o sistema de arquivos padrão para o cluster HDInsight. Você pode escolher uma destas três opções:
			<ul>
				<li>Usar Armazenamento Existente</li>
				<li>Criar Novo Armazenamento</li>
				<li>Usar Armazenamento de Outra Assinatura</li>
			</ul>
			</td></tr>
		<tr><td>NOME DA CONTA</td>
			<td>Quando a opção <b>Usar Armazenamento Existente</b> estiver selecionada, a caixa de listagem lista apenas as contas de armazenamento localizadas no mesmo data center.</td></tr>
		<tr><td>CHAVE DA CONTA</td>
			<td>Esse campo só estará disponível quando <strong>Usar Armazenamento de Outra Assinatura</strong> estiver selecionado no campo CONTA DE ARMAZENAMENTO.</td></tr>	
		<tr><td>CONTÊINER PADRÃO</td>
			<td>O contêiner padrão da conta de armazenamento será usado como o sistema de arquivos padrão para o cluster HDInsight. Quando <strong>Usar Armazenamento Existente</strong> no campo CONTA DE ARMAZENAMENTO e <strong>Criar contêiner padrão</strong> no campo CONTÊINER PADRÃO estiverem selecionados, o nome do contêiner padrão terá o mesmo nome do cluster. Se um contêiner com o nome do cluster já existir, será acrescentado um número de sequência ao nome do contêiner. Por exemplo, mycontainer1, mycontainer2 e assim por diante.</td></tr>
		<tr><td>CONTAS DE ARMAZENAMENTO ADICIONAIS</td>
			<td>O HDInsight oferece suporte a várias contas de armazenamento. Não há nenhum limite de contas de armazenamento adicional que podem ser usadas por um cluster. No entanto, se você criar um cluster usando o Portal de Gerenciamento, você encontrará um limite de sete, devido às restrições da interface do usuário. Cada conta de armazenamento adicional que você especificar nesse campo adiciona uma página extra de Conta de Armazenamento onde você pode especificar as informações da conta. Por exemplo, na captura de tela a seguir, uma conta de armazenamento adicional está selecionada, a página quatro é adicionada à caixa de diálogo.</td></tr>		
	</table>

	![HDI.CustomProvision.Page3][image-customprovision-page3]

8. Clique na seta para a direita no canto inferior direito da página.
9. Na página Conta de armazenamento, digite as informações da conta de armazenamento adicional:

	![HDI.CustomProvision.Page4][image-customprovision-page4]

10. Clique no botão Concluído (o ícone de seleção) no canto inferior direito para iniciar o processo de provisionamento do cluster HDInsight.

Pode levar vários minutos para configurar um cluster.  Quando o processo de provisionamento for concluído com êxito, a coluna de status do cluster mostrará **Executando**.

> [WACOM.NOTE] Depois de escolher uma conta de armazenamento do Azure para o cluster HDInsight, você não pode excluir a conta, nem alterá-la para uma conta diferente.













































##<a id="powershell"></a> Usando o PowerShell do Azure
O PowerShell do Azure é um ambiente de script poderoso que você pode usar para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Azure. Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do PowerShell do HDInsight, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure]. Para obter mais informações sobre como usar o PowerShell com o HDInsight, consulte [Administrar o HDInsight usando o PowerShell][hdinsight-admin-powershell]. Para obter a lista de cmdlets do PowerShell do Azure, consulte [Referência a cmdlets do HDInsight][hdinsight-powershell-reference].

Os procedimentos a seguir são necessários para provisionar um cluster HDInsight usando o PowerShell:

- Criar uma conta de Armazenamento do Azure
- Criar um contêiner de blob do Azure
- Criar um cluster HDInsight

O HDInsight usa um contêiner de Armazenamento de Blob do Azure como o sistema de arquivos padrão. Uma conta de Armazenamento do Azure e o contêiner de armazenamento são necessários para poder criar um cluster HDInsight. A conta de armazenamento deve estar localizada no mesmo datacenter que o cluster HDInsight.


**Para criar uma conta de Armazenamento do Azure**

- Execute os seguintes comandos em uma janela de console do PowerShell do Azure:

		$storageAccountName = "<StorageAcccountName>"
		$location = "<MicrosoftDataCenter>"		# For example, "West US"
		
		# Create an Azure storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location
	
	Se já tiver uma conta de armazenamento, mas não souber o nome e a chave da conta, você poderá usar os seguintes comandos do PowerShell para recuperar as informações:
	
		# List storage accounts for the current subscription
		Get-AzureStorageAccount

		# List the keys for a storage account
		Get-AzureStorageKey "<StorageAccountName>"
	
**Para criar o contêiner de armazenamento do Azure**

- Execute os seguintes comandos em uma janela do PowerShell do Azure:

		$storageAccountName = "<StorageAccountName>"
		$storageAccountKey = "<StorageAccountKey>"
		$containerName="<ContainerName>"

		# Create a storage context object
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName 
		                                       -StorageAccountKey $storageAccountKey  
		 
		# Create a Blob storage container
		New-AzureStorageContainer -Name $containerName -Context $destContext

Depois de preparar a conta de armazenamento e o contêiner de blob, você estará pronto para criar um cluster. 

**Para provisionar um cluster HDInsight**

- Execute os seguintes comandos em uma janela do PowerShell do Azure:		

		$subscriptionName = "<SubscriptionName>"		# The name of the Azure subscription.
		$storageAccountName = "<StorageAccountName>"	# The Azure storage account that hosts the default container. The default container will be used as the default file system.
		$containerName = "<ContainerName>"				# The Azure Blob storage container that will be used as the default file system for the HDInsight cluster.

		$clusterName = "<HDInsightClusterName>"			# The name you will name your HDInsight cluster.
		$location = "<MicrosoftDataCenter>"				# The location of the HDInsight cluster. It must in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNodes>			# The number of nodes in the HDInsight cluster.

		# Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

	Pode levar vários minutos para que a provisão do cluster seja concluída.

	![HDI.CLI.Provision][image-hdi-ps-provision]

Você também pode provisionar o cluster e configurá-lo para conectar-se a mais de um Armazenamento de Blob do Azure ou metastores de Hive e Oozie personalizados. Esse recurso avançado permite separar o tempo de vida de seus dados e metadados do tempo de vida do cluster. 

**Para provisionar um cluster HDInsight usando a configuração**

- Execute os seguintes comandos em uma janela do PowerShell do Azure:

		$subscriptionName = "<SubscriptionName>"
		$clusterName = "<ClusterName>"
		$location = "<MicrosoftDataCenter>"
		$clusterNodes = <ClusterSizeInNodes>
		
		$storageAccountName_Default = "<DefaultFileSystemStorageAccountName>"
		$containerName_Default = "<DefaultFileSystemContainerName>"
		
		$storageAccountName_Add1 = "<AdditionalStorageAccountName>"
		
		$hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
		$hiveSQLDatabaseName = "<SQLDatabaseDatabaseNameForHiveMetastore>"
		$oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>"
		$oozieSQLDatabaseName = "<SQLDatabaseDatabaseNameForOozieMetastore>"
		
		# Get the storage account keys
		Select-AzureSubscription $subscriptionName
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }
		
		$oozieCreds = Get-Credential -Message "Oozie metastore"
		$hiveCreds = Get-Credential -Message "Hive metastore"
		
		# Create a Blob storage container
		#$dest1Context = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default  
		#New-AzureStorageContainer -Name $containerName_Default -Context $dest1Context
		
		# Create a new HDInsight cluster
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
		    Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
		    Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 |
		    Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore |
		    Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore |
		        New-AzureHDInsightCluster -Name $clusterName -Location $location

**Para listar os clusters HDInsight**

- Execute os seguintes comandos em uma janela do PowerShell do Azure:

		Get-AzureHDInsightCluster -Name <ClusterName>











































































































##<a id="cli"></a> Usando linha de comando entre plataformas

Outra opção para o provisionamento de um cluster HDInsight é a interface de linha de comando entre plataformas. A ferramenta de linha de comando é implementada no Node.js. Ela pode ser usada em qualquer plataforma que ofereça suporte ao Node.js, incluindo Windows, Mac e Linux. A ferramenta de linha de comando é software livre.  O código fonte é gerenciado no GitHub em <a href= "https://github.com/WindowsAzure/azure-sdk-tools-xplat">https://github.com/WindowsAzure/azure-sdk-tools-xplat</a>. Para obter um guia geral de como usar a interface de linha de comando, consulte [Como usar as Ferramentas de Linha de Comando do Azure para Mac e Linux][azure-command-line-tools]. Para obter documentação de referência abrangente, consulte [Ferramenta de Linha de Comando do Azure para Mac e Linux][azure-command-line-tool]. Este artigo aborda apenas o uso da interface de linha de comando do Windows.


Os procedimentos a seguir são necessários para provisionar um cluster HDInsight usando a linha de comando entre plataformas:

- Instalar linha de comando entre plataformas
- Baixar e importar as configurações de publicação da conta do Azure
- Criar uma conta de Armazenamento do Azure
- Provisionar um cluster

A interface de linha de comando pode ser instalada usando o *NPM (Gerenciador de Pacotes do Node.js)* ou o Windows Installer.

**Para instalar a interface de linha de comando usando o NPM**

1.	Procure o **www.nodejs.org**.
2.	Clique em **INSTALAR** e siga as instruções usando as configurações padrão.
3.	Abra o **Prompt de Comando** (ou o *Prompt de Comando do Azure* ou o *Prompt de Comando do Desenvolvedor para VS2012*) em sua estação de trabalho.
4.	Execute o seguinte comando na janela de prompt de comando.

		npm install -g azure-cli

	> [WACOM.NOTE] Se você receber um erro informando que o comando NPM não foi encontrado, verifique se os caminhos a seguir estão na variável de ambiente PATH: <i>C:\Arquivos de Programas (x86) \nodejs;C:\Users\[username]\AppData\Roaming\npm</i> ou <i>C:\Arquivos de Programas\nodejs;C:\Users\[username]\AppData\Roaming\npm</i>
	


5.	Execute o comando a seguir para verificar a instalação:

		azure hdinsight -h

	Você pode usar a opção *-h* em diferentes níveis para exibir as informações da Ajuda.  Por exemplo:
		
		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Para instalar a interface de linha de comando usando o Windows Installer**

1.	Navegue para **http://www.windowsazure.com/pt-br/downloads/**.
2.	Role para baixo até a seção **Ferramentas de linha de comando** e, em seguida, clique em **Interface de Linha de Comando entre Plataformas** e siga o assistente do Web Platform Installer.

Antes de usar a interface de linha de comando, você deve configurar a conectividade entre a estação de trabalho e o Azure. As informações da assinatura do Azure são usadas pela interface de linha de comando para se conectar à sua conta. Essas informações podem ser obtidas no Azure em um arquivo de configurações de publicação. O arquivo de configurações de publicação pode ser importado como uma definição de configuração local persistente que a interface de linha de comando irá usar para operações subsequentes. Você precisa importar as configurações de publicação apenas uma vez.


> [WACOM.NOTE] O arquivo de configurações de publicação contém informações confidenciais. É recomendável excluir o arquivo ou executar etapas adicionais para criptografar a pasta de usuário que contém o arquivo. No Windows, modifique as propriedades da pasta ou use o BitLocker.


**Para baixar e importar as configurações de publicação**

1.	Abra um **Prompt de comando**.
2.	Execute o comando a seguir para baixar o arquivo de configurações da publicação.

		azure account download
 
	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	O comando mostra as instruções para baixar o arquivo, incluindo uma URL.

3.	Abra o **Internet Explorer** e navegue até a URL listada na janela do prompt de comando.
4.	Clique em **Salvar** para salvar o arquivo na estação de trabalho.
5.	Na janela do prompt de comando, execute o seguinte comando para importar o arquivo de configurações da publicação:

		azure account import <file>

	Na captura de tela anterior, o arquivo de configurações de publicação foi salvo na pasta C:\HDInsight na estação de trabalho.


O HDInsight usa um contêiner de Armazenamento de Blob do Azure como o sistema de arquivos padrão. Uma conta de Armazenamento do Azure é necessária para poder criar um cluster HDInsight. A conta de armazenamento deve estar localizada no mesmo datacenter.

**Para criar uma conta de Armazenamento do Azure**

- Execute o seguinte comando na janela do prompt de comando:

		azure account storage create [options] <StorageAccountName>

Para obter informações sobre como criar uma conta de Armazenamento do Azure usando o Portal de Gerenciamento do Azure, consulte [Como criar uma conta de armazenamento][azure-create-storageaccount].

Se já tiver uma conta de armazenamento, mas não souber o nome e a chave da conta, você poderá usar os seguintes comandos para recuperar as informações:

	-- lists storage accounts
	azure account storage list
	-- Shows a storage account
	azure account storage show <StorageAccountName>
	-- Lists the keys for a storage account
	azure account storage keys list <StorageAccountName>

Para obter detalhes de como obter informações sobre como usar o Portal de Gerenciamento, consulte a seção *Como exibir, copiar e regenerar chaves de acesso de armazenamento* de [Como gerenciar contas de armazenamento][azure-manage-storageaccount].

O comando *azure hdinsight cluster create* cria o contêiner caso ela não exista. Se você optar por criar o contêiner com antecedência, poderá usar o seguinte comando:

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]
		
Depois de preparar a conta de armazenamento e o contêiner de blob, você estará pronto para criar um cluster.

**Para criar um cluster HDInsight**

- Execute o seguinte comando na janela do prompt de comando:

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName <StorageAccountName> --storageAccountKey <storageAccountKey> --storageContainer <StorageContainer> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

	![HDI.CLIClusterCreation][image-cli-clustercreation]

















Normalmente, você provisiona um cluster HDInsight, executa seus trabalhos e, em seguida, exclui o cluster para reduzir o custo. A interface de linha de comando oferece a opção de salvar as configurações em um arquivo, de modo que você possa reutilizá-lo toda vez que provisionar um cluster.  

**Para provisionar um cluster HDInsight usando um arquivo de configuração**

- Execute o seguinte comando na janela do prompt de comando:
 
		azure hdinsight cluster config create <file>
		 
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"
		 
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
		       --storageAccountKey "<StorageAccountKey>"
		 
		azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"
		 
		azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"
		 
		azure hdinsight cluster create --config <file>
		 


	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**Para listar e mostrar detalhes do cluster**

- Use os seguintes comandos para listar e mostrar detalhes do cluster:

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>
	
	![HDI.CLIListCluster][image-cli-clusterlisting]


**Para excluir um cluster**

- Use o seguinte comando para excluir um cluster:

		azure hdinsight cluster delete <ClusterName>



##<a id="sdk"></a> Usando o SDK do .NET do HDInsight
O SDK do .NET do HDInsight fornece bibliotecas de cliente .NET que facilitam o trabalho com o HDInsight no .NET. 

Os procedimentos a seguir são necessários para provisionar um cluster HDInsight usando o SDK:

- Instalar o SDK do .NET do HDInsight
- Criar um aplicativo de console
- Executar o aplicativo


**Para instalar o SDK do .NET do HDInsight**
Você pode instalar a compilação publicada mais recentemente do SDK no [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). As instruções serão mostradas no próximo procedimento.

**Para criar um aplicativo de console do Visual Studio**

1. Abra o Visual Studio 2012.

2. No menu Arquivo, clique em **Novo** e, em seguida, em **Projeto**.

3. No Novo Projeto, digite ou selecione os seguintes valores:

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


5. No menu **Ferramentas**, clique em **Gerenciador de Pacotes da Biblioteca** e em **Console do Gerenciador de Pacotes**.

6. Execute os seguintes comandos no console para instalar os pacotes.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight


	Esse comando adiciona bibliotecas .NET e referências a elas no projeto atual do Visual Studio.

7. No Gerenciador de Soluções, clique duas vezes em **Program.cs** para abri-lo.

8. Adicione as seguintes instruções using na parte superior do arquivo:

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;

	
9. Na função Main(), copie e cole o código a seguir:
		
        string certfriendlyname = "<CertificateFriendlyName>";
        string subscriptionid = "<WindowsAzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<WindowsAzureStorageAccountName>";     // Full path must be used
        string storageaccountkey = "<WindowsAzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);

        // Create the storage account if it doesn't exist.

        // Create the container if it doesn't exist.

		// Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply th cluster information
        ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
        {
            Name = clustername,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = containername,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize
        };

		// Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue.");
        Console.ReadKey();

10. Substitua as variáveis no início da função main(). 

**Para executar o aplicativo**

Enquanto o aplicativo estiver aberto no Visual Studio, pressione **F5** para executar o aplicativo. Uma janela de console deve ser aberta e exibir o status do aplicativo. Pode levar vários minutos para criar um cluster HDInsight.



##<a id="nextsteps"></a> Próximas etapas
Neste artigo, você aprendeu várias maneiras de provisionar um cluster HDInsight. Para saber mais, consulte os seguintes artigos:

* [Introdução ao Azure HDInsight][hdinsight-getting-started]
* [Administrar o HDInsight usando o PowerShell][hdinsight-admin-powershell]
* [Enviar trabalhos Hadoop de forma programática][hdinsight-submit-jobs]
* [Documentação do SDK do Azure HDInsight][hdinsight-sdk-documentation]

[hdinsight-version]: /pt-br/manage/services/hdinsight/versioning-in-hdinsight/
[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/pt-br/library/dn479185.aspx
[hdinsight-getting-started]: /pt-br/manage/services/hdinsight/get-started-hdinsight/
[hdinsight-storage]: /pt-br/manage/services/hdinsight/howto-blob-store/
[hdinsight-admin-powershell]: /pt-br/manage/services/hdinsight/administer-hdinsight-using-powershell/
[hdinsight-submit-jobs]: /pt-br/manage/services/hdinsight/submit-hadoop-jobs-programmatically/
[hdinsight-configure-powershell]: /pt-br/manage/services/hdinsight/install-and-configure-powershell-for-hdinsight/ 
[hdinsight-powershell-reference]: http://msdn.microsoft.com/pt-br/library/windowsazure/dn479228.aspx

[azure-create-storageaccount]: /pt-br/manage/services/storage/how-to-create-a-storage-account/ 
[azure-management-portal]: https://manage.windowsazure.com/
[azure-command-line-tools]: /pt-br/develop/nodejs/how-to-guides/command-line-tools/
[azure-command-line-tool]: /pt-br/manage/linux/other-resources/command-line-tools/
[azure-manage-storageaccount]: /pt-br/manage/services/storage/how-to-manage-a-storage-account/
[azure-purchase-options]: https://www.windowsazure.com/pt-br/pricing/purchase-options/
[azure-member-offers]: https://www.windowsazure.com/pt-br/pricing/member-offers/
[azure-free-trial]: https://www.windowsazure.com/pt-br/pricing/free-trial/

[Powershell-install-configure]: /pt-br/documentation/articles/install-configure-powershell/


[image-customprovision-page1]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page1.png 
[image-customprovision-page2]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page2.png 
[image-customprovision-page3]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png 
[image-customprovision-page4]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png 

[image-cli-account-download-import]: ./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "List and show clusters"

[image-hdi-ps-provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png


