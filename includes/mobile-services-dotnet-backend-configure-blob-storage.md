
##<a name="storage-client-server"></a>Instalar o cliente de armazenamento do projeto de serviço móvel

Para gerar uma SAS para carregar imagens no armazenamento de Blob, primeiro adicione o pacote NuGet que instala a biblioteca de cliente de armazenamento no projeto do serviço móvel.

1. Em **Gerenciador de Soluções** no Visual Studio, clique com o botão direito do mouse no projeto do serviço móvel e selecione **Gerenciar Pacotes NuGet**.

2. No painel esquerdo, selecione a categoria **Online**, selecione **Apenas Estáveis**, pesquise **WindowsAzure.Storage**, clique em **Instalar** no pacote **Armazenamento do Azure** e aceite os contratos de licença.

  	![](./media/mobile-services-configure-blob-storage/mobile-add-storage-nuget-package-dotnet.png)

  	Isso adiciona a biblioteca de cliente para os serviços de armazenamento do Azure ao projeto do serviço móvel.

##<a name="update-data-model"></a>Atualizar a definição de TodoItem no modelo de dados

A classe TodoItem define o objeto de dados, e é necessário adicionar a essa classe as mesmas propriedades adicionadas ao cliente.

1. No Visual Studio 2013, abra o projeto do serviço móvel, expanda a pasta DataObjects e abra o arquivo de projeto TodoItem.cs.
	
2. Adicione as novas propriedades a seguir à classe **TodoItem**:

        public string containerName { get; set; }
		public string resourceName { get; set; }
		public string sasQueryString { get; set; }
		public string imageUri { get; set; } 

	Essas propriedades são utilizadas para gerar a SAS e armazenar informações de imagem. Observe as maiúsculas e minúsculas nessas propriedades correspondem à versão back-end do JavaScript.

	>[AZURE.NOTE]Ao usar o inicializador de banco de dados padrão, o Entity Framework irá remover e recriar o banco de dados quando detectar uma alteração no modelo de dados na definição Code First. Para fazer com que esse modelo de dados altere e mantenha os dados existentes no banco de dados, você deve usar as Migrações Code First. O inicializador padrão não pode ser usado em um Banco de Dados SQL do Azure. Para obter mais informações, consulte [Como usar as Migrações Code First para atualizar o modelo de dados](../articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations.md).

##<a name="update-scripts"></a>Atualizar o controlador de TodoItem para gerar uma assinatura de acesso compartilhado 

O **TodoItemController** existente é atualizado para que o método **PostTodoItem** gere uma SAS quando um novo TodoItem é inserido. Você também

0. Se você ainda não criou sua conta de armazenamento, consulte [Como criar uma conta de armazenamento].

1. No Portal de Gerenciamento, clique em **Armazenamento**, clique na conta de armazenamento e clique em **Gerenciar Chaves**.

2. Anote o **Nome da Conta de Armazenamento** e a **Chave de Acesso**.
 
3. No seu serviço móvel, clique na guia **Configurar**, role para baixo até **Configurações do aplicativo** e digite um par de **Nome** e **Valor** para cada uma das opções a seguir que você adquiriu da conta de armazenamento e clique em **Salvar**.

	+ `STORAGE_ACCOUNT_NAME`
	+ `STORAGE_ACCOUNT_ACCESS_KEY`

	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-app-settings.png)

	A chave de acesso da conta de armazenamento é armazenada criptografada em configurações do aplicativo. Você pode acessar essa chave de qualquer script de servidor em tempo de execução. Para obter mais informações, consulte [Configurações do aplicativo].

4. No Gerenciador de Soluções no Visual Studio, abra o arquivo Web.config do projeto do serviço móvel e adicione as novas configurações de aplicativo a seguir, substituindo os espaços reservados pelo nome da conta de armazenamento e pela chave de acesso que você acabou de definir no portal:

		<add key="STORAGE_ACCOUNT_NAME" value="**your_account_name**" />
		<add key="STORAGE_ACCOUNT_ACCESS_KEY" value="**your_access_token_secret**" />

	O serviço móvel usa essas configurações armazenadas quando é executado no computador local, o que permite testar o código antes de publicá-lo. Ao executar no Azure, o serviço móvel usa o conjunto de valores das configurações do aplicativo no portal e ignora essas configurações do projeto.

7.  Na pasta Controllers, abra o arquivo TodoItemController.cs e adicione as seguintes diretivas **using**:

		using System;
		using Microsoft.WindowsAzure.Storage.Auth;
		using Microsoft.WindowsAzure.Storage.Blob;
  
8.  Substitua o método **PostTodoItem** existente pelo código a seguir:

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            string storageAccountName;
            string storageAccountKey;

            // Try to get the Azure storage account token from app settings.  
            if (!(Services.Settings.TryGetValue("STORAGE_ACCOUNT_NAME", out storageAccountName) |
            Services.Settings.TryGetValue("STORAGE_ACCOUNT_ACCESS_KEY", out storageAccountKey)))
            {
                Services.Log.Error("Could not retrieve storage account settings.");
            }

            // Set the URI for the Blob Storage service.
            Uri blobEndpoint = new Uri(string.Format("https://{0}.blob.core.windows.net", storageAccountName));

            // Create the BLOB service client.
            CloudBlobClient blobClient = new CloudBlobClient(blobEndpoint, 
                new StorageCredentials(storageAccountName, storageAccountKey));

            if (item.containerName != null)
            {
                // Set the BLOB store container name on the item, which must be lowercase.
                item.containerName = item.containerName.ToLower();

                // Create a container, if it doesn't already exist.
                CloudBlobContainer container = blobClient.GetContainerReference(item.containerName);
                await container.CreateIfNotExistsAsync();

                // Create a shared access permission policy. 
                BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

                // Enable anonymous read access to BLOBs.
                containerPermissions.PublicAccess = BlobContainerPublicAccessType.Blob;
                container.SetPermissions(containerPermissions);

                // Define a policy that gives write access to the container for 5 minutes.                                   
                SharedAccessBlobPolicy sasPolicy = new SharedAccessBlobPolicy()
                {
                    SharedAccessStartTime = DateTime.UtcNow,
                    SharedAccessExpiryTime = DateTime.UtcNow.AddMinutes(5),
                    Permissions = SharedAccessBlobPermissions.Write
                };

                // Get the SAS as a string.
                item.sasQueryString = container.GetSharedAccessSignature(sasPolicy); 

                // Set the URL used to store the image.
                item.imageUri = string.Format("{0}{1}/{2}", blobEndpoint.ToString(), 
                    item.containerName, item.resourceName);
            }

            // Complete the insert operation.
            TodoItem current = await InsertAsync(item);
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

   	Esse método POST agora gera uma nova SAS para o item inserido, que é válida por 5 minutos e atribui o valor da SAS gerada à propriedade `sasQueryString` do item devolvido. A propriedade `imageUri` também é definida como o caminho do recurso do novo BLOB para habilitar a exibição da imagem durante a associação na interface do usuário do cliente.

	>[AZURE.NOTE]Esse código cria uma SAS para um BLOB individual. Se você precisar carregar vários blobs em um contêiner usando a mesma SAS, poderá chamar o <a href="http://go.microsoft.com/fwlink/?LinkId=390455" target="_blank">método generateSharedAccessSignature</a> com um nome de recurso de blob vazio, da seguinte forma:<pre><code>blobService.generateSharedAccessSignature(containerName, '', sharedAccessPolicy);</code></pre>

Em seguida, você atualizará o aplicativo quickstart para adicionar funcionalidade de carregamento de imagem usando a SAS gerada em Inserir.
 
<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/mobile-services-configure-blob-storage/mobile-blob-storage-account.png
[1]: ./media/mobile-services-configure-blob-storage/mobile-blob-storage-account-keys.png

[3]: ./media/mobile-services-configure-blob-storage/mobile-portal-data-tables.png
[4]: ./media/mobile-services-configure-blob-storage/mobile-insert-script-blob.png





[10]: ./media/mobile-services-configure-blob-storage/mobile-blob-storage-app-settings.png

<!-- URLs. -->
[Como criar uma conta de armazenamento]: /manage/services/storage/how-to-create-a-storage-account
[Configurações do aplicativo]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

<!--HONumber=54-->