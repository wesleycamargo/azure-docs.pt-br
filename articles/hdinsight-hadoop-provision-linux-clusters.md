<properties 
   pageTitle="Provisionar clusters Hadoop em Linux no HDInsight | Microsoft Azure" 
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
   ms.date="04/17/2015"
   ms.author="nitinme"/>


#Provisionar cluster Hadoop Linux no HDInsight usando opções personalizadas (Preview)

Neste artigo, você aprenderá as diferentes maneiras de provisionar, de forma personalizada, um cluster Hadoop em Linux no Azure HDInsight: usando o portal do Azure, o PowerShell do Azure, ferramentas de linha de comando ou o HDInsight .NET SDK.

## O que é um cluster HDInsight?

Você já se perguntou por que mencionamos clusters sempre que falamos de Hadoop ou Big Data? O motivo é que o Hadoop permite o processamento distribuído de grandes volumes de dados, espalhados em diferentes nós de um cluster. O cluster tem uma arquitetura mestre/de trabalho com um nó principal (também chamado de nó de cabeçalho ou nó de nome) e certo número de nós de trabalho (também chamados de nós de dados). Para obter mais informações, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>.

![Cluster do HDInsight][img-hdi-cluster]


Um cluster HDInsight resume os detalhes de implementação do Hadoop para que você não precise se preocupar com a forma de comunicação com diferentes nós de um cluster. Ao provisionar um cluster HDInsight, você provisiona recursos de computação do Azure que contêm o Hadoop e aplicativos relacionados. Para obter mais informações, consulte [Introdução ao Hadoop no HDInsight](hdinsight-hadoop-introduction.md). Os dados que sofrerem variação serão armazenados no armazenamento de Blob do Azure. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight](hdinsight-use-blob-storage.md).


Este artigo fornece instruções sobre diferentes maneiras de provisionar um cluster. Se estiver procurando uma abordagem de início rápido para provisionar um cluster, consulte [Introdução ao Azure HDInsight no Linux](hdinsight-hadoop-linux-get-started.md).

**Pré-requisitos**

Antes de começar este artigo, você deve ter o seguinte:

- Uma assinatura do Azure. O Azure é uma plataforma baseada em assinatura. Os cmdlets do PowerShell do Azure para HDInsight executam as tarefas com a sua assinatura. Para obter mais informações sobre como adquirir uma assinatura, consulte <a href="http://azure.microsoft.com/pricing/purchase-options/" target="_blank">Opções de Compra</a>, <a href="http://azure.microsoft.com/pricing/member-offers/" target="_blank">Ofertas para Membros</a> ou <a href="http://azure.microsoft.com/pricing/free-trial/" target="_blank">Avaliação Gratuita</a>.
- Chaves SSH. Se você quiser acesso remoto em um cluster do Linux usando SSH com uma chave, em vez de senha, o método recomendado é usar uma chave, pois é mais segura. Para obter instruções sobre como gerar chaves SSH, consulte os seguintes artigos:
	-  Em um computador Linux: [Usar SSH com o HDInsight baseado em Linux (Hadoop) por meio de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
	-  Em um computador Windows: [Usar SSH com o HDInsight baseado em Linux (Hadoop) no Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

## <a id="configuration"></a>Opções de configuração

### Clusters no Linux

O HDInsight fornece a opção de provisionamento de clusters do Linux no Azure. Provisione um cluster Linux se você estiver familiarizado com Linux ou Unix, se estiver migrando de uma solução Hadoop baseada em Linux existente ou se quiser integração fácil com componentes do ecossistema Hadoop criados para Linux. Para obter mais informações sobre o Azure HDInsight no Linux, consulte [Introdução ao Hadoop no HDInsight](hdinsight-hadoop-introduction.md).


### Armazenamento adicional

Durante a configuração, você deve especificar uma conta de Armazenamento do Blob do Azure e um contêiner padrão. Eles são usados como o local de armazenamento padrão pelo cluster. Opcionalmente, você pode especificar blobs adicionais que também serão associados ao cluster.


Para obter mais informações sobre o uso de repositórios de blob secundários, consulte [Usando o armazenamento de Blob do Azure com o HDInsight](hdinsight-use-blob-storage.md).


### Metastore

O metastore contém informações sobre tabelas Hive, partições, esquemas, colunas, etc. Essas informações são usadas pelo Hive para localizar onde os dados estão armazenados no HDFS (Sistema de Arquivos Distribuído do Hadoop) ou no armazenamento de Blob do Azure para HDInsight. Por padrão, o Hive usa um banco de dados integrado para armazenar essas informações.

Ao provisionar um cluster HDInsight, você pode especificar um banco de dados SQL para conter o metastore para Hive. Isso permite que as informações de metadados sejam preservadas quando você excluir um cluster, já que elas são armazenadas externamente no banco de dados SQL.


> [AZURE.NOTE]Atualmente, a opção de usar metastores está disponível somente enquanto você estiver provisionando HDInsight para Linux usando o .NET SDK. Para obter instruções, consulte [Provisionar clusters HDInsight no Linux usando o .NET SDK](#sdk).



## <a id="options"></a> Opções para provisionar um cluster HDInsight em Linux

Você pode provisionar um cluster HDInsight Hadoop em Linux tanto em um computador Linux, quanto em um computador baseado em Windows. A tabela a seguir fornece informações sobre as opções de provisionamento disponíveis dos diferentes sistemas operacionais e links para instruções de cada um.

Provisionar clusters Linux de um computador que executa este sistema operacional| Usando o portal do Azure | Usando a interface de linha de comando do Azure | Usando o .NET SDK | Usando o PowerShell do Azure
-----------------| ------------------------| -------------------| ---------- | ---------
Linux| Clique [aqui](#portal) | Clique [aqui](#cli)| Não aplicável | Não aplicável
Windows | Clique [aqui](#portal) | Clique [aqui](#cli) | Clique [aqui](#sdk) | Clique [aqui](#powershell)

### <a id="portal"></a> Usando o portal do Azure

Clusters HDInsight usam um contêiner de Armazenamento de Blob do Azure como o sistema de arquivos padrão. Para poder criar um cluster HDInsight, você precisa de uma conta do Armazenamento do Azure localizada no mesmo datacenter. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight](hdinsight-use-blob-storage.md). Para obter detalhes sobre como criar uma conta do Armazenamento do Azure, consulte [Como criar uma conta de armazenamento](storage-create-storage-account.md).


> [AZURE.NOTE]Atualmente, apenas as regiões **Sudeste da Ásia**, **Norte da Europa**, **Leste dos EUA** e **Centro-Sul dos EUA** podem hospedar clusters HDInsight em Linux.

**Para criar um cluster HDInsight usando a opção Criação Personalizada**

1. Entre no [Portal do Azure][azure-management-portal].
2. No final da página, clique em **+ NOVO**, em **SERVIÇOS DE DADOS**, em **HDINSIGHT** e em **CRIAÇÃO PERSONALIZADA**.
3. Na página **Detalhes do Cluster**, digite ou selecione os seguintes valores:

	![Fornecer detalhes do cluster HDInsight do Hadoop](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page1.png)

    <table border='1'>
	<tr><th>Propriedade</th><th>Valor</th></tr>
	<tr><td>Nome do cluster</td>
		<td><p>Nome do cluster. </p>
			<ul>
			<li>O nome do DNS (Sistema de Nomes de Domínio) deve começar e terminar com caracteres alfanuméricos e pode conter traços.</li>
			<li>O campo deve ser uma cadeia de caracteres com 3 a 63 caracteres.</li>
			</ul></td></tr>
	<tr><td>Tipo de cluster</td>
		<td>Selecione <strong>Hadoop</strong>.</td></tr>
	<tr><td>Sistema operacional</td>
		<td>Selecione <b>Ubuntu 12.04 LTS (visualização)</b> para provisionar um cluster HDInsight no Linux. Para provisionar um cluster Windows, consulte <a href="http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/" target="_blank">Provisionar clusters Hadoop em Windows no HDInsight</a>.</td></tr>
	<tr><td>Versão do HDInsight</td>
		<td>Escolha a versão. Para HDInsight em Linux, o padrão é HDInsight versão 3.2, que usa Hadoop 2.6.</td></tr>
	</table>Digite ou selecione os valores, como mostra a tabela, e clique na seta para a direita.

4. Na página **Configurar Cluster**, digite ou selecione os seguintes valores:

	![Fornecer detalhes do cluster HDInsight do Hadoop](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page2.png)

	<table border="1">
<tr><th>Nome</th><th>Valor</th></tr>
<tr><td>Nós de dados</td><td>Número de nós de dados que você deseja implantar. Para fins de teste, crie um cluster de nó único. <br />O limite de tamanho do cluster varia para as assinaturas do Azure. Contate o suporte de cobrança do Azure para aumentar o limite.</td></tr>
<tr><td>Região/Rede virtual</td><td><p>Escolha a mesma região da conta de armazenamento que você criou anteriormente. Clusters HDInsight exigem que a conta de armazenamento esteja localizada na mesma região. Mais adiante na configuração, você poderá escolher somente uma conta de armazenamento que esteja na mesma região especificada aqui.</p></td></tr>
<tr><td>Tamanho do nó principal</td><td><p>Selecione um tamanho de VM (máquina virtual) para o nó de cabeçalho.</p></td></tr>
<tr><td>Tamanho do nó de dados</td><td><p>Selecione um tamanho de VM para os nós de dados.</p></td></tr>
</table>>[AZURE.NOTE]O custo pode variar de acordo com sua escolha de VMs. O HDInsight usa todas as VMs de camada padrão para nós de cluster. Para obter informações sobre como os tamanhos de VM afetam os preços, consulte <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">Preços do HDInsight</a>.


5. Na página **Configurar Usuário de Cluster**, forneça os seguintes valores:

    ![Fornecer usuário do cluster HDInsight do Hadoop](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page3.png)

    <table border='1'>
	<tr><th>Propriedade</th><th>Valor</th></tr>
	<tr><td>Senha HTTP</td>
		<td>Especifique a senha do usuário HTTP padrão, <strong>admin</strong>.</td></tr>
	<tr><td>Nome de Usuário SSH</td>
		<td>Especifique o nome de usuário SSH. Você utilizará esse nome de usuário para iniciar uma sessão SSH remota em nós de cluster HDInsight.</td></tr>
	<tr><td>Tipo de Autenticação SSH</td>
		<td>Especifique se deseja usar uma senha ou uma chave SSH para autenticar um usuário SSH.</td></tr>
	<tr><td>Senha SSH</td>
		<td>Se você optar por uma senha como tipo de autenticação, especifique a senha SSH para autenticar um usuário SSH. Essa senha será solicitada ao tentar iniciar uma sessão SSH no computador remoto do Linux.</td></tr>
	<tr><td>Chave Pública SSH</td>
		<td>Se você optar por uma chave como tipo de autenticação, especifique a chave pública SSH que você já deve ter gerado. Ao iniciar uma sessão SSH com um nó no cluster do Linux, você usará a chave privada associada a esta chave pública.<br>
		Para obter instruções sobre como gerar uma chave SSH em um computador Linux, clique <a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-linux-use-ssh-unix/" target="_blank">aqui</a>. Para obter instruções sobre como gerar uma chave SSH em um computador baseado em Windows, clique <a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-linux-use-ssh-windows/" target="_blank">aqui</a>.
	</td></tr>
	<tr><td>Inserir como metastore do Hive/Oozie</td>
		<td>Marque esta caixa de seleção para especificar um banco de dados SQL no mesmo datacenter que o cluster, a ser usado como metastore do Hive/Oozie. Se você marcar esta caixa de seleção, deverá especificar detalhes sobre o banco de dados SQL do Azure nas páginas seguintes do assistente. Isso será útil se você quiser reter os metadados sobre os trabalhos do Hive/Oozie após um cluster ter sido excluído.</td></tr>
	</td></tr>
	</table>> [AZURE.NOTE]Recomendamos usar autenticação por chave pública SSH ao utilizar SSH, pois ela é mais segura do que a autenticação por senha.

	Clique na seta à direita.

6. Na página **Configurar Metastore do Hive/Oozie**, forneça os seguintes valores:

    ![Fornecer usuário do cluster HDInsight do Hadoop](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page4.png)

	Especifique um banco de dados SQL do Azure que será usado como metastore do Hive/Oozie. Você pode especificar o mesmo banco de dados para os metastores do Hive e do Oozie. Esse Banco de Dados SQL deve estar no mesmo data center que o cluster HDInsight. A caixa de listagem lista apenas os bancos de dados SQL no mesmo datacenter que você especificou na página <strong>Detalhes do Cluster</strong>. Especifique também o nome de usuário e senha para se conectar ao banco de dados SQL do Azure selecionado.

    >[AZURE.NOTE]O banco de dados SQL do Azure usado para o metastore deve permitir a conectividade com outros serviços do Azure, incluindo o Azure HDInsight. No painel do banco de dados SQL do Azure, no lado direito, clique no nome do servidor. Esse é o servidor no qual a instância do banco de dados SQL está sendo executada. Quando você estiver na exibição do servidor, clique em **Configurar** e depois em **Serviços do Azure**; clique em **Sim** e depois em **Salvar**.

    Clique na seta à direita.


6. Na página **Conta de Armazenamento**, forneça os seguintes valores:


    ![Fornecer conta de armazenamento para o cluster Hadoop do HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page5.png)

	<table border='1'>
	<tr><th>Propriedade</th><th>Valor</th></tr>
	<tr><td>Conta de armazenamento</td>
		<td>Especifique a conta de Armazenamento do Azure que será usada como o sistema de arquivos padrão para o cluster HDInsight. Você pode escolher uma destas três opções:
		<ul>
			<li><strong>Usar Armazenamento Existente</strong></li>
			<li><strong>Criar Novo Armazenamento</strong></li>
			<li><strong>Usar Armazenamento de Outra Assinatura</strong></li>
		</ul>
		</td></tr>
	<tr><td>Nome da conta</td>
		<td><ul>
			<li>Se você optar por usar um armazenamento existente, para <strong>Nome da conta</strong>, selecione uma conta de Armazenamento existente. A lista suspensa lista apenas as contas do Armazenamento localizadas no mesmo datacenter que você escolheu para provisionar o cluster.</li>
			<li>Se você escolheu a opção <strong>Criar Novo Armazenamento</strong> ou <strong>Usar Armazenamento de Outra Assinatura</strong>, forneça o nome da conta do Armazenamento.</li>
		</ul></td></tr>
	<tr><td>Chave de conta</td>
		<td>Se você escolheu a opção <strong>Usar Armazenamento de Outra Assinatura</strong>, especifique a chave dessa conta do Armazenamento.</td></tr>
	<tr><td>Contêiner padrão</td>
		<td><p>Especifique o contêiner padrão da conta do Armazenamento que é utilizado como sistema de arquivos padrão para o cluster HDInsight. Se você tiver escolhido <strong>Usar Armazenamento Existente</strong> no campo <strong>Conta de Armazenamento</strong> e não existir nenhum contêiner nessa conta, por padrão, o contêiner será criado com o mesmo nome que o nome do cluster. Se um contêiner com o nome do cluster já existir, será acrescentado um número de sequência ao nome do contêiner. Por exemplo, meucontêiner1, meucontêiner2 e assim por diante. No entanto, se a conta do Armazenamento tiver um contêiner com um nome diferente do nome do cluster especificado, você poderá usar esse contêiner também.</p>
        <p>Se você optou por criar um novo armazenamento ou usar o armazenamento de outra assinatura do Azure, especifique o nome do contêiner padrão.</p>
    </td></tr>
	<tr><td>Contas de armazenamento adicionais</td>
		<td>O HDInsight dá suporte a várias contas do Armazenamento. Não há nenhum limite quanto ao número de contas do Armazenamento que um cluster pode usar. No entanto, se criar um cluster usando o Portal do Azure, você terá um limite de sete contas, devido a restrições da interface do usuário. Cada conta do Armazenamento adicional que você especifica adiciona um página <strong>Conta de Armazenamento</strong> extra ao assistente, em que você pode especificar as informações da conta. Por exemplo, na captura de tela acima, uma conta do Armazenamento adicional está selecionada e, portanto, a página 5 é adicionada à caixa de diálogo.</td></tr>
</table>Clique na seta à direita.

7. Se você optar por configurar armazenamento adicional para o cluster, na página **Conta de Armazenamento**, insira as informações da conta de armazenamento adicional:

	![Fornecer detalhes de armazenamento adicionais para o cluster HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CustomProvision.Page6.png)

    Aqui, mais uma vez, você tem a opção de escolher entre um armazenamento existente, criar um novo armazenamento ou usar um armazenamento de outra assinatura do Azure. O procedimento para fornecer os valores é semelhante à etapa anterior.


    > [AZURE.NOTE]Depois que você escolhe uma conta do Armazenamento do Azure para seu cluster HDInsight, a conta não pode ser excluída e você não pode mudar a conta para um cluster diferente.


 	Após especificar a conta de Armazenamento adicional, clique na marca de seleção para iniciar o provisionamento do cluster.

###<a id="cli"></a> Usando a linha de comando entre plataformas

Outra opção para provisionar um cluster HDInsight é a Interface de linha de comando do Azure para Mac, Linux e Windows. A CLI do Azure é implementada no Node. js. Ela pode ser usada em qualquer plataforma que dá suporte a Node. js, incluindo Windows, Mac e Linux. Você pode instalar a interface de linha de comando nos seguintes locais:

- **SDK do Node.js** - <a href="https://www.npmjs.com/package/azure-mgmt-hdinsight" target="_blank">https://www.npmjs.com/package/azure-mgmt-hdinsight</a>
- **CLI do Azure para Mac, Linux e Windows**: <a href="https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz" target="_blank">https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz</a>  

Para ver um guia geral sobre como usar a interface de linha de comando, consulte [CLI do Azure para Mac, Linux e Windows](xplat-cli.md).

As instruções a seguir explicam como instalar a linha de comando entre plataformas em Linux e Windows e como usar a linha de comando para provisionar um cluster.

- [Instalar a linha de comando entre plataformas para Linux](#clilin)
- [Instalar a linha de comando entre plataformas para Windows](#cliwin)
- [Provisionar clusters HDInsight usando a linha de comando entre plataformas](#cliprovision)

#### <a id="clilin"></a>Instalar a linha de comando entre plataformas para Linux

Execute os procedimentos a seguir para configurar seu computador Linux para usar as ferramentas de linha de comando do Azure:

- Instalar a interface de linha de comando usando o NPM (Gerenciador de Pacotes Node.js)
- Conecte-se à sua assinatura do Azure

**Para instalar a interface de linha de comando usando o NPM**

1.	Abra uma janela de terminal no seu computador Linux e execute o seguinte comando:

		sudo npm install -g https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

2.	Execute o comando a seguir para verificar a instalação:

		azure hdinsight -h

	Você pode usar a opção **-h** em diferentes níveis para exibir as informações da Ajuda. Por exemplo:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Para conectar-se à sua assinatura do Azure**

Antes de usar a interface de linha de comando, você deve configurar a conectividade entre a estação de trabalho e o Azure. As informações da assinatura do Azure são usadas pela interface de linha de comando para se conectar à sua conta. Essas informações podem ser obtidas no Azure em um arquivo de configurações de publicação. O arquivo de configurações de publicação pode ser importado como uma definição de configuração local persistente que a interface de linha de comando irá usar para operações subsequentes. Você precisa importar suas configurações de publicação apenas uma vez.


> [AZURE.NOTE]O arquivo de configurações de publicação contém informações confidenciais. A Microsoft recomenda excluir o arquivo ou executar etapas adicionais para criptografar a pasta do usuário que contém o arquivo. No Windows, modifique as propriedades da pasta ou use Criptografia de Unidade de Disco BitLocker.



1.	Abra uma janela de terminal.
2.	Execute o seguinte comando para fazer logon em sua assinatura do Azure:

		azure account download

	![HDI.Linux.CLIAccountDownloadImport](./media/hdinsight-hadoop-provision-linux-clusters/HDI.Linux.CLIAccountDownloadImport.png)

	O comando inicia a página da Web na qual se baixa o arquivo de configurações de publicação. Se a página da Web não se abrir, clique no link na janela do terminal para abrir a página do navegador e fazer logon no portal.

3.	Baixe o arquivo de configurações de publicação para o computador.
4.	Na janela do prompt de comando, execute o seguinte comando para importar o arquivo de configurações da publicação:

		azure account import <path/to/the/file>

	
#### <a id="cliwin"></a>Instalar a linha de comando entre plataformas para Windows

Execute os procedimentos a seguir para configurar o computador Windows para usar ferramentas de linha de comando do Azure:

- Instalar a interface de linha de comando usando o NPM ou o Windows Installer
- Baixar e importar as configurações de publicação da conta do Azure


A interface de linha de comando pode ser instalada usando o NPM ou o Windows Installer. A Microsoft recomenda que você a instale usando apenas uma das duas opções.

**Para instalar a interface de linha de comando usando o NPM**

1.	Procure o **www.nodejs.org**.
2.	Clique em **INSTALAR** e siga as instruções usando as configurações padrão.
3.	Abra o **Prompt de Comando** (ou o **Prompt de Comando do Azure** ou o **Prompt de Comando do Desenvolvedor para VS2012**) em sua estação de trabalho.
4.	Execute o seguinte comando na janela do prompt de comando:

		npm install -g https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

	> [AZURE.NOTE]Se você receber um erro informando que o comando NPM não foi encontrado, confira se os caminhos a seguir estão na variável de ambiente **PATH**: <i>C:\\Program Files (x86)\\nodejs;C:\\Users[username]\\AppData\\Roaming\\npm</i> ou <i>C:\\Program Files\\nodejs;C:\\Users[username]\\AppData\\Roaming\\npm</i>


5.	Execute o comando a seguir para verificar a instalação:

		azure hdinsight -h

	Você pode usar a opção **-h** em diferentes níveis para exibir as informações da Ajuda. Por exemplo:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Para instalar a interface de linha de comando usando o Windows Installer**

1.	Navegue até **http://azure.microsoft.com/downloads/**. 2.	Role para baixo até a seção **Ferramentas de linha de comando** e, em seguida, clique em **Interface de Linha de Comando entre Plataformas** e siga o assistente do Web Platform Installer.

**Para baixar e importar as configurações de publicação**

Antes de usar a interface de linha de comando, você deve configurar a conectividade entre a estação de trabalho e o Azure. As informações da assinatura do Azure são usadas pela interface de linha de comando para se conectar à sua conta. Essas informações podem ser obtidas no Azure em um arquivo de configurações de publicação. O arquivo de configurações de publicação pode ser importado como uma definição de configuração local persistente que a interface de linha de comando irá usar para operações subsequentes. Você precisa importar suas configurações de publicação apenas uma vez.


> [AZURE.NOTE]O arquivo de configurações de publicação contém informações confidenciais. A Microsoft recomenda excluir o arquivo ou executar etapas adicionais para criptografar a pasta do usuário que contém o arquivo. No Windows, modifique as propriedades da pasta ou use o BitLocker.



1.	Abra um prompt de comando.
2.	Execute o comando a seguir para baixar o arquivo de configurações da publicação:

		azure account download


	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	O comando inicia a página da Web na qual se baixa o arquivo de configurações de publicação.


3.	No prompt para salvar o arquivo, clique em **Salvar** e forneça um local onde o arquivo deve ser salvo.
5.	Na janela do prompt de comando, execute o seguinte comando para importar o arquivo de configurações da publicação:

		azure account import <path/to/the/file>

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	
#### <a id="cliprovision"></a>Provisionar clusters HDInsight usando a linha de comando entre plataformas

Os procedimentos a seguir são necessários para provisionar um cluster HDInsight usando a linha de comando entre plataformas:

- Criar uma conta de Armazenamento do Azure
- Provisionar um cluster


**Para criar uma conta do armazenamento do Azure**

O HDInsight usa um contêiner de Armazenamento de Blob do Azure como o sistema de arquivos padrão. Uma conta de Armazenamento do Azure é necessária para poder criar um cluster HDInsight. A conta do Armazenamento deve estar localizada no mesmo datacenter.

- Na janela do prompt de comando, execute o seguinte comando para criar uma conta do Armazenamento do Azure:

		azure storage account create [options] <StorageAccountName>


	Quando for solicitado um local, selecione um local onde um cluster HDInsight em Linux possa ser provisionado. O armazenamento deve estar no mesmo local que o cluster HDInsight.


Para obter informações sobre como criar uma conta do Armazenamento do Azure usando o portal do Azure, consulte [Criar, gerenciar ou excluir uma conta de armazenamento](storage-create-storage-account.md).

Se você já tem uma conta do Armazenamento, mas não se lembra do nome da conta e da chave de conta, você pode usar os seguintes comandos para recuperar as informações:

	-- Lists Storage accounts
	azure storage account list

	-- Shows information for a Storage account
	azure storage account show <StorageAccountName>

	-- Lists the keys for a Storage account
	azure storage account keys list <StorageAccountName>

Para ver os detalhes sobre como obter as informações usando o portal do Azure, consulte a seção "Como: exibir, copiar e generar novamente as chaves de acesso ao armazenamento" de [Criar, gerenciar ou excluir uma conta de armazenamento](storage-create-storage-account.md).

Um cluster HDInsight também requer um contêiner em uma conta do Armazenamento. Se a conta do Armazenamento que você fornecer ainda não tiver um contêiner, **azure hdinsight cluster create** solicitará um nome de contêiner e criará o contêiner. No entanto, se você desejar criar o contêiner com antecedência, poderá usar o seguinte comando:

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

Depois de preparar a conta de armazenamento e o contêiner de Blob, você estará pronto para criar um cluster.

**Para provisionar um cluster HDInsight**

- Execute o seguinte comando na janela do prompt de comando:

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <StorageAccountKey> --storageContainer <StorageContainerName> --dataNodeCount <NumberOfNodes> --location <DataCenterLocation> --userName <HDInsightClusterUsername> --password <HDInsightClusterPassword> --osType linux --sshUserName <SSH username> --sshPassword <SSH user password>

	>[AZURE.NOTE]Os valores especificados para **--userName** e **--password** são relativos ao usuário do Hadoop. Para o usuário do Hadoop, você sempre deve especificar --userName como "admin".

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**Para provisionar um cluster HDInsight usando um arquivo de configuração**

Normalmente, você provisiona um cluster HDInsight, executa os trabalhos e exclui o cluster para reduzir o custo. A interface de linha de comando oferece a opção de salvar as configurações em um arquivo, de modo que você possa reutilizá-lo toda vez que provisionar um cluster.

- Execute os seguintes comandos na janela do prompt de comando:


		#Create the config file
		azure hdinsight cluster config create <file>

		#Add commands to create a basic cluster. Make sure you specify --userName as "admin"
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --dataNodeCount <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --userName "<Username>" --password "<UserPassword>" --osType linux --sshUserName <SSH username> --sshPassword <SSH user password>

		#If required, include commands to use additional Blob storage with the cluster
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
		       --storageAccountKey "<StorageAccountKey>"

		#Run this command to create a cluster by using the config file
		azure hdinsight cluster create --config <file>


	>[AZURE.NOTE]O banco de dados SQL do Azure usado para o metastore deve permitir a conectividade com outros serviços do Azure, incluindo o Azure HDInsight. No painel do Banco de Dados SQL, no lado direito, clique no nome do servidor. Esse é o servidor no qual a instância do banco de dados SQL está sendo executada. Quando você estiver na exibição do servidor, clique em **Configurar** e depois em **Serviços do Azure**; clique em **Sim** e depois em **Salvar**.



	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**Para listar e mostrar detalhes do cluster**

- Use os seguintes comandos para listar e mostrar os detalhes do cluster:

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**Para excluir um cluster**

- Use o seguinte comando para excluir um cluster:

		azure hdinsight cluster delete <ClusterName>

###<a id="powershell"></a> Usando o PowerShell do Azure
O PowerShell do Azure é um ambiente de script poderoso que você pode usar para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Azure. Esta seção fornece instruções sobre como provisionar um cluster HDInsight usando o PowerShell do Azure. Para obter informações sobre como configurar uma estação de trabalho para executar cmdlets do PowerShell do HDInsight, consulte [Instalar e configurar o PowerShell do Azure](install-configure-powershell.md). Para obter mais informações sobre como usar o PowerShell do Azure com o HDInsight, consulte [Administrar o HDInsight usando o PowerShell](hdinsight-administer-use-powershell.md). Para obter a lista de cmdlets do Windows PowerShell do HDInsight, consulte [Referência de cmdlets do HDInsight](http://msdn.microsoft.com/library/windowsazure/dn479228.aspx).

Os procedimentos a seguir são necessários para provisionar um cluster HDInsight usando o PowerShell do Azure:

- Criar uma conta de Armazenamento do Azure
- Criar um contêiner de Blob do Azure
- Crie um cluster HDInsight

Você pode usar o console do Windows PowerShell ou o ISE (Ambiente de Script Integrado) do Windows PowerShell para executar os scripts.
 
O HDInsight usa um contêiner de Armazenamento de Blob do Azure como o sistema de arquivos padrão. Você precisa ter uma conta do Armazenamento do Azure e um contêiner de armazenamento para poder criar um cluster HDInsight. A conta do Armazenamento deve estar localizada no mesmo datacenter que o cluster HDInsight. Atualmente, apenas as regiões **Sudeste da Ásia**, **Norte da Europa**, **Leste dos EUA** e **Centro-Sul dos EUA** podem hospedar clusters HDInsight em Linux.

**Para se conectar à sua conta do Azure**

		Add-AzureAccount 

Será solicitado que você insira suas credenciais de conta do Azure.

**Para criar uma conta do Armazenamento do Azure**

		$storageAccountName = "<StorageAcccountName>"	# Provide a Storage account name
		$location = "<MicrosoftDataCenter>"				# For example, "West US"

		# Create an Azure Storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

Se você já tem uma conta do Armazenamento, mas não se lembra do nome da conta e da chave de conta, você pode usar os seguintes comandos do Windows PowerShell para recuperar as informações:

		# List Storage accounts for the current subscription
		Get-AzureStorageAccount

		# List the keys for a Storage account
		Get-AzureStorageKey "<StorageAccountName>"

**Para criar o contêiner de armazenamento de Blob do Azure**

		$storageAccountName = "<StorageAccountName>"	# Provide the Storage account name
		$containerName="<ContainerName>"				# Provide a container name

		# Create a storage context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName
		                                       -StorageAccountKey $storageAccountKey  

		# Create a Blob storage container
		New-AzureStorageContainer -Name $containerName -Context $destContext

Depois de preparar a conta de armazenamento e o contêiner de Blob, você estará pronto para criar um cluster.

**Para provisionar um cluster HDInsight**

Os dois parâmetros mais importantes que devem ser definidos para provisionar que clusters Linux são aquelas em que você especifica o tipo de sistema operacional e os detalhes do usuário SSH:

- Garanta que você especificou o parâmetro **-OSType** como **Linux**.
- Para usar SSH para sessões remotas nos clusters, você pode especificar a senha do usuário SSH ou a chave pública SSH. Se você especificar tanto a senha do usuário SSH, quanto a chave pública SSH, a chave será ignorada. Se quiser usar a chave SSH para sessões remotas, você deverá especificar uma senha SSH em branco quando for solicitada.


		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created

		$storageAccountName = "<AzureStorageAccountName>" # HDInsight cluster requires an existing Azure Storage account to be used as the default file system

		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster
        $containerName = $clusterName					  # Azure Blob container that is used as the default file system for the HDInsight cluster
		
		
		# Get the credentials for HTTP and SSH users for the cluster
		$clusterCredentials = Get-Credential            	  # Make sure you specify the username as "admin". This is the Hadoop user name and password for the cluster. You will use this account to connect to the cluster. 
        $sshCredentials = Get-Credential				      # SSH user name for the cluster. You will use this account to start an SSH session on the remote cluster.
		$sshPublicKey = "<SSH public key>"           

		# Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		
        # The location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $location = Get-AzureStorageAccount -StorageAccountName $storageAccountName | %{$_.Location} 

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Credential $clusterCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes -Version "3.2" -OSType Linux -SshCredential $sshCredentials -SshPublicKey $sshPublicKey


>[AZURE.NOTE]Os valores especificados para **$clusterCredentials** são usados para criar a conta de usuário do Hadoop para o cluster. Você usará essa conta para se conectar ao cluster. Os valores especificados para **$sshCredentials** são usados para criar o usuário SSH para o cluster. Você pode usar essa conta para iniciar uma sessão SSH remota no cluster e executar trabalhos. Se você usar a opção Criação Rápida no portal do Azure para provisionar um cluster, o nome de usuário do Hadoop padrão será "admin" e o nome de usuário SSH padrão será "hdiuser".

Pode levar vários minutos para que o provisionamento do cluster seja concluído.

![HDI.CLI.Provision][image-hdi-ps-provision]

**Para provisionar um cluster HDInsight usando as opções de configuração personalizadas**

Ao provisionar um cluster, você pode usar outras opções de configuração, como conectar-se a mais de um contêiner de armazenamento de Blob do Azure ou usar um banco de dados SQL do Azure para os metastores do Hive e do Oozie. Isso permite que você separe o tempo de vida de seus dados e metadados do tempo de vida do cluster.

Os dois parâmetros mais importantes que devem ser definidos para provisionar que clusters Linux são aquelas em que você especifica o tipo de sistema operacional e os detalhes do usuário SSH:

- Garanta que você especificou o parâmetro **-OSType** como **Linux**.
- Para usar SSH para sessões remotas nos clusters, você pode especificar a senha do usuário SSH ou a chave pública SSH. Se você especificar tanto a senha do usuário SSH, quanto a chave pública SSH, a chave será ignorada. Se quiser usar a chave SSH para sessões remotas, você deverá especificar uma senha SSH em branco quando for solicitada.


		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created
		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster
        		
		# Get the credentials for HTTP user, SSH user, and Hive/Oozie metastore databases for the cluster
		$clusterCredentials = Get-Credential            	  # Make sure you specify the username as "admin". This is the Hadoop user name and password for the cluster. You will use this account to connect to the cluster. 
        $sshCredentials = Get-Credential				      # SSH user name for the cluster. You will use this account to start an SSH session on the remote cluster.
		$sshPublicKey = "<SSH public key>"
		$oozieCreds = Get-Credential -Message "Oozie metastore"
		$hiveCreds = Get-Credential -Message "Hive metastore"

		$storageAccountName_Default = "<DefaultFileSystemStorageAccountName>"
		$containerName_Default = $clusterName
		$storageAccountName_Add1 = "<AdditionalStorageAccountName>"

		$hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
		$hiveSQLDatabaseName = "<SQLDatabaseDatabaseNameForHiveMetastore>"
		$oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>"
		$oozieSQLDatabaseName = "<SQLDatabaseDatabaseNameForOozieMetastore>"

		# Get the Storage account keys
		Select-AzureSubscription $subscriptionName
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }

		# The location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $location = Get-AzureStorageAccount -StorageAccountName $storageAccountName_Default | %{$_.Location}

		# Create a cluster configuration file
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes | Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default | Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 | Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore | Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore
		        
		# Create the cluster
		New-AzureHDInsightCluster -Name $clusterName -Config $config -Credential $clusterCredentials -Location $clusterLocation -Version "3.2" -OSType Linux -SshCredential $sshCredentials -SshPublicKey $sshPublicKey

>[AZURE.NOTE]O banco de dados SQL do Azure usado para o metastore deve permitir a conectividade com outros serviços do Azure, incluindo o Azure HDInsight. No painel do banco de dados SQL do Azure, no lado direito, clique no nome do servidor. Esse é o servidor no qual a instância do banco de dados SQL está sendo executada. Quando você estiver na exibição do servidor, clique em **Configurar** e depois em **Serviços do Azure**; clique em **Sim** e depois em **Salvar**.

Pode levar vários minutos para que o provisionamento do cluster seja concluído.

![HDI.CLI.Provision][image-hdi-ps-config-provision]

###<a id="sdk"></a> Usando o SDK do .NET do HDInsight
O SDK do .NET do HDInsight fornece bibliotecas de cliente .NET que facilitam o trabalho com o HDInsight em um aplicativo .NET Framework.

Os procedimentos a seguir devem ser executados para provisionar um cluster HDInsight no Linux usando o SDK:

- Instalar o SDK do .NET do HDInsight
- Crie um certificado autoassinado
- Criar um aplicativo de console
- Executar o aplicativo


**Para instalar o SDK do .NET do HDInsight**

Você pode instalar a compilação publicada mais recentemente do SDK no [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). As instruções serão mostradas no próximo procedimento.

**Para criar um certificado autoassinado**

Crie um certificado autoassinado, instale-o em sua estação de trabalho e o carregue para sua assinatura do Azure. Para obter instruções, consulte [Criar um certificado autoassinado](http://go.microsoft.com/fwlink/?LinkId=511138).


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

5. No menu **Ferramentas**, clique em **Gerenciador de Pacotes NuGet** e em **Console do Gerenciador de Pacotes**.

6. Execute o seguinte comando no console para instalar os pacotes:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Esse comando adiciona bibliotecas .NET e referências a elas no projeto atual do Visual Studio.

7. No Gerenciador de Soluções, clique duas vezes em **Program.cs** para abri-lo.

8. Adicione o seguinte usando as instruções na parte superior do arquivo :

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;


9. Na função **Main ()**, copie e cole o seguinte código:

	> [AZURE.NOTE]Confira se o local que você especificou é um destes: as regiões **Sudeste da Ásia**, **Norte da Europa**, **Leste dos EUA** e **Centro-Sul dos EUA** podem hospedar clusters HDInsight em Linux. A conta de armazenamento que você forneceu também deve estar na mesma região.

        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";		# Make sure you specify this username as "admin"
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;
		string version = "<version>";
        string sshusername = "<ssh user name>";
        string sshpublickey = "<ssh public key>;
		

		// If required, provide details of the Hive and Oozie metastore that you want to configure. ServerName is the name of the server on which the SQL databases are provisioned. HiveStoreSqlDatabaseName and OozieStoreSqlDatabaseName are the names of databases created on the server. You can also use the same database for both Hive and Oozie metastores

		Metastore hiveMetastore = new Metastore("<ServerName>.database.windows.net", "<HiveStoreSqlDatabaseName>", "<SqlDatabaseUser>", "<SqlDatabasePassword>");
        Metastore oozieMetastore = new Metastore("<ServerName>.database.windows.net", "<OozieStoreSqlDatabaseName>", "<SqlDatabaseUser>", "<SqlDatabasePassword>");

        // Get the certificate object from certificate store by using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply the cluster information
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
			HiveMetastore = hiveMetastore,		//Only if you created a hivemetastore object earlier
            OozieMetastore = oozieMetastore		//Only if you created an ooziemetastore object earlier
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

10. Substitua as variáveis no início da função **Main()**.

**Para executar o aplicativo**

Enquanto o aplicativo estiver aberto no Visual Studio, pressione **F5** para executar o aplicativo. Uma janela de console deve ser aberta e exibir o status do aplicativo. Pode levar vários minutos para criar um cluster HDInsight.



##<a id="nextsteps"></a> Próximas etapas
Neste artigo, você aprendeu várias maneiras de provisionar um cluster HDInsight Hadoop no Linux. Para saber mais, consulte os seguintes artigos:

- [Trabalhando com o HDInsight no Linux](hdinsight-hadoop-linux-information.md): conheça as nuances de trabalhar com um cluster HDInsight no Linux.
- [Gerenciar clusters HDInsight usando o Ambari](hdinsight-hadoop-manage-ambari.md): aprenda a monitorar e gerenciar seu Hadoop baseado em Linux no cluster HDInsight usando Web do Ambari ou a API REST do Ambari. 
- [Usar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md): saiba mais sobre as diferentes maneiras de executar trabalhos do MapReduce em um cluster.
- [Usar o Hive com o HDInsight](hdinsight-use-hive.md): saiba mais sobre as diferentes maneiras de executar uma consulta Hive em um cluster.
- [Usar o Pig com o HDInsight](hdinsight-use-pig.md): saiba mais sobre as diferentes maneiras de executar um trabalho do Pig em um cluster.
- [Usar o armazenamento de Blob do Azure com o HDInsight](hdinsight-use-blob-storage.md): saiba como o HDInsight usa o armazenamento de Blob do Azure para armazenar dados de clusters HDInsight.
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
[image-cli-clusterlisting]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIListClusters.png "Listar e mostrar clusters"

[image-hdi-ps-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.provision.png
[image-hdi-ps-config-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.config.provision.png

[img-hdi-cluster]: ./media/hdinsight-provision-clusters/HDI.Cluster.png

[89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Use o Sqoop com o HDInsight"

<!--HONumber=54-->