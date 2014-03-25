<properties linkid="dev-net-2-how-to-blob-storage" urlDisplayName="Serviço Blob (2.0)" pageTitle="Como usar o Blob Storage - Guia de recursos do Windows Azure" metaKeywords="Introdução ao Blob no Azure   Dados não estruturados do Azure   Armazenamento não estruturado do Azure   Blob no Azure   Blob Storage do Azure   .NET para Blob no Azure   C# para Blob no Azure   C# para Blob no Azure" description="Saiba como usar o Serviço Blob no Windows Azure para carregar, baixar, listar e excluir conteúdo blob. As amostras são escritas em C#." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Como usar o Serviço Blob Storage no Windows Azure no .NET" />


# Como usar o Serviço Blob Storage no Windows Azure no .NET

<div class="dev-center-tutorial-selector">
<a href="/pt-br/develop/net/how-to-guides/blob-storage-v17/" title="versão 1.7">versão 1.7</a>
<a href="/pt-br/develop/net/how-to-guides/blob-storage/" title="versão 2.0" class="current">versão 2.0</a></div>


Este guia demonstra como executar cenários comuns usando o Serviço Blob Storage no Windows Azure. As amostras são escritas em C\# e usam a Biblioteca de Cliente de Armazenamento Windows Azure para .NET (Versão 2.0) no Windows Azure. Os cenários cobertos incluem **carregamento**, **listagem**, **download** e **exclusão** de blobs. Para obter mais informações sobre blobs, consulte a seção [Próximas etapas][].

##Sumário

-   [O que é Blob Storage][]
-   [Conceitos][]
-   [Criar uma conta de Armazenamento do Windows Azure][]
-   [Configurar uma cadeia de conexão de armazenamento][]
-   [Como acessar o blob storage programaticamente][]
-   [Como criar um contêiner][]
-   [Como carregar um blob em um contêiner][]
-   [Como listar os blobs em um contêiner][]
-   [Como baixar blobs][]
-   [Como excluir blobs][]
-   [Próximas etapas][]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

##<a name="create-account"></a><span  class="short-header">Criar uma conta</span>Criar uma conta de Armazenamento do Windows Azure
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

##<a name="setup-connection-string"></a><span  class="short-header">Configurar uma cadeia de conexão</span>Configurar uma cadeia de conexão de armazenamento

A Biblioteca de Cliente de Armazenamento Windows Azure para .NET oferece suporte ao uso de uma cadeia de conexão de armazenamento para configurar pontos de extremidade e credenciais para acesso a serviços de armazenamento. Você pode colocar a cadeia de conexão de armazenamento em um arquivo de configuração, em vez de embuti-la no código:

- Ao usar os Serviços de Nuvem do Windows Azure, é recomendável armazenar a cadeia de conexão usando o sistema de configuração de serviço do Windows Azure (arquivos '*.csdef' e '*.cscfg').
- Ao usar Sites e Máquinas Virtuais do Windows Azure ou ao criar aplicativos .NET destinados para serem executados fora do Windows Azure, é recomendável armazenar sua cadeia de conexão usando o sistema de configuração do .NET (por exemplo, arquivo 'web. config' ou 'app. config').

A recuperação de sua cadeia de conexão é mostrada neste guia.

### Configurando a cadeia de conexão ao usar os Serviços de Nuvem

O mecanismo de configuração de serviço é exclusivo para projetos de Serviços de Nuvem do Windows Azure e permite que você altere dinamicamente os parâmetros de configuração no Portal de Gerenciamento do Windows Azure sem reimplantar o aplicativo.

Para configurar a cadeia de conexão na configuração de serviço do Windows Azure:

1.  No Gerenciador de Soluções do Visual Studio, na pasta **Funções** do Projeto de Implantação do Windows Azure, clique com o botão direito do mouse na sua função web ou função de trabalho e clique em **Propriedades**.  
    ![Selecione as propriedades em uma função de Serviço de Nuvem do Visual Studio][Blob5]

2.  Clique na guia **Configurações** e pressione o botão **Adicionar Configuração**.  
    ![Adicione uma configuração de Serviço de Nuvem do Visual Studio][Blob6]

    Uma nova entrada **Setting1** será mostrada na grade de configurações.

3.  No menu suspenso **Tipo** da nova entrada **Setting1**, escolha **Cadeia de Conexão**.  
    ![Blob7][Blob7]

4.  Clique no botão **...** na extremidade direita da entrada **Setting1**.
    A caixa de diálogo **Cadeia de Conexão da Conta de Armazenamento** será aberta.

5.  Escolha se deseja direcionar o emulador de armazenamento (armazenamento do Windows Azure simulado em sua máquina local) ou uma conta de armazenamento na nuvem. O código deste guia funciona com qualquer uma dessas opções. Digite o valor da **Chave de Acesso Primária** copiado na etapa anterior deste tutorial, se desejar armazenar dados blob na conta de armazenamento que criamos anteriormente no Windows Azure.   
    ![Blob8][Blob8]

6.  Altere a entrada **Nome** de **Setting1** para um nome mais amigável como **StorageConnectionString**. Você fará referência a essa
cadeia de conexão mais tarde no código deste guia.  
    ![Blob9][Blob9]
	
### Configurando sua cadeia de conexão usando a configuração do .NET

Se você estiver escrevendo um aplicativo que não seja um serviço de nuvem do Windows Azure (consulte a seção anterior), é recomendável usar o sistema de configuração do .NET (por exemplo, 'Web. config' ou 'App. config').  Isso inclui os Sites ou as Máquinas Virtuais do Windows Azure, bem como aplicativos desenvolvidos para serem executados fora do Windows Azure.  Você armazena a cadeia de conexão usando o elemento '<appSettings>' da seguinte maneira:

	<configuration>
  		<appSettings>
    		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey" />
  		</appSettings>
	</configuration>

Leia [Configurando cadeias de conexão][] para obter mais informações sobre cadeias de conexão de armazenamento.
	
Agora você está pronto para executar as tarefas das instruções deste guia.

## <a name="configure-access"> </a><span  class="short-header">Acessar programaticamente</span>Como acessar o armazenamento de blob programaticamente

###Obtendo o assembly
Você pode usar o NuGet para obter o assembly 'Microsoft.WindowsAzure.Storage.dll'. Clique com o botão direito do mouse no **Gerenciador de Soluções** e escolha **Gerenciar Pacotes NuGet**  Pesquise online por "WindowsAzure.Storage" e clique em **Instalar** para instalar o pacote Armazenamento do Windows Azure e as dependências.

O `Microsoft.WindowsAzure.Storage.dll` também é incluído no SDK do Windows Azure para .NET 2.0, que pode ser baixado na <a href="http://www.windowsazure.com/pt-br/develop/net/#">Central de desenvolvedores do .NET (a página pode estar em inglês)</a>. O assembly é instalado no diretório '%Arquivos de Programas%\Microsoft SDKs\Windows Azure\ .NET SDK\v2.0\ref\'.

###Declarações de namespace
Adicione as seguintes declarações de namespace à parte superior de qualquer arquivo C\# no qual você deseje acessar o Armazenamento do Windows Azure programaticamente:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;

Faça referência ao assembly 'Microsoft.WindowsAzure.Storage.dll'.

###Recuperando sua cadeia de conexão
Você pode usar o tipo **CloudStorageAccount** para representar as informações de sua Conta de Armazenamento. Se estiver usando um modelo de projeto do Windows Azure e/ou tiver uma referência ao Microsoft.WindowsAzure.CloudConfigurationManager, você poderá usar o tipo **CloudConfigurationManager** para recuperar a cadeia de conexão do armazenamento e as informações da conta de armazenamento na configuração de serviço do Windows Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Se estiver criando um aplicativo sem nenhuma referência ao Microsoft.WindowsAzure.CloudConfigurationManager, e sua cadeia de conexão estiver localizada no 'web.config' ou no 'app.config' como mostrado acima, você poderá usar o **ConfigurationManager** para recuperar a cadeia de conexão.  Você precisará adicionar uma referência ao System.Configuration.dll a seu projeto e adicionar outra declaração de namespace para ele:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

O tipo **CloudBlobClient** permite que você recupere objetos que representam os contêineres e blobs armazenados no Blob Storage Service. O código a seguir cria um objeto **CloudBlobClient** usando o objeto da conta de armazenamento que recuperamos acima:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

###Dependências do ODataLib
As dependências do ODataLib na Biblioteca de Cliente de Armazenamento para .NET são resolvidas por meio de pacotes do ODataLib (versão 5.0.2) disponíveis por meio do NuGet e não do WCF Data Services.  As bibliotecas do ODataLib podem ser baixadas diretamente ou referenciadas por seu projeto de código por meio do NuGet.  Os pacotes ODataLib específicos são [OData], [Edm] e [Spatial].

## <a name="create-container"> </a><span  class="short-header">Criar um contêiner</span>Como criar um contêiner

Todos os blobs de armazenamento residem em um contêiner. Você pode usar um objeto **CloudBlobClient** para obter uma referência ao contêiner que deseja usar. Você pode criar o contêiner se ele não existir:

    // Recuperar a conta de armazenamento da cadeia de conexão.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de blob.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Recuperar uma referência a um contêiner. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Criar o contêiner se ele ainda não existir.
    container.CreateIfNotExists();

Por padrão, o novo contêiner é particular e você deve especificar sua chave de acesso de armazenamento para fazer o download dos blobs desse recipiente. Se desejar disponibilizar os arquivos dentro do contêiner para todas as pessoas, você pode definir o contêiner como público usando o seguinte código:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
 	    BlobContainerPublicAccessType.Blob }); 

Qualquer pessoa na internet pode ver blobs em um contêiner público, mas você pode modificar ou excluí-los somente se tiver a chave de acesso apropriada.

<h2> <a name="upload-blob"> </a><span  class="short-header">Carregar um contêiner</span>Como carregar um blob em um contêiner</h2>

O Blob Storage no Windows Azure oferece suporte a blobs de blocos e a blobs de página.  Na maioria dos casos, o blob de blocos é o tipo recomendado a ser usado.

Para carregar um arquivo em um blob de blocos, obtenha uma referência de contêiner e use-a para obter uma referência de blob de blocos. Depois de ter uma referência de blob, você pode carregar qualquer fluxo de dados nele chamando o método **UploadFromStream**. Essa operação criará o blob, caso ele não existisse anteriormente, ou o substituirá, caso ele já exista. O exemplo a seguir mostra como carregar um blob em um contêiner e pressupõe que o contêiner já tenha sido criado.

    // Recuperar a conta de armazenamento da cadeia de conexão.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de blob.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Recuperar uma referência a um contêiner criado anteriormente.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Recuperar uma referência a um blob denominado "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Criar ou substituir o blob "myblob" com o conteúdo de um arquivo local.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    } 

##<a name="list-blob"> </a><span  class="short-header">Listar blobs em um contêiner</span>Como listar blobs em um contêiner

Para listar blobs em um contêiner, primeiro obtenha uma referência ao contêiner. Você pode usar o método **ListBlobs** do contêiner para recuperar os blobs e/ou diretórios dentro dele. Para acessar o conjunto avançado de propriedades e métodos de um **IListBlobItem** retornado, você deve convertê-lo em um objeto **CloudBlockBlob**, 
**CloudPageBlob** ou **CloudBlobDirectory**.  Se o tipo for desconhecido, você poderá usar uma verificação de tipo para determinar no qual convertê-lo.  O código a seguir demonstra como recuperar e apresentar a saída do URI de cada item no contêiner 'photos':

    // Recuperar a conta de armazenamento da cadeia de conexão.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de blob. 
	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Recuperar uma referência a um contêiner criado anteriormente.
	CloudBlobContainer container = blobClient.GetContainerReference("photos");

	// Executar um loop sobre os itens no contêiner e fornecer a saída do comprimento e do URI.
	foreach (IListBlobItem item in container.ListBlobs(null, false))
	{
		if (item.GetType() == typeof(CloudBlockBlob))
		{
			CloudBlockBlob blob = (CloudBlockBlob)item;

			Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
                                        
		}
		else if (item.GetType() == typeof(CloudPageBlob))
		{
			CloudPageBlob pageBlob = (CloudPageBlob)item;

			Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

		}
		else if (item.GetType() == typeof(CloudBlobDirectory))
		{
			CloudBlobDirectory directory = (CloudBlobDirectory)item;
			
			Console.WriteLine("Directory: {0}", directory.Uri);
		}
	}

Como mostrado acima, o serviço de blob também tem o conceito de diretórios dentro de contêineres. Isso é para que você possa organizar seus blobs em uma estrutura mais semelhante a uma pasta. Por exemplo, considere o seguinte conjunto de blobs de blocos em um contêiner chamado 'photos':

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

Quando você chama **ListBlobs** no contêiner 'photos' (como no exemplo acima), a coleção retornada conterá objetos **CloudBlobDirectory** e **CloudBlockBlob** que representam os diretórios e os blobs contidos no nível superior. Esta seria a saída resultante:

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Opcionalmente, você pode definir o parâmetro **UseFlatBlobListing** do método **ListBlobs** como **true**. Isso resultaria em cada blob sendo retornado como um **CloudBlockBlob**, independentemente da pasta.  Esta seria a chamada para **ListBlobs**:

    // Executar um loop sobre os itens no contêiner e fornecer a saída do comprimento e do URI.
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}

e estes seriam os resultados:

	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Para obter mais informações, consulte [CloudBlobContainer.ListBlobs][].

## <a name="download-blobs"> </a><span  class="short-header">Baixar blobs</span>Como baixar blobs

Para baixar blobs, primeiro recupere uma referência a um blob e, em seguida, chame o método **DownloadToStream**. O exemplo a seguir usa o método **DownloadToStream** para transferir o conteúdo blob para um objeto de fluxo que você pode persistir para um arquivo local.

    // Recuperar a conta de armazenamento da cadeia de conexão.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de blob.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Recuperar uma referência a um contêiner criado anteriormente.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Recuperar uma referência a um blob denominado "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Salvar o conteúdo do blob em um arquivo.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    } 

Você também pode usar o método **DownloadToStream** para baixar o conteúdo de um blob como uma cadeia de texto.

	// Recuperar a conta de armazenamento da cadeia de conexão.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de blob.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Recuperar uma referência a um contêiner criado anteriormente.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

	// Recuperar uma referência a um blob denominado "myblob.txt".	CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

	string text;
	using (var memoryStream = new MemoryStream())
	{
		blockBlob2.DownloadToStream(memoryStream);
		text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
	}

##<a name="delete-blobs"> </a><span  class="short-header">Excluir blobs</span>Como excluir blobs

Para excluir um blob, primeiro obtenha uma referência a um blob e, em seguida, chame o método **Delete** sobre ele.

    // Recuperar a conta de armazenamento da cadeia de conexão.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de blob.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Recuperar uma referência a um contêiner criado anteriormente.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Recuperar uma referência a um blob denominado "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Excluir um blob.
    blockBlob.Delete(); 

## <a name="next-steps"></a><span  class="short-header">Próximas etapas</span>Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de blob, siga estes links para saber como executar tarefas de armazenamento mais complexas.
<ul>
<li>Consulte a documentação de referência do serviço blob para obter detalhes completos sobre as APIs disponíveis:
  <ul>
    <li><a href="http://msdn.microsoft.com/pt-br/library/windowsazure/wa_storage_api_ref_reference_home.aspx">Referência à Biblioteca de Cliente de Armazenamento para .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/pt-br/library/windowsazure/dd179355">Referência da API REST</a></li>
  </ul>
</li>
<li>Conheça tarefas mais avançadas que você pode executar com o Armazenamento do Windows Azure em <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx">Armazenando e acessando dados no Windows Azure (a página pode estar em inglês)</a>.</li>
<li>Exibir mais guias de recursos para obter informações sobre opções adicionais para armazenar dados no Windows Azure.
  <ul>
    <li>Usar o <a href="/pt-br/develop/net/how-to-guides/table-services/">Armazenamento de Tabela</a> para armazenar dados estruturados.</li>
    <li>Usar o <a href="/pt-br/develop/net/how-to-guides/sql-database/">SQL Database</a> para armazenar dados relacionais.</li>
  </ul>
</li>
</ul>

  [Próximas etapas]: #next-steps
  [O que é Blob Storage]: #what-is
  [Conceitos]: #concepts
  [Criar uma conta de armazenamento do Windows Azure]: #create-account
  [Configurar uma cadeia de conexão de armazenamento]: #setup-connection-string
  [Como acessar o blob storage programaticamente]: #configure-access
  [Como criar um contêiner]: #create-container
  [Como carregar um blob em um contêiner]: #upload-blob
  [Como listar os blobs em um contêiner]: #list-blob
  [Como baixar blobs]: #download-blobs
  [Como excluir blobs]: #delete-blobs
  [Blob5]: ./media/storage-dotnet-how-to-use-blobs-20/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs-20/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs-20/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs-20/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs-20/blob9.png
  
  [Acessar e armazenar dados no Windows Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx
  [Blog da equipe do Armazenamento do Windows Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configurando cadeias de conexão]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee758697.aspx
  [Referência à biblioteca cliente do .NET]: http://msdn.microsoft.com/pt-br/library/windowsazure/wl_svchosting_mref_reference_home
  [Referência da API REST]: http://msdn.microsoft.com/pt-br/library/windowsazure/dd179355
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2

