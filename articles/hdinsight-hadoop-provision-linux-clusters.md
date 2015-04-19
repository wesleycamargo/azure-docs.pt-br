<properties 
   pageTitle="Provisionar os clusters de Hadoop no Linux no HDInsight | Azure" 
   description="Saiba como configurar clusters de Hadoop no Linux para o HDInsight usando o portal de gerenciamento, a linha de comando e o SDK do .NET." 
   services="hdinsight" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="03/03/2015"
   ms.author="nitinme"/>


#Provisionar cluster Hadoop Linux no HDInsight usando opções personalizadas (Preview)

Neste artigo você aprenderá as diferentes formas de provisionar de maneira personalizada um cluster Hadoop no Linux HDInsight - usando o Portal de Gerenciamento do Azure, o PowerShell, ferramentas de linha de comando ou o SDK do .NET do HDInsight .

## O que é um cluster HDInsight?

Você já pensou por que mencionamos clusters sempre que falamos sobre Hadoop ou BigData? O motivo é que o Hadoop permite o processamento distribuído de grandes volumes de dados, espalhados em diferentes nós de um cluster. O cluster tem uma arquitetura de mestre/servidor subordinado com um mestre (também chamado headnode ou namenode) e qualquer número de servidores subordinados (também chamados nó de dados). Para obter mais informações, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>.

![HDInsight Cluster][img-hdi-cluster]

Um cluster HDInsight resume os detalhes de implementação do Hadoop para que você não precise se preocupar com a forma de comunicação com diferentes nós de um cluster. Ao provisionar um cluster HDInsight, você provisiona recursos de computação do Azure que contêm o Hadoop e aplicativos relacionados. Para obter mais informações, consulte [Introdução ao Hadoop no HDInsight](hdinsight-hadoop-introduction.md). Os dados que sofrerem variação serão armazenados no Armazenamento do Blob do Azure, também denominado *Armazenamento do Azure - Blob* (ou WASB) no contexto do HDInsight. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight](hdinsight-use-blob-storage.md).

Este artigo fornece instruções sobre diferentes maneiras de provisionar um cluster. Se você procura uma abordagem de início rápido para provisionar um cluster, consulte [Introdução ao Azure HDInsight no Linux](hdinsight-hadoop-linux-get-started.md).

**Pré-requisitos:**

Antes de começar este artigo, você deve ter o seguinte:

- Uma assinatura do Azure. O Azure é uma plataforma baseada em assinatura. Os cmdlets do PowerShell do HDInsight executam as tarefas com sua assinatura. Para obter mais informações sobre como adquirir uma assinatura, consulte <a href="http://azure.microsoft.com/pricing/purchase-options/" target="_blank">Opções de compra</a>, <a href="http://azure.microsoft.com/pricing/member-offers/" target="_blank">Ofertas para membros</a> ou <a href="http://azure.microsoft.com/pricing/free-trial/" target="_blank">Avaliação gratuita</a>.
- Chaves SSH. Se você quiser acesso remoto em um cluster do Linux usando o SSH com uma chave, em vez de uma senha. Usar uma chave é o método recomendado, pois é mais seguro. Para obter instruções sobre como gerar chaves SSH consulte os seguintes artigos
	-  De um computador Linux - [Use SSH com o HDInsight baseado em Linux (Hadoop) em Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
	-  De um computador Windows - [Use SSH com o HDInsight baseado em Linux (Hadoop) do Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

## <a id="configuration"></a>Opções de configuração

### Clusters no Linux

O HDInsight fornece a opção de provisionamento de clusters do Linux no Azure. Provisione um cluster do Linux, se você estiver familiarizado com o Linux ou Unix, migre de uma solução Hadoop baseada em Linux existente ou deseja fácil integração com componentes do ecossistema do Hadoop criados para o Linux. Para obter mais informações sobre o Azure HDInsight no Linux, consulte [Introdução ao Hadoop no HDInsight](hdinsight-hadoop-introduction.md). 

### Armazenamento adicional

Durante a configuração, você deve especificar uma conta de Armazenamento do Blob do Azure e um contêiner padrão. Eles são usados como o local de armazenamento padrão pelo cluster. Opcionalmente, você pode especificar blobs adicionais que também serão associados ao cluster.

Para obter mais informações sobre como usar armazenamentos de blob secundários, consulte [Usando o Armazenamento do Blob do Azure com o HDInsight](http://azure.microsoft.com/documentation/articles/hdinsight-use-blob-storage/).

### Metastore

A Metastore contém informações sobre as tabelas, partições, esquemas, colunas, etc. do Hive. Essas informações são usadas pelo Hive para localizar onde os dados estão armazenados no HDFS (ou WASB para o HDInsight.) Por padrão, o Hive usa um banco de dados integrado para armazenar essas informações.

Ao provisionar um cluster do HDInsight, você pode especificar um Banco de Dados SQL que conterá a Metastore para o Hive. Isso permite que as informações de metadados sejam preservadas ao excluir um cluster, já que são armazenadas externamente no Banco de Dados SQL.

> [WACOM.NOTE] Atualmente, a opção de usar metastores só está disponível durante o provisionamento do HDInsight para Linux usando o SDK do .NET. Para obter instruções, consulte [Provisionar clusters HDInsight no Linux usando o SDK do .NET](#sdk).


## <a id="options"></a> Opções para provisionar um cluster HDInsight Linux

Você pode provisionar um cluster HDInsight Hadoop Linux de um computador Linux, bem como um computador com Windows. A tabela a seguir fornece informações sobre as opções de provisionamento disponíveis dos diferentes sistemas operacionais e links para instruções de cada um.

Provisionar os clusters do Linux de um computador executando esse sistema operacional| Usando o Portal de Gerenciamento do Azure | Usando a CLI multiplataformas | Usando o SDK do .NET | Usando o PowerShell
-----------------| ------------------------| -------------------| ---------- | ---------
Linux| Clique [aqui](#portal) |Clique [aqui](#cli)|Não aplicável | Disponível em breve
Windows | Clique [aqui](#portal) |Clique [aqui](#cli) |Clique [aqui](#sdk) |Disponível em breve

### <a id="portal"></a>Usando o Portal de Gerenciamento do Azure

Clusters HDInsight usam um contêiner de Armazenamento de Blob do Azure como o sistema de arquivos padrão. Uma conta de Armazenamento do Azure localizada no mesmo data center é necessária para poder criar um cluster HDInsight. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight](hdinsight-use-blob-storage.md). Para obter detalhes sobre como criar uma conta de armazenamento do Azure, consulte [Como criar uma conta de armazenamento][azure-create-storageaccount].


> [WACOM.NOTE] Atualmente, apenas as regiões **Ásia Oriental**, **Sudeste da Ásia**, **Norte da Europa**, **Oeste da Europa**, **Leste dos EUA**, **Oeste dos EUA**, **Centro-Norte dos EUA** e **Centro-Sul dos EUA** podem hospedar clusters HDInsight.

**Para criar um cluster HDInsight usando a opção de criação personalizada**

1. Entre no [Portal de Gerenciamento do Azure][azure-management-portal].
2. Clique em **+ NOVO** na parte inferior da página, clique em **SERVIÇOS DE DADOS**, clique em **HDINSIGHT** e, em seguida, clique em **CRIAÇÃO PERSONALIZADA**.
3. Na página **Detalhes do Cluster**, digite ou escolha os valores a seguir:

	![Provide Hadoop HDInsight cluster details](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page1.png)

    <table border='1'>
		<tr><th>Propriedade</th><th>Valor</th></tr>
		<tr><td>Nome do cluster</td>
			<td><p>Nome do cluster. </p>
				<ul>
				<li>O nome DNS deve iniciar e terminar com alfa numéricos e pode conter traços.</li>
				<li>O campo deve ser uma cadeia de caracteres com 3 a 63 caracteres.</li>
				</ul></td></tr>
		<tr><td>Tipo de cluster</td>
			<td>Para tipo de cluster, selecione <strong>Hadoop</strong>.</td></tr>
		<tr><td>Sistema operacional</td>
			<td>Selecione <b>Ubuntu 12.04 LTS Preview</b> para provisionar cluster HDInsight no Linux. Para provisionar um cluster do Windows, consulte <a href="http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/" target="_blank">Provisionar clusters Hadoop no Windows no HDInsight</a>.</td></tr>
		<tr><td>Versão do HDInsight</td>
			<td>Escolha a versão. Para o HDInsight no Linux, o padrão é HDInsight versão 3.2, que usa o Hadoop 2.5.</td></tr>
		</table>

	Insira ou selecione os valores, conforme mostrado na tabela, e clique na seta para a direita.

4. Na página **Configurar Cluster**, insira ou selecione os valores a seguir:

	<table border="1">
	<tr><th>Nome</th><th>Valor</th></tr>
	<tr><td>Nós de dados</td><td>Número de nós de dados que você deseja implantar. Para fins de teste, crie um cluster de nó único. <br />O limite de tamanho do cluster varia para as assinaturas do Azure. Contate o suporte de cobrança do Azure para aumentar o limite.</td></tr>
	<tr><td>Região/Rede virtual</td><td><p>Escolha a mesma região da conta de armazenamento que você criou no último procedimento. O HDInsight requer que a conta de armazenamento esteja localizada na mesma região. Mais adiante na configuração, você poderá escolher somente uma conta de armazenamento que esteja na mesma região especificada aqui.</p><p>As regiões disponíveis são: <strong>Ásia Oriental</strong>, <strong>Sudeste da Ásia</strong>, <strong>Norte da Europa</strong>, <strong>Oeste da Europa</strong>, <strong>Leste dos EUA</strong>, <strong>Oeste dos EUA</strong>, <strong>Leste dos EUA</strong> e <strong>Centro-Sul dos EUA</strong><br/></p></td></tr>
	</table>



5. Na página **Configurar Usuário de Cluster**, forneça os seguintes valores:

    ![Provide Hadoop HDInsight cluster user](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page3.png)

    <table border='1'>
		<tr><th>Propriedade</th><th>Valor</th></tr>
		<tr><td>Senha HTTP</td>
			<td>Especifique a senha para o usuário HTTP padrão, <i>admin</i>.</td></tr>
		<tr><td>Nome de usuário do SSH</td>
			<td>Especifique o nome de usuário do SSH. Você usará esse nome de usuário para iniciar uma sessão remota do SSH em nós do cluster HDInsight.</td></tr>
		<tr><td>Tipo de autenticação SSH</td>
			<td>Especifique se deseja usar uma senha ou uma chave SSH para autenticar um usuário SSH</td></tr>
		<tr><td>Senha SSH</td>
			<td>Se você escolher uma senha como tipo de autenticação, especifique a senha SSH para autenticar um usuário SSH. Essa senha será solicitada ao tentar iniciar uma sessão SSH no computador remoto do Linux.</td></tr>
		<tr><td>Chave pública SSH</td>
			<td>Se você escolher uma chave como tipo de autenticação, especifique a chave pública SSH que você já deve ter gerado. Quando você inicia uma sessão SSH com um nó no cluster do Linux, usará a chave privada associada com essa chave pública.<br>
			Para obter instruções sobre como gerar uma chave SSH em um computador Linux, consulte <a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-linux-use-ssh-unix/" target="_blank">aqui</a>. Para obter instruções sobre como gerar uma chave SSH em um computador Windows, consulte <a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-linux-use-ssh-windows/" target="_blank">aqui</a>.
		</td></tr>
		</table>

	> [WACOM.NOTE] É recomendável usar a autenticação de chave pública SSH com o SSH porque é mais seguro do que a autenticação de senha.

	Clique na seta à direita.


6. Na página **Conta de Armazenamento**, forneça o seguinte valor:

    ![Provide storage account for Hadoop HDInsight cluster](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page4.png)

	<table border='1'>
		<tr><th>Propriedade</th><th>Valor</th></tr>
		<tr><td>Conta de Armazenamento</td>
			<td>Especifique a conta de armazenamento do Azure que será usada como o sistema de arquivos padrão para o cluster HDInsight. Você pode escolher uma destas três opções:
			<ul>
				<li>Usar Armazenamento Existente</li>
				<li>Criar Novo Armazenamento</li>
				<li>Usar armazenamento de outra assinatura,</li>
			</ul>
			</td></tr>
		<tr><td>Nome da conta</td>
			<td><ul>
				<li>Se você optar por usar um armazenamento existente, para <strong>Nome da conta</strong>, selecione uma conta de armazenamento existente. A lista suspensa exibe somente as contas de armazenamento localizadas no mesmo data center onde você optou por provisionar o cluster.</li>
				<li>Se você escolher as opções <strong>Criar novo armazenamento</strong> ou <strong>usar armazenamento de outra assinatura</strong>, deverá fornecer o nome da conta de armazenamento.</li>
			</ul></td></tr>
		<tr><td>Chave de conta</td>
			<td>Se você selecionar a opção <strong>Usar armazenamento de outra assinatura</strong>, especifique a chave de conta para essa conta de armazenamento.</td></tr>
		<tr><td>Contêiner padrão</td>
			<td><p>Especifica o contêiner padrão na conta de armazenamento usado como o sistema de arquivos padrão para o cluster HDInsight. Se você selecionar <strong>Usar Armazenamento Existente</strong> para o campo <strong>Conta de Armazenamento</strong>, e não houver contêineres existentes nessa conta, o contêiner será criado por padrão com o mesmo nome do cluster. Se um contêiner com o nome do cluster já existir, será acrescentado um número de sequência ao nome do contêiner. Por exemplo, meucontêiner1, meucontêiner2 e assim por diante. No entanto, se a conta de armazenamento existente tiver contêiner com um nome diferente do nome do cluster que você especificou, você também pode usar esse contêiner.</p>
            <p>Caso opte por criar um novo armazenamento ou usar um armazenamento de outra assinatura do Azure, especifique o nome do contêiner padrão.</p>
        </td></tr>
		<tr><td>Contas de armazenamento adicionais</td>
			<td>O HDInsight dá suporte a várias contas de armazenamento. Não há nenhum limite de contas de armazenamento adicionais que podem ser usadas por um cluster. No entanto, se criar um cluster usando o Portal de Gerenciamento, você terá um limite de sete, devido às restrições da interface do usuário. Cada conta de armazenamento adicional que você especificar adiciona uma página extra de Conta de Armazenamento ao assistente, onde você pode especificar as informações da conta. Por exemplo, na captura de tela acima, uma conta de armazenamento adicional está selecionada, e portanto a página cinco é adicionada à caixa de diálogo.</td></tr>
	</table>

	Clique na seta à direita.

7. Se você optar por configurar o armazenamento adicional para o cluster, na página **Conta de Armazenamento**, insira as informações da conta para a conta de armazenamento adicional:

	![Provide additional storage details for HDInsight cluster](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page5.png)

    Aqui, mais uma vez, você tem a opção de escolher entre um armazenamento existente, criar um novo armazenamento ou usar um armazenamento de outra assinatura do Azure. O procedimento para fornecer os valores é semelhante à etapa anterior.

    > [WACOM.NOTE] Depois de escolher uma conta de armazenamento do Azure para o cluster HDInsight, você não pode excluir a conta, nem alterá-la para uma conta diferente.

 	Depois que você especificou a conta de armazenamento adicional, clique na marca de seleção para iniciar o provisionamento do cluster. 

###<a id="cli"></a> Usando linha de comando de plataforma cruzada

Outra opção para o provisionamento de um cluster HDInsight é a interface de linha de comando de plataforma cruzada. A ferramenta de linha de comando é implementada no Node.js. Ela pode ser usada em qualquer plataforma que ofereça suporte ao Node.js, incluindo Windows, Mac e Linux. Você pode instalar a CLI nos seguintes locais:

- **SDK Node.JS** - <a href="https://www.npmjs.com/package/azure-mgmt-hdinsight" target="_blank">https://www.npmjs.com/package/azure-mgmt-hdinsight</a>
- **CLI da plataforma cruzada** -<a href="https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz" target="_blank">https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz</a>  

Para obter um guia geral sobre como usar a interface de linha de comando, consulte [Ferramenta de linha de comando do Azure para Mac e Linux][azure-command-line-tools].

As instruções a seguir orientam sobre como instalar a linha de comando de plataforma cruzada no Linux e no Windows e como usar a linha de comando para provisionar um cluster.

- [Configurar a linha de comando entre plataformas do Azure para Linux](#clilin)
- [Configurar a linha de comando entre plataformas do Azure para Windows](#cliwin)
- [Provisionar clusters HDInsight usando a linha de comando entre plataformas do Azure](#cliprovision)

#### <a id="clilin"></a>Configurar a linha de comando de plataforma cruzada para Linux

Execute os seguintes procedimentos para configurar seu computador Linux para usar as ferramentas de linha de comando do Azure.

- Instalar a linha de comando entre plataformas usando NPM
- Conecte-se à sua assinatura do Azure

**Para instalar a interface de linha de comando usando o NPM**

1.	Abra uma janela de terminal no seu computador Linux e execute o seguinte comando:

		sudo npm install -g https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

2.	Execute o comando a seguir para verificar a instalação:

		azure hdinsight -h

	Você pode usar a opção *-h* em diferentes níveis para exibir as informações da Ajuda. Por exemplo:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Para conectar-se a sua assinatura do Azure**

Antes de usar a interface de linha de comando, você deve configurar a conectividade entre a estação de trabalho e o Azure. As informações da assinatura do Azure são usadas pela interface de linha de comando para se conectar à sua conta. Essas informações podem ser obtidas no Azure em um arquivo de configurações de publicação. O arquivo de configurações de publicação pode ser importado como uma definição de configuração local persistente que a interface de linha de comando vai usar para operações subsequentes. Você precisa importar as configurações de publicação apenas uma vez.

> [WACOM.NOTE] O arquivo de configurações de publicação contém informações confidenciais. A Microsoft recomenda excluir o arquivo ou executar etapas adicionais para criptografar a pasta do usuário que contém o arquivo. No Windows, modifique as propriedades da pasta ou use o BitLocker. 


1.	Abra uma janela de terminal.
2.	Execute o seguinte comando para fazer logon em sua assinatura do Azure.

		azure account download

	![HDI.Linux.CLIAccountDownloadImport](./media/hdinsight-hadoop-provision-linux-clusters/HDI.Linux.CLIAccountDownloadImport.png)

	O comando inicia a página da Web na qual baixar o arquivo de configurações de publicação. Se a página da Web não abrir, clique no link na janela do terminal para abrir a página do navegador e efetuar logon no portal. 

3.	Baixe o arquivo de configurações de publicação para o computador.
5.	Na janela do prompt de comando, execute o seguinte comando para importar o arquivo de configurações da publicação:

		azure account import <path/to/the/file>

	
#### <a id="cliwin"></a>Configurar linha de comando de plataforma cruzada para Windows

Execute os seguintes procedimentos para configurar seu computador Windows para usar as ferramentas de linha de comando do Azure.

- Instalar a linha de comando entre plataformas (usando NPM ou o Windows installer)
- Baixar e importar as configurações de publicação da conta do Azure


A interface de linha de comando pode ser instalada usando o  *NPM (Gerenciador de Pacotes do Node.js)* ou o Windows Installer. A Microsoft recomenda instalar usando apenas uma das duas opções.

**Para instalar a interface de linha de comando usando o NPM**

1.	Procure o **www.nodejs.org**.
2.	Clique em **INSTALAR** e siga as instruções usando as configurações padrão.
3.	Abra o **Prompt de Comando** (ou *Prompt de Comando do Azure* ou o  *Prompt de Comando do Desenvolvedor para VS2012*) em sua estação de trabalho.
4.	Execute o seguinte comando na janela de prompt de comando.

		npm install -g https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

	> [WACOM.NOTE] Se você receber um erro informando que o comando NPM não foi encontrado, verifique se os caminhos a seguir estão na variável de ambiente PATH: <i>C:\Program Files (x86)\nodejs;C:\Users\[username]\AppData\Roaming\npm</i> ou <i>C:\Program Files\nodejs;C:\Users\[username]\AppData\Roaming\npm</i>

5.	Execute o comando a seguir para verificar a instalação:

		azure hdinsight -h

	Você pode usar a opção *-h* em diferentes níveis para exibir as informações da Ajuda. Por exemplo:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Para instalar a interface de linha de comando usando o Windows Installer**

1.	Navegue até **http://azure.microsoft.com/downloads/**.
2.	Role para baixo até a seção **Ferramentas de linha de comando** e, em seguida, clique em **Interface de Linha de Comando entre Plataformas** e siga o assistente do Web Platform Installer.

**Para baixar e importar as configurações de publicação**

Antes de usar a interface de linha de comando, você deve configurar a conectividade entre a estação de trabalho e o Azure. As informações da assinatura do Azure são usadas pela interface de linha de comando para se conectar à sua conta. Essas informações podem ser obtidas no Azure em um arquivo de configurações de publicação. O arquivo de configurações de publicação pode ser importado como uma definição de configuração local persistente que a interface de linha de comando vai usar para operações subsequentes. Você precisa importar as configurações de publicação apenas uma vez.

> [WACOM.NOTE] O arquivo de configurações de publicação contém informações confidenciais. A Microsoft recomenda excluir o arquivo ou executar etapas adicionais para criptografar a pasta do usuário que contém o arquivo. No Windows, modifique as propriedades da pasta ou use o BitLocker.


1.	Abra um **Prompt de comando**.
2.	Execute o comando a seguir para baixar o arquivo de configurações da publicação.

		azure account download

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	O comando inicia a página da Web na qual baixar o arquivo de configurações de publicação.

3.	No prompt para salvar o arquivo, clique em **Salvar** e forneça um local onde o arquivo deve ser salvo.
5.	Na janela do prompt de comando, execute o seguinte comando para importar o arquivo de configurações da publicação:

		azure account import <path/to/the/file>

	
#### <a id="cliprovision"></a>Provisionar clusters HDInsight usando a linha de comando de plataforma cruzada do Azure

Os procedimentos a seguir são necessários para provisionar um cluster HDInsight usando a linha de comando de plataforma cruzada:

- Criar uma conta de Armazenamento do Azure
- Provisionar um cluster


**Para criar uma conta de Armazenamento do Azure**

O HDInsight usa um contêiner de Armazenamento de Blob do Azure como o sistema de arquivos padrão. Uma conta de Armazenamento do Azure é necessária para que seja possível criar um cluster HDInsight. A conta de armazenamento deve estar localizada no mesmo datacenter.

- Na janela do prompt de comando, execute o seguinte comando para criar uma conta de armazenamento do Azure:

		azure storage account create [options] <StorageAccountName>

	Quando um local for solicitado, selecione o local onde um cluster HDInsight pode ser provisionado. O armazenamento deve estar no mesmo local que o cluster HDInsight. Atualmente, apenas as regiões **Ásia Oriental**, **Sudeste da Ásia**, **Norte da Europa**, **Oeste da Europa**, **Leste dos EUA**, **Oeste dos EUA**, **Centro-Norte dos EUA** e **Centro-Sul dos EUA** podem hospedar clusters HDInsight.  

Para obter informações sobre como criar uma conta de armazenamento do Azure usando o Portal de Gerenciamento do Azure, consulte [Como criar, gerenciar ou excluir uma conta de armazenamento][azure-create-storageaccount].

Se você já tiver uma conta de armazenamento, mas não souber o nome e a chave da conta, poderá usar os seguintes comandos para recuperar as informações:

	-- lists storage accounts
	azure storage account list

	-- Shows information for a storage account
	azure storage account show <StorageAccountName>

	-- Lists the keys for a storage account
	azure storage account keys list <StorageAccountName>

Para obter detalhes de como obter informações usando o portal de gerenciamento, consulte a seção *Como: Exibir, copiar e regenerar chaves de acesso de armazenamento* de [Como criar, gerenciar ou excluir uma conta de armazenamento][azure-create-storageaccount].

Um cluster HDInsight também requer um contêiner dentro de uma conta de armazenamento. Se a conta de armazenamento fornecida ainda não tiver um contêiner, o comando  *azure hdinsight cluster create* solicitará um nome de contêiner e também o criará. No entanto, se você desejar criar o contêiner com antecedência, poderá usar o seguinte comando:

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

Depois de preparar a conta de armazenamento e o contêiner de blob, você estará pronto para criar um cluster.

**Para provisionar um cluster do HDInsight**

- Execute o seguinte comando na janela do prompt de comando:

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <StorageAccountKey> --storageContainer <StorageContainerName> --dataNodeCount <NumberOfNodes> --location <DataCenterLocation> --userName <HDInsightClusterUsername> --password <HDInsightClusterPassword> --osType linux --sshUserName <SSH username> --sshPassword <SSH user password>		

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**Para provisionar um cluster HDInsight usando um arquivo de configuração**

Normalmente, você provisiona um cluster HDInsight, executa os trabalhos e exclui o cluster para reduzir o custo. A interface de linha de comando oferece a opção de salvar as configurações em um arquivo, de modo que você possa reutilizá-lo toda vez que provisionar um cluster.

- Execute os seguintes comandos na janela do prompt de comando:


		#Create the config file
		azure hdinsight cluster config create <file>

		#Add commands to create a basic cluster
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --dataNodeCount <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --userName "<Username>" --password "<UserPassword>" --osType linux --sshUserName <SSH username> --sshPassword <SSH user password>

		#If requred, include commands to use additional blob storage with the cluster
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
		       --storageAccountKey "<StorageAccountKey>"

		#Run this command to create a cluster using the config file
		azure hdinsight cluster create --config <file>

	>[WACOM.NOTE] O banco de dados SQL do Azure usado para o metastore deve permitir a conectividade com outros serviços do Azure, incluindo o Azure HDInsight. No painel do banco de dados SQL do Azure, no lado direito, clique no nome do servidor. Esse é o servidor no qual a instância do banco de dados SQL está sendo executada. Quando estiver na exibição do servidor, clique em **Configurar** e, para **Serviços do Windows Azure**, clique em **Sim** e em **Salvar**.


	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**Para listar e mostrar detalhes do cluster**

- Use os seguintes comandos para listar e mostrar os detalhes do cluster:

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**Para excluir um cluster**

- Use o seguinte comando para excluir um cluster:

		azure hdinsight cluster delete <ClusterName>



###<a id="sdk"></a> Usando o SDK do .NET do HDInsight
O SDK do .NET do HDInsight fornece bibliotecas de cliente .NET que facilitam o trabalho com o HDInsight em um aplicativo .NET.

Os procedimentos a seguir devem ser executados para provisionar um cluster HDInsight no Linux usando o SDK:

- Instale o SDK do HDInsight .NET
- Criar um certificado autoassinado
- Criar um aplicativo de console
- Executar o aplicativo


**Para instalar o SDK do .NET do HDInsight**

Você pode instalar a compilação publicada mais recentemente do SDK no [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). As instruções serão mostradas no próximo procedimento.

**Para criar um certificado autoassinado**

Crie um certificado autoassinado, instale-o em sua estação de trabalho e o carregue para sua assinatura do Azure. Para obter instruções, consulte [Criar um certificado autoassinado](http://go.microsoft.com/fwlink/?LinkId=511138).


**Para criar um aplicativo de console do Visual Studio**

1. Abra o Visual Studio 2013.

2. No menu Arquivo, clique em **Novo** e **Projeto**.

3. No Novo Projeto, digite ou selecione os seguintes valores:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Propriedade</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Valor</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Categoria</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Modelos/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Template</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Console Aplicativo</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateHDICluster</td></tr>
	</table>

4. Clique em **OK** para criar o projeto.

5. No menu **Ferramentas**, clique em **Gerenciador de Pacotes Nuget** e, em seguida, clique em **Console do Gerenciador de Pacotes**.

6. Execute os seguintes comandos no console para instalar os pacotes.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Esse comando adiciona bibliotecas .NET e referências a elas no projeto atual do Visual Studio.

7. No Gerenciador de Soluções, clique duas vezes com o botão direito do mouse em **Program.cs** para abri-lo.

8. Adicione o seguinte usando as instruções na parte superior do arquivo :

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;


9. Na função Main(), copie e cole o código a seguir:

        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;
		string version = "<version>";
        string sshusername = "<ssh user name>";
        string sshpublickey = "<ssh public key>;
		
		//if required, provide details of the Hive and Oozie metastore that you want to configure. ServerName is the name of the server on which the SQL databases are provisioned. HiveStoreSqlDatabaseName and OozieStoreSqlDatabaseName are the names of databases created on the server. You can also use the same database for both Hive and Oozie metastores
		Metastore hiveMetastore = new Metastore("<ServerName>.database.windows.net", "<HiveStoreSqlDatabaseName>", "<SqlDatabaseUser>", "<SqlDatabasePassword>");
        Metastore oozieMetastore = new Metastore("<ServerName>.database.windows.net", "<OozieStoreSqlDatabaseName>", "<SqlDatabaseUser>", "<SqlDatabasePassword>");

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply th cluster information
        ClusterCreateParametersV2 clusterInfo = new ClusterCreateParametersV2()
        {
            Name = clustername,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = containername,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize
			Version = version,
            OSType = OSType.Linux,
            SshUserName = sshusername,
            SshPublicKey = sshpublickey,
			HiveMetastore = hiveMetastore,		//only if you created a hivemetastore object earlier
            OozieMetastore = oozieMetastore		//only if you created an ooziemetastore object earlier
        };

		// Configure Hive and Oozie if you opted for the metastores earlier
		clusterInfo.HiveConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("datanucleus.connectionPoolingType", "none"));
		clusterInfo.OozieConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("oozie.service.AuthorizationService.security.enabled", "false"));
        

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
Neste artigo, você aprendeu várias maneiras de provisionar um cluster HDInsight Hadoop no Linux. Para saber mais, consulte os seguintes artigos:

- [Trabalhando com o HDInsight no Linux](hdinsight-hadoop-linux-information.md). Familiarize-se com as nuances de trabalhar com um cluster HDInsight no Linux.
- [Gerenciar clusters HDInsight usando Ambari](hdinsight-hadoop-manage-ambari.md). Saiba como monitorar e gerenciar seu Hadoop baseado em Linux no cluster HDInsight usando Ambari Web ou a API REST do Ambari. 
- [Usar o MapReduce com HDInsight][hdinsight-use-mapreduce]. Saiba mais sobre as diferentes maneiras de executar trabalhos MapReduce em um cluster.
- [Usar o Hive com HDInsight][hdinsight-use-hive]. Saiba mais sobre as diferentes formas de executar uma consulta de Hive em um cluster.
- [Usar o Pig com o HDInsight][hdinsight-use-pig]. Saiba mais sobre as diferentes maneiras de executar um trabalho Pig em um cluster.
- [Usar o armazenamento de blob do Azure com o HDInsight](hdinsight-use-blob-storage.md). Saiba como o HDInsight usa o armazenamento de Blob do Azure para armazenar dados para clusters HDInsight.
- [Carregar dados no HDInsight][hdinsight-upload-data]. Saiba como trabalhar com dados armazenados em um armazenamento de Blob do Azure para um cluster HDInsight.

[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: http://msdn.microsoft.com/library/windowsazure/dn479228.aspx

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
[image-cli-clusterlisting]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIListClusters.png "List and show clusters"

[image-hdi-ps-provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png

[img-hdi-cluster]: ./media/hdinsight-provision-clusters/HDI.Cluster.png

[89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Use Sqoop with HDInsight"

<!--HONumber=47-->
