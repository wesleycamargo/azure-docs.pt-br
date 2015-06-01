<properties 
	pageTitle="Como utilizar o armazenamento de arquivos do Azure | Microsoft Azure" 
	description="Saiba como utilizar o armazenamento de arquivos do Microsoft Azure para criar compartilhamentos de arquivos e gerenciar o conteúdo de arquivos. As amostras são escritas em PowerShell e C#." 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/06/2015" 
	ms.author="tamram"/>

# Como utilizar o armazenamento de arquivos do Azure

## Visão geral

Neste guia de introdução, demonstramos os fundamentos sobre como utilizar o armazenamento de arquivos do Microsoft Azure. Primeiramente, utilizamos o PowerShell para mostrar como criar um novo compartilhamento de arquivo do Azure, adicionar um diretório, fazer upload de um arquivo local no compartilhamento e listar os arquivos no diretório. Em seguida, mostramos como montar o compartilhamento de arquivo por meio de uma máquina virtual do Azure, da mesma forma como seria feito com qualquer compartilhamento SMB.

Para usuários que possam querer acessar os arquivos em um compartilhamento por meio de um aplicativo local, bem como por meio de uma máquina virtual ou serviço de nuvem do Azure, mostramos como usar a Biblioteca de Cliente de Armazenamento para .NET do Azure para trabalhar com compartilhamento de arquivos em um aplicativo de área de trabalho.

> [AZURE.NOTE] Executar os exemplos de código do .NET neste guia exige a Biblioteca de Cliente de Armazenamento para .NET do Azure 4.x ou versão posterior. A Biblioteca de Cliente de Armazenamento está disponível por meio do [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/).

## O que é o armazenamento de arquivos do Azure?

O armazenamento de arquivos oferece armazenamento compartilhado para aplicativos usando o protocolo padrão SMB 2.1. As máquinas virtuais e os serviços de nuvem do Microsoft Azure podem compartilhar dados de arquivos entre componentes de aplicativos por meio de compartilhamentos montados, e aplicativos locais podem acessar dados de arquivos em um compartilhamento por meio da API de armazenamento de arquivo.

Aplicativos executados em máquinas virtuais ou em serviços de nuvem do Azure podem montar um compartilhamento de armazenamento de arquivos para acessar dados de arquivos, da mesma forma que um aplicativo de área de trabalho montaria um compartilhamento SMB típico. Qualquer quantidade de máquinas virtuais ou funções do Azure podem montar e acessar o compartilhamento de armazenamento de arquivos simultaneamente.

Uma vez que um compartilhamento de armazenamento de arquivos é um compartilhamento de arquivo SMB 2.1 padrão, os aplicativos executados no Azure podem acessar dados no compartilhamento por meio das APIs de E/S de arquivos. Os desenvolvedores podem, portanto, utilizar seus códigos e habilidades existentes para migrar aplicativos existentes. Os profissionais de TI podem usar cmdlets do PowerShell para criar, montar e gerenciar compartilhamentos de armazenamento de arquivos como parte da administração de aplicativos do Azure. Este guia mostrará exemplos de ambos os casos.

Os usos comuns do armazenamento de arquivos incluem:

- Migrar aplicativos locais que dependem dos compartilhamentos de arquivos para serem executados em máquinas virtuais ou em serviços de nuvem do Azure, sem regravações caras
- Armazenar configurações de aplicativos compartilhados, por exemplo, em arquivos de configuração
- Armazenar dados de diagnóstico, como logs, métricas e despejos de falha em um local compartilhado 
- Armazenar ferramentas e utilitários necessários para desenvolver ou administrar máquinas virtuais ou serviços de nuvem do Azure

## Conceitos do armazenamento de arquivo

O armazenamento de arquivos contém os seguintes componentes:

![files-concepts][files-concepts]


-   **Conta de armazenamento:** todo o acesso ao armazenamento do Azure é feito por meio de uma conta de armazenamento. Consulte [Metas de desempenho e escalabilidade do armazenamento do Azure](http://msdn.microsoft.com/library/azure/dn249410.aspx) para obter detalhes sobre a capacidade da conta de armazenamento.

-   **Compartilhamento:** um compartilhamento de arquivo de armazenamento é um compartilhamento de arquivos SMB 2.1 no Azure.
    Todos os arquivos e diretórios devem ser criados em um compartilhamento pai. Uma conta de armazenamento pode conter um número ilimitado de compartilhamentos, e um compartilhamento pode conter um número ilimitado de arquivos, até os limites de capacidade da conta de armazenamento.

-   **Diretório:** uma hierarquia opcional de diretórios. 

-	**Arquivo:** um arquivo no compartilhamento. Um arquivo pode ser de até 1 TB.

-   **Formato de URL:** os arquivos são endereçáveis usando o seguinte formato
    format:   
    https://`<storage
    account>`.file.core.windows.net/`<share>`/`<directory/directories>`/`<file>`  
    
    A URL de exemplo a seguir poderia ser usada para endereçar um dos arquivos no diagrama acima:  
    `http://acmecorp.file.core.windows.net/cloudfiles/diagnostics/log.txt`



Para obter detalhes sobre como nomear compartilhamentos, diretórios e arquivos, consulte [Nomeando e fazendo referência a compartilhamentos, diretórios, arquivos e metadados](http://msdn.microsoft.com/library/azure/dn167011.aspx).

## Criar uma conta de Armazenamento do Azure

O armazenamento de arquivos do Azure está atualmente em visualização. Para solicitar acesso à visualização, vá até a [página de visualização do Microsoft Azure](/services/preview/) e solicite acesso aos **Arquivos do Azure**. Após a solicitação ser aprovada, você será notificado de que pode acessar a visualização do armazenamento de arquivos. Você pode então criar uma conta de armazenamento para acessar o armazenamento de arquivos.

> [AZURE.NOTE] O armazenamento de arquivos está disponível atualmente apenas para novas contas de armazenamento. Após ser concedido acesso à sua assinatura para o armazenamento de arquivos, crie uma nova conta de armazenamento para ser usada com este guia.

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

## Usar o PowerShell para criar um compartilhamento de arquivo

Em seguida, usaremos o PowerShell do Azure para criar um compartilhamento de arquivos. Quando o compartilhamento de arquivos tiver sido criado, você poderá montá-lo em qualquer sistema de arquivos que dê suporte a SMB 2.1. 

### Instalar os cmdlets do PowerShell para Armazenamento do Azure

Para se preparar para usar o PowerShell, baixe e instale os cmdlets do PowerShell do Azure. Consulte [Como instalar e configurar o PowerShell do Azure](install-configure-powershell.md) para ver as instruções de instalação e o ponto de instalação.

> [AZURE.NOTE] Os cmdlets do PowerShell para o serviço de arquivos estão disponíveis somente no módulo mais recente do PowerShell do Azure, versão 0.8.5 e posterior. É recomendável baixar e instalar ou atualizar para o módulo mais recente do PowerShell do Azure.

Abra uma janela do PowerShell do Azure clicando em **Iniciar** e digitando **PowerShell do Azure**. A janela do PowerShell do Azure carrega do módulo do PowerShell do Azure para você.

### Criar um contexto para sua conta e chave de armazenamento

Agora, crie o contexto da conta de armazenamento. O contexto encapsula o nome e a chave da conta. Substitua `account-name` e `account-key` pelo nome e a chave da sua conta no seguinte exemplo:

    # create a context for account and key
    $ctx=New-AzureStorageContext account-name account-key
    
### Criar um novo compartilhamento de arquivo

Em seguida, crie o nove compartilhamento, denominado `sampleshare` neste exemplo:

    # create a new share
    $s = New-AzureStorageShare sampleshare -Context $ctx

Agora você tem um compartilhamento de arquivo no armazenamento de arquivos. Em seguida, adicionaremos um diretório e um arquivo.

### Criar um diretório no compartilhamento de arquivos

Em seguida, crie um diretório no compartilhamento. No exemplo a seguir, o diretório é nomeado `sampledir`:

    # create a directory in the share
    New-AzureStorageDirectory -Share $s -Path sampledir

### Carregar um arquivo local no diretório

Agora, carregue um arquivo local no diretório. O exemplo a seguir carrega um arquivo de `C:\temp\samplefile.txt`. Edite o caminho do arquivo para que ele aponte para um arquivo válido em seu computador local: 
    
    # upload a local file to the new directory
    Set-AzureStorageFileContent -Share $s -Source C:\temp\samplefile.txt -Path sampledir

### Listar os arquivos no diretório

Para ver o arquivo no diretório, você pode listar os arquivos do diretório. Esse comando também listará os subdiretórios, mas como não há um subdiretório neste exemplo, somente o arquivo será listado.  

    # list files in the new directory
    Get-AzureStorageFile -Share $s -Path sampledir

## Montar o compartilhamento de uma máquina virtual do Azure que executa o Windows

Para demonstrar como montar um compartilhamento de arquivos do Azure, criaremos agora uma máquina virtual do Azure que executa o Windows e estabeleceremos comunicação remota nela para montar o compartilhamento. 

1. Primeiramente, crie uma nova máquina virtual do Azure seguindo as instruções em [Criar uma máquina virtual executando o Windows Server](virtual-machines-windows-tutorial.md).
2. Em seguida, acesse remotamente a máquina virtual seguindo as instruções em [Como fazer logon em uma máquina virtual executando o Windows Server](virtual-machines-log-on-windows-server.md).
3. Abra uma janela do PowerShell na máquina virtual. 

### Persistir as credenciais da sua conta de armazenamento para a máquina virtual

Antes de montar o compartilhamento de arquivos, primeiro é preciso persistir as credenciais da sua conta de armazenamento na máquina virtual. Essa etapa permite ao Windows reconectar automaticamente ao compartilhamento de arquivos quando a máquina virtual é reinicializada. Para persistir as credenciais da sua conta, execute o comando `cmdkey` na janela do PowerShell na máquina virtual. Substitua `<storage-account>` pelo nome da sua conta de armazenamento e `<account-key>` pela chave da conta de armazenamento:

	cmdkey /add:<storage-account>.file.core.windows.net /user:<storage-account> /pass:<account-key>

O Windows reconectará ao compartilhamento de arquivo quando a máquina virtual for reinicializada. Você pode verificar se o compartilhamento foi reconectado executando o comando `net use` em uma janela do PowerShell.

### Montar o compartilhamento de arquivos usando as credenciais persistidas

Após ter uma conexão remota com a máquina virtual, você poderá executar o comando  `net use` para montar o compartilhamento de arquivos usando a sintaxe a seguir. Substitua `<storage-account>` pelo nome da sua conta de armazenamento e `<share-name>` pelo nome do compartilhamento de armazenamento de arquivos.

	net use z: <storage-account>.file.core.windows.net<share-name>

> [AZURE.NOTE] Como você persistiu as credenciais da conta de armazenamento na etapa anterior, não será preciso fornecê-las com o comando  `net use`. Se ainda não tiver persistido suas credenciais, inclua-as como um parâmetro passado para o comando  `net use`. Substitua `<storage-account>` pelo nome da conta de armazenamento, `<share-name>` pelo nome do compartilhamento de armazenamento de arquivos e `<account-key>` pela chave da conta de armazenamento:
	   
	net use z: <storage-account>.file.core.windows.net<share-name> /u:<storage-account> <account-key>

Você agora pode trabalhar com o compartilhamento de armazenamento de arquivos na máquina virtual como faria com qualquer outra unidade. Você pode emitir comandos de arquivo padrão do prompt de comando ou exibir o compartilhamento montado e seu conteúdo no Explorador de Arquivos. Você também pode executar código na máquina virtual que avalia o compartilhamento de arquivos usando as APIs de E/S de arquivos padrão do Windows, como as fornecidas pelos [namespaces do System.IO](http://msdn.microsoft.com/library/gg145019(v=vs.110).aspx) no .NET Framework. 

Você também pode montar o compartilhamento de arquivos por meio de uma função executada em um serviço de nuvem do Azure ao estabelecer comunicação remota à função.

## Criar um aplicativo local para trabalhar com o armazenamento de arquivos

Você pode montar o compartilhamento de arquivos em uma máquina virtual ou serviço de nuvem executado no Azure, como demonstrado acima. No entanto, não é possível montar o compartilhamento de arquivos por meio de um aplicativo local. Para acessar os dados de compartilhamento por meio de um aplicativo local, você deve usar a API de armazenamento de arquivos. Este exemplo demonstra como trabalhar com um compartilhamento de arquivo por meio da [Biblioteca de Cliente de Armazenamento para .NET do Azure](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409). 

Para mostrar como usar a API por meio de um aplicativo local, criaremos um aplicativo de console simples sendo executado em área de trabalho.

### Criar o aplicativo do console e obter o assembly

Para criar um novo aplicativo de console no Visual Studio e instalar o pacote NuGet no Armazenamento do Azure:

1. No Visual Studio, escolha **Arquivo** -> **Novo Projeto** e escolha **Windows** -> **Aplicativo de Console** na lista de modelos do Visual C#.
2. Forneça um nome para o aplicativo de console e clique em **OK**.
3. Após o projeto ser criado, clique com o botão direito do mouse no projeto no Gerenciador de Soluções e escolha **Gerenciar Pacotes NuGet**. Pesquise online por "WindowsAzure.Storage" e clique em **Instalar** para instalar o pacote Armazenamento do Azure e as dependências.

### Salvar suas credenciais da conta de armazenamento no arquivo app.config

Em seguida, salve suas credenciais no arquivo app.config do projeto. Edite o arquivo app.config para que ele pareça semelhante ao exemplo a seguir, substituindo  `myaccount` pelo nome da sua conta de armazenamento e  `mykey` pela chave da sua conta de armazenamento:

    <?xml version="1.0" encoding="utf-8" ?>
	<configuration>
		<startup> 
			<supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
		</startup>
		<appSettings>
			<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey" />
		</appSettings>
	</configuration>

> [AZURE.NOTE] A versão mais recente do emulador de armazenamento do Azure não dá suporte ao armazenamento de arquivos. Sua cadeia de conexão deve ter com destino uma conta de armazenamento do Azure na nuvem com acesso à visualização de arquivos.


### Adicionar declarações do namespace
Abra o arquivo program.cs por meio do Gerenciador de Soluções e adicione as seguintes declarações de namespace à parte superior do arquivo:

    using Microsoft.WindowsAzure;
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.File;

### Recuperar sua cadeia de conexão programaticamente
Você pode recuperar suas credenciais salvas por meio do arquivo app.config usando a classe `Microsoft.WindowsAzure.CloudConfigurationManager` ou a classe `System.Configuration.ConfigurationManager `. O exemplo aqui mostra como recuperar suas credenciais usando a classe `CloudConfigurationManager` e encapsulá-las com a classe  `CloudStorageAccount`. Adicione o seguinte código ao método  `Main()` em program.cs:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

### Acessar o compartilhamento de armazenamento de arquivo programaticamente

Em seguida, adicione o código a seguir ao método  `Main()` após o código mostrado acima para recuperar a cadeia de conexão. Esse código obtém uma referência para o arquivo que criamos anteriormente e exporta seu conteúdo para a janela do console.

	//Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

	//Get a reference to the file share we created previously.
    CloudFileShare share = fileClient.GetShareReference("sampleshare");

	//Ensure that the share exists.
    if (share.Exists())
    {
		//Get a reference to the root directory for the share.
        CloudFileDirectory rootDir = share.GetRootDirectoryReference();

		//Get a reference to the sampledir directory we created previously.
        CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("sampledir");

		//Ensure that the directory exists.
        if (sampleDir.Exists())
        {
			//Get a reference to the file we created previously.
            CloudFile file = sampleDir.GetFileReference("samplefile.txt");

			//Ensure that the file exists.
            if (file.Exists())
            {
				//Write the contents of the file to the console window.
                Console.WriteLine(file.DownloadTextAsync().Result);
            }
        }
    }

Execute o aplicativo de console para ver a saída.

## Montar o compartilhamento de uma máquina virtual do Azure executando o Linux

Ao criar uma máquina virtual do Azure, você pode especificar uma imagem do Ubuntu da galeria de imagens de disco para garantir suporte a SMB 2.1. No entanto, qualquer distribuição do Linux que dá suporte ao SMB 2.1 pode montar um compartilhamento de arquivos do Azure. 

Para uma demonstração de como montar um compartilhamento de arquivos do Azure no Linux, consulte [Armazenamento compartilhado em Linux por meio da visualização de arquivos do Azure - parte 1](http://channel9.msdn.com/Blogs/Open/Shared-storage-on-Linux-via-Azure-Files-Preview-Part-1) no Channel 9.

## Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de arquivos, siga estes links
para obter informações mais detalhadas.
<ul>
<li>Consulte a documentação de referência de serviço de arquivos para obter detalhes completos sobre as APIs disponíveis:
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Referência à Biblioteca de Cliente de Armazenamento para .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/library/azure/dn167006.aspx">Referência à API REST do serviço de arquivos</a></li>
  </ul>
</li>
<li>Consulte as postagens do blog da Equipe de Armazenamento do Azure relativas ao serviço de arquivos:
  <ul>
    <li><a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx">Apresentando o serviço de arquivo do Microsoft Azure</a>
    </li>
    <li><a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx">Persistindo conexões para arquivos do Microsoft Azure</a></li>
  </ul>
</li><li>Consulte outros guias de recursos para obter informações sobre opções adicionais para armazenar dados no Azure.
  <ul>
    <li>Usar o <a href="/documentation/articles/storage-dotnet-how-to-use-blobs/">Armazenamento de Blob</a> para armazenar dados não estruturados.</li>
    <li>Usar o <a href="/documentation/articles/storage-dotnet-how-to-use-tables/">Armazenamento de Tabela</a> para armazenar dados estruturados.</li>
    <li>Usar o <a href="/documentation/articles/storage-dotnet-how-to-use-queues/">Armazenamento de Fila</a> para armazenar mensagens de maneira confiável.</li>
    <li>Usar o <a href="/documentation/articles/sql-database-dotnet-how-to-use/">Banco de Dados SQL</a> para armazenar dados relacionais.</li>
  </ul>
</li>
</ul>

[files-concepts]: ./media/storage-dotnet-how-to-use-files/files-concepts.png


<!--HONumber=49--> 

<!--HONumber=49-->