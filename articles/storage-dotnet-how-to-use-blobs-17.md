<properties linkid="dev-net-2-how-to-blob-storage" urlDisplayName="Serviço Blob (2.0)" pageTitle="Como usar o armazenamento de blob - Guia de recursos do Azure" metaKeywords="Introdução ao blob do Azure, dados não estruturados do Azure, Armazenamento não estruturado do Azure, blob do Azure, armazenamento de blob do Azure, .NET de blob do Azure, C# de blob do Azure, C# de blob do Azure" description="Saiba como usar o serviço de blob do Azure para carregar, baixar, listar e excluir conteúdo de blob. As amostras são escritas em C#." metaCanonical="" services="" documentationCenter="" title="Como usar o Serviço Armazenamento de Blob do Azure no .NET" authors=""  solutions="" writer="" manager="" editor=""  />

# Como usar o Serviço Blob Storage no Azure no .NET

<div class="dev-center-tutorial-selector">
<a href="/pt-br/develop/net/how-to-guides/blob-storage-v17/" title="versão 1.7" class="current">versão 1.7</a>
<a href="/pt-br/develop/net/how-to-guides/blob-storage/" title="versão 2.0">versão 2.0</a> 
</div>


Este guia demonstra como executar cenários comuns usando o Serviço Blob Storage no Azure. As amostras são escritas em C\# e
usam a API do .NET. Os cenários abordados incluem
**carregamento**, **listagem**, **download** e **exclusão** de blobs. Para obter mais informações sobre blobs, consulte a seção [Próximas etapas][].

<h2>Sumário</h2>

-   [O que é Blob Storage][]
-   [Conceitos][]
-   [Criar uma conta de armazenamento do Azure][]
-   [Configurar uma cadeia de conexão de armazenamento][]
-   [Como: acessar o armazenamento de blob programaticamente][]
-   [Como: criar um contêiner][]
-   [Como: carregar um blob em um contêiner][]
-   [Como: listar os blobs em um contêiner][]
-   [Como: baixar blobs][]
-   [Como: excluir blobs][]
-   [Próximas etapas][]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

<h2><a name="create-account"></a><span  class="short-header">Criar uma conta</span>Criar uma conta de armazenamento do Azure</h2>
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a name="setup-connection-string"></a><span  class="short-header">Configurar uma cadeia de conexão</span>Configurar uma cadeia de conexão de armazenamento</h2>

A API de armazenamento do .NET no Azure oferece suporte usando uma cadeia de conexão
de armazenamento para configurar pontos de extremidade e credenciais para acessar serviços
de armazenamento. Você pode colocar a cadeia de conexão de armazenamento em um arquivo de configuração, em vez de embuti-la no código:

- Ao usar os Serviços de Nuvem do Azure, é recomendável que você armazene a cadeia de conexão usando o sistema de configuração de serviço do Azure (arquivos `*.csdef` e `*.cscfg`).
- Ao usar os Sites do Azure ou Máquinas Virtuais do Azure, é recomendável que você armazene sua cadeia de conexão usando o sistema de configuração do .NET (por exemplo, arquivos `web.config`).

Nos dois casos, você pode recuperar sua cadeia de conexão usando o método `CloudConfigurationManager.GetSetting`, conforme mostrado neste guia.

### Configurando a cadeia de conexão ao usar os Serviços de Nuvem

O mecanismo de configuração de serviço é exclusivo para projetos de Serviços de Nuvem do Azure e permite que você altere dinamicamente os parâmetros de configuração no Portal de Gerenciamento do Azure sem reimplantar
o aplicativo.

Para configurar a cadeia de conexão na configuração de serviço do Windows
Azure:

1.  No Gerenciador de Soluções do Visual Studio, na pasta **Funções**
    do seu Projeto de Implantação do Azure, clique com o botão direito do mouse na
    sua função web ou função de trabalho e clique em **Propriedades**.  
    ![Selecione as propriedades em uma função de Serviço de Nuvem do Visual Studio][Blob5]

2.  Clique na guia **Configurações** e pressione o botão **Adicionar Configuração**.  
    ![Adicione uma configuração de Serviço de Nuvem do Visual Studio][Blob6]

    Uma nova entrada **Setting1** será mostrada na grade de configurações.

3.  No menu suspenso **Tipo** da nova entrada **Setting1**, escolha
    **Cadeia de Conexão**.  
    ![Blob7][Blob7]

4.  Clique no botão **...** na extremidade direita da entrada **Setting1**.
    A caixa de diálogo **Cadeia de Conexão da Conta de Armazenamento** será aberta.

5.  Escolha se deseja direcionar o emulador de armazenamento (o armazenamento do Windows
    Azure simulado em sua máquina local) ou uma conta de armazenamento
    real na nuvem. O código deste guia funciona com qualquer uma dessas opções. Digite o valor da **Chave de Acesso Primária** copiado na etapa anterior deste tutorial, se desejar armazenar dados blob na conta de armazenamento que criamos anteriormente no Azure.   
    ![Blob8][Blob8]

6.  Altere a entrada **Nome** de **Setting1** para um nome mais amigável,
    como **StorageConnectionString**. Você fará referência a essa
    cadeia de conexão posteriormente no código deste guia.  
    ![Blob9][Blob9]
	
### Configurando a cadeia de conexão ao usar Sites ou Máquinas Virtuais

Ao usar Sites ou Máquinas Virtuais, é recomendável que você use o sistema de configuração do .NET (por exemplo, `web.config`).  Você armazena a cadeia de conexão usando o elemento `<appSettings>`:

	<configuration>
	    <appSettings>
		    <add key="StorageConnectionString"
			     value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
		</appSettings>
	</configuration>

Leia [Configurando cadeias de conexão][] para obter mais informações sobre cadeias de conexão de armazenamento.
	
Agora você está pronto para executar as tarefas das instruções deste guia.

<h2> <a name="configure-access"> </a><span  class="short-header">Acessar programaticamente</span>Como: acessar o armazenamento de blob programaticamente</h2>

Adicione as seguintes declarações de namespace à parte superior de qualquer arquivo C\# no qual você deseja acessar o Armazenamento do Azure programaticamente:

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.StorageClient;

Você pode usar os tipos **CloudStorageAccount** e
**CloudConfigurationManager**
para recuperar sua cadeia de conexão de armazenamento e as informações da conta
de armazenamento na configuração de serviço do Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

O tipo **CloudBlobClient** permite que você recupere objetos que representam os contêineres e blobs armazenados no Blob Storage Service. O código a seguir cria um objeto **CloudBlobClient** usando o objeto da conta de armazenamento que recuperamos acima:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

<h2> <a name="create-container"> </a><span  class="short-header">Criar um contêiner</span>Como: criar um contêiner</h2>

Todos os blobs de armazenamento residem em um contêiner. Você pode usar um objeto **CloudBlobClient** para obter uma referência ao contêiner que deseja usar. Você pode criar o contêiner se ele não existir:

    // Recuperar a conta de armazenamento da cadeia de conexão
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de blob 
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Recuperar uma referência a um contêiner 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Criar o contêiner se ele ainda não existir
    container.CreateIfNotExist();

Por padrão, o novo contêiner é particular, portanto, você deve especificar sua
chave de acesso de armazenamento (como antes) para baixar blobs desse
contêiner. Se desejar disponibilizar os arquivos dentro do contêiner para todas as pessoas, você pode definir o contêiner como público usando o seguinte código:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
 	    BlobContainerPublicAccessType.Blob }); 

Qualquer pessoa na internet pode ver blobs em um contêiner público, mas você pode modificar ou excluí-los somente se tiver a chave de acesso apropriada.

<h2> <a name="upload-blob"> </a><span  class="short-header">Carregar um contêiner</span>Como: carregar um blob em um contêiner</h2>

Para carregar um arquivo para um blob, obtenha uma referência de contêiner e use-a para obter
uma referência de blob. Depois que tiver uma referência de blob, você poderá carregar qualquer
fluxo de dados para ele, chamando o método **UploadFromStream** na
referência de blob. Essa operação criará o blob, se ele ainda não existir,
ou o substituirá, caso ele exista. A amostra de código abaixo mostra isso e pressupõe
que o contêiner já tenha sido criado.

    // Recuperar a conta de armazenamento da cadeia de conexão
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de blob
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Recuperar uma referência a um contêiner criado anteriormente
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Recuperar uma referência a um blob denominado "myblob"
    CloudBlob blob = container.GetBlobReference("myblob");

    // Criar ou substituir o blob "myblob" pelo conteúdo de um arquivo local
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blob.UploadFromStream(fileStream);
    } 

<h2> <a name="list-blob"> </a><span  class="short-header">Listar blobs em um contêiner</span>Como: listar blobs em um contêiner</h2>

Para listar blobs em um contêiner, primeiro obtenha uma referência ao contêiner. Você
pode usar o método **ListBlobs** do contêiner para recuperar os blobs
dentro dele. O código a seguir demonstra como recuperar e gerar a saída
do **Uri** de cada blob em um contêiner:

    // Recuperar a conta de armazenamento da cadeia de conexão
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de blob
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Recuperar uma referência a um contêiner criado anteriormente
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Executar um loop nos blobs do contêiner e gerar a saída do URI para cada um deles
    foreach (var blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    } 

O serviço de blob também tem o conceito de diretórios dentro de
contêineres. Isso é para que você possa organizar seus blobs em uma estrutura mais semelhante a uma pasta. Por exemplo, você poderia ter um contêiner denominado 'photos', no
qual poderia carregar os blobs 'rootphoto1', '2010/photo1',
'2010/photo2' e '2011/photo1'. Isso praticamente criaria os
diretórios '2010' e '2011' dentro do contêiner 'photos'. Quando você
chamar **ListBlobs** no contêiner 'photos', a coleção retornada
conterá os objetos **CloudBlobDirectory** e **CloudBlob**
que representam os diretórios e os blobs contidos no nível superior. Nesse
caso, os diretórios '2010' e '2011', bem como photo 'rootphoto1',
seriam retornados. Opcionalmente, você pode passar uma nova classe
**BlobRequestOptions** com **UseFlatBlobListing** definida como
**true**. Isso resultaria no retorno de cada blob sendo, independentemente do
diretório. Para obter mais informações, consulte [CloudBlobContainer.ListBlobs][].

<h2> <a name="download-blobs"> </a><span  class="short-header">Baixar blobs</span>Como: baixar blobs</h2>

Para baixar blobs, primeiro recupere uma referência de blob. O exemplo a seguir usa o método **DownloadToStream** para transferir o conteúdo blob para um objeto de fluxo que você pode persistir para um arquivo local.
Você também poderia chamar os métodos **DownloadToFile**, **DownloadByteArray**
ou **DownloadText** do blob.

    // Recuperar a conta de armazenamento da cadeia de conexão
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de blob
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Recuperar uma referência a um contêiner criado anteriormente
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Recuperar uma referência a um blob denominado "myblob"
    CloudBlob blob = container.GetBlobReference("myblob");

    // Salvar o conteúdo do blob no disco
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blob.DownloadToStream(fileStream);
    } 

<h2> <a name="delete-blobs"> </a><span  class="short-header">Excluir blobs</span>Como: excluir blobs</h2>

Finalmente, para excluir um blob, obtenha uma referência de blob e, em seguida, chame
o método **Delete** que ela contém.

    // Recuperar a conta de armazenamento da cadeia de conexão
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de blob
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Recuperar uma referência a um contêiner criado anteriormente
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Recuperar uma referência a um blob denominado "myblob"
    CloudBlob blob = container.GetBlobReference("myblob");

    // Excluir o blob
    blob.Delete(); 

<h2> <a name="next-steps"></a><span  class="short-header">Próximas etapas</span>Próximas etapas</h2>

Agora que você aprendeu os conceitos básicos do armazenamento de blob, siga estes links para saber como executar tarefas de armazenamento mais complexas.
<ul>
<li>Consulte a documentação de referência do serviço blob para obter detalhes completos sobre as APIs disponíveis:
  <ul>
    <li><a href="http://msdn.microsoft.com/pt-br/library/windowsazure/wl_svchosting_mref_reference_home">Referência à biblioteca cliente do .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/pt-br/library/windowsazure/dd179355">Referência da API REST</a></li>
  </ul>
</li>
<li>Conheça tarefas mais avançadas que você pode executar com o Armazenamento do Azure em <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx">Armazenando e acessando dados no Azure</a> (a página pode estar em inglês).</li>
<li>Exibir mais guias de recursos para obter informações sobre opções adicionais para armazenar dados no Azure.
  <ul>
    <li>Usar o <a href="/pt-br/develop/net/how-to-guides/table-services/">Armazenamento de Tabela</a> para armazenar dados estruturados.</li>
    <li>Usar o <a href="/pt-br/develop/net/how-to-guides/sql-database/">SQL Database</a> para armazenar dados relacionais.</li>
  </ul>
</li>
</ul>

  [Próximas etapas]: #next-steps
  [O que é Blob Storage]: #what-is
  [Conceitos]: #concepts
  [Criar uma conta de armazenamento do Azure]: #create-account
  [Configurar uma cadeia de conexão de armazenamento]: #setup-connection-string
  [Como: acessar o armazenamento de blob programaticamente]: #configure-access
  [Como: criar um contêiner]: #create-container
  [Como: carregar um blob em um contêiner]: #upload-blob
  [Como: listar os blobs em um contêiner]: #list-blob
  [Como: baixar blobs]: #download-blobs
  [Como: excluir blobs]: #delete-blobs
  [Blob5]: ./media/storage-dotnet-how-to-use-blobs-17/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs-17/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs-20/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs-20/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs-20/blob9.png
  
  [Acessar e armazenar dados no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx
  [Blog da equipe do Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configurando cadeias de conexão]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee758697.aspx
  [Referência à biblioteca cliente do .NET]: http://msdn.microsoft.com/pt-br/library/windowsazure/wl_svchosting_mref_reference_home
  [Referência da API REST]: http://msdn.microsoft.com/pt-br/library/windowsazure/dd179355

