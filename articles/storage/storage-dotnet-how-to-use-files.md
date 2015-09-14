<properties
			pageTitle="Como usar o armazenamento de arquivo do Azure com o PowerShell e .NET | Microsoft Azure"
	description="Saiba como utilizar o armazenamento de arquivos do Azure para criar compartilhamentos de arquivos na nuvem e gerenciar o conteúdo de arquivos. O armazenamento de arquivos permite que as empresas movam os aplicativos dependentes do compartilhamentos de arquivos SMB para o Azure. Guarde suas credenciais de conta de armazenamento para a máquina virtual para reconectar-se ao compartilhamento de arquivos após a reinicialização."
	services="storage"
	documentationCenter=".net"
	authors="tamram"
	manager="adinah"
	editor=""/>

<tags ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="08/04/2015"
	ms.author="tamram"/>

# Como usar o armazenamento de arquivo do Azure com o PowerShell e .NET

## Visão geral

O serviço de Arquivos do Azure expõe os compartilhamentos de arquivos usando o protocolo SMB 2.1 padrão. Os aplicativos executados no Azure podem utilizar esse serviço para compartilhar arquivos entre VMs utilizando APIs com um sistema de arquivos familiar padrão, como ReadFile e WriteFile. Além disso, os arquivos também podem ser acessados simultaneamente por meio de uma interface REST, que abre uma variedade de cenários híbridos. Por fim, os Arquivos do Azure baseiam-se na mesma tecnologia que os serviços Blobs, Tabelas e Fila, o que significa que os Arquivos do Azure podem aproveitar a disponibilidade, durabilidade, escalabilidade e redundância geográfica existentes e incorporadas à plataforma de armazenamento do Azure.

## Sobre este tutorial

Este guia de introdução mostra os fundamentos sobre como utilizar o armazenamento de Arquivos do Microsoft Azure. Neste tutorial, nós iremos:

- Utilizar o PowerShell do Azure para mostrar como criar um novo compartilhamento de arquivo do Azure, adicionar um diretório, fazer upload de um arquivo local no compartilhamento e listar os arquivos no diretório.
- Montar o compartilhamento de arquivo por meio de uma máquina virtual do Azure, da mesma forma como seria feito com qualquer compartilhamento SMB.
- Use a Biblioteca de Cliente do Armazenamento do Azure para .NET para acessar o compartilhamento de arquivos de um aplicativo local. Crie um aplicativo de console e execute estas ações com o compartilhamento de arquivos:
	- Grave o conteúdo de um arquivo no compartilhamento na janela do console.
	- Defina a cota (tamanho máximo) para o compartilhamento de arquivos.
	- Crie uma assinatura de acesso compartilhado para um arquivo que usa uma política de acesso compartilhado definida no compartilhamento.
	- Copie um arquivo em outro arquivo na mesma conta de armazenamento.
	- Copie um arquivo em um blob na mesma conta de armazenamento.

[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]


## Criar uma conta de armazenamento do Azure

O armazenamento de arquivos do Azure está atualmente em visualização. Para solicitar acesso à visualização, navegue até o [portal de visualização do Azure](/services/preview/) e solicite acesso aos **Arquivos do Azure**. Após a solicitação ser aprovada, você será notificado de que pode acessar a visualização do armazenamento de arquivos. Você pode então criar uma conta de armazenamento para acessar o armazenamento de arquivos.

> [AZURE.NOTE]O armazenamento de arquivos está disponível atualmente apenas para novas contas de armazenamento. Após ser concedido acesso à sua assinatura para o armazenamento de arquivos, crie uma nova conta de armazenamento para ser usada com este guia.
>
> O armazenamento de arquivo do Azure não oferece suporte para assinaturas de acesso compartilhado.

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Usar o PowerShell para gerenciar um compartilhamento de arquivos

Em seguida, usaremos o PowerShell do Azure para criar um compartilhamento de arquivos. Quando o compartilhamento de arquivos tiver sido criado, você poderá montá-lo em qualquer sistema de arquivos que dê suporte a SMB 2.1.

### Instalar os cmdlets do PowerShell para Armazenamento do Azure

Para se preparar para usar o PowerShell, baixe e instale os cmdlets do PowerShell do Azure. Consulte [Como instalar e configurar o PowerShell do Azure](../install-configure-powershell.md) para obter o ponto e as instruções de instalação.

> [AZURE.NOTE]Os cmdlets do PowerShell para o serviço de arquivos estão disponíveis somente no módulo mais recente do PowerShell do Azure, versão 0.8.5 e posterior. É recomendável baixar e instalar ou atualizar para o módulo mais recente do PowerShell do Azure.

Abra uma janela do PowerShell do Azure clicando em **Iniciar** e digitando **PowerShell do Azure**. A janela do PowerShell do Azure carrega do módulo do PowerShell do Azure para você.

### Criar um contexto para sua conta e chave de armazenamento

Agora, crie o contexto da conta de armazenamento. O contexto encapsula a conta de armazenamento e a chave da conta. Para obter instruções sobre como copiar a chave da conta no portal do Azure, consulte [Exibir, copiar e regenerar chaves de acesso de armazenamento](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

Substitua `storage-account-name` e `storage-account-key` pelo nome e chave da sua conta de armazenamento no exemplo a seguir.

	# create a context for account and key
	$ctx=New-AzureStorageContext storage-account-name storage-account-key

### Criar um novo compartilhamento de arquivo

Em seguida, crie o novo compartilhamento, denominado `logs`.

	# create a new share
	$s = New-AzureStorageShare logs -Context $ctx

Agora você tem um compartilhamento de arquivo no armazenamento de arquivos. Em seguida, adicionaremos um diretório e um arquivo.

> [AZURE.IMPORTANT]O nome do seu compartilhamento de arquivo deve estar em minúsculas. Para obter detalhes completos sobre como nomear arquivos e compartilhamentos de arquivos, consulte [Nomenclatura e referência de compartilhamentos, diretórios, arquivos e metadados](https://msdn.microsoft.com/library/azure/dn167011.aspx).

### Criar um diretório no compartilhamento de arquivos

Em seguida, crie um diretório no compartilhamento. No exemplo a seguir, o diretório é denominado `CustomLogs`:

    # create a directory in the share
    New-AzureStorageDirectory -Share $s -Path CustomLogs

### Carregar um arquivo local no diretório

Agora, carregue um arquivo local no diretório. O exemplo a seguir carrega um arquivo de `C:\temp\Log1.txt`. Edite o caminho do arquivo para que ele aponte para um arquivo válido em seu computador local.

    # upload a local file to the new directory
    Set-AzureStorageFileContent -Share $s -Source C:\temp\Log1.txt -Path CustomLogs

### Listar os arquivos no diretório

Para ver o arquivo no diretório, você pode listar os arquivos do diretório. Esse comando também listará os subdiretórios, mas como não há um subdiretório neste exemplo, somente o arquivo será listado.

	# list files in the new directory
	Get-AzureStorageFile -Share $s -Path CustomLogs

### Copiar arquivos

A partir da versão 0.9.7 do Azure PowerShell, você pode copiar um arquivo em outro arquivo, um arquivo em um blob ou um blob em um arquivo. A seguir, demonstramos como executar essas operações de cópia usando cmdlets.

	# copy a file to the new directory
    Start-AzureStorageFileCopy -SrcShareName srcshare -SrcFilePath srcdir/hello.txt -DestShareName destshare -DestFilePath destdir/hellocopy.txt -Context $srcCtx -DestContext $destCtx
    # copy a blob to a file directory
    Start-AzureStorageFileCopy -SrcContainerName srcctn -SrcBlobName hello2.txt -DestShareName hello -DestFilePath hellodir/hello2copy.txt -DestContext $ctx -Context $ctx


## Montar o compartilhamento de uma máquina virtual do Azure que executa o Windows

Para demonstrar como montar um compartilhamento de arquivos do Azure, criaremos agora uma máquina virtual do Azure que executa o Windows e estabeleceremos comunicação remota nela para montar o compartilhamento.

1. Primeiramente, crie uma nova máquina virtual do Azure seguindo as instruções em [Criar uma máquina virtual executando o Windows Server](../virtual-machines-windows-tutorial.md).
2. Em seguida, acesse remotamente a máquina virtual seguindo as instruções em [Como fazer logon em uma máquina virtual executando o Windows Server](../virtual-machines-log-on-windows-server.md).
3. Abra uma janela do PowerShell na máquina virtual.

### Persistir as credenciais da sua conta de armazenamento para a máquina virtual

Antes de montar o compartilhamento de arquivos, primeiro é preciso persistir as credenciais da sua conta de armazenamento na máquina virtual. Essa etapa permite ao Windows reconectar automaticamente ao compartilhamento de arquivos quando a máquina virtual é reinicializada. Para persistir as credenciais da sua conta, execute o comando `cmdkey` na janela do PowerShell na máquina virtual. Substitua `<storage-account-name>` pelo nome da conta de armazenamento e `<storage-account-key>` pela chave da conta de armazenamento.

	cmdkey /add:<storage-account-name>.file.core.windows.net /user:<storage-account-name> /pass:<storage-account-key>

O Windows reconectará ao compartilhamento de arquivo quando a máquina virtual for reinicializada. Você pode verificar se o compartilhamento foi reconectado executando o comando `net use` em uma janela do PowerShell.

### Montar o compartilhamento de arquivos usando as credenciais persistidas

Após ter uma conexão remota com a máquina virtual, você poderá executar o comando `net use` para montar o compartilhamento de arquivos usando a sintaxe a seguir. Substitua `<storage-account-name>` pelo nome da sua conta de armazenamento e `<share-name>` pelo nome do compartilhamento de armazenamento de arquivos.

    net use <drive-letter>: \<storage-account-name>.file.core.windows.net<share-name>

	example :
	net use z: \\samples.file.core.windows.net\logs

Como você persistiu as credenciais da conta de armazenamento na etapa anterior, não será preciso fornecê-las com o comando `net use`. Se ainda não tiver persistido suas credenciais, inclua-as como um parâmetro passado para o comando `net use`, conforme exibido no exemplo a seguir.

    net use <drive-letter>: \<storage-account-name>.file.core.windows.net<share-name> /u:<storage-account-name> <storage-account-key>

	example :
	net use z: \\samples.file.core.windows.net\logs /u:samples <storage-account-key>

Você agora pode trabalhar com o compartilhamento de armazenamento de arquivos na máquina virtual como faria com qualquer outra unidade. Você pode emitir comandos de arquivo padrão do prompt de comando ou exibir o compartilhamento montado e seu conteúdo no Explorador de Arquivos. Você também pode executar código na máquina virtual que avalia o compartilhamento de arquivos usando as APIs de E/S de arquivos padrão do Windows, como as fornecidas pelos [namespaces do System.IO](http://msdn.microsoft.com/library/gg145019.aspx) no .NET Framework.

Você também pode montar o compartilhamento de arquivos por meio de uma função executada em um serviço de nuvem do Azure ao estabelecer comunicação remota à função.

## Criar um aplicativo local para trabalhar com o armazenamento de arquivos

Você pode montar o compartilhamento de arquivos em uma máquina virtual ou um serviço de nuvem executado no Azure, como demonstrado anteriormente. No entanto, não é possível montar o compartilhamento de arquivos por meio de um aplicativo local. Para acessar os dados de compartilhamento por meio de um aplicativo local, você deve usar a API de armazenamento de arquivos. Este exemplo demonstra como trabalhar com um compartilhamento de arquivo por meio da [Biblioteca de Cliente de Armazenamento para .NET do Azure](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409).

Para mostrar como usar a API por meio de um aplicativo local, criaremos um aplicativo de console simples sendo executado em área de trabalho.

### Criar o aplicativo do console e obter o assembly

Para criar um novo aplicativo de console no Visual Studio e instalar o pacote NuGet no Armazenamento do Azure:

1. No Visual Studio, escolha **Arquivo > Novo Projeto** e, em seguida, **Windows > Aplicativo de Console** na lista de modelos do Visual C#.
2. Forneça um nome para o aplicativo de console e clique em **OK**.
3. Após o projeto ser criado, clique com o botão direito do mouse no projeto no Gerenciador de Soluções e escolha **Gerenciar Pacotes NuGet**. Pesquise online por "WindowsAzure.Storage" e clique em **Instalar** para instalar o pacote Armazenamento do Azure e as dependências.

### Salvar suas credenciais da conta de armazenamento no arquivo app.config

Em seguida, salve suas credenciais no arquivo app.config do projeto. Edite o arquivo app.config para que ele pareça semelhante ao exemplo a seguir, substituindo `myaccount` pelo nome da conta de armazenamento e `mykey` pela chave da conta de armazenamento.

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	    <startup>
	        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
	    </startup>
	    <appSettings>
	        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
	    </appSettings>
	</configuration>

> [AZURE.NOTE]A versão mais recente do emulador de armazenamento do Azure não dá suporte ao armazenamento de arquivos. Sua cadeia de conexão deve ter como destino uma conta de armazenamento do Azure na nuvem com acesso à visualização do Armazenamento de arquivo.


### Adicionar declarações do namespace

Abra o arquivo program.cs no Gerenciador de Soluções e adicione as seguintes declarações de namespace à parte superior do arquivo.

	using Microsoft.WindowsAzure;
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Blob;
	using Microsoft.WindowsAzure.Storage.File;

### Recuperar sua cadeia de conexão programaticamente

Você pode recuperar suas credenciais salvas por meio do arquivo app.config usando a classe `Microsoft.WindowsAzure.CloudConfigurationManager` ou a classe `System.Configuration.ConfigurationManager `. O pacote do Gerenciador de Configuração do Microsoft Azure, que inclui a classe `Microsoft.WindowsAzure.CloudConfigurationManager`, está disponível em [Nuget](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager).

O exemplo aqui mostra como recuperar suas credenciais usando a classe `CloudConfigurationManager` e encapsulá-las com a classe `CloudStorageAccount`. Adicione o código a seguir ao método `Main()` em program.cs.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    	CloudConfigurationManager.GetSetting("StorageConnectionString")); 

### Acessar o compartilhamento de arquivos programaticamente

Em seguida, adicione o código a seguir ao método `Main()` (após o código mostrado acima) para recuperar a cadeia de conexão. Esse código obtém uma referência para o arquivo que criamos anteriormente e exporta seu conteúdo para a janela do console.

	// Create a CloudFileClient object for credentialed access to File storage.
	CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

	// Get a reference to the file share we created previously.
	CloudFileShare share = fileClient.GetShareReference("logs");

	// Ensure that the share exists.
	if (share.Exists())
	{
	    // Get a reference to the root directory for the share.
	    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

	    // Get a reference to the directory we created previously.
	    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

	    // Ensure that the directory exists.
	    if (sampleDir.Exists())
	    {
	        // Get a reference to the file we created previously.
	        CloudFile file = sampleDir.GetFileReference("Log1.txt");

	        // Ensure that the file exists.
	        if (file.Exists())
	        {
	            // Write the contents of the file to the console window.
	            Console.WriteLine(file.DownloadTextAsync().Result);
	        }
	    }
	}

Execute o aplicativo de console para ver a saída.

## Definir o tamanho máximo de um compartilhamento de arquivos

A partir da versão 5.x da Biblioteca de Cliente do Armazenamento do Azure, você pode definir a cota (ou tamanho máximo) de um compartilhamento de arquivos, em gigabytes. Você também pode verificar a quantidade de dados atualmente armazenada no compartilhamento.

Ao definir a cota para um compartilhamento, você pode limitar o tamanho total dos arquivos armazenados no compartilhamento. Se o tamanho total dos arquivos no compartilhamento ultrapassar a cota definida no compartilhamento, os clientes não poderão aumentar o tamanho dos arquivos existentes ou criar novos arquivos, a menos que eles estejam vazios.

O exemplo a seguir mostra como verificar o uso atual de um compartilhamento e como definir a cota para o compartilhamento.

    // Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

    // Get a reference to the file share we created previously.
    CloudFileShare share = fileClient.GetShareReference("logs");

    // Ensure that the share exists.
    if (share.Exists())
    {
        // Check current usage stats for the share.
		// Note that the ShareStats object is part of the protocol layer for the File service.
        Microsoft.WindowsAzure.Storage.File.Protocol.ShareStats stats = share.GetStats();
        Console.WriteLine("Current share usage: {0} GB", stats.Usage.ToString());

        // Specify the maximum size of the share, in GB.
        // This line sets the quota to be 10 GB greater than the current usage of the share.
        share.Properties.Quota = 10 + stats.Usage;
        share.SetProperties();

        // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
        share.FetchAttributes();
        Console.WriteLine("Current share quota: {0} GB", share.Properties.Quota);
    }

## Gerar uma assinatura de acesso compartilhado para um arquivo ou compartilhamento de arquivos

A partir da versão 5.x da Biblioteca de Cliente do Armazenamento do Azure, você pode gerar uma SAS (assinatura de acesso compartilhado) para um compartilhamento de arquivos ou para um arquivo individual. Você também pode criar uma política de acesso compartilhado em um compartilhamento de arquivos para gerenciar assinaturas de acesso compartilhado. Recomendamos a criação de uma política de acesso compartilhado, pois ela fornece um meio de revogar o SAS se ele for comprometido.

O exemplo a seguir cria uma política de acesso compartilhado em um compartilhamento e, em seguida, usa essa política para fornecer as restrições a um SAS em um arquivo no compartilhamento.

    // Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

    // Get a reference to the file share we created previously.
    CloudFileShare share = fileClient.GetShareReference("logs");

    // Ensure that the share exists.
    if (share.Exists())
    {
        string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

        // Create a new shared access policy and define its constraints.
        SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
                Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
            };

        // Get existing permissions for the share.
        FileSharePermissions permissions = share.GetPermissions();

        // Add the shared access policy to the share's policies. Note that each policy must have a unique name.
        permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
        share.SetPermissions(permissions);

        // Generate a SAS for a file in the share and associate this access policy with it.
        CloudFileDirectory rootDir = share.GetRootDirectoryReference();
        CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
        CloudFile file = sampleDir.GetFileReference("Log1.txt");
        string sasToken = file.GetSharedAccessSignature(null, policyName);
        Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

        // Create a new CloudFile object from the SAS, and write some text to the file.
        CloudFile fileSas = new CloudFile(fileSasUri);
        fileSas.UploadText("This write operation is authenticated via SAS.");
        Console.WriteLine(fileSas.DownloadText());
    }

Para saber mais sobre como criar e usar assinaturas de acesso compartilhado, consulte [Assinaturas de acesso compartilhado: noções básicas sobre o modelo SAS](storage-dotnet-shared-access-signature-part-1.md) e [Criar e usar uma SAS com o serviço Blob](storage-dotnet-shared-access-signature-part-2.md).

## Copiar arquivos

A partir da versão 5.x da Biblioteca de Cliente do Armazenamento do Azure, você pode copiar um arquivo em outro arquivo, um arquivo em um blob ou um blob em um arquivo. Nas próximas seções, demonstramos como executar essas operações de cópia de modo programático.

Você também pode usar o AzCopy para copiar um arquivo para outro, ou para copiar um blob em um arquivo ou vice-versa. Para obter detalhes sobre como copiar arquivos com o AzCopy, consulte [Como usar o AzCopy com o Armazenamento do Microsoft Azure](storage-use-azcopy.md#copy-files-in-azure-file-storage-with-azcopy-preview-version-only).

> [AZURE.NOTE]Se você estiver copiando um blob em um arquivo, ou um arquivo em um blob, use uma assinatura de acesso compartilhado (SAS) para autenticar o objeto de origem, mesmo se você estiver copiando dentro da mesma conta de armazenamento.

### Copiar um arquivo para outro arquivo

O exemplo a seguir copia um arquivo em outro arquivo no mesmo compartilhamento. Como essa operação de cópia realiza a cópia entre arquivos na mesma conta de armazenamento, você pode usar a autenticação de Chave compartilhada para executar a cópia.

    // Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

    // Get a reference to the file share we created previously.
    CloudFileShare share = fileClient.GetShareReference("logs");

    // Ensure that the share exists.
    if (share.Exists())
    {
        // Get a reference to the root directory for the share.
        CloudFileDirectory rootDir = share.GetRootDirectoryReference();

        // Get a reference to the directory we created previously.
        CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

        // Ensure that the directory exists.
        if (sampleDir.Exists())
        {
            // Get a reference to the file we created previously.
            CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

            // Ensure that the source file exists.
            if (sourceFile.Exists())
            {
                // Get a reference to the destination file.
                CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

                // Start the copy operation.
                destFile.StartCopy(sourceFile);

                // Write the contents of the destination file to the console window.
                Console.WriteLine(destFile.DownloadText());
            }
        }
    }


### Copiar um arquivo em um blob

O exemplo a seguir cria um arquivo e o copia em um blob dentro da mesma conta de armazenamento. O exemplo cria uma SAS para o arquivo de origem, que o serviço usa para autenticar o acesso ao arquivo de origem durante a operação de cópia.

    // Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

    // Create a new file share, if it does not already exist.
    CloudFileShare share = fileClient.GetShareReference("sample-share");
    share.CreateIfNotExists();

    // Create a new file in the root directory.
    CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
    sourceFile.UploadText("A sample file in the root directory.");

    // Get a reference to the blob to which the file will be copied.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();
    CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

    // Create a SAS for the file that's valid for 24 hours.
    // Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
    // to authenticate access to the source object, even if you are copying within the same
    // storage account.
    string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
    {
        // Only read permissions are required for the source file.
        Permissions = SharedAccessFilePermissions.Read,
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
    });

    // Construct the URI to the source file, including the SAS token.
    Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

    // Copy the file to the blob.
    destBlob.StartCopy(fileSasUri);

    // Write the contents of the file to the console window.
    Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
    Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());

Você pode copiar um blob em um arquivo da mesma maneira. Se o objeto de origem for um blob, crie uma SAS para autenticar o acesso ao blob durante a operação de cópia.

## Usar o Armazenamento de arquivos com o Linux

Para criar e gerenciar um compartilhamento de arquivos do Linux, use a CLI do Azure. Consulte [Usando a CLI do Azure com o Armazenamento do Azure](storage-azure-cli.md#create-and-manage-file-shares) para saber mais sobre como usar a CLI do Azure com o Armazenamento de arquivos.

Você pode montar o compartilhamento de arquivo do Azure a partir de uma máquina virtual executando o Linux Ao criar sua máquina virtual do Azure, você pode especificar uma imagem do Linux que dá suporte a SMB 2.1 da galeria de imagens do Azure, como a versão mais recente do Ubuntu. No entanto, qualquer distribuição do Linux que ofereça suporte ao SMB 2.1 pode montar um compartilhamento de arquivos do Azure.

Para saber mais como montar um compartilhamento de arquivos do Azure no Linux, consulte [Armazenamento compartilhado no Linux por meio da visualização dos Arquivos do Azure - parte 1](http://channel9.msdn.com/Blogs/Open/Shared-storage-on-Linux-via-Azure-Files-Preview-Part-1), no Channel 9.

## Próximas etapas

Consulte estes links para obter mais informações sobre o armazenamento de arquivo do Azure.

### Tutoriais e referência

- Referência à [Biblioteca de Cliente de Armazenamento para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
- [Referência à API REST do serviço de arquivos](http://msdn.microsoft.com/library/azure/dn167006.aspx)
- [Usar o AzCopy com o Armazenamento do Microsoft Azure](storage-use-azcopy.md)
- [Usando o PowerShell do Azure com o Armazenamento do Azure](storage-powershell-guide-full.md)
- [Usando a CLI do Azure com o Armazenamento do Azure](storage-azure-cli.md)

### Postagens no blog

- [Apresentando o serviço de arquivo do Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
- [Persistindo conexões para arquivos do Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)

<!---HONumber=September15_HO1-->