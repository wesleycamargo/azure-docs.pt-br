Um novo script de inserção está registrado e gerará uma SAS quando um novo item de Todo for inserido.

0. Se você ainda não criou sua conta de armazenamento, consulte [Como criar uma conta de armazenamento](../storage/storage-create-storage-account.md).

1. No Portal de Gerenciamento, clique em **Armazenamento**, clique na conta de armazenamento e clique em **Gerenciar Chaves**.

2. Anote o **Nome da Conta de Armazenamento** e a **Chave de Acesso**.

   	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-account-keys.png)

3. No seu serviço móvel, clique na guia **Configurar**, role para baixo até **Configurações do aplicativo** e digite um par de **Nome** e **Valor** para cada uma das opções a seguir que você adquiriu da conta de armazenamento e clique em **Salvar**.

	+ `STORAGE_ACCOUNT_NAME`
	+ `STORAGE_ACCOUNT_ACCESS_KEY`

	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-app-settings.png)

	A chave de acesso da conta de armazenamento é armazenada criptografada em configurações do aplicativo. Você pode acessar essa chave de qualquer script de servidor em tempo de execução. Para obter mais informações, consulte [Configurações do aplicativo].

4. Na guia Configurar, verifique se [Esquema dinâmico](http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7)está habilitado. Você precisa do esquema dinâmico habilitado para poder adicionar novas colunas à tabela TodoItem. O esquema dinâmico não deve ser habilitado em qualquer serviço de produção.

4. Clique na guia **Dados** e, em seguida, clique na tabela **TodoItem**.

5.  Em **todoitem**, clique na guia **Script** e selecione **Inserir**. Substitua a função de inserção pelo código a seguir e clique em **Salvar**:

		var azure = require('azure');
		var qs = require('querystring');
		var appSettings = require('mobileservice-config').appSettings;
		
		function insert(item, user, request) {
		    // Get storage account settings from app settings. 
		    var accountName = appSettings.STORAGE_ACCOUNT_NAME;
		    var accountKey = appSettings.STORAGE_ACCOUNT_ACCESS_KEY;
		    var host = accountName + '.blob.core.windows.net';
		
		    if ((typeof item.containerName !== "undefined") && (
		    item.containerName !== null)) {
		        // Set the BLOB store container name on the item, which must be lowercase.
		        item.containerName = item.containerName.toLowerCase();
		
		        // If it does not already exist, create the container 
		        // with public read access for blobs.        
		        var blobService = azure.createBlobService(accountName, accountKey, host);
		        blobService.createContainerIfNotExists(item.containerName, {
		            publicAccessLevel: 'blob'
		        }, function(error) {
		            if (!error) {
		
		                // Provide write access to the container for the next 5 mins.        
		                var sharedAccessPolicy = {
		                    AccessPolicy: {
		                        Permissions: azure.Constants.BlobConstants.SharedAccessPermissions.WRITE,
		                        Expiry: new Date(new Date().getTime() + 5 * 60 * 1000)
		                    }
		                };
		
		                // Generate the upload URL with SAS for the new image.
		                var sasQueryUrl = 
		                blobService.generateSharedAccessSignature(item.containerName, 
		                item.resourceName, sharedAccessPolicy);
		
		                // Set the query string.
		                item.sasQueryString = qs.stringify(sasQueryUrl.queryString);
		
		                // Set the full path on the new new item, 
		                // which is used for data binding on the client. 
		                item.imageUri = sasQueryUrl.baseUrl + sasQueryUrl.path;
		
		            } else {
		                console.error(error);
		            }
		            request.execute();
		        });
		    } else {
		        request.execute();
		    }
		}

   	Isso substitui a função que é chamada quando ocorre uma inserção na tabela TodoItem por um novo script. Esse novo script gera uma nova SAS para a inserção, que é válida por 5 minutos e atribui o valor da SAS gerada à propriedade `sasQueryString` do item devolvido. A propriedade `imageUri` também é definida como o caminho do recurso do novo BLOB para habilitar a exibição da imagem durante a associação na interface do usuário do cliente.

	>[AZURE.NOTE]Esse código cria uma SAS para um BLOB individual. Se precisar carregar vários blobs em um contêiner usando a mesma SAS, você poderá chamar o método [generateSharedAccessSignature](http://go.microsoft.com/fwlink/?LinkId=390455)</a> com um nome de recurso de blob vazio, como este:
	>                 
	>     blobService.generateSharedAccessSignature(containerName, '', sharedAccessPolicy);

Em seguida, você atualizará o aplicativo quickstart para adicionar funcionalidade de carregamento de imagem usando a SAS gerada em Inserir.
 
<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Configurações do aplicativo]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

<!---HONumber=July15_HO3-->