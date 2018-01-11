---
title: "Guia de início rápido do Azure – Transferir objetos para/do Armazenamento de Blobs do Azure usando Python | Microsoft Docs"
description: Aprenda rapidamente a transferir objetos de/para o Armazenamento de Blobs do Azure usando Python
services: storage
author: ruthogunnnaike
manager: jeconnoc
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 10/12/2017
ms.author: v-ruogun
ms.openlocfilehash: 50f43e6ef9ee60cbf489bb8d0c1c64ca61a393e1
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2018
---
#  <a name="transfer-objects-tofrom-azure-blob-storage-using-python"></a>Transferir objetos de/para o Armazenamento de Blobs do Azure usando o Python
Neste guia de início rápido, você aprenderá como usar Python para carregar, baixar e listar blobs de bloco em um contêiner no Armazenamento de Blobs do Azure. 

## <a name="prerequisites"></a>pré-requisitos

Para concluir este guia de início rápido: 
* Instalar o [Python](https://www.python.org/downloads/)
* Baixar e instalar o [SDK do Armazenamento do Azure para Python](storage-python-how-to-use-blob-storage.md#download-and-install-azure-storage-sdk-for-python). 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Baixar o aplicativo de exemplo
O [aplicativo de exemplo](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git) usado neste guia de início rápido é um aplicativo Python.  

Use o [git](https://git-scm.com/) para baixar uma cópia do aplicativo para seu ambiente de desenvolvimento. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

Este comando clona o repositório para sua pasta do git local. Para abrir o programa de Python, procure a pasta storage-blobs-python-quickstart e o arquivo example.py.  

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de conexão de armazenamento
No aplicativo, você deve fornecer o nome da conta de armazenamento e a chave de conta para criar um objeto `BlockBlobService`. Abra o arquivo `example.py` no Gerenciador de Soluções na sua IDE. Substitua os valores **accountname** e **accountkey** pelo nome e chave da conta. 

```python 
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
```

## <a name="run-the-sample"></a>Execute o exemplo
Este exemplo cria um arquivo de teste na pasta 'Documents'. O programa de exemplo carrega o arquivo de teste no armazenamento de Blobs, lista os blobs no contêiner e baixa o arquivo com um novo nome. 

Execute o exemplo. A saída a seguir é um exemplo da saída retornada ao executar o aplicativo:
  
```
Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
Quando você pressiona qualquer tecla para continuar, o programa de exemplo exclui o contêiner de armazenamento e os arquivos. Antes de continuar, verifique se os dois documentos estão na pasta 'Documentos'. Você pode abri-los e ver que eles são idênticos.

Você também pode usar uma ferramenta como o [Gerenciador de Armazenamento do Azure](http://storageexplorer.com) para exibir os arquivos no Armazenamento de Blobs. O Gerenciador de Armazenamento do Azure é uma ferramenta gratuita de multiplataforma que permite que você acesse as informações da sua conta de armazenamento. 

Depois de verificar os arquivos, pressione qualquer tecla para concluir a demonstração e excluir os arquivos de teste. Agora que você sabe o que o exemplo faz, abra o arquivo example.py para examinar o código. 

## <a name="understand-the-sample-code"></a>Entender o código de exemplo

Em seguida, vamos percorrer o código de exemplo para que você possa entender como ele funciona.

### <a name="get-references-to-the-storage-objects"></a>Obter referências a objetos de armazenamento
A primeira coisa a fazer é criar as referências aos objetos usados para acessar e gerenciar o Armazenamento de Blobs. Esses objetos dependem uns dos outros, e cada um é usado pelo próximo na lista.

* Crie uma instância do objeto **BlockBlobService**, que aponta para o serviço Blob na sua conta de armazenamento. 

* Crie uma instância do objeto **CloudBlobContainer** que representa o contêiner que você está acessando. Os contêineres são usados para organizar seus blobs da mesma forma que você usa pastas no seu computador para organizar seus arquivos.

Assim que você tiver o contêiner de Blob de Nuvem, poderá criar a instância do objeto **CloudBlockBlob** que aponta para o blob específico no qual você está interessado e realizar as operações como carregar, baixar e copiar.

> [!IMPORTANT]
> Os nomes de contêiner devem estar em minúsculas. Consulte [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Nomenclatura e referência de contêineres, blobs e metadados) para obter mais informações sobre nomes de contêiner e de blobs.

Nesta seção, você instancia os objetos, cria um novo contêiner e, em seguida, define as permissões no contêiner para que os blobs sejam públicos. O contêiner é chamado de **quickstartblobs**. 

```python 
# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
 
# Create a container called 'quickstartblobs'.
container_name ='quickstartblobs'
block_blob_service.create_container(container_name) 

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(container_name, public_access=PublicAccess.Container)
```
### <a name="upload-blobs-to-the-container"></a>Carregar blobs para o contêiner

O Armazenamento de Blobs dá suporte a blobs de blocos, blobs de acréscimo e blobs de páginas. Os blobs de blocos são utilizados com mais frequência e são usados nesse guia de início rápido.  

Para carregar um arquivo em um blob, obtenha o caminho completo do arquivo unindo o nome do diretório e o nome do arquivo na unidade local. Em seguida, você pode carregar o arquivo no caminho especificado usando o método **create\_blob\_from\_path**. 

O exemplo de código cria um arquivo local a ser usado para upload e download, armazenando o arquivo a ser carregado como **file\_path\_to\_file** e o nome do blob como **local\_file\_name**. O exemplo a seguir carrega o arquivo para seu contêiner chamado **quickstartblobs**.

```python
# Create a file in Documents to test the upload and download.
local_path=os.path.expanduser("~\Documents")
local_file_name ="QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file =os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file,  'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name
block_blob_service.create_blob_from_path(container_name, local_file_name, full_path_to_file)
```

Há vários métodos de upload que você pode usar com o Armazenamento de Blobs. Por exemplo, se você tiver um fluxo de memória, poderá usar o método **create\_blob\_from\_stream** em vez do **create\_blob\_from\_path**. 

Os blobs de bloco podem ter até 4,7 TB e podem ser qualquer coisa desde planilhas do Excel até arquivos de vídeo grandes. Os blobs de páginas são usados principalmente para os arquivos VHD usados auxiliar as VMs IaaS. Os blobs de acréscimo são usados para registro em log, como quando você quer gravar em um arquivo e depois adicionar mais informações. A maioria dos objetos armazenados no Armazenamento de Blobs são blobs de blocos.

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

Obter uma lista de arquivos no contêiner usando o método **list_blobs**. Esse método retorna um gerador. O código a seguir recupera a lista de blobs e a percorre, mostrando os nomes dos blobs encontrados em um contêiner.  

```python
# List the blobs in the container
print("\nList blobs in the container")
    generator = block_blob_service.list_blobs(container_name)
    for blob in generator:
        print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>Baixar os blobs

Baixar blobs para o seu disco local usando o método **get\_blob\_to\_path**. O código a seguir baixa o blob carregado em uma seção anterior. "_DOWNLOADED" é adicionado como um sufixo ao nome do blob para que você possa ver ambos os arquivos no disco local. 

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, string.replace(local_file_name ,'.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>Limpar recursos
Se você não precisar mais dos blobs carregados neste guia de início rápido, poderá excluir o contêiner inteiro usando **delete\_container**. Se os arquivos criados não forem mais necessárias, você usa o método **delete\_blob** para excluir os arquivos.

```python
# Clean up resources. This includes the container and the temp files
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```

## <a name="next-steps"></a>Próximas etapas
 
Nesse guia de início rápido, você aprendeu a transferir arquivos entre o disco local e o armazenamento de Blobs do Azure usando Python. Para saber mais sobre como trabalhar com o armazenamento de blobs, prossiga para as instruções do armazenamento de blobs.

> [!div class="nextstepaction"]
> [Instruções de operações do Armazenamento de Blobs](./storage-python-how-to-use-blob-storage.md)
 

Para obter mais informações sobre o Gerenciador de Armazenamento e os Blobs, consulte [Gerenciar os recursos de Armazenamento de Blobs do Azure com o Gerenciador de Armazenamento](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
