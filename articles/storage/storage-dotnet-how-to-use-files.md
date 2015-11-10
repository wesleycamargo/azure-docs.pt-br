<properties
			pageTitle="Como usar o armazenamento de arquivos do Azure com o Windows | Microsoft Azure"
            description="Crie um compartilhamento de arquivos na nuvem e gerencie o conteúdo do arquivo. Monte um compartilhamento de arquivos de uma VM do Azure ou de um aplicativo local."
            services="storage"
            documentationCenter=".net"
            authors="tamram"
            manager="adinah"
            editor="" />

<tags ms.service="storage"
      ms.workload="storage"
      ms.tgt_pltfrm="na"
      ms.devlang="dotnet"
      ms.topic="hero-article"
      ms.date="10/26/2015"
      ms.author="tamram" />

# Como usar o armazenamento de arquivo do Azure com o Windows

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]

## Visão geral

O armazenamento de arquivos do Azure oferece compartilhamentos de arquivos na nuvem usando o protocolo SMB padrão. O armazenamento de arquivo agora está disponível e dá suporte a SMB 2.1 e a SMB 3.0.

Você pode criar compartilhamentos de arquivos do Azure usando o portal de visualização do Azure, os cmdlets do PowerShell de armazenamento do Azure, as bibliotecas de cliente de armazenamento do Azure ou APIs REST do armazenamento do Azure. Além disso, como os compartilhamentos de arquivos são compartilhamentos do SMB, você pode acessá-los por meio de APIs padrão e clássicas do sistema de arquivos.

Os aplicativos em execução no Azure podem facilmente montar compartilhamentos de arquivos a partir das máquinas virtuais do Azure. E com a versão mais recente do Armazenamento de arquivos, também é possível montar um compartilhamento de arquivos a partir de um aplicativo local que dá suporte ao SMB 3.0.

O armazenamento de arquivos baseia-se na mesma tecnologia de armazenamento de Blobs, Tabelas e Fila, o que significa que o armazenamento de arquivo pode aproveitar a disponibilidade, a durabilidade, a escalabilidade e a redundância geográfica existentes e incorporadas à plataforma de armazenamento do Azure.

Para saber mais sobre como usar o armazenamento de arquivos com o Linux, confira [Como usar o armazenamento de arquivos do Azure com Linux](storage-how-to-use-files-linux.md).

Para saber mais sobre metas de escalabilidade para armazenamento de arquivos, confira [Metas de desempenho e escalabilidade do armazenamento do Azure](storage-scalability-targets.md#scalability-targets-for-standard-storage-accounts).

[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

## Vídeo: Usando o armazenamento de arquivos do Azure com o Windows

Veja um vídeo que demonstra como criar e usar compartilhamentos de arquivos do Azure no Windows.

> [AZURE.VIDEO azure-file-storage-with-windows]

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

Agora há suporte de armazenamento de arquivos para todas as contas de armazenamento; assim, você pode usar uma conta de armazenamento existente ou criar uma nova conta de armazenamento. Confira [Como criar, gerenciar ou excluir uma conta de armazenamento](storage-create-storage-account.md#create-a-storage-account) para saber como criar uma nova conta de armazenamento.

## Use o portal de visualização do Azure para gerenciar um compartilhamento de arquivos

O [portal de visualização do Azure](https://ms.portal.azure.com/) fornece uma interface de usuário para que os clientes possam gerenciar o Armazenamento de arquivos. No portal de visualização, você pode:

- Carregar e baixar arquivos de e para o compartilhamento de arquivos
- Monitorar o uso real de cada compartilhamento de arquivos
- Ajustar a cota de tamanho de compartilhamento
- Obtenha o comando `net use` a ser usado para montar o compartilhamento de arquivos de um Windows Client 

## Usar o PowerShell para gerenciar um compartilhamento de arquivos

Em seguida, usaremos o PowerShell do Azure para criar um compartilhamento de arquivos. Quando o compartilhamento de arquivos tiver sido criado, você poderá montá-lo em qualquer sistema de arquivos que dê suporte a SMB 2.1 ou SMB 3.0.

### Instalar os cmdlets do PowerShell para Armazenamento do Azure

Para se preparar para usar o PowerShell, baixe e instale os cmdlets do PowerShell do Azure. Consulte [Como instalar e configurar o PowerShell do Azure](../install-configure-powershell.md) para obter o ponto e as instruções de instalação.

> [AZURE.NOTE]É recomendável baixar e instalar ou atualizar para o módulo mais recente do PowerShell do Azure.

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

> [AZURE.IMPORTANT]O nome do seu compartilhamento de arquivo deve estar em minúsculas. Para obter detalhes completos sobre como nomear arquivos e compartilhamentos de arquivos, confira [Nomenclatura e referência de compartilhamentos, diretórios, arquivos e metadados](https://msdn.microsoft.com/library/azure/dn167011.aspx).

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

A partir da versão 0.9.7 do Azure PowerShell, você pode copiar um arquivo em outro arquivo, um arquivo em um blob ou um blob em um arquivo. A seguir, demonstramos como executar essas operações de cópia usando cmdlets do PowerShell.

	# copy a file to the new directory
    Start-AzureStorageFileCopy -SrcShareName srcshare -SrcFilePath srcdir/hello.txt -DestShareName destshare -DestFilePath destdir/hellocopy.txt -Context $srcCtx -DestContext $destCtx

    # copy a blob to a file directory
    Start-AzureStorageFileCopy -SrcContainerName srcctn -SrcBlobName hello2.txt -DestShareName hello -DestFilePath hellodir/hello2copy.txt -DestContext $ctx -Context $ctx

## Montar o compartilhamento de arquivos 

Com suporte para SMB 3.0, o armazenamento de arquivos agora dá suporte a criptografia e identificadores persistentes de clientes SMB 3.0. O suporte a criptografia significa que os clientes SMB 3.0 podem montar um compartilhamento de arquivos de qualquer lugar, inclusive de:

- Uma máquina virtual do Azure na mesma região (também com suporte de SMB 2.1)
- Uma máquina virtual do Azure em uma região diferente (somente SMB 3.0)
- Um aplicativo cliente local (somente para o SMB 3.0) 

Quando um cliente acessa o armazenamento de arquivos, a versão SMB usada depende da versão SMB com suporte do sistema operacional. A tabela a seguir fornece um resumo de suporte para Windows Clients. Para obter mais detalhes, confira << Which version of the SMB protocol blog post>>.

| Windows Client | Com suporte à versão do SMB |
|------------------------|----------------------|
| Windows 7 | SMB 2.1 |
| Windows Server 2008 R2 | SMB 2.1 |
| Windows 8 | SMB 3.0 |
| Windows Server 2012 | SMB 3.0 |
| Windows Server 2012 R2 | SMB 3.0 |
| Windows 10 | SMB 3.0 |

### Montar o compartilhamento de arquivos de uma máquina virtual do Azure com o Windows em execução

Para demonstrar como montar um compartilhamento de arquivos do Azure, criaremos agora uma máquina virtual do Azure que executa o Windows e estabeleceremos comunicação remota nela para montar o compartilhamento.

1. Primeiro, crie uma nova máquina virtual do Azure seguindo as instruções em [Criar uma máquina virtual executando o Windows Server](../virtual-machines-windows-tutorial.md).
2. Em seguida, acesse remotamente a máquina virtual seguindo as instruções em [Como fazer logon em uma máquina virtual executando o Windows Server](../virtual-machines-log-on-windows-server.md).
3. Abra uma janela do PowerShell na máquina virtual.

### Persistir as credenciais da sua conta de armazenamento para a máquina virtual

Antes de montar o compartilhamento de arquivos, primeiro é preciso persistir as credenciais da sua conta de armazenamento na máquina virtual. Essa etapa permite ao Windows reconectar automaticamente ao compartilhamento de arquivos quando a máquina virtual é reinicializada. Para persistir as credenciais da sua conta, execute o comando `cmdkey` na janela do PowerShell na máquina virtual. Substitua `<storage-account-name>` pelo nome da conta de armazenamento e `<storage-account-key>` pela chave da conta de armazenamento.

	cmdkey /add:<storage-account-name>.file.core.windows.net /user:<storage-account-name> /pass:<storage-account-key>

O Windows reconectará ao compartilhamento de arquivo quando a máquina virtual for reinicializada. Você pode verificar se o compartilhamento foi reconectado executando o comando `net use` em uma janela do PowerShell.

Observe que as credenciais são mantidas somente no contexto no qual `cmdkey` é executado. Se você estiver desenvolvendo um aplicativo que é executado como um serviço, precisará manter suas credenciais nesse contexto também.

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

### Montar o compartilhamento de arquivos a partir de um cliente local que executa o Windows 

Para montar o compartilhamento de arquivos de um cliente local, siga estas etapas:

- Instale uma versão do Windows que dê suporte a SMB 3.0. O Windows aproveitará a criptografia SMB 3.0 para transferir dados com segurança entre o cliente local e o compartilhamento de arquivo do Azure na nuvem. 
- Abra o acesso à Internet para a porta 445 (TCP de saída) em sua rede local, conforme exigido pelo protocolo SMB. 

> [AZURE.NOTE]Alguns provedores de serviços de Internet podem bloquear a porta 445; portanto, é necessário verificar com seu provedor de serviços.

## Desenvolver com o armazenamento de arquivo

Para trabalhar com o armazenamento de arquivos programaticamente, você pode usar as bibliotecas de cliente de armazenamento para .NET e Java ou APIs REST de armazenamento do Azure. O exemplo nesta seção demonstra como trabalhar com um compartilhamento de arquivos usando a [Biblioteca de Clientes de Armazenamento do Azure .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409) de um aplicativo de console simples em execução na área de trabalho.

### Criar o aplicativo do console e obter o assembly

Para criar um novo aplicativo de console no Visual Studio e instalar o pacote NuGet no Armazenamento do Azure:

1. No Visual Studio, escolha **Arquivo > Novo Projeto** e **Windows > Aplicativo de Console** na lista de modelos do Visual C#.
2. Forneça um nome para o aplicativo de console e clique em **OK**.
3. Após o projeto ser criado, clique com o botão direito do mouse no projeto no Gerenciador de Soluções e escolha **Gerenciar Pacotes NuGet**. Pesquise online por "WindowsAzure.Storage" e clique em **Instalar** para instalar o pacote Armazenamento do Azure e as dependências.

### Salvar suas credenciais da conta de armazenamento no arquivo app.config

Em seguida, salve suas credenciais no arquivo app.config do projeto. Edite o arquivo app.config para que ele se pareça com o exemplo a seguir, substituindo `myaccount` pelo nome da conta de armazenamento e `mykey` pela chave da conta de armazenamento.

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	    <startup>
	        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
	    </startup>
	    <appSettings>
	        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
	    </appSettings>
	</configuration>

> [AZURE.NOTE]A versão mais recente do emulador de armazenamento do Azure não dá suporte ao armazenamento de arquivos. Sua cadeia de conexão deve ter como destino uma conta de armazenamento do Azure na nuvem para funcionar com o Armazenamento de arquivo.

### Adicionar declarações do namespace

Abra o arquivo program.cs no Gerenciador de Soluções e adicione as seguintes declarações de namespace à parte superior do arquivo.

	using Microsoft.WindowsAzure;
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Blob;
	using Microsoft.WindowsAzure.Storage.File;

### Recuperar sua cadeia de conexão programaticamente

Você pode recuperar suas credenciais salvas por meio do arquivo app.config usando a classe `Microsoft.WindowsAzure.CloudConfigurationManager` ou a classe `System.Configuration.ConfigurationManager `. O pacote do Gerenciador de Configuração do Microsoft Azure, que inclui a classe `Microsoft.WindowsAzure.CloudConfigurationManager`, está disponível no [Nuget](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager).

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

### Definir o tamanho máximo de um compartilhamento de arquivos

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

### Gerar uma assinatura de acesso compartilhado para um arquivo ou compartilhamento de arquivos

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

### Copiar arquivos

A partir da versão 5.x da Biblioteca de Cliente do Armazenamento do Azure, você pode copiar um arquivo em outro arquivo, um arquivo em um blob ou um blob em um arquivo. Nas próximas seções, demonstramos como executar essas operações de cópia de modo programático.

Você também pode usar o AzCopy para copiar um arquivo para outro, ou para copiar um blob em um arquivo ou vice-versa. Para obter detalhes sobre como copiar arquivos com o AzCopy, consulte [Como usar o AzCopy com o Armazenamento do Microsoft Azure](storage-use-azcopy.md#copy-files-in-azure-file-storage-with-azcopy-preview-version-only).

> [AZURE.NOTE]Se você estiver copiando um blob em um arquivo, ou um arquivo em um blob, use uma assinatura de acesso compartilhado (SAS) para autenticar o objeto de origem, mesmo se você estiver copiando dentro da mesma conta de armazenamento.

**Copiar um arquivo para outro arquivo**

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


**Copiar um arquivo em um blob**

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

## Solucionando problemas de armazenamento de arquivos usando métricas

A análise de armazenamento do Azure agora dá suporte a métricas para armazenamento de arquivos. Com dados de métricas, você pode rastrear solicitações e diagnosticar problemas.

Você pode habilitar métricas para armazenamento de arquivos no portal do Azure. Você também pode habilitar métricas programaticamente ao chamar a operação Definir Propriedades de Serviço do Arquivo pela API REST ou uma operação semelhante na biblioteca de cliente de armazenamento.

## Perguntas frequentes sobre armazenamento de arquivo

1. **A autenticação baseada no Active Directory tem suporte do armazenamento de arquivos?** 

	Atualmente não damos suporte a autenticação baseada no AD ou em ACLs, mas a temos em nossa lista de solicitações de recursos. Por enquanto, as chaves de conta de armazenamento do Azure são usadas para permitir a autenticação de compartilhamento de arquivos. Oferecemos uma solução alternativa usando SAS (assinaturas de acesso compartilhado) pela API REST ou pelas bibliotecas de cliente. Com a SAS, você pode gerar tokens com permissões específicas que são válidas em um intervalo de tempo especificado. Por exemplo, você pode gerar um token de acesso somente leitura a um determinado arquivo. Qualquer pessoa que possua esse token enquanto for válido terá acesso somente leitura ao arquivo.

	A SAS tem suporte somente via API REST ou bibliotecas de cliente. Quando você monta o compartilhamento de arquivos por meio do protocolo SMB, não pode usar uma SAS para delegar acesso a seu conteúdo.

2. **Os compartilhamentos de arquivo do Azure são visíveis publicamente na Internet ou somente podem ser acessados do Azure?**
 
	Desde que a porta 445 (TCP de saída) esteja aberta e o cliente dê suporte ao protocolo SMB 3.0 (*por exemplo,*, o Windows 8 ou o Windows Server 2012), o compartilhamento de arquivos está disponível por meio da Internet.

3. **O tráfego de rede entre uma máquina virtual do Azure e uma contagem de compartilhamento de arquivo conta como largura de banda externa cobrada na assinatura?**

	Se o compartilhamento de arquivos e a máquina virtual estiverem em regiões diferentes, o tráfego entre elas será cobrado como largura de banda externa.
 
4. **Se o tráfego de rede estiver entre uma máquina virtual e um compartilhamento de arquivos na mesma região, ele será gratuito?**

	Sim. Será gratuito se o tráfego estiver na mesma região.

5. **A conexão de máquinas virtuais locais ao armazenamento de arquivo do Azure depende da Rota Expressa do Azure?**

	Não. Se você não tiver a Rota Expressa, ainda poderá acessar o compartilhamento de arquivos do local, desde que a porta 445 (TCP de saída) esteja aberta para acesso à Internet. No entanto, se desejar, você pode usar a Rota Expressa com o armazenamento de arquivos.

6. **Uma “testemunha de compartilhamento de arquivo” de um cluster de failover é um dos casos de uso para armazenamento de arquivo do Azure?**

	Não há suporte para isso no momento.
 
7. **O armazenamento de arquivos é replicado somente via LRS ou GRS agora, certo?**

	Planejamos dar suporte a RA-GRS, mas ainda não há nenhum cronograma para compartilhar.

8. **Quando posso usar contas de armazenamento existentes no armazenamento de arquivo do Azure?**

	O armazenamento de arquivos do Azure agora está habilitado para todas as contas de armazenamento.

9. **Uma operação de renomeação também será adicionada à API REST?**

	Ainda não há suporte para Renomear na nossa API REST.

10. **Pode-se ter compartilhamentos aninhados, ou seja, um compartilhamento em outro compartilhamento?**

	Não. O compartilhamento de arquivos é o driver virtual que você pode montar; portanto, não há suporte para compartilhamentos aninhados.

11. **É possível especificar permissões somente leitura ou somente gravação em pastas no compartilhamento?**

	Você não tem esse nível de controle sobre as permissões se montar o compartilhamento de arquivos via SMB. No entanto, você pode conseguir isso criando uma SAS (assinatura de acesso compartilhado) por meio de bibliotecas de API REST ou de cliente.

12. **O desempenho era lento durante a tentativa de descompactar arquivos no Armazenamento de arquivos. O que devo fazer?**

	Para transferir grandes quantidades de arquivos para o armazenamento de arquivos, recomendamos que você use o AzCopy, o Azure Powershell (Windows) ou a CLI do Azure (Unix/Linux), já que essas ferramentas foram otimizadas para transferência de rede.

## Próximas etapas

Consulte estes links para obter mais informações sobre o armazenamento de arquivo do Azure.

### Artigos e vídeos conceituais

- [Armazenamento de Arquivos do Azure: um sistema de arquivos SMB de nuvem ininterrupto SMB para Windows e Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
- [Como utilizar o armazenamento de arquivos do Azure com Linux](storage-how-to-use-files-linux.md)

### Suporte de ferramentas para o armazenamento de arquivos

- [Usando o PowerShell do Azure com o Armazenamento do Azure](storage-powershell-guide-full.md)
- [Como usar o AzCopy com o Armazenamento do Microsoft Azure](storage-use-azcopy.md)
- [Usando a CLI do Azure com o Armazenamento do Azure](storage-azure-cli.md#create-and-manage-file-shares)

### Referência

- Referência à [Biblioteca de Cliente de Armazenamento para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
- [Referência à API REST do serviço de arquivos](http://msdn.microsoft.com/library/azure/dn167006.aspx)

### Postagens no blog

- [O Armazenamento de arquivos do Azure agora está disponível ao público geral](http://go.microsoft.com/fwlink/?LinkID=626728&clcid=0x409)
- [Aprofunde-se com o Armazenamento de arquivos do Azure](http://go.microsoft.com/fwlink/?LinkID=626729&clcid=0x409) 
- [Apresentando o serviço de arquivo do Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
- [Persistindo conexões para arquivos do Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)

<!---HONumber=Nov15_HO2-->