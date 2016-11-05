---
title: Como usar o armazenamento de Blobs do Azure (armazenamento de objeto) do Python | Microsoft Docs
description: Armazene dados não estruturados na nuvem com o Armazenamento de Blobs do Azure (armazenamento de objetos).
services: storage
documentationcenter: python
author: tamram
manager: carmonm
editor: tysonn

ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 09/20/2016
ms.author: jwillis;tamram

---
# Como usar o armazenamento de Blob do Azure no Python
[!INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## Visão geral
O Armazenamento de Blobs do Azure é um serviço que armazena dados não estruturados na nuvem como objetos/blobs. O Armazenamento de Blobs pode conter qualquer tipo de texto ou de dados binários, como um documento, um arquivo de mídia ou um instalador de aplicativo. O Armazenamento de Blobs também é chamado de armazenamento de objetos.

Este artigo mostra como executar cenários comuns usando o armazenamento de Blob. Os exemplos são escritos em Python e usam o [Microsoft Azure Storage SDK for Python] \(SDK do Armazenamento do Microsoft Azure para Python). Os cenários abrangidos incluem carregar, listar, baixar e excluir blobs.

[!INCLUDE [armazenamento-blob-conceitos-include](../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Criar um contêiner
Com base no tipo de blob que você deseja usar, crie um objeto **BlockBlobService**, **AppendBlobService** ou **PageBlobService**. O código a seguir usa um objeto **BlockBlobService**. Adicione o seguinte na parte superior de qualquer arquivo Python no qual você deseja acessar o Armazenamento de Blobs de Blocos do Azure com programação.

    from azure.storage.blob import BlockBlobService

O código a seguir cria um objeto **BlockBlobService** usando o nome da conta de armazenamento e a chave de conta. Substitua “myaccount” e “mykey” pelo nome da sua conta e sua chave.

    block_blob_service = BlockBlobService(account_name='myaccount', account_key='mykey')

[!INCLUDE [armazenamento-contêiner-nomeando-regras-include](../../includes/storage-container-naming-rules-include.md)]

No exemplo de código a seguir, você poderá usar um objeto **BlockBlobService** para criar o contêiner, se ele não existir.

    block_blob_service.create_container('mycontainer')

Por padrão, o novo contêiner é particular; portanto, você deve especificar sua chave de acesso de armazenamento (como anteriormente) para baixar blobs desse contêiner. Se deseja disponibilizar os blobs dentro do contêiner para todas as pessoas, você pode criar o contêiner e passar o nível de acesso público usando o código a seguir.

    from azure.storage.blob import PublicAccess
    block_blob_service.create_container('mycontainer', public_access=PublicAccess.Container)

Como alternativa, você pode modificar um contêiner após você tê-lo criado usando o código a seguir.

    block_blob_service.set_container_acl('mycontainer', public_access=PublicAccess.Container)

Após essa alteração, qualquer pessoa na Internet pode ver blobs em um contêiner público, mas apenas você pode modificar ou excluí-los.

## Carregar um blob em um contêiner
Para criar um blob de blocos e carregar dados, use os métodos **create\_blob\_from\_path**, **create\_blob\_from\_stream**, **create\_blob\_from\_bytes** ou **create\_blob\_from\_text**. Esses são métodos de alto nível que realizam a separação por partes necessária quando o tamanho do arquivo excede 64 MB.

**create\_blob\_from\_path** carrega o conteúdo de um arquivo do caminho especificado e **create\_blob\_from\_stream** carrega o conteúdo de um arquivo/transmissão já aberta. **create\_blob\_from\_bytes** carrega uma matriz de bytes e **create\_blob\_from\_text** carrega o valor de texto especificado usando a codificação especificada (usa UTF-8 como padrão).

O exemplo a seguir carrega o conteúdo do arquivo **sunset.png** no blob **myblob**.

    from azure.storage.blob import ContentSettings
    block_blob_service.create_blob_from_path(
        'mycontainer',
        'myblockblob',
        'sunset.png',
        content_settings=ContentSettings(content_type='image/png')
                )

## Listar os blobs em um contêiner
Para listar os blobs em um contêiner, use o método **list\_blobs**. Esse método retorna um gerador. O código a seguir produz o **nome** de cada blob em um contêiner para o console.

    generator = block_blob_service.list_blobs('mycontainer')
    for blob in generator:
        print(blob.name)

## Baixar blobs
Para baixar dados de um blob, use **get\_blob\_to\_path**, **get\_blob\_to\_stream**, **get\_blob\_to\_bytes** ou **get\_blob\_to\_text**. Esses são métodos de alto nível que realizam a separação por partes necessária quando o tamanho do arquivo excede 64 MB.

O exemplo a seguir demonstra o uso de **get\_blob\_to\_path** para baixar o conteúdo do blob **myblob** e armazená-lo no arquivo **out-sunset.png**.

    block_blob_service.get_blob_to_path('mycontainer', 'myblockblob', 'out-sunset.png')

## Excluir um blob
Finalmente, para excluir um blob, chame **delete\_blob**.

    block_blob_service.delete_blob('mycontainer', 'myblockblob')

## Gravar um blob de anexo
Um blob de acréscimo é otimizado para operações de acréscimo, como o registro em log. Como um blob de blocos, um blob de acréscimo é composto por blocos; no entanto, quando você adiciona um novo bloco a um blob de acréscimo, ele sempre é acrescentado ao fim do blob. Não é possível atualizar ou excluir um bloco existente em um blob de acréscimo. As IDs de bloco de um blob de acréscimo não ficam expostas como em um blob de blocos.

Cada bloco em um blob de acréscimo pode ter um tamanho diferente, até no máximo 4 MB, e um blob de acréscimo pode incluir no máximo 50.000 blocos. O tamanho máximo de um blob de acréscimo, portanto, é de pouco mais de 195 GB (4 MB x 50.000 blocos).

O exemplo a seguir cria um novo blob de anexo e acrescenta alguns dados a ele, simulando uma operação simples de registro em log.

    from azure.storage.blob import AppendBlobService
    append_blob_service = AppendBlobService(account_name='myaccount', account_key='mykey')

    # The same containers can hold all types of blobs
    append_blob_service.create_container('mycontainer')

    # Append blobs must be created before they are appended to
    append_blob_service.create_blob('mycontainer', 'myappendblob')
    append_blob_service.append_blob_from_text('mycontainer', 'myappendblob', u'Hello, world!')

    append_blob = append_blob_service.get_blob_to_text('mycontainer', 'myappendblob')

## Próximas etapas
Agora que você aprendeu os conceitos básicos do armazenamento de Blobs, siga estes links para saber mais.

* [Centro de desenvolvedores do Python](/develop/python/)
* [API REST de serviços de armazenamento do Azure](http://msdn.microsoft.com/library/azure/dd179355)
* [Blog da equipe de Armazenamento do Azure]
* [SDK do Armazenamento do Microsoft Azure para Python]

[Blog da equipe de Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK for Python]: https://github.com/Azure/azure-storage-python
[SDK do Armazenamento do Microsoft Azure para Python]: https://github.com/Azure/azure-storage-python

<!----HONumber=AcomDC_0921_2016-->