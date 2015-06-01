<properties 
	pageTitle="Usando o PowerShell do Azure com o Armazenamento do Azure" 
	description="Saiba como usar o PowerShell do Azure para Armazenamento do Azure" 
	services="storage" 
	documentationCenter="na" 
	authors="Selcin" 
	manager="adinah"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/27/2015" 
	ms.author="selcint"/>

# Usando o PowerShell do Azure com o Armazenamento do Azure 

Bem-vindo ao guia do PowerShell do Azure para Armazenamento do Azure!

Você pode usar diversas ferramentas de desenvolvimento e administração para executar uma variedade de tarefas no armazenamento do Azure, como o [Portal de gerenciamento](, a http://manage.windowsazure.com/)[Biblioteca de cliente de armazenamento](http://msdn.microsoft.com/library/azure/dn261237.aspx), [API REST](http://msdn.microsoft.com/library/azure/dd179355.aspx) e [PowerShell do Azure](http://msdn.microsoft.com/library/azure/jj156055.aspx). 

Neste guia, exploraremos como usar o PowerShell do Azure para executar tarefas comuns no Armazenamento do Azure. O PowerShell no Azure é um módulo que fornece cmdlets para gerenciar o Azure por meio do Windows PowerShell. Ele é um shell de linha de comando baseado em tarefa e linguagem de script criado especialmente para administração do sistema. Com o PowerShell, você pode facilmente controlar e automatizar a administração dos seus aplicativos e serviços do Azure. Na maioria dos casos, é possível usar os cmdlets para executar as mesmas tarefas que você pode executar por meio do Portal de Gerenciamento do Azure. 

Este guia pressupõe que você tenha experiência anterior usando o [Armazenamento do Azure](http://azure.microsoft.com/documentation/services/storage/) e [Windows PowerShell](http://technet.microsoft.com/library/bb978526.aspx). Este guia fornece vários scripts para demonstrar o uso do PowerShell com o Armazenamento do Azure. Você deve atualizar as variáveis de script com base em sua configuração antes de executar cada script.

A primeira seção deste guia fornece uma visão rápida no Armazenamento do Azure e do PowerShell. Para obter informações e instruções detalhadas, comece com os [Pré-requisitos para usar o PowerShell do Azure com o armazenamento do Azure](#pre).

## Sumário
  
 - [Introdução ao Armazenamento do Azure e ao PowerShell em 5 minutos][]
 - [Pré-requisitos para usar o PowerShell do Azure com o armazenamento do Azure][]
 - [Como gerenciar contas de armazenamento no Azure][]
	 - [Como definir uma assinatura padrão do Azure][]
	 - [Como criar uma nova conta de armazenamento do Azure][]
	 - [Como configurar uma conta padrão de armazenamento do Azure][]
	 - [Como listar todas as contas de armazenamento do Azure em uma assinatura][]
	 - [Como criar um contexto de armazenamento do Azure][]
 - [Como gerenciar blobs do Azure e instantâneos de blob][]
	 - [Como criar um contêiner][]
	 - [Como carregar um blob para um contêiner][]
	 - [Como baixar blobs de um contêiner][]
	 - [Como copiar blobs de um contêiner de armazenamento para outro][]
	 - [Como excluir um blob][]
	 - [Como gerenciar instantâneos de blob do Azure][]
	 	- [Como criar um instantâneo de blob][]
	 	- [Como listar instantâneos de um blob][]
	 	- [Como copiar um instantâneo de um blob][]
 - [Como gerenciar tabelas do Azure e entidades de tabela][]
	 - [Como criar uma tabela][]
	 - [Como recuperar uma tabela][]
	 - [Como excluir uma tabela][]
	 - [Como gerenciar entidades de tabela][]
	 	- [Como adicionar entidades de tabela][]
	 	- [Como consultar entidades de tabela][]
	 	- [Como excluir entidades de tabela][]
 - [Como gerenciar filas do Azure e mensagens da fila][]
	 - [Como criar uma fila][]
	 - [Como recuperar uma fila][]
	 - [Como excluir uma fila][]
	 - [Como gerenciar mensagens da fila][]
	 	- [Como inserir uma mensagem em uma fila][]
	 	- [Como retirar a próxima mensagem da fila][]
 - [Como gerenciar arquivos e compartilhamentos de arquivos do Azure][]
 - [Como definir e consultar análises de armazenamento][]
 - [Como gerenciar a Assinatura de Acesso Compartilhado (SAS) e a Política de Acesso Armazenada][]
 - [Como usar o Armazenamento do Azure para o governo dos EUA e o Azure China][]
 - [Próximas etapas][]
 
## <a name="getstart"></a>Introdução ao Armazenamento do Azure e ao PowerShell em 5 minutos
Esta seção mostra como acessar o Armazenamento do Azure por meio do PowerShell em 5 minutos. 

**Novo no Azure:** obtenha uma assinatura do Microsoft Azure e uma conta da Microsoft associada a esta assinatura. Para obter informações sobre as opções de compra do Azure, consulte [avaliação gratuita](, http://azure.microsoft.com/pricing/free-trial/)[opções de compra](http://azure.microsoft.com/pricing/purchase-options/) e [ofertas para membros](http://azure.microsoft.com/pricing/member-offers/) (para membros de outros programas da Microsoft, MSDN, Microsoft Partner Network e BizSpark).

**Confuso sobre contas e assinaturas do Microsoft Azure:** consulte [Gerenciar contas, assinaturas e funções administrativas](https://msdn.microsoft.com/library/azure/hh531793.aspx).

**Depois de criar uma assinatura e conta do Microsoft Azure:**

1.	Baixe e instale o [PowerShell do Azure](http://go.microsoft.com/?linkid=9811175&clcid=0x409) em seu computador local.
2.	Inicie o ISE (ambiente de script integrado) do Windows PowerShell: No computador local, vá até o menu **Iniciar**. Digite **Ferramentas Administrativas** e clique em para executá-las. Na janela **Ferramentas Administrativas**, clique com botão direito em **ISE do Windows PowerShell** e clique em **Executar como administrador**. 
3.	No **ISE do Windows PowerShell**, clique em **Arquivo** > **Novo** para criar um novo arquivo de script. 
4.	Agora, você terá um script simples que mostra os comandos básicos do PowerShell para acessar o armazenamento do Azure. O script primeiro solicitará suas credenciais da conta do Azure para adicioná-la ao ambiente local do PowerShell. Depois, o script definirá a assinatura padrão do Azure e criará uma nova conta de armazenamento no Azure. Em seguida, o script criará um novo contêiner nessa nova conta de armazenamento e carregará um arquivo de imagem existente (blob) para esse contêiner. Depois que o script listar todos os blobs nesse contêiner, ele criará um novo diretório de destino no computador local e baixará o arquivo de imagem.
5.	Na seção de código a seguir, selecione o script entre os comentários **#begin** e **#end**. Pressione CTRL + C para copiá-lo para a área de transferência. 

    	#begin
    	# Update with the name of your subscription.
    	$SubscriptionName="YourSubscriptionName"
    
    	# Give a name to your new storage account. It must be lowercase! 
    	$StorageAccountName="yourstorageaccountname"
    
    	# Choose "West US" as an example.
    	$Location = "West US"
    
    	# Give a name to your new container.
    	$ContainerName = "imagecontainer"
    
    	# Have an image file and a source directory in your local computer.
    	$ImageToUpload = "C:\Images\HelloWorld.png"
    
    	# A destination directory in your local computer.
    	$DestinationFolder = "C:\DownloadImages"
    
    	# Add your Azure account to the local PowerShell environment.
    	Add-AzureAccount
    
    	# Set a default Azure subscription.
    	Select-AzureSubscription -SubscriptionName $SubscriptionName -Default
    
    	# Create a new storage account.
    	New-AzureStorageAccount -StorageAccountName $StorageAccountName -Location $Location
    
    	# Set a default storage account.
    	Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName
    
    	# Create a new container.
    	New-AzureStorageContainer -Name $ContainerName -Permission Off
    
    	# Upload a blob into a container.
    	Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload 
    
    	# List all blobs in a container.
    	Get-AzureStorageBlob -Container $ContainerName
    
    	# Download blobs from the container:
    	# Get a reference to a list of all blobs in a container.
    	$blobs = Get-AzureStorageBlob -Container $ContainerName
    
    	# Create the destination directory.
    	New-Item -Path $DestinationFolder -ItemType Directory -Force  
    
    	# Download blobs into the local destination directory.
    	$blobs | Get-AzureStorageBlobContent -Destination $DestinationFolder
    	#end
    
5.	Em **ISE do Windows PowerShell**, pressione CTRL + V para copiar o script. Clique em **Arquivo** > **Salvar**. Na janela de diálogo **Salvar como**, digite o nome do arquivo de script, como "mystoragescript". Clique em **Salvar**.

6.	Agora, você precisa atualizar as variáveis de script com base nas suas configurações. Você deve atualizar a variável **$SubscriptionName** com sua própria assinatura. Você pode manter as outras variáveis conforme especificado no script ou atualizá-las como desejar.

	- **$SubscriptionName:** você deve atualizar essa variável com seu próprio nome de assinatura. Siga uma das três maneiras a seguir para localizar o nome de sua assinatura:
	
		a. No **ISE do Windows PowerShell**, clique em **Arquivo** > **Novo** para criar um novo arquivo de script. Copie o seguinte script para o novo arquivo de script e clique em **Depurar** > **Executar**. O script a seguir primeiro solicitará suas credenciais de conta do Azure para adicioná-la ao ambiente do PowerShell local e, em seguida, mostrará todas as assinaturas que estão conectadas à sessão do PowerShell local. Anote o nome da assinatura que você deseja usar e siga este tutorial: 
	 
    		Add-AzureAccount 
       		Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName 


		b. Atualmente, o Azure dá suporte a dois portais: o [Portal de Gerenciamento](https://manage.windowsazure.com/) atual e o [Portal de Visualização do Azure](https://portal.azure.com/). Se você entrar no [Portal de Gerenciamento do Azure](https://portal.azure.com/) atual, role para baixo e clique em **Configurações** no lado esquerdo do portal. Clique em **Assinaturas**. Copie o nome da assinatura que você deseja usar ao executar os scripts fornecidos neste guia. Consulte a captura de tela a seguir como exemplo.

		![Azure Management Portal][Image1]

		c. Se você entrar no [Portal de Visualização do Azure](https://portal.azure.com/), no menu de Hub do lado esquerdo, clique em **Procurar**. Em seguida, clique em **Tudo** e em **Assinaturas**. Copie o nome da assinatura que você deseja usar ao executar os scripts fornecidos neste guia. Consulte a captura de tela a seguir como exemplo.

		![Azure Preview Portal][Image2]
 

	- **$StorageAccountName:** use o nome fornecido no script ou insira um novo nome para sua conta de armazenamento. **Importante:** o nome da conta de armazenamento deve ser exclusivo no Azure. Ele também deve ser em minúsculas.
	 
	- **$Location:** use o "Oeste dos EUA" fornecido no script ou escolha outros locais do Azure, como Leste dos EUA, Norte da Europa e assim por diante.
	  
	- **$ContainerName:** use o nome fornecido no script ou insira um novo nome para seu contêiner.
	
	- **$ImageToUpload:** insira um caminho para uma imagem em seu computador local, como: "C:\Images\HelloWorld.png".
	
	- **$DestinationFolder:** insira um caminho para um diretório local para armazenar os arquivos baixados do armazenamento do Azure, como: "C:\DownloadImages".

7.	Depois de atualizar as variáveis de script no arquivo "mystoragescript.ps1", clique em **Arquivo** > **Salvar**. Em seguida, clique em **Depurar** > **Executar** ou pressione **F5** para executar o script.  

Depois que o script é executado, você deve ter uma pasta de destino que inclui o arquivo de imagem baixada. A captura de tela a seguir mostra um exemplo de saída:

![Download Blobs][Image3]


> [AZURE.NOTE] A seção "Introdução ao armazenamento do Azure e ao PowerShell em 5 minutos" forneceu uma rápida introdução sobre como usar o PowerShell do Azure com o Armazenamento do Azure. Para obter informações e instruções detalhadas, recomendamos que você leia as seções a seguir.

## <a name="pre"></a>Pré-requisitos para usar o PowerShell do Azure com o armazenamento do Azure
Você precisa de uma assinatura e conta do Azure para executar os cmdlets do PowerShell fornecidos neste guia. Para obter opções de assinatura do Azure disponíveis, visite [Introdução ao Azure](http://azure.microsoft.com/pricing/free-trial/).

O PowerShell no Azure é um módulo que fornece cmdlets para gerenciar o Azure por meio do Windows PowerShell. Para obter informações sobre como instalar e configurar o Azure PowerShell, consulte [Como instalar e configurar o Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/). É recomendável baixar e instalar ou atualizar para o módulo PowerShell do Azure mais recente antes de usar este guia. 

Você pode executar os cmdlets no console do PowerShell do Azure, o console do Windows PowerShell padrão ou o ISE (Ambiente de Script Integrado) do Windows PowerShell. Por exemplo, para abrir um **console do PowerShell do Azure**, vá para o menu Iniciar, digite Microsoft Azure PowerShell, clique com o botão direito e clique em Executar como administrador. Para abrir o **ISE do Windows PowerShell**, vá para o menu Iniciar, digite ferramentas administrativas e clique para executá-lo. Na janela Ferramentas Administrativas, clique com botão direito em ISE do Windows PowerShell e clique em Executar como administrador. Para ver opções de configuração e instalação detalhadas, consulte a seção Como instalar o Azure PowerShell no tutorial [Como instalar e configurar o PowerShell do Azure](http://azure.microsoft.com/documentation/articles/install-configure-powershell/).

## <a name="manageaccount"></a>Como gerenciar contas de armazenamento no Azure
### <a name="setdefsub"></a>Como definir uma assinatura padrão do Azure
Para gerenciar o armazenamento do Azure usando o PowerShell do Azure, você precisa autenticar seu ambiente de cliente com o Azure por meio de autenticação do Active Directory do Azure ou autenticação baseada em certificado. Para obter informações detalhadas, consulte a seção [Como: Conecte-se à sua assinatura](http://azure.microsoft.com/documentation/articles/install-configure-powershell/#Connect) no tutorial [Como instalar e configurar o Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/). Este guia usa a autenticação do Active Directory do Azure.

1.	No console do PowerShell do Azure ou no ISE do Windows PowerShell, digite o seguinte comando para adicionar a conta do Azure ao ambiente do PowerShell local:

    `Add-AzureAccount`

2.	Na janela "Entrar no Microsoft Azure", digite o endereço de email e a senha associados à sua conta. O Azure autentica e salva as informações de credenciais e, em seguida, fecha a janela.

3.	Em seguida, execute o seguinte comando para exibir as contas do Azure no seu ambiente do PowerShell local e verifique se sua conta está listada:
 
	`Get-AzureAccount`
  
4.	Em seguida, execute o seguinte cmdlet para exibir todas as assinaturas que estão conectadas à sessão do PowerShell local e verifique se sua assinatura está listada:

	`Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName`
 
5.	Para definir uma assinatura padrão do Azure, execute o cmdlet Select-AzureSubscription:
 
	    $SubscriptionName = 'Your subscription Name'
    	Select-AzureSubscription -SubscriptionName $SubscriptionName -Default

6.	Verifique o nome da assinatura padrão executando o cmdlet Get-AzureSubscription:

	`Get-AzureSubscription -Default`

7.	Para ver todos os cmdlets do PowerShell disponíveis para o Armazenamento do Azure, execute:

	`Get-Command -Module Azure -Noun *Storage*`

### <a name="createaccount"></a>Como criar uma nova conta de armazenamento do Azure
Você precisa de uma conta de armazenamento para usar o Armazenamento do Azure. Depois de configurar seu computador para se conectar à sua assinatura, você pode criar uma nova conta de Armazenamento do Azure. 

1.	Execute o cmdlet Get-AzureLocation para localizar todos os locais de datacenters disponíveis:

    `Get-AzureLocation | format-Table -Property Name, AvailableServices, StorageAccountTypes`

2.	Em seguida, execute o cmdlet New-AzureStorageAccount para criar uma nova conta de armazenamento. O exemplo a seguir cria uma nova conta de armazenamento no data center "Oeste dos Estados Unidos".
  
    	$location = "West US"
	    $StorageAccountName = "yourstorageaccount"
	    New-AzureStorageAccount -StorageAccountName $StorageAccountName -Location $location

Para obter informações detalhadas, consulte [Sobre contas de Armazenamento do Azure](http://azure.microsoft.com/documentation/articles/storage-whatis-account/).

> [AZURE.IMPORTANTE] O nome da conta de armazenamento é exclusivo dentro do Azure e deve estar em minúsculas. Para ver as convenções e restrições de nomenclatura, consulte [Sobre contas de Armazenamento do Azure](, http://azure.microsoft.com/documentation/articles/storage-whatis-account/)[Nomeando e referenciando contêineres, blobs e metadados](http://msdn.microsoft.com/library/azure/dd135715.aspx) e [Criar conta de armazenamento](http://msdn.microsoft.com/library/azure/hh264518.aspx).

### <a name="defaultaccount"></a>Como configurar uma conta padrão de armazenamento do Azure
Você pode ter várias contas de armazenamento na sua assinatura. Você pode escolher uma delas e defini-la como a conta de armazenamento padrão para todos os comandos de armazenamento na mesma sessão do PowerShell. Isso permite que você execute os comandos de armazenamento do Azure PowerShell sem especificar explicitamente o contexto de armazenamento. 

1.	Para definir uma conta de armazenamento padrão para a sua assinatura, você pode executar o cmdlet Set-AzureSubscription. 

		$SubscriptionName = "Your subscription name" 
     	$StorageAccountName = "yourstorageaccount"  
    	Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName 

2.	Em seguida, execute o cmdlet Get-AzureSubscription para verificar se a conta de armazenamento está associada à sua conta de assinatura padrão. Esse comando retorna as propriedades de assinatura da assinatura atual, incluindo sua conta de armazenamento atual.

	    Get-AzureSubscription -Current

### <a name="listaccounts"></a>Como listar todas as contas de armazenamento do Azure em uma assinatura
Cada assinatura do Azure pode ter até 100 contas de armazenamento. Para obter as informações mais atualizadas sobre os limites, consulte [Assinatura do Azure e limites de serviço, cotas e restrições](http://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/).

Execute o seguinte cmdlet para descobrir o nome e o status das contas de armazenamento na assinatura atual:

    Get-AzureStorageAccount | Format-Table -Property StorageAccountName, Location, AccountType, StorageAccountStatus

### <a name="createctx"></a>Como criar um contexto de armazenamento do Azure
O contexto de Armazenamento do Azure é um objeto no PowerShell para encapsular as credenciais de armazenamento. Usar um contexto de armazenamento enquanto executa qualquer cmdlet subsequente permite autenticar a solicitação sem especificar a conta de armazenamento e sua chave de acesso explicitamente. Você pode criar um contexto de armazenamento de várias maneiras, como usando a chave de acesso e do nome da conta de armazenamento, o token de SAS (assinatura de acesso compartilhado), cadeia de conexão ou anonimamente. Para obter mais informações, consulte [New-AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx).  

Use um dos três procedimentos a seguir para criar um contexto de armazenamento:

- Execute o cmdlet [Get-AzureStorageKey](http://msdn.microsoft.com/library/azure/dn495235.aspx) para descobrir a chave de acesso de armazenamento principal para sua conta de armazenamento do Azure. Em seguida, chame o cmdlet [New-AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx) para criar um contexto de armazenamento:

    	$StorageAccountName = "yourstorageaccount"
    	$StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    	$Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary


- Gere um token de assinatura de acesso compartilhado para um contêiner de armazenamento do Azure e use-o para criar um contexto de armazenamento:

    	$sasToken = New-AzureStorageContainerSASToken -Container abc -Permission rl
    	$Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -SasToken $sasToken

	Para obter mais informações, consulte [New-AzureStorageContainerSASToken](http://msdn.microsoft.com/library/azure/dn806416.aspx) e [Assinaturas de acesso compartilhado, parte 1: Noções básicas sobre o modelo SAS](http://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).

- Em alguns casos, você talvez queira especificar os pontos de extremidade de serviço ao criar um novo contexto de armazenamento. Isso pode ser necessário quando você registrou um nome de domínio personalizado para sua conta de armazenamento com o serviço Blob ou se desejar usar uma assinatura de acesso compartilhado para acessar recursos de armazenamento. Defina os pontos de extremidade do serviço em uma cadeia de conexão e use-a para criar um novo contexto de armazenamento, conforme mostrado abaixo:

    	$ConnectionString = "DefaultEndpointsProtocol=http;BlobEndpoint=<blobEndpoint>;QueueEndpoint=<QueueEndpoint>;TableEndpoint=<TableEndpoint>;AccountName=<AccountName>;AccountKey=<AccountKey>"
    	$Ctx = New-AzureStorageContext -ConnectionString $ConnectionString

Para obter mais informações sobre como configurar uma cadeia de conexão, consulte [Configurando cadeias de conexão](http://msdn.microsoft.com/library/azure/ee758697.aspx).

Agora você configurou o seu computador e sabe como gerenciar assinaturas e contas de armazenamento usando o PowerShell do Azure. Vá para a próxima seção para aprender a gerenciar blobs do Azure e instantâneos de blob.

## <a name="manageblobs"></a>Como gerenciar blobs do Azure e instantâneos de blob
A Armazenamento de Blob do Azure é um serviço para armazenar grandes quantidades de dados não estruturados, como texto ou dados binários, que podem ser acessados de qualquer lugar do mundo por meio de HTTP ou HTTPS. Esta seção pressupõe que você já esteja familiarizado com os conceitos do serviço de Armazenamento de Blob do Azure. Para obter informações detalhadas, consulte [Como usar o Armazenamento de Blob no .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs) e [Conceitos do serviço Blob](http://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="container"></a>Como criar um contêiner
Todos os blobs no armazenamento do Azure devem residir em um contêiner. Você pode criar um contêiner privado usando o cmdlet New-AzureStorageContainer:

    $StorageContainerName = "yourcontainername"
    New-AzureStorageContainer -Name $StorageContainerName -Permission Off

> [AZURE.NOTE] Há três níveis de acesso de leitura anônima: **Desativado**, **Blob** e **Contêiner**. Para evitar o acesso anônimo a blobs, defina o parâmetro de permissão como **Desativado**. Por padrão, o novo contêiner é privado e pode ser acessado apenas pelo proprietário da conta. Para permitir acesso de leitura público anônimo para recursos de blob, mas não aos metadados do contêiner ou à lista de blobs no contêiner, defina o parâmetro de permissão como **Blob**. Para permitir acesso de leitura público completo para recursos, metadados do contêiner e a lista de blobs no contêiner de blob, defina o parâmetro de permissão como **Contêiner**. Para obter mais informações, consulte a enumeração [BlobContainerPublicAccessType](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storageclient.blobcontainerpublicaccesstype(v=azure.95).aspx).

### <a name="uploadblob"></a>Como carregar um blob para um contêiner
O Armazenamento de Blob do Azure dá suporte a blobs de blocos e a blobs de páginas. Para obter mais informações, consulte [Noções básicas sobre blobs de bloco e blobs de página](http://msdn.microsoft.com/library/azure/ee691964.aspx).

Para carregar blobs em um contêiner, você pode usar o cmdlet [Set-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx). Por padrão, esse comando carrega os arquivos locais para um blob de blocos. Para especificar o tipo do blob, você pode usar o parâmetro -BlobType. 

O exemplo a seguir executa o cmdlet [Get-ChildItem](http://technet.microsoft.com/library/hh849800.aspx) para obter todos os arquivos na pasta especificada e os passa para o próximo cmdlet usando o operador de pipeline. O cmdlet [Set-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx) carrega os arquivos locais para o contêiner:

    Get-ChildItem -Path C:\Images* | Set-AzureStorageBlobContent -Container "yourcontainername"

### <a name="downblob"></a>Como baixar blobs de um contêiner
O exemplo a seguir demonstra como baixar blobs de um contêiner. O exemplo primeiro estabelece uma conexão com o armazenamento do Azure usando o contexto de conta de armazenamento, que inclui o nome da conta de armazenamento e sua chave de acesso primário. Em seguida, o exemplo recupera uma referência de blob usando o cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx). Em seguida, o exemplo usa o cmdlet [Get-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806418.aspx) para baixar blobs para a pasta de destino local. 

    #Define the variables.
	ContainerName = "yourcontainername" 
    $DestinationFolder = "C:\DownloadImages" 
    
    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #List all blobs in a container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx
    
    #Download blobs from a container. 
    New-Item -Path $DestinationFolder -ItemType Directory -Force 
    $blobs | Get-AzureStorageBlobContent -Destination $DestinationFolder -Context $Ctx

### <a name="copyblob"></a>Como copiar blobs de um contêiner de armazenamento para outro
Você pode copiar blobs em regiões e contas de armazenamento de forma assíncrona. O exemplo a seguir demonstra como copiar blobs de um contêiner de armazenamento para outro em duas contas de armazenamento diferentes. O exemplo primeiro define variáveis para contas de armazenamento de origem e de destino e, em seguida, cria um contexto de armazenamento para cada conta. Em seguida, o exemplo copia blobs do contêiner de origem para o contêiner de destino usando o cmdlet [Start-AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx). O exemplo supõe que as contas de armazenamento de origem e de destino e os contêineres já existem. 

    #Define the source storage account and context.
    $SourceStorageAccountName = "yoursourcestorageaccount"
    $SourceStorageAccountKey = "Storage key for yoursourcestorageaccount"
    $SrcContainerName = "yoursrccontainername"
    $SourceContext = New-AzureStorageContext -StorageAccountName $SourceStorageAccountName -StorageAccountKey $SourceStorageAccountKey
    
    #Define the destination storage account and context.
    $DestStorageAccountName = "yourdeststorageaccount"
    $DestStorageAccountKey = "Storage key for yourdeststorageaccount"
    $DestContainerName = "destcontainername"
    $DestContext = New-AzureStorageContext -StorageAccountName $DestStorageAccountName -StorageAccountKey $DestStorageAccountKey
    
    #Get a reference to blobs in the source container.
    $blobs = Get-AzureStorageBlob -Container $SrcContainerName -Context $SourceContext
    
    #Copy blobs from one container to another.
    $blobs| Start-AzureStorageBlobCopy -DestContainer $DestContainerName -DestContext $DestContext

Observe que este exemplo executa uma cópia assíncrona. Você pode monitorar o status de cada cópia executando o cmdlet [Get-AzureStorageBlobCopyState](http://msdn.microsoft.com/library/azure/dn806406.aspx).

### <a name="deleteblob"></a>Como excluir um blob
Para excluir um blob, primeiro obtenha uma referência de blob e, em seguida, chame o cmdlet Remove-AzureStorageBlob. O exemplo a seguir exclui todos os blobs em um determinado contêiner. O exemplo primeiro define variáveis para uma conta de armazenamento e, em seguida, cria um contexto de armazenamento. Em seguida, o exemplo recupera uma referência de blob usando o cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) e executa o cmdlet [Remove-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806399.aspx) para remover os blobs de um contêiner no armazenamento do Azure. 

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "containername"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #Get a reference to all the blobs in the container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx
    
    #Delete blobs in a specified container.
    $blobs| Remove-AzureStorageBlob 

## <a name="manageshots"></a>Como gerenciar instantâneos de blob do Azure
O Azure permite criar um instantâneo de um blob. Um instantâneo é uma versão somente leitura de um blob capturada em um momento no tempo. Quando um instantâneo tiver sido criado, ele pode ser lido, copiado ou excluído, mas não modificado. Os instantâneos fornecem uma maneira de fazer backup de um blob da maneira como ele aparece em um momento específico. Para obter mais informações, consulte [Criar um instantâneo de um blob](http://msdn.microsoft.com/library/azure/hh488361.aspx).

### <a name="createshot"></a>Como criar um instantâneo de blob
Para criar um instantâneo de um blob, primeiro obtenha uma referência de blob e, em seguida, chame o método ICloudBlob.CreateSnapshot nele. O exemplo a seguir define primeiro as variáveis para uma conta de armazenamento e, em seguida, cria um contexto de armazenamento. Em seguida, o exemplo recupera uma referência de blob usando o cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) e executa o método [ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) para criar um instantâneo. 

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "yourcontainername"
    $BlobName = "yourblobname"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $ContainerName -Blob $BlobName
    
    #Create a snapshot of the blob.
    $snap = $blob.ICloudBlob.CreateSnapshot() 

### <a name="listshot"></a>Como listar instantâneos de um blob
Você pode criar tantos snapshots quanto desejar para um blob. Você pode listar os instantâneos associados ao seu blob para rastrear seus instantâneos atuais. O exemplo a seguir usa um blob predefinido e chama o cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) para listar os instantâneos do blob.  

    #Define the blob name.
    $BlobName = "yourblobname"
    
    #List the snapshots of a blob.
    Get-AzureStorageBlob -Context $Ctx -Prefix $BlobName -Container $ContainerName  | Where-Object  { $_.ICloudBlob.IsSnapshot -and $_.Name -eq $BlobName }

### <a name="copyshot"></a>Como copiar um instantâneo de um blob
Você pode copiar um instantâneo de um blob para restaurar o instantâneo. Para obter informações detalhadas e restrições, consulte [Criar um instantâneo de um Blob](http://msdn.microsoft.com/library/azure/hh488361.aspx). O exemplo a seguir define primeiro as variáveis para uma conta de armazenamento e, em seguida, cria um contexto de armazenamento. Em seguida, o exemplo define as variáveis de nome de contêiner e blob. O exemplo recupera uma referência de blob usando o cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) e executa o método [ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) para criar um instantâneo. Em seguida, o exemplo executa o cmdlet [Start-AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx) para copiar o instantâneo de um blob usando o objeto ICloudBlob como o blob de origem. Certifique-se de atualizar as variáveis com base na sua configuração antes de executar o exemplo. Observe que o exemplo a seguir supõe que os contêineres de origem e destino, e o blob de origem, já existem. 

    #Defina a conta de armazenamento e o contexto.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #Define the variables.
    $SrcContainerName = "yoursourcecontainername"
    $DestContainerName = "yourdestcontainername"
    $SrcBlobName = "yourblobname"
    $DestBlobName = "CopyBlobName"
    
    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $SrcContainerName -Blob $SrcBlobName
    
    #Create a snapshot of a blob.
    $snap = $blob.ICloudBlob.CreateSnapshot() 
    
    #Copy the snapshot to another container.
    Start-AzureStorageBlobCopy -Context $Ctx -ICloudBlob $snap -DestBlob $DestBlobName -DestContainer $DestContainerName

Agora você aprendeu como gerenciar blobs do Azure e o instantâneos de blob com o Azure PowerShell. Vá para a próxima seção para aprender a gerenciar arquivos, filas e tabelas.

## <a name="managetables"></a>Como gerenciar tabelas do Azure e entidades de tabela
O serviço de armazenamento de tabela do Azure é um armazenamento de dados NoSQL, que pode ser usado para armazenar e consultar grandes conjuntos de dados estruturados e não relacionais. Os principais componentes do serviço são tabelas, entidades e propriedades. Uma tabela é uma coleção de entidades. Uma entidade é um conjunto de propriedades. Cada entidade pode ter até 252 propriedades, que são todas pares de nome-valor. Esta seção pressupõe que você já esteja familiarizado com os conceitos do serviço de Armazenamento de Tabela do Azure. Para obter informações detalhadas, consulte [Noções básicas sobre o modelo de dados do serviço Tabela](http://msdn.microsoft.com/library/azure/dd179338.aspx) e [Como usar o armazenamento de tabela no .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/).

Nas subseções a seguir, você aprenderá como gerenciar o serviço de armazenamento de tabela do Azure usando o PowerShell do Azure. Os cenários abordados incluem a **criação**, **exclusão** e **recuperação** de **tabelas**, bem como **adição**, **consulta** e **exclusão de entidades de tabela**.

### <a name="createtable"></a>Como criar uma tabela
Cada tabela deve residir em uma conta de armazenamento do Azure. O exemplo a seguir demonstra como criar uma tabela no armazenamento do Azure. O exemplo primeiro estabelece uma conexão com o armazenamento do Azure usando o contexto de conta de armazenamento, que inclui o nome da conta de armazenamento e sua chave de acesso. Em seguida, ele usa o cmdlet [New-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx) cmdlet para criar uma tabela no armazenamento do Azure. 

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey 
    
    #Create a new table.
    $tabName = "yourtablename"
    New-AzureStorageTable -Name $tabName -Context $Ctx 

### <a name="gettable"></a>Como recuperar uma tabela
Você pode consultar e recuperar uma ou todas as tabelas em uma conta de armazenamento. O exemplo a seguir demonstra como recuperar uma determinada tabela usando o cmdlet [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx).

    #Retrieve a table.
    $tabName = "yourtablename"
    Get-AzureStorageTable -Name $tabName -Context $Ctx 

Se você chamar o cmdlet Get-AzureStorageTable sem parâmetros, ele obtém todas as tabelas de armazenamento para uma conta de armazenamento.

### <a name="remtable"></a>Como excluir uma tabela
Você pode excluir uma tabela de uma conta de armazenamento usando o cmdlet [Remove-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806393.aspx).  

    #Delete a table.
    $tabName = "yourtablename"
    Remove-AzureStorageTable -Name $tabName -Context $Ctx 

### <a name="mngentity"></a>Como gerenciar entidades de tabela
Atualmente, o PowerShell do Azure não fornece cmdlets para gerenciar entidades de tabela diretamente. Para executar operações em entidades de tabela, você pode usar as classes fornecidas na [biblioteca de cliente de armazenamento do Azure para .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx). 

#### <a name="addentity"></a>Como adicionar entidades de tabela
Para adicionar uma entidade a uma tabela, primeiro crie um objeto que defina as propriedades da entidade. Uma entidade pode ter até 255 propriedades, incluindo três propriedades do sistema: **PartitionKey**, **RowKey** e **Timestamp**. Você é responsável por inserir e atualizar os valores de **PartitionKey** e **RowKey**. O servidor gerencia o valor de **Timestamp**, que não pode ser modificado. As **PartitionKey** e **RowKey** juntas identificam exclusivamente cada entidade dentro de uma tabela. 

-	**PartitionKey**: Determina a partição na qual a entidade está armazenada.
-	**RowKey**: Identifica exclusivamente a entidade na partição.

Você pode definir até 252 propriedades personalizadas para uma entidade. Para obter informações, consulte [Noções básicas sobre o modelo de dados do serviço Tabela](http://msdn.microsoft.com/library/azure/dd179338.aspx).

O exemplo a seguir demonstra como adicionar entidades a uma tabela. O exemplo mostra como recuperar a tabela de funcionários e adicionar várias entidades a ela. Primeiro, ele estabelece uma conexão com o armazenamento do Azure usando o contexto de conta de armazenamento, que inclui o nome da conta de armazenamento e sua chave de acesso primário. Em seguida, ele recupera a tabela em questão usando o cmdlet [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx). Se a tabela não existir, o cmdlet [New-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx) é usado para criar uma tabela no armazenamento do Azure. Em seguida, o exemplo define uma função personalizada Add-Entity para adicionar entidades à tabela especificando a partição de cada entidade e a chave de linha. A função Add-Entity chama o cmdlet [New-Object](http://technet.microsoft.com/library/hh849885.aspx) na classe [Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.dynamictableentity.aspx) para criar um objeto de entidade. Posteriormente, o exemplo chama o método [Microsoft.WindowsAzure.Storage.Table.TableOperation.Insert](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.insert.aspx) nesse objeto de entidade para adicioná-lo a uma tabela. 

    #Function Add-Entity: Adds an employee entity to a table.
    function Add-Entity()
    {
    param(
       $table, 
       [String]$partitionKey, 
       [String]$rowKey,
       [String]$name, 
       [Int]$id
    )
    
      $entity = New-Object Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity $partitionKey, $rowKey
      $entity.Properties.Add("Name", $name)
      $entity.Properties.Add("ID", $id)
    
      $result = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Insert($entity))
    }
    
    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey
    $TableName = "Employees"
    
    #Retrieve the table if it already exists.
    $table = Get-AzureStorageTable -Name $TableName -Context $Ctx -ErrorAction Ignore
    
    #Create a new table if it does not exist.
    if ($table -eq $null)
    {
       $table = New-AzureStorageTable -Name $TableName -Context $Ctx
    }
    
    #Add multiple entities to a table.
    Add-Entity $table "Partition1" "Row1" "Chris" 1 
    Add-Entity $table "Partition1" "Row2" "Jessie" 2 
    Add-Entity $table "Partition2" "Row1" "Christine" 3 
    Add-Entity $table "Partition2" "Row2" "Steven" 4 


#### <a name="queryentity"></a>Como consultar entidades de tabela
Para consultar uma tabela, use a classe [Microsoft.WindowsAzure.Storage.Table.TableQuery](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tablequery.aspx). O exemplo a seguir pressupõe que você executou o script fornecido na seção "Como adicionar entidades" deste guia. O exemplo primeiro estabelece uma conexão com o armazenamento do Azure usando o contexto de armazenamento, que inclui o nome da conta de armazenamento e sua chave de acesso primário. Em seguida, ele tentará recuperar a tabela "Funcionários" criada anteriormente usando o cmdlet [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx). Chamar o cmdlet [New-Object](http://technet.microsoft.com/library/hh849885.aspx) na classe Microsoft.WindowsAzure.Storage.Table.TableQuery cria um novo objeto de consulta. O exemplo procura as entidades que têm uma coluna de 'ID' cujo valor é 1 conforme especificado em um filtro de cadeia de caracteres. Para obter informações detalhadas, consulte a seção "Construir cadeias de caracteres" em [Consultar tabelas e entidades](http://msdn.microsoft.com/library/azure/dd894031.aspx). Quando você executa essa consulta, ela retorna todas as entidades que correspondem aos critérios de filtro.    

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    $TableName = "Employees"
    
    #Get a reference to a table.
    $table = Get-AzureStorageTable -Name $TableName -Context $Ctx
    
    #Create a table query.
    $query = New-Object Microsoft.WindowsAzure.Storage.Table.TableQuery
    
    #Define columns to select. 
    $list = New-Object System.Collections.Generic.List[string] 
    $list.Add("RowKey")
    $list.Add("ID")
    $list.Add("Name")
    
    #Set query details.
    $query.FilterString = "ID gt 0"
    $query.SelectColumns = $list
    $query.TakeCount = 20
    
    #Execute the query.
    $entities = $table.CloudTable.ExecuteQuery($query)
    
    #Display entity properties with the table format.
    $entities  | Format-Table PartitionKey, RowKey, @{ Label = "Name"; Expression={$_.Properties["Name"].StringValue}}, @{ Label = "ID"; Expression={$_.Properties["ID"].Int32Value}} -AutoSize 

#### <a name="deleteentity"></a>Como excluir entidades de tabela
Você pode excluir uma entidade usando suas chaves de partição e de linha. O exemplo a seguir pressupõe que você executou o script fornecido na seção "Como adicionar entidades" deste guia. O exemplo primeiro estabelece uma conexão com o armazenamento do Azure usando o contexto de armazenamento, que inclui o nome da conta de armazenamento e sua chave de acesso primário. Em seguida, ele tentará recuperar a tabela "Funcionários" criada anteriormente usando o cmdlet [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx). Se a tabela existir, o exemplo chama o método [Microsoft.WindowsAzure.Storage.Table.TableOperation.Retrieve](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx) para recuperar uma entidade com base em seus valores de chave de partição e de linha. Em seguida, transmita a entidade para o método [Microsoft.WindowsAzure.Storage.Table.TableOperation.Delete](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.delete.aspx) para excluir. 

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #Retrieve the table.
    $TableName = "Employees"
    $table = Get-AzureStorageTable -Name $TableName -Context $Ctx -ErrorAction Ignore

    #If the table exists, start deleting its entities.
    if ($table -ne $null)
    {
       #Together the PartitionKey and RowKey uniquely identify every  
       #entity within a table.
       $tableResult = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Retrieve("Partition2", "Row1"))
       $entity = $tableResult.Result;
    if ($entity -ne $null) 
    {
       #Delete the entity.$table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Delete($entity))
    }
    }

## <a name="managequeues"></a>Como gerenciar filas do Azure e mensagens da fila
O armazenamento de filas do Azure é um serviço para armazenamento de um grande número de mensagens que podem ser acessadas de qualquer lugar do mundo por meio de chamadas autenticadas usando HTTP ou HTTPS. Esta seção pressupõe que você já esteja familiarizado com os conceitos do serviço de Armazenamento de Filas do Azure. Para obter informações detalhadas, consulte [Como usar o Armazenamento de Filas no .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-queues/) e [Conceitos do serviço Fila](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-queues/#concepts).

Esta seção mostrará como gerenciar o serviço de armazenamento de fila do Azure usando o PowerShell do Azure. Os cenários abordados incluem **inserção** e **exclusão** de mensagens da fila, bem como a **criação**, **exclusão** e **recuperação de filas**.
 
### <a name="createqueue"></a>Como criar uma fila
O exemplo a seguir primeiro estabelece uma conexão com o armazenamento do Azure usando o contexto de conta de armazenamento, que inclui o nome da conta de armazenamento e sua chave de acesso primária. Em seguida, ele chama o cmdlet [New-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806382.aspx) para criar uma fila denominada 'queuename'. 

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    $QueueName = "queuename"
    $Queue = New-AzureStorageQueue -Name $QueueName -Context $Ctx 

Para obter informações sobre convenções de nomenclatura do serviço Fila do Azure, consulte [Nomear filas e metadados](http://msdn.microsoft.com/library/azure/dd179349.aspx).

### <a name="getqueue"></a>Como recuperar uma fila
Você pode consultar e recuperar uma lista de todas as filas em uma conta de armazenamento ou de uma fila específica. O exemplo a seguir demonstra como recuperar uma determinada fila usando o cmdlet [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx).

    #Retrieve a queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $Ctx 

Se você chamar o cmdlet [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx) sem parâmetros, ele obtém uma lista de todas as filas.

### <a name="remqueue"></a>Como excluir uma fila
Para excluir uma fila e todas as mensagens contidas nela, chame o cmdlet Remove-AzureStorageQueue. O exemplo a seguir demonstra como excluir uma determinada fila usando o cmdlet Remove-AzureStorageQueue. 

    #Delete a queue.
    $QueueName = "yourqueuename"
    Remove-AzureStorageQueue -Name $QueueName -Context $Ctx 

### <a name="mngqueuemsg"></a>Como gerenciar mensagens da fila
Atualmente, o PowerShell do Azure não fornece cmdlets para gerenciar mensagens da fila diretamente. Para executar operações em mensagens da fila, você pode usar as classes fornecidas na [biblioteca de cliente de armazenamento do Azure para .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx). 

#### <a name="addqueuemsg"></a>Como inserir uma mensagem em uma fila
Para inserir uma mensagem em uma fila existente, primeiro crie uma nova instância da classe [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx). Em seguida, chame o método [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx). Um CloudQueueMessage pode ser criado por meio de uma cadeia de caracteres (em formato UTF-8) ou de uma matriz de bytes.
 
O exemplo a seguir demonstra como adicionar uma mensagem a uma fila. O exemplo primeiro estabelece uma conexão com o armazenamento do Azure usando o contexto de conta de armazenamento, que inclui o nome da conta de armazenamento e sua chave de acesso. Em seguida, ele recupera a fila especificada usando o cmdlet [Get-AzureStorageQueue](https://msdn.microsoft.com/library/azure/dn806377.aspx). Se a fila existir, o cmdlet [New-Object](http://technet.microsoft.com/library/hh849885.aspx) é usado para criar uma instância da classe [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx). Posteriormente, o exemplo chama o método [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) nesse objeto de entidade para adicioná-lo a uma fila. Aqui está o código que recupera uma fila e insere a mensagem 'MessageInfo':

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx 
    
    #If the queue exists, add a new message.
    if ($Queue -ne $null)
    {
       # Create a new message using a constructor of the CloudQueueMessage class.
       $QueueMessage = New-Object "Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage" "MessageInfo"
    
       #Add a new message to the queue.
       $Queue.CloudQueue.AddMessage($QueueMessage)
    } 


#### <a name="dequeuemsg"></a>Como retirar a próxima mensagem da fila
Seu código remove uma mensagem de um fila em duas etapas. Quando você chama o método [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx), receberá a próxima mensagem em uma fila. Uma mensagem retornadade **GetMessage** torna-se invisível para todas as outras mensagens de leitura de códigoda fila. Para concluir a remoção da mensagem da fila, você também deve chamar o método [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx). Este processo de duas etapas de remover uma mensagem garante que se o código não processar uma mensagem devido a falhas de hardware ou de software, outra instância do seu código poderá receber a mesma mensagem e tentar novamente. Seu código chama **deleteMessage** logo depois que a mensagem é processada.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx
    
    $InvisibleTimeout = [System.TimeSpan]::FromSeconds(10)
    
    #Get the message object from the queue.
    $QueueMessage = $Queue.CloudQueue.GetMessage($InvisibleTimeout)
    #Delete the message.
    $Queue.CloudQueue.DeleteMessage($QueueMessage) 

## <a name="files"></a>Como gerenciar arquivos e compartilhamentos de arquivos do Azure
O armazenamento de arquivos do Azure oferece armazenamento compartilhado para aplicativos usando o protocolo padrão SMB 2.1. As máquinas virtuais e os serviços de nuvem do Microsoft Azure podem compartilhar dados de arquivos entre componentes de aplicativos por meio de compartilhamentos montados, e aplicativos locais podem acessar dados de arquivos em um compartilhamento por meio da API de armazenamento de arquivo ou o Azure PowerShell.

Para obter informações detalhadas sobre o armazenamento de arquivos do Azure, consulte [Como usar o armazenamento de arquivos do Azure](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/) e [REST API de serviços de arquivo](http://msdn.microsoft.com/library/azure/dn167006.aspx).

Para saber como gerenciar o armazenamento de arquivos do Azure usando o PowerShell do Azure, consulte [Usar o PowerShell para criar um compartilhamento de arquivos](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/#use-cmdlets).

## <a name="stganalytics"></a>Como definir e consultar análises de armazenamento
A [análise de armazenamento do Azure](http://msdn.microsoft.com/library/azure/hh343270.aspx) permite coletar métricas (métricas de armazenamento) de suas contas de armazenamento do Azure e de log (log de armazenamento) de dados sobre solicitações enviadas à sua conta de armazenamento. Você pode usar métricas de armazenamento para monitorar a integridade de uma conta de armazenamento e o armazenamento de log para diagnosticar e solucionar problemas com sua conta de armazenamento.
Por padrão, as métricas de armazenamento não estão habilitadas para os serviços de armazenamento. Você pode habilitar o monitoramento usando o Portal de Gerenciamento do Azure, Windows PowerShell ou com programação por meio de uma API de armazenamento. O log de armazenamento ocorre no servidor e permite que você registre detalhes de solicitações bem-sucedidas e com falhas na conta de armazenamento. Esses registros permitem ver detalhes de operações de leitura, gravação e exclusão em suas tabelas, filas e blobs bem como as razões para solicitações com falha.

Para saber como habilitar e exibir dados de métricas de armazenamento usando o PowerShell, consulte [Como habilitar métricas de armazenamento usando o PowerShell](http://msdn.microsoft.com/library/azure/dn782843.aspx#HowtoenableStorageMetricsusingPowerShell).

Para saber como habilitar e recuperar dados de log de armazenamento usando o PowerShell, consulte 
[Como habilitar o log de armazenamento usando o PowerShell](http://msdn.microsoft.com/library/azure/dn782840.aspx#HowtoenableStorageLoggingusingPowerShell) e [Localizar os dados de log de armazenamento](.http://msdn.microsoft.com/library/azure/dn782840.aspx#FindingyourStorageLogginglogdata)
Para obter informações detalhadas sobre o uso de métricas de armazenamento e o armazenamento de log para solucionar problemas de armazenamento, consulte [Monitoramento, diagnóstico e solução de problemas de armazenamento do Microsoft Azure](http://azure.microsoft.com/documentation/articles/storage-monitoring-diagnosing-troubleshooting/).

## <a name="sas"></a>Como gerenciar a Assinatura de Acesso Compartilhado (SAS) e a Política de Acesso Armazenada
Assinaturas de acesso compartilhado são uma parte importante do modelo de segurança para qualquer aplicativo que utilize o Armazenamento do Azure. Elas são úteis para fornecer permissões limitadas para a sua conta de armazenamento aos clientes que não devem ter a chave de conta. Por padrão, somente o proprietário da conta de armazenamento pode acessar blobs, tabelas e filas nessa conta. Se seu serviço ou aplicativo precisar tornar esses recursos disponíveis para outros clientes sem compartilhar sua chave de acesso, você tem três opções:

- Definir permissões do contêiner para permitir o acesso anônimo de leitura para o contêiner e seus blobs. Isso não é permitido para tabelas ou filas.
- Usar uma assinatura de acesso compartilhado que concede direitos de acesso restrito para contêineres, blobs, filas e tabelas por um intervalo de tempo específico.
- Usar uma política de acesso armazenado para obter um nível adicional de controle sobre assinaturas de acesso compartilhado para um contêiner ou seus blobs, uma fila ou uma tabela. A política de acesso armazenado permite que você altere a hora de início, a hora de término ou as permissões para uma assinatura ou revogue-a depois que ela tiver sido emitida.

Uma assinatura de acesso compartilhado pode estar em uma das duas formas:

- **SAS Ad hoc**: Quando você cria uma SAS ad hoc, a hora de início, a hora de término e as permissões para a SAS são especificadas no URI SAS. Esse tipo de SAS pode ser criado em um contêiner, blob, tabela ou fila e é não pode ser revogado.
- **SAS com política de acesso armazenada**: Uma política de acesso armazenada é definida em um contêiner de recurso um contêiner de blob, tabela ou fila - e você pode usá-la para gerenciar restrições para uma ou mais assinaturas de acesso compartilhado. Quando você associa uma SAS a uma política de acesso armazenada, a SAS herda as restrições - a hora de início, a hora de expiração e as permissões - definidas para a política de acesso armazenada. Esse tipo de SAS pode ser revogado.

Para obter mais informações, consulte [Assinaturas de Acesso Compartilhado](storage-dotnet-shared-access-signature-part-1.md), [Parte 1: Noções básicas sobre o modelo SAS](storage-dotnet-shared-access-signature-part-1.md) e [Gerenciar o acesso aos recursos de Armazenamento do Azure](http://msdn.microsoft.com/library/azure/ee393343.aspx).

Nas seções a seguir, você aprenderá a como criar uma política de acesso armazenado e um token de assinatura de acesso compartilhado para tabelas do Azure. O PowerShell do Azure fornece cmdlets semelhantes para contêineres, blobs e filas. Para executar os scripts nesta seção, baixe o [PowerShell do Azure versão 0.8.14](http://go.microsoft.com/?linkid=9811175&clcid=0x409) ou posterior.

### <a name="sub1"></a>Como criar uma política baseada no token de Assinatura de Acesso Compartilhado
Use o cmdlet New-AzureStorageTableStoredAccessPolicy para criar uma nova política de acesso armazenada. Em seguida, chame o cmdlet [New-AzureStorageTableSASToken](http://msdn.microsoft.com/library/azure/dn806400.aspx) para criar um novo token de assinatura de acesso compartilhado com base em política para uma Tabela de Armazenamento do Azure.

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx
    New-AzureStorageTableSASToken -Name $tableName -Policy $policy -Context $Ctx

### <a name="sub2"></a>Como criar um token de Assinatura de Acesso Compartilhado (não revogável) ad hoc
Use o cmdlet [New-AzureStorageTableSASToken](http://msdn.microsoft.com/library/azure/dn806400.aspx) para criar um novo (não revogável) token de Assinatura de Acesso Compartilhado ad-hoc para uma tabela de armazenamento do Azure:

    New-AzureStorageTableSASToken -Name $tableName -Permission "rqud" -StartTime "2015-01-01" -ExpiryTime "2015-02-01" -Context $Ctx

### <a name="sub3"></a>Como criar uma política de acesso armazenada
Use o cmdlet New-AzureStorageTableStoredAccessPolicy para criar uma nova política de acesso armazenada para uma tabela de armazenamento do Azure:

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx

### <a name="sub4"></a>Como atualizar uma política de acesso armazenada
Use o cmdlet Set-AzureStorageTableStoredAccessPolicy para atualizar uma política de acesso armazenada existente para uma tabela de armazenamento do Azure:

    Set-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Permission "rd" -NoExpiryTime -NoStartTime -Context $Ctx

### <a name="sub5"></a>Como excluir uma política de acesso armazenada
Use o cmdlet Remove-AzureStorageTableStoredAccessPolicy para excluir uma política de acesso armazenada em uma tabela de armazenamento do Azure:

    Remove-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Context $Ctx


## <a name="gov"></a>Como usar o Armazenamento do Azure para o governo dos EUA e o Azure China
Um ambiente do Azure é uma implantação independente do Microsoft Azure, como [Azure Government para o governo dos EUA](, http://azure.microsoft.com/features/gov/)[AzureCloud para Azure global](https://manage.windowsazure.com) e [AzureChinaCloud para o Azure operado pela 21Vianet na China](http://www.windowsazure.cn/). Você pode implantar novos ambientes do Azure para o governo dos EUA e Azure China. 

Para usar o Armazenamento do Azure com AzureChinaCloud, você precisa criar um contexto de armazenamento associado à AzureChinaCloud. Siga estas etapas para começar:

1.	Execute o cmdlet [Get-AzureEnvironment](https://msdn.microsoft.com/library/azure/dn790368.aspx) para ver os ambientes do Azure disponíveis:

    `Get-AzureEnvironment`

2.	Adicionar uma conta do Azure China ao Windows PowerShell:

    `Add-AzureAccount -Environment AzureChinaCloud`

3.	Crie um contexto de armazenamento para uma conta de AzureChinaCloud:

    	$Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment AzureChinaCloud

Para usar o armazenamento do Azure com [Azure Government dos EUA](http://azure.microsoft.com/features/gov/), você deve definir um novo ambiente e, em seguida, criar um novo contexto de armazenamento com esse ambiente:

1. Chame o cmdlet [Add-AzureEnvironment](http://msdn.microsoft.com/library/azure/dn790364.aspx) para criar um novo ambiente do Azure para seu datacenter particular. 

    	Add-AzureEnvironment -Name $EnvironmentName -PublishSettingsFileUrl $publishSettingsFileUrl -ServiceEndpoint $serviceEndpoint -ManagementPortalUrl $managementPortalUrl -StorageEndpoint $storageEndpoint -ActiveDirectoryEndpoint $activeDirectoryEndpoint -ResourceManagerEndpoint $resourceManagerEndpoint -GalleryEndpoint $galleryEndpoint -ActiveDirectoryServiceEndpointResourceId $activeDirectoryServiceEndpointResourceId -GraphEndpoint $graphEndpoint -SubscriptionDataFile $subscriptionDataFile

2. Execute o cmdlet [New-AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx) para criar um novo contexto de armazenamento para esse novo ambiente, conforme mostrado abaixo. 
   
	    $Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment $EnvironmentName

Para obter mais informações, consulte:

- [Guia do Desenvolvedor do Microsoft Azure Government](azure-government-developer-guide.md). 
- [Diferenças entre AzureCloud para o Azure global e AzureChinaCloud para Azure operado pelo 21Vianet na China](https://msdn.microsoft.com/library/azure/dn578439.aspx)

## <a name="next"></a>Próximas etapas
Neste guia, você aprendeu como gerenciar o armazenamento do Azure com o PowerShell do Azure. Estes são alguns artigos e recursos relacionados para saber mais sobre eles.

- [Documentação do Armazenamento do Azure](http://azure.microsoft.com/documentation/services/storage/)
- [Referência do MSDN de Armazenamento do Azure](http://msdn.microsoft.com/library/azure/gg433040.aspx)
- [Cmdlets do PowerShell do Armazenamento do Azure](http://msdn.microsoft.com/library/azure/dn806401.aspx)
- [Referência do Windows PowerShell](https://msdn.microsoft.com/library/ms714469.aspx)

[Image1]: ./media/storage-powershell-guide-full/Subscription_currentportal.png
[Image2]: ./media/storage-powershell-guide-full/Subscription_Previewportal.png
[Image3]: ./media/storage-powershell-guide-full/Blobdownload.png


[Introdução ao Armazenamento do Azure e ao PowerShell em 5 minutos]: #getstart
[Pré-requisitos para usar o PowerShell do Azure com o armazenamento do Azure]: #pre
[Como gerenciar contas de armazenamento no Azure]: #manageaccount
[Como definir uma assinatura padrão do Azure]: #setdefsub
[Como criar uma nova conta de armazenamento do Azure]: #createaccount
[Como configurar uma conta padrão de armazenamento do Azure]: #defaultaccount
[Como listar todas as contas de armazenamento do Azure em uma assinatura]: #listaccounts
[Como criar um contexto de armazenamento do Azure]: #createctx
[Como gerenciar blobs do Azure e instantâneos de blob]: #manageblobs
[Como criar um contêiner]: #container
[Como carregar um blob para um contêiner]: #uploadblob
[Como baixar blobs de um contêiner]: #downblob
[Como copiar blobs de um contêiner de armazenamento para outro]: #copyblob
[Como excluir um blob]: #deleteblob
[Como gerenciar instantâneos de blob do Azure]: #manageshots
[Como criar um instantâneo de blob]: #createshot
[Como listar instantâneos de um blob]: #listshot
[Como copiar um instantâneo de um blob]: #copyshot
[Como gerenciar tabelas do Azure e entidades de tabela]: #managetables
[Como criar uma tabela]: #createtable
[Como recuperar uma tabela]: #gettable
[Como excluir uma tabela]: #remtable
[Como gerenciar entidades de tabela]: #mngentity
[Como adicionar entidades de tabela]: #addentity
[Como consultar entidades de tabela]: #queryentity
[Como excluir entidades de tabela]: #deleteentity
[Como gerenciar filas do Azure e mensagens da fila]: #managequeues
[Como criar uma fila]: #createqueue
[Como recuperar uma fila]: #getqueue
[Como excluir uma fila]: #remqueue
[Como gerenciar mensagens da fila]: #mngqueuemsg
[Como inserir uma mensagem em uma fila]: #addqueuemsg
[Como retirar a próxima mensagem da fila]: #dequeuemsg
[Como gerenciar arquivos e compartilhamentos de arquivos do Azure]: #files
[Como definir e consultar análises de armazenamento]: #stganalytics
[Como gerenciar a Assinatura de Acesso Compartilhado (SAS) e a Política de Acesso Armazenada]: #sas
[Como usar o Armazenamento do Azure para o governo dos EUA e o Azure China]: #gov
[Próximas etapas]: #next

<!--HONumber=47-->
