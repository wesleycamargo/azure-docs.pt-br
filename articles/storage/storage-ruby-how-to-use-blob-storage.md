<properties
	pageTitle="Como usar o Armazenamento de Blob do Ruby | Microsoft Azure"
	description="Armazene dados não estruturados na nuvem com o Armazenamento de Blobs do Azure (armazenamento de objeto)."
	services="storage"
	documentationCenter="ruby"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="ruby"
	ms.topic="article"
    ms.date="04/29/2016"
	ms.author="robmcm"/>


# Como usar o Armazenamento de blob no Ruby

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Visão geral

Este guia mostra como executar cenários comuns usando o Armazenamento de Blobs. Os exemplos de código são gravados com a API do Ruby. Os cenários cobertos incluem **carregamento listagem, download** e **exclusão** de blobs.

[AZURE.INCLUDE [armazenamento-blob-conceitos-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Criar um aplicativo Ruby

Crie um aplicativo Ruby. Para obter instruções, veja [Aplicativo Web Ruby on Rails em uma VM do Azure](../virtual-machines/virtual-machines-linux-classic-ruby-rails-web-app.md)

## Configurar seu aplicativo para acessar o Armazenamento

Para usar o Armazenamento do Azure, você deverá baixar e usar o pacote do Azure do Ruby, que inclui um conjunto de bibliotecas convenientes que se comunicam com os serviços REST do armazenamento.

### Usar RubyGems para obter o pacote

1. Use uma interface da linha de comando como **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix).

2. Digite "gem install azure" na janela de comandos para instalar a gema e as dependências.

### Importar o pacote

Usando seu editor de texto favorito, adicione o seguinte na parte superior do arquivo do Ruby no qual você pretende usar o armazenamento:

	require "azure"

## Configurar uma conexão de armazenamento do Azure

O módulo do Azure lerá as variáveis de ambiente **AZURE\_STORAGE\_ACCOUNT** e **AZURE\_STORAGE\_ACCESS\_KEY** para obter as informações necessárias para se conectar à sua conta de armazenamento do Azure. Se essas variáveis de ambiente não forem definidas, você deverá especificar as informações da conta antes de usar **Azure::Blob::BlobService** com o seguinte código:

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your azure storage access key>"


Para obter esses valores de uma Conta de Armazenamento Clássica ou ARM no Portal do Azure:

1. Faça logon no [Portal do Azure](https://portal.azure.com).
2. Navegue até a conta de armazenamento que você deseja usar.
3. Na folha Configurações no lado direito, clique em **Chaves de Acesso**.
4. Na folha Acessar chaves exibida, você verá as teclas de acesso 1 e 2. Você pode usar qualquer uma das duas. 
5. Clique no ícone de cópia para copiar a chave para a área de transferência. 

Para obter esses valores de uma Conta de Armazenamento Clássica no Portal Clássico:

1. Faça logon no [Portal Clássico](https://manage.windowsazure.com).
2. Navegue até a conta de armazenamento que você deseja usar.
3. Clique em **GERENCIAR CHAVES DE ACESSO** na parte inferior do painel de navegação.
4. Na caixa de diálogo pop-up, você verá o nome da conta de armazenamento, a chave de acesso primária e a chave de acesso secundária. Para a chave de acesso, você pode usar tanto a primária quanto a secundária. 
5. Clique no ícone de cópia para copiar a chave para a área de transferência.

## Criar um contêiner

[AZURE.INCLUDE [armazenamento-contêiner-nomeando-regras-include](../../includes/storage-container-naming-rules-include.md)]

O objeto **Azure::Blob::BlobService** permite que você trabalhe com contêineres e blobs. Para criar um contêiner, use o método **create\_container()**.

O exemplo de código a seguir cria um contêiner ou imprime o erro, se houver algum.

	azure_blob_service = Azure::Blob::BlobService.new
	begin
	  container = azure_blob_service.create_container("test-container")
	rescue
	  puts $!
	end

Se desejar tornar públicos os arquivos do contêiner, você pode definir as permissões do contêiner.

Você pode modificar apenas a chamada <strong>create\_container()</strong> para passar a opção **:public\_access\_level**:

	container = azure_blob_service.create_container("test-container",
	  :public_access_level => "<public access level>")


Os valores válidos da opção **:public\_access\_level** são:

* **blob:** Especifica o acesso de leitura público completo dos dados do contêiner e de blob. Os clientes podem enumerar os blobs no contêiner por meio de uma solicitação anônima, mas não podem enumerar os contêineres em uma conta de armazenamento.

* **contêiner:** Especifica o acesso de leitura público de blobs. Os dados do blob nesse contêiner podem ser lidos por meio de solicitação anônima, mas os dados do contêiner não estão disponíveis. Os clientes não podem enumerar os blobs no contêiner por meio de uma solicitação anônima.

Você também pode modificar o nível de acesso público de um contêiner usando **set\_container\_acl()** para especificar o nível de acesso público.

O exemplo a seguir altera o nível de acesso público para **contêiner**:

	azure_blob_service.set_container_acl('test-container', "container")

## Carregar um blob em um contêiner

Para carregar o conteúdo em um blob, use o método **create\_block\_blob()** para criar o blob e use um arquivo ou uma cadeia como o conteúdo de blob.

O código a seguir carrega o arquivo **test.png** como um novo blob chamado "image-blob" no contêiner.

	content = File.open("test.png", "rb") { |file| file.read }
	blob = azure_blob_service.create_block_blob(container.name,
	  "image-blob", content)
	puts blob.name

## Listar os blobs em um contêiner

Para listar os contêineres, use o método **list\_containers()**. Para listar os blobs em um contêiner, use o método **list\_blobs()**.

Ele envia as urls de todos os blobs em todos os contêineres à conta.

	containers = azure_blob_service.list_containers()
	containers.each do |container|
	  blobs = azure_blob_service.list_blobs(container.name)
	  blobs.each do |blob|
	    puts blob.name
	  end
	end

## Baixar blobs

Para baixar blobs, use o método **get\_blob()** para recuperar o conteúdo.

O exemplo a seguir demonstra o uso de **get\_blob()** para baixar o conteúdo de "image-blob" e gravá-lo em um arquivo local.

	blob, content = azure_blob_service.get_blob(container.name,"image-blob")
	File.open("download.png","wb") {|f| f.write(content)}

## Excluir um blob
Finalmente, para excluir um blob, use o método **delete\_blob()**. O exemplo de código a seguir demonstra como excluir um blob.

	azure_blob_service.delete_blob(container.name, "image-blob")

## Próximas etapas

Para saber mais sobre tarefas complexas de armazenamento, siga estes links:

- [Blog da equipe de Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- [SDK do Azure para repositório Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) no GitHub
- [Transferir dados com o Utilitário de Linha de Comando AzCopy](storage-use-azcopy.md)

<!---HONumber=AcomDC_0511_2016-->