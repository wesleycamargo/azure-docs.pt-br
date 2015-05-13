<properties 
    pageTitle="Como usar o armazenamento de blobs (C++) | Microsoft Azure" 
    description="Saiba como usar o serviço de armazenamento de blobs no Azure. As amostras são escritas em C++." 
    services="storage" 
    documentationCenter=".net" 
    authors="tamram" 
    manager="adinah" 
    editor=""/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/06/2015" 
    ms.author="tamram"/>

# Como usar o Armazenamento de Blob em C++  

[AZURE.INCLUDE [storage-selector-blob-include](../includes/storage-selector-blob-include.md)]

## Visão geral
Este guia demonstra como executar cenários comuns usando oServiço de armazenamento de blobs do Azure. Os exemplos são escritos em C++ e usam a [Biblioteca do Cliente de Armazenamento do Azure para C++](https://github.com/Azure/azure-storage-cpp/blob/v0.5.0-preview/README.md). Os cenários cobertos incluem **carregamento**, **listagem**, **download** e **exclusão** de blobs.

>[AZURE.NOTE]Este guia tem como alvo a Biblioteca do Cliente de Armazenamento do Azure para C++, versão 0.5.0 e superior. A versão recomendada é a Biblioteca do Cliente de Armazenamento 0.5.0, que está disponível via [NuGet](http://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/).

[AZURE.INCLUDE [armazenamento-blob-conceitos-include](../includes/storage-blob-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

## Criar um aplicativo em C++
Neste guia, você usará os recursos de armazenamento que podem ser executados em um aplicativo C++.

Para isso, você precisará instalar a Biblioteca do Cliente de Armazenamento do Azure para C++ e criar uma conta de armazenamento do Azure em sua assinatura do Azure.

Para instalar a Biblioteca do Cliente de Armazenamento do Azure para C++, você pode usar os seguintes métodos:

-	**Linux:** siga as instruções dadas na página README da [Biblioteca do Cliente de Armazenamento do Azure para C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md).  
-	**Windows:** no Visual Studio, clique em **Ferramentas \> Gerenciador de Pacotes do NuGet \> Console do Gerenciador de Pacotes**. Digite o seguinte comando no console do [Gerenciador de Pacotes do NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) e pressione **ENTER**.  

		Install-Package wastorage -Pre

## Configurar seu aplicativo para acessar o Armazenamento de blobs  
Adicione as seguintes instruções include à parte superior do arquivo C++ no qual deseja usar as APIs de armazenamento do Azure para acessar os blobs:

	#include "was/storage_account.h"
	#include "was/blob.h"

## Configurar uma cadeia de conexão de armazenamento do Azure
Um cliente de armazenamento do Azure usa uma cadeia de conexão para armazenar pontos de extremidade e credenciais para acessar serviços de gerenciamento de dados. Ao executar um aplicativo cliente, é necessário fornecer a cadeia de conexão de armazenamento no formato a seguir, usando o nome da conta de armazenamento e a chave de acesso de armazenamento da conta de armazenamento listada no Portal de Gerenciamento para os valores *AccountName* e *AccountKey*. Para obter informações sobre as contas de armazenamento e as chaves de acesso, consulte [Sobre as contas de armazenamento do Azure](storage-create-storage-account.md). Este exemplo mostra como você pode declarar um campo estático para armazenar a cadeia de conexão:

	// Define the connection-string with your values.
	const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

Para testar seu aplicativo no computador local do Windows, você pode usar o emulador de armazenamento do [Microsoft Azure](https://msdn.microsoft.com/library/azure/hh403989.aspx) que é instalado com o [SDK do Azure](http://azure.microsoft.com/downloads/). O emulador de armazenamento é um utilitário que simula os serviços Blob, fila e tabela disponíveis no Azure em sua máquina de desenvolvimento local. Este exemplo mostra como você pode declarar um campo estático para manter a cadeia de conexão em seu emulador de armazenamento local:

	// Define the connection-string with Azure Storage Emulator.
	const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

Para iniciar o emulador de armazenamento do Azure, selecione o botão **Iniciar** ou pressione a tecla **Windows**. Comece digitando **Emulador de Armazenamento do Azure** e selecione **Emulador de Armazenamento do Microsoft Azure** na lista de aplicativos.

Os exemplos abaixo pressupõem que você usou um desses dois métodos para obter a cadeia de conexão do armazenamento.

## Recuperar sua cadeia de conexão
Você pode usar a classe **cloud\_storage\_account** para representar as informações da conta de armazenamento. Para recuperar as informações da conta de armazenamento na cadeia de conexão de armazenamento, você pode usar o método **Analisar**.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

Em seguida, obtenha uma referência para uma classe **cloud\_blob\_client** que permita recuperar os objetos que representam os contêineres e os blobs armazenados no serviço de armazenamento de Blobs. O código a seguir cria um objeto **cloud\_blob\_client** usando o objeto da conta de armazenamento que recuperamos acima:

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  

## Como criar um contêiner
Todos os blobs no armazenamento do Azure devem residir em um contêiner. Este exemplo mostra como criar um contêiner se ele ainda não existir:

	try 
	{
   // Recuperar a conta de armazenamento na cadeia de conexão. azure::storage::cloud\_storage\_account storage\_account = azure::storage::cloud\_storage\_account::parse\(storage\_connection\_string\);

   // Criar o cliente de blob. azure::storage::cloud\_blob\_client blob\_client = storage\_account.create\_cloud\_blob\_client\(\);

   // Recuperar uma referência para um contêiner. azure::storage::cloud\_blob\_container container = blob\_client.get\_container\_reference\(U\("my-sample-container"\)\);

   // Criar o contêiner se ele ainda não existir. container.create\_if\_not\_exists\(\); } catch \(const std::exception& e\) { std::wcout \<\< U\("Erro: "\) \<\< e.what\(\) \<\< std::endl; }

Por padrão, o novo contêiner é privado e você deve especificar sua chave de acesso do armazenamento para baixar blobs desse contêiner. Se você quiser disponibilizar os arquivos \(blobs\) dentro do contêiner para todas as pessoas, poderá definir o contêiner como público usando o seguinte código:

	// Make the blob container publicly accessible.
	azure::storage::blob_container_permissions permissions;
	permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
	container.upload_permissions(permissions);  

Qualquer pessoa na Internet pode ver blobs em um contêiner público, mas você só pode modificar ou excluí-los se tiver a chave de acesso apropriada.

## Como: carregar um blob em um contêiner
O Armazenamento de Blob do Azure oferece suporte a blobs de blocos e a blobs de páginas. Na maioria dos casos, o blob de blocos é o tipo recomendado a ser usado.

Para carregar um arquivo em um blob de blocos, obtenha uma referência de contêiner e use-a para obter uma referência de blob de blocos. Depois de ter uma referência do blob, você pode carregar qualquer fluxo de dados para ele chamando o método **upload\_from\_stream**. Essa operação criará o blob, caso ele não exista, ou o substituirá, caso ele já exista. O exemplo a seguir mostra como carregar um blob em um contêiner e pressupõe que o contêiner já tenha sido criado.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Retrieve reference to a blob named "my-blob-1".
	azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

	// Create or overwrite the "my-blob-1" blob with contents from a local file.
	concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
	blockBlob.upload_from_stream(input_stream);
	input_stream.close().wait();

	// Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
	// Retrieve a reference to a blob named "my-blob-2".
	azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
	blob2.upload_text(U("more text"));

	// Retrieve a reference to a blob named "my-blob-3".
	azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
	blob3.upload_text(U("other text"));  

Como alternativa, você pode usar o método **upload\_from\_file** para carregar um arquivo em um blob de bloco.

## Como: listar os blobs em um contêiner
Para listar blobs em um contêiner, primeiro obtenha uma referência ao contêiner. Você pode usar o método **list\_blobs\_segmented** do contêiner para recuperar os blobs e/ou diretórios dentro dele. Para acessar o conjunto avançado de propriedades e métodos para um **blob\_result\_segment** retornado, você deve chamar a propriedade **blob\_result\_segment.blobs** para obter um objeto **cloud\_blob** ou a propriedade**blob\_result\_segment.directories** para obter um objeto cloud\_blob\_directory. O código a seguir demonstra como recuperar e apresentar a URI de cada item no contêiner **my-sample-container**:

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Loop over items within the container and output the length and URI.
	azure::storage::continuation_token token;
	do
	{
   azure::storage::blob\_result\_segment result = container.list\_blobs\_segmented\(token\); std::vector\<azure::storage::cloud\_blob\> blobs = result.blobs\(\);

   para \(std::vector\<azure::storage::cloud\_blob\>::const\_iterator it = blobs.cbegin\(\); it != blobs.cend\(\); ++it\) { std::wcout \<\< U\("Blob: "\) \<< it->uri\(\).primary\_uri\(\).to\_string\(\) \<\< std::endl; }

   std::vector\<azure::storage::cloud\_blob\_directory\> directories = result.directories\(\);

   para \(std::vector\<azure::storage::cloud\_blob\_directory\>::const\_iterator it = directories.cbegin\(\); it != directories.cend\(\); ++it\) { std::wcout \<\< U\("Diretório: "\) \<< it->uri\(\).primary\_uri\(\).to\_string\(\) \<\< std::endl; } token = result.continuation\_token\(\); } while \(!token.empty\(\)\);


## Como: baixar blobs
Para baixar blobs, primeiro recupere uma referência do blob, em seguida, chame o método **download\_to\_stream**. O exemplo a seguir usa o método **download\_to\_stream** para transferir o conteúdo do blob para um objeto de fluxo que você pode persistir em um arquivo local.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Retrieve reference to a blob named "my-blob-1".
	azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

	// Save blob contents to a file.
	concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
	concurrency::streams::ostream output_stream(buffer);
	blockBlob.download_to_stream(output_stream);

	std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
	std::vector<unsigned char>& data = buffer.collection();
		
	outfile.write((char *)&data[0], buffer.size());
	outfile.close();  

Como alternativa, você pode usar o método **download\_to\_file** para baixar o conteúdo de um blob em um arquivo. Você também pode usar o método **download\_text** para baixar o conteúdo de um blob como uma cadeia de texto.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Retrieve reference to a blob named "my-blob-2".
	azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

	// Download the contents of a blog as a text string.
	utility::string_t text = text_blob.download_text();

## Como: excluir blobs
Para excluir um blob, primeiro obtenha uma referência do blob, em seguida, chame o método **delete\_blob**.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Retrieve reference to a blob named "my-blob-1".
	azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

	// Delete the blob.
	blockBlob.delete_blob();

## Próximas etapas
Agora que você aprendeu os conceitos básicos do armazenamento de blobs, siga estes links para saber mais sobre o armazenamento do Azure.

-	[Como usar o Armazenamento de Filas do C++](storage-c-plus-plus-how-to-use-queues.md)
-	[Como usar o Armazenamento de Tabelas do C++](storage-c-plus-plus-how-to-use-tables.md)
-	[Biblioteca do Cliente de Armazenamento para C++](https://msdn.microsoft.com/library/azure/gg433040.aspx) 
-	[Referência do MSDN de Armazenamento do Azure](https://msdn.microsoft.com/library/azure/gg433040.aspx)
-	[Documentação do Armazenamento do Azure](http://azure.microsoft.com/documentation/services/storage/)





<!--HONumber=52-->
