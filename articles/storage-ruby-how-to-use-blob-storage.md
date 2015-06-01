<properties 
	pageTitle="Como usar o Armazenamento de Blob do Ruby | Microsoft Azure" 
	description="Saiba como usar o serviço Blob do Azure para carregar, baixar, listar e excluir o conteúdo de blob. Exemplos gravados no Ruby." 
	services="storage" 
	documentationCenter="ruby" 
	authors="tfitzmac,tamram" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="tomfitz"/>


# Como usar o Armazenamento de blob no Ruby

[AZURE.INCLUDE [storage-selector-blob-include](../includes/storage-selector-blob-include.md)]

## Visão geral

Este guia mostra como executar cenários comuns usando o
serviço Blob do Azure. Os exemplos de código são gravados com a API do Ruby.
Os cenários abrangidos incluem **carregar, listar*, *baixar e **excluir** blobs.

[AZURE.INCLUDE [storage-blob-concepts-include](../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

## Criar um aplicativo Ruby

Crie um aplicativo Ruby. Para obter instruções, 
consulte [Criar um aplicativo Ruby no Azure](/develop/ruby/tutorials/web-app-with-linux-vm/).

## Configurar seu aplicativo para acessar o armazenamento

Para usar o armazenamento do Azure, você deverá baixar e usar o pacote do Azure do Ruby, que inclui um conjunto de bibliotecas convenientes que se comunicam com os serviços REST do armazenamento.

### Use o RubyGems para obter o pacote

1. Use uma interface de linha de comando, como o **PowerShell** (Windows), o **Terminal** (Mac) ou o **Bash** (Unix).

2. Digite "gem install azure" na janela de comando para instalar a gema e as dependências.

### Importar o pacote

Usando seu editor de texto favorito, adicione o seguinte na parte superior do arquivo do Ruby no qual você pretende usar o armazenamento:

	exigir "azure"

## Configurar uma conexão de armazenamento do Azure

O módulo do Azure lerá as variáveis de ambiente **AZURE_STORAGE_ACCOUNT** e **AZURE_STORAGE_ACCESS_KEY** 
para obter informações necessárias para se conectar à sua conta de armazenamento do Azure. Se essas variáveis de ambiente não forem definidas, você deverá especificar as informações da conta antes de usar **Azure::BlobService** com o seguinte código:

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your azure storage access key>"


Para obter esses valores:

1. Faça logon no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com/).
2. Navegue até a conta de armazenamento que você deseja usar
3. Clique em **GERENCIAR CHAVES** na parte inferior do painel de navegação.
4. Na caixa de diálogo pop-up, você verá o nome da conta de armazenamento, a chave de acesso primária e a chave de acesso secundária. Para a chave de acesso, você pode usar tanto a primária quanto a secundária.

## Como: Criar um contêiner

O objeto **Azure::BlobService** permite que você trabalhe com contêineres e blobs. Para criar um contêiner, use o método **create_container()**.

O seguinte exemplo cria um contêiner ou imprime o erro, se houver algum.

	azure_blob_service = Azure::BlobService.new
	begin
	  container = azure_blob_service.create_container("test-container")
	rescue
	  puts $!
	end

Se desejar tornar públicos os arquivos do contêiner, você pode definir as permissões do contêiner. 

Você pode modificar apenas a chamada <strong>create_container()</strong> para passar a opção **:public_access_level**:

	container = azure_blob_service.create_container("test-container", 
	  :public_access_level => "<public access level>")


Os valores válidos para a opção **:public_access_level** são:

* **blob:** especifica o acesso de leitura público completo para dados de contêiner e blob. Os clientes podem enumerar blobs dentro do contêiner por meio de uma solicitação anônima, mas não é possível enumerar contêineres dentro da conta de armazenamento.

* **container:** especifica o acesso de leitura público para blobs. Dados de blob nesse contêiner podem ser lidos por solicitação anônima, mas os dados do contêiner não estão disponíveis. Os clientes não podem enumerar blobs dentro do contêiner por solicitação anônima.

Como alternativa, você pode modificar o nível de acesso público de um contêiner usando o método **set_container_acl()** para especificar o nível de acesso público.
 
O exemplo a seguir altera o nível de acesso público para **contêiner**:

	azure_blob_service.set_container_acl('test-container', "container")

## Como: Carregar um blob em um contêiner

Para carregar conteúdo em um blob, use o método **create_block_blob()** para criar o blob e use um arquivo ou uma cadeia de caracteres como o conteúdo do blob. 

O código a seguir carregará o arquivo **test.png** como um novo blob chamado "image-blob" no contêiner.

	content = File.open("test.png", "rb") { |file| file.read }
	blob = azure_blob_service.create_block_blob(container.name,
	  "image-blob", content)
	puts blob.name

## Como: Listar os blobs em um contêiner

Para listar os contêineres, use o método **list_containers()**. 
Para listar os blobs em um contêiner, use o método **list_blobs()**. 

Ele envia as urls de todos os blobs em todos os contêineres à conta.

	containers = azure_blob_service.list_containers()
	containers.each do |container|
	  blobs = azure_blob_service.list_blobs(container.name)
	  blobs.each do |blob|
	    puts blob.name
	  end
	end

## Como: Baixar blobs

Para baixar blobs, use o método **get_blob()** para recuperar o conteúdo. 

O exemplo a seguir demonstra o uso de **get_blob()** para baixar o conteúdo de "image-blob" e gravá-lo em um arquivo local.

	blob, content = azure_blob_service.get_blob(container.name,"image-blob")
	File.open("download.png","wb") {|f| f.write(content)}

## Como: Excluir um blob
Finalmente, para excluir um blob, use o método **delete_blob()**. O exemplo a seguir demonstra como excluir um blob.

	azure_blob_service.delete_blob(container.name, "image-blob")

## Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de blobs, siga estes links para saber mais sobre tarefas de armazenamento mais complexas.

- Consulte a referência de MSDN: [Armazenamento do Azure](http://msdn.microsoft.com/library/azure/gg433040.aspx)
- Visite o Blog da equipe do [Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- Visite o repositório [SDK do Azure para Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) no GitHub

<!--HONumber=49-->