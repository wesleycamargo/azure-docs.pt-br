<properties linkid="dev-ruby-how-to-blob-storage" urlDisplayName="Serviço Blob" pageTitle="Como usar o armazenamento de blobs (Ruby) | Microsoft Azure" metaKeywords="Introdução aos blobs do Azure, Dados não estruturados do Azure, Armazenamento não estruturado do Azure, Blob do Azure, Armazenamento de blobs no Azure, Ruby para blobs no Azure" description="Saiba como usar o serviço Blob do Azure para carregar, baixar, listar e excluir o conteúdo de blobs. Os exemplos foram escritos em Ruby." metaCanonical="" services="storage" documentationCenter="Ruby" title="Como usar o serviço Blob com Ruby" authors="guayan" solutions="" manager="" editor="" />





#Como usar o serviço Blob com Ruby

Este guia mostra como executar cenários comuns usando o serviço Blob do Azure. Os exemplos de código são gravados com a API do Ruby.
Os cenários abrangidos incluem **carregar, listar, baixar** e **excluir** blobs.
Para obter mais informações sobre blobs, consulte a seção [Próximas etapas](#next-steps).

##Sumário

* [O que é o serviço Blob?](#what-is)
* [Conceitos](#concepts)
* [Criar uma conta de armazenamento do Azure](#CreateAccount)
* [Criar um aplicativo Ruby](#CreateRubyApp)
* [Configurar seu aplicativo para acessar o armazenamento](#ConfigAccessStorage)
* [Configurar uma conexão de armazenamento do Azure](#SetupStorageConnection)
* [Como criar um contêiner](#CreateContainer)
* [Como carregar um blob em um contêiner](#UploadBlob)
* [Como listar os blobs em um contêiner](#ListBlobs)
* [Como baixar blobs](#DownloadBlobs)
* [Como: excluir um blob](#DeleteBlob)
* [Próximas etapas](#NextSteps)


[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <a id="CreateAccount"></a>Criar uma conta de armazenamento do Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a id="CreateRubyApp"></a>Criar um aplicativo Ruby

Crie um aplicativo Ruby. Para obter instruções, consulte [Criar um aplicativo Ruby no Azure (a página pode estar em inglês)](/pt-br/develop/ruby/tutorials/web-app-with-linux-vm/).

## <a id="ConfigAccessStorage"></a>Configurar seu aplicativo para acessar o armazenamento

Para usar o armazenamento do Azure, você deverá baixar e usar o pacote do Azure do Ruby, que inclui um conjunto de bibliotecas convenientes que se comunicam com os serviços REST do armazenamento.

### Usar RubyGems para obter o pacote

1. Use uma interface de linha de comando, como o **PowerShell** (Windows), o **Terminal** (Mac) ou o **Bash** (Unix).

2. Digite "gem install azure" na janela de comando para instalar o gem e as dependências.

### Importar o pacote

Usando seu editor de texto favorito, adicione o seguinte na parte superior do arquivo do Ruby no qual você pretende usar o armazenamento:

	require "azure"

## <a id="SetupStorageConnection"></a>Configurar uma conexão de armazenamento do Azure

O módulo do Azure lerá as variáveis de ambiente **AZURE\_STORAGE\_ACCOUNT** e **AZURE\_STORAGE\_ACCESS_KEY** para obter as informações necessárias para se conectar à sua conta de armazenamento do Azure. Se essas variáveis de ambiente não forem definidas, você deverá especificar as informações da conta antes de usar **Azure::BlobService** com o seguinte código:

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your azure storage access key>"


Para obter esses valores:

1. Faça logon no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com/).
2. Navegue até a conta de armazenamento que deseja usar.
3. Clique em **GERENCIAR CHAVES** na parte inferior do painel de navegação.
4. Na caixa de diálogo pop-up, você verá o nome da conta de armazenamento, a chave de acesso primária e a chave de acesso secundária. Para a chave de acesso, você pode usar tanto a primária quanto a secundária.

## <a id="CreateContainer"></a>Como criar um contêiner

O objeto **Azure::BlobService** permite que você trabalhe com contêineres e blobs. Para criar um contêiner, use o método **create\_container()**.

O seguinte exemplo cria um contêiner ou imprime o erro, se houver algum.

	azure_blob_service = Azure::BlobService.new
	begin
	  container = azure_blob_service.create_container("test-container")
	rescue
	  puts $!
	end

Se desejar tornar públicos os arquivos do contêiner, você pode definir as permissões do contêiner. 

Você pode modificar apenas a chamada <strong>create\_container()</strong> para transmitir a opção **:public\_access\_level**:

	container = azure_blob_service.create_container("test-container", 
	  :public_access_level => "<public access level>")


Os valores válidos para a opção **:public\_access\_level** são:

* **blob:** Especifica o acesso de leitura público completo dos dados do contêiner e de blob. Os clientes podem enumerar os blobs no contêiner por meio de uma solicitação anônima, mas não podem enumerar os contêineres em uma conta de armazenamento.

* **contêiner:** Especifica o acesso de leitura público de blobs. Os dados do blob nesse contêiner podem ser lidos por meio de solicitação anônima, mas os dados do contêiner não estão disponíveis. Os clientes não podem enumerar os blobs no contêiner por meio de uma solicitação anônima.

Como alternativa, você pode modificar o nível de acesso público de um contêiner usando o método **set\_container\_acl()** para especificar o nível de acesso público.
 
O exemplo a seguir altera o nível de acesso público para **contêiner**:

	azure_blob_service.set_container_acl('test-container', "container")

## <a id="UploadBlob"></a>Como carregar um blob em um contêiner

Para carregar conteúdo em um blob, use o método **create\_block\_blob()** para criar o blob e use um arquivo ou uma cadeia de caracteres como o conteúdo do blob. 

O código a seguir carregará o arquivo **test.png** como um novo blob chamado "image-blob" no contêiner.

	content = File.open("test.png", "rb") { |file| file.read }
	blob = azure_blob_service.create_block_blob(container.name,
	  "image-blob", content)
	puts blob.name

## <a id="ListBlobs"></a>Como listar os blobs em um contêiner

Para listar os contêineres, use o método **list_containers()**. 
Para listar os blobs em um contêiner, use o método **list\_blobs()**. 

Ele envia os urls de todos os blobs em todos os contêiner para a conta.

	containers = azure_blob_service.list_containers()
	containers.each do |container|
	  blobs = azure_blob_service.list_blobs(container.name)
	  blobs.each do |blob|
	    puts blob.name
	  end
	end

## <a id="DownloadBlobs"></a>Como baixar blobs

Para baixar blobs, use o método **get\_blob()** para recuperar o conteúdo. 

O exemplo a seguir demonstra o uso de **get\_blob()** para baixar o conteúdo de "image-blob" e gravá-lo em um arquivo local.

	blob, content = azure_blob_service.get_blob(container.name,"image-blob")
	File.open("download.png","wb") {|f| f.write(content)}

## <a id="DeleteBlob"></a>Como excluir um blob
Por fim, para excluir um blob, use o método **delete\_blob()**. O exemplo a seguir demonstra como excluir um blob.

	azure_blob_service.delete_blob(container.name, "image-blob")

## <a id="NextSteps"></a>Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de blobs, siga estes links para saber como fazer tarefas de armazenamento mais complexas.

-   Consulte a referência do MSDN: [Armazenando e acessando dados no Azure (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx)
-   Acesse o [Blog da Equipe de Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/)
-   Visite o repositório do [SDK do Azure para Ruby (a página pode estar em inglês)](https://github.com/WindowsAzure/azure-sdk-for-ruby) no GitHub

