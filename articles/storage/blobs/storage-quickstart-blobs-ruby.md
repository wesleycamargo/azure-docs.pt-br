---
title: "Guia de início rápido do Azure – Transferir objetos para/do armazenamento de Blobs do Azure usando o Ruby | Microsoft Docs"
description: Aprenda rapidamente a transferir objetos de/para o Armazenamento de Blobs do Azure usando o Ruby
services: storage
author: ruthogunnnaike
manager: jeconnoc
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: quickstart
ms.date: 01/18/2018
ms.author: seguler
ms.openlocfilehash: 649099f045639c8c506fb4a4be65736626044fe6
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2018
---
#  <a name="transfer-objects-tofrom-azure-blob-storage-using-ruby"></a>Transferir objetos de/para o Armazenamento de Blobs do Azure usando o Ruby
Neste guia de início rápido, você aprenderá a usar o Ruby para carregar, baixar e listar blobs de blocos em um contêiner no Armazenamento de Blobs do Azure. 

## <a name="prerequisites"></a>pré-requisitos

Para concluir este guia de início rápido: 
* Instalar o [Ruby](https://www.ruby-lang.org/en/downloads/)
* Instale a [biblioteca do Armazenamento do Azure para o Ruby](https://docs.microsoft.com/azure/storage/blobs/storage-ruby-how-to-use-blob-storage#configure-your-application-to-access-storage) usando o pacote rubygem. 

```
gem install azure-storage-blob
```

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Baixar o aplicativo de exemplo
O [aplicativo de exemplo](https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git) usado neste guia de início rápido é um aplicativo Ruby.  

Use o [git](https://git-scm.com/) para baixar uma cópia do aplicativo para seu ambiente de desenvolvimento. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git 
```

Este comando clona o repositório para sua pasta do git local. Para abrir o aplicativo de exemplo Ruby, procure a pasta storage-blobs-ruby-quickstart e abra o arquivo example.rb.  

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de conexão de armazenamento
No aplicativo, você deve fornecer o nome da conta de armazenamento e a chave de conta para criar a instância `BlobService` do seu aplicativo. Abra o arquivo `example.rb` no Gerenciador de Soluções na sua IDE. Substitua os valores **accountname** e **accountkey** pelo nome e chave da conta. 

```ruby 
blob_client = Azure::Storage::Blob::BlobService.create(
            storage_account_name: account_name,
            storage_access_key: account_key
          )
```

## <a name="run-the-sample"></a>Execute o exemplo
Este exemplo cria um arquivo de teste na pasta 'Documents'. O programa de exemplo carrega o arquivo de teste no armazenamento de Blobs, lista os blobs no contêiner e baixa o arquivo com um novo nome. 

Execute o exemplo. A saída a seguir é um exemplo da saída retornada ao executar o aplicativo:
  
```
Creating a container: quickstartblobs7b278be3-a0dd-438b-b9cc-473401f0c0e8

Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
Quando você pressiona qualquer tecla para continuar, o programa de exemplo exclui o contêiner de armazenamento e os arquivos. Antes de continuar, verifique se os dois documentos estão na pasta 'Documentos'. Você pode abri-los e ver que eles são idênticos.

Você também pode usar uma ferramenta como o [Gerenciador de Armazenamento do Azure](http://storageexplorer.com) para exibir os arquivos no Armazenamento de Blobs. O Gerenciador de Armazenamento do Azure é uma ferramenta gratuita de multiplataforma que permite que você acesse as informações da sua conta de armazenamento. 

Depois de verificar os arquivos, pressione qualquer tecla para concluir a demonstração e excluir os arquivos de teste. Agora que você sabe o que o exemplo faz, abra o arquivo example.rb para examinar o código. 

## <a name="understand-the-sample-code"></a>Entender o código de exemplo

Em seguida, vamos percorrer o código de exemplo para que você possa entender como ele funciona.

### <a name="get-references-to-the-storage-objects"></a>Obter referências a objetos de armazenamento
A primeira coisa a fazer é criar as referências aos objetos usados para acessar e gerenciar o Armazenamento de Blobs. Esses objetos dependem uns dos outros, e cada um é usado pelo próximo na lista.

* Crie uma instância do objeto do **BlobService** do Armazenamento do Azure para configurar as credenciais de conexão. 
* Crie o objeto **Container**, que representa o contêiner que você está acessando. Os contêineres são usados para organizar seus blobs da mesma forma que você usa pastas no seu computador para organizar seus arquivos.

Assim que você tiver o contêiner de Blob de Nuvem, poderá criar o objeto **Block** do blob, que aponta para o blob específico de interesse, e poderá realizar operações como carregar, baixar e copiar.

> [!IMPORTANT]
> Os nomes de contêiner devem estar em minúsculas. Consulte [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Nomenclatura e referência de contêineres, blobs e metadados) para obter mais informações sobre nomes de contêiner e de blobs.

Nesta seção, você configura uma instância do cliente do armazenamento do Azure, instancia o objeto do serviço Blob, cria um novo contêiner e, em seguida, define as permissões no contêiner para que os blobs sejam públicos. O contêiner é chamado de **quickstartblobs**. 

```ruby 
# Create a BlobService object
blob_client = Azure::Storage::Blob::BlobService.create(
    storage_account_name: account_name,
    storage_access_key: account_key
    )

# Create a container called 'quickstartblobs'.
container_name ='quickstartblobs' + SecureRandom.uuid
container = blob_client.create_container(container_name)   

# Set the permission so the blobs are public.
blob_client.set_container_acl(container_name, "container")
```

### <a name="upload-blobs-to-the-container"></a>Carregar blobs para o contêiner

O Armazenamento de Blobs dá suporte a blobs de blocos, blobs de acréscimo e blobs de páginas. Os blobs de blocos são utilizados com mais frequência e são usados nesse guia de início rápido.  

Para carregar um arquivo em um blob, obtenha o caminho completo do arquivo unindo o nome do diretório e o nome do arquivo na unidade local. Em seguida, você pode carregar o arquivo no caminho especificado usando o método **create\_block\_blob()**. 

O exemplo de código cria um arquivo local a ser usado para upload e download, armazenando o arquivo a ser carregado como **file\_path\_to\_file** e o nome do blob como **local\_file\_name**. O exemplo a seguir carrega o arquivo para seu contêiner chamado **quickstartblobs**.

```ruby
# Create a file in Documents to test the upload and download.
local_path=File.expand_path("~/Documents")
local_file_name ="QuickStart_" + SecureRandom.uuid + ".txt"
full_path_to_file =File.join(local_path, local_file_name)

# Write text to the file.
file = File.open(full_path_to_file,  'w')
file.write("Hello, World!")
file.close()

puts "Temp file = " + full_path_to_file
puts "\nUploading to Blob storage as blob" + local_file_name

# Upload the created file, using local_file_name for the blob name
blob_client.create_block_blob(container.name, local_file_name, full_path_to_file)
```

Para executar uma atualização parcial do conteúdo de um blob de blocos, use o método **create\_block\_list()**. Os blobs de bloco podem ter até 4,7 TB e podem ser qualquer coisa desde planilhas do Excel até arquivos de vídeo grandes. Os blobs de páginas são usados principalmente para os arquivos VHD usados auxiliar as VMs IaaS. Os blobs de acréscimo são usados para registro em log, como quando você quer gravar em um arquivo e depois adicionar mais informações. O acréscimo de blobs deve ser usado em um único modo de gravação. A maioria dos objetos armazenados no Armazenamento de Blobs são blobs de blocos.

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

É possível obter uma lista de arquivos no contêiner usando o método **list\_blobs()**. O código a seguir recupera a lista de blobs e a percorre, mostrando os nomes dos blobs encontrados em um contêiner.  

```ruby
# List the blobs in the container
nextMarker = nil
loop do
    blobs = blob_client.list_blobs(container_name, { marker: nextMarker })
    blobs.each do |blob|
        puts "\tBlob name #{blob.name}"
    end
    nextMarker = blobs.continuation_token
    break unless nextMarker && !nextMarker.empty?
end
```

### <a name="download-the-blobs"></a>Baixar os blobs

Baixe os blobs para o seu disco local usando o método **get\_blob()**. O código a seguir baixa o blob carregado em uma seção anterior. "_DOWNLOADED" é adicionado como um sufixo ao nome do blob para que você possa ver ambos os arquivos no disco local. 

```ruby
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = File.join(local_path, local_file_name.gsub('.txt', '_DOWNLOADED.txt'))

puts "\n Downloading blob to " + full_path_to_file2
blob, content = blob_client.get_blob(container_name,local_file_name)
File.open(full_path_to_file2,"wb") {|f| f.write(content)}
```

### <a name="clean-up-resources"></a>Limpar recursos
Se você não precisar mais dos blobs carregados neste guia de início rápido, poderá excluir o contêiner inteiro usando o método **delete\_container()**. Se os arquivos criados não forem mais necessárias, você usa o método **delete\_blob()** para excluir os arquivos.

```ruby
# Clean up resources. This includes the container and the temp files
blob_client.delete_container(container_name)
File.delete(full_path_to_file)
File.delete(full_path_to_file2)    
```

## <a name="next-steps"></a>Próximas etapas
 
Nesse guia de início rápido, você aprendeu a transferir arquivos entre um disco local e o armazenamento de blobs do Azure usando o Ruby. Para saber mais sobre como trabalhar com o armazenamento de blobs, prossiga para as instruções do armazenamento de blobs.

> [!div class="nextstepaction"]
> [Instruções de operações do Armazenamento de Blobs](./storage-ruby-how-to-use-blob-storage.md)


Para obter mais informações sobre o Gerenciador de Armazenamento e os Blobs, consulte [Gerenciar os recursos de Armazenamento de Blobs do Azure com o Gerenciador de Armazenamento](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
