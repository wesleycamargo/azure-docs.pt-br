<properties urlDisplayName="Blob Service" pageTitle="Como usar o armazenamento de blob (Python) | Microsoft Azure" metaKeywords="Azure blob service Python, Azure blobs Python" description="Saiba como usar o servi&ccedil;o Blob do Azure para carregar, baixar, listar e excluir blobs." metaCanonical="" disqusComments="1" umbracoNaviHide="0" services="storage" documentationCenter="Python" title="Como usar o servi&ccedil;o Blob no Pyton" authors="huvalo" videoId="" scriptId="" manager="wpickett" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="09/19/2014" ms.author="huvalo" />

# Como usar o serviço de armazenamento de blobs no Python

Este guia mostra como executar cenários comuns usando o
serviço de armazenamento de Blob do Azure. Os exemplos foram escritos usando a
API do Python. Os cenários cobertos incluem **carregamento**, **listagem**,
**download** e **exclusão** de blobs. Para obter mais informações sobre blobs,
consulte a seção [Próximas etapas][Próximas etapas].

## Sumário

[O que é Armazenamento de Blob?][O que é Armazenamento de Blob?]
 [Conceitos][Conceitos]
 [Criar uma conta de armazenamento do Azure][Criar uma conta de armazenamento do Azure]
 [Como: Criar um contêiner][Como: Criar um contêiner]
 [Como: Carregar um blob em um contêiner][Como: Carregar um blob em um contêiner]
 [Como: Listar os blobs em um contêiner][Como: Listar os blobs em um contêiner]
 [Como: Baixar blobs][Como: Baixar blobs]
 [Como: Excluir um blob][Como: Excluir um blob]
 [Como: Carregar e baixar blobs grandes][Como: Carregar e baixar blobs grandes]
 [Próximas etapas][Próximas etapas]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <a name="create-account"> </a> Criar uma conta de armazenamento do Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="create-container"> </a>Como: Criar um contêiner

**Observação:** se você precisar instalar o Python ou as bibliotecas de cliente, consulte o [Guia de instalação do Python (a página pode estar em inglês)][Guia de instalação do Python (a página pode estar em inglês)].

O objeto **serviço Blob** permite que você trabalhe com contêineres e blobs. O
código a seguir cria um objeto **BlobService**. Adicione o seguinte código próximo
à parte superior de qualquer arquivo Python no qual você deseja acessar o Armazenamento do Azure programaticamente:

    from azure.storage import BlobService

O código a seguir cria um objeto **Serviço de blob** usando o nome da conta de armazenamento e a chave da conta. Substitua 'Minha conta' e 'mykey' com a conta real e a chave.

    blob_service = BlobService(account_name='myaccount', account_key='mykey')

Todos os blobs de armazenamento residem em um contêiner. Você pode usar uma **BlobService** objeto para criar o contêiner, se ele não existir:

    blob_service.create_container('mycontainer')

Por padrão, o novo contêiner é particular; portanto, você deve especificar suachave de acesso de armazenamento (como anteriormente) para baixar blobs dessecontêiner. Se desejar disponibilizar os arquivos dentro do contêiner para todas as pessoas, você pode criar o contêiner e passar o nível de acesso público usando o seguinte código:

    blob_service.create_container('mycontainer', x_ms_blob_public_access='container') 

Como alternativa, você pode modificar um contêiner após você tê-la criado usando o seguinte código:

    blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

Após essa alteração, qualquer pessoa na Internet pode ver blobs em um contêiner
público, mas apenas você pode modificar ou excluí-los.

## <a name="upload-blob"> </a>Como: Carregar um blob em um contêiner

Para carregar dados em um blob, use os métodos **put\_block\_blob\_from\_path**, **put\_block\_blob\_from\_file**, **put\_block\_blob\_from\_bytes** ou **put\_block\_blob\_from\_text**. Esses são métodos de alto nível que realizam a separação por partes necessária quando o tamanho do arquivo excede 64 MB.

**put\_block\_blob\_from\_path** carrega o conteúdo de um arquivo a partir do caminho especificado, **put\_block\_blob\_from\_file** carrega o conteúdo de um arquivo/fluxo já aberto. **put\_block\_blob\_from\_bytes** carrega uma matriz de bytes, **put\_block\_blob\_from\_text** carrega o valor de texto especificado usando a codificação especificada (UTF-8 é o padrão).

O exemplo a seguir carrega o conteúdo do arquivo **task1.txt** no blob **myblob**.

    blob_service.put_block_blob_from_path('mycontainer', 'myblob', 'task1.txt')

## <a name="list-blob"> </a>Como: Listar os blobs em um contêiner

Para listar os blobs em um contêiner, use o método **list\_blobs** com um loop
**for** para exibir o nome de cada blob no contêiner. O
código a seguir exibe o **nome** e a **url** de cada blob em um contêiner para o
console.

    blobs = blob_service.list_blobs('mycontainer')
    for blob in blobs:
        print(blob.name)
        print(blob.url)

## <a name="download-blobs"> </a>Como: Baixar blobs

Para baixar dados de um blob, use **get\_blob\_to\_path**, **get\_blob\_to\_file**, **get\_blob\_to\_bytes** ou **get\_blob\_to\_text**. Esses são métodos de alto nível que realizam a separação por partes necessária quando o tamanho do arquivo excede 64 MB.

O exemplo a seguir demonstra o uso de **get\_blob\_to\_path** para baixar o conteúdo do blob **myblob** e armazená-lo no arquivo **out-task1.txt**:

    blob_service.get_blob_to_path('mycontainer', 'myblob', 'out-task1.txt')

## <a name="delete-blobs"> </a>Como: Excluir um blob

Finalmente, para excluir um blob, chame **delete\_blob**.

    blob_service.delete_blob('mycontainer', 'myblob') 

## <a name="next-steps"> </a>Próximas etapas

Agora que você conhece os conceitos básicos do armazenamento de blobs, acesse os links a seguir
para saber como executar tarefas de armazenamento mais complexas.

-   Consulte a referência de MSDN: [Armazenando e acessando dados no Azure][Armazenando e acessando dados no Azure]
-   Visite o [Blog da Equipe de Armazenamento do Azure][Blog da Equipe de Armazenamento do Azure]

  [Próximas etapas]: #next-steps
  [O que é Armazenamento de Blob?]: #what-is
  [Conceitos]: #concepts
  [Criar uma conta de armazenamento do Azure]: #create-account
  [Como: Criar um contêiner]: #create-container
  [Como: Carregar um blob em um contêiner]: #upload-blob
  [Como: Listar os blobs em um contêiner]: #list-blob
  [Como: Baixar blobs]: #download-blobs
  [Como: Excluir um blob]: #delete-blobs
  [Como: Carregar e baixar blobs grandes]: #large-blobs
  [Guia de instalação do Python (a página pode estar em inglês)]: ../python-how-to-install/
  [Armazenando e acessando dados no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx
  [Blog da Equipe de Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
