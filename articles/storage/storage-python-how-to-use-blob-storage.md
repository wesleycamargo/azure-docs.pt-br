<properties 
	pageTitle="Como usar o Armazenamento de Blob do Python | Microsoft Azure" 
	description="Saiba como usar o serviço Blob do Azure do Python para carregar, baixar, listar e excluir blobs." 
	services="storage" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="huvalo"/>

# Como usar o armazenamento de Blob no Python

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Visão geral

Este guia mostra como executar cenários comuns usando o
Serviço de armazenamento de Blob do Azure. Os exemplos são escritos em Python e usam o [Pacote do Python Azure][]. Os cenários abordados incluem **carregamento**, **listagem**,
**download** e **exclusão** de blobs.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Como: Criar um contêiner

> [AZURE.NOTE] Se você precisar instalar o Python ou o [pacote do Python Azure][], consulte o [Guia de instalação do Python](../python-how-to-install.md).


O objeto **serviço Blob** permite que você trabalhe com contêineres e blobs. O código a seguir cria um objeto **BlobService**. Adicione o seguinte próximo à parte superior de qualquer arquivo Python no qual você deseja acessar o Armazenamento do Azure programaticamente:

	from azure.storage import BlobService

O código a seguir cria um objeto **Serviço de blob** usando o nome da conta de armazenamento e a chave da conta.  Substitua 'myaccount' e 'mykey' pela conta e a chave reais.

	blob_service = BlobService(account_name='myaccount', account_key='mykey')

Todos os blobs de armazenamento residem em um contêiner. Você pode usar uma **BlobService** objeto para criar o contêiner, se ele não existir:

	blob_service.create_container('mycontainer')

Por padrão, o novo contêiner é particular; portanto, você deve especificar sua chave de acesso de armazenamento (como anteriormente) para baixar blobs desse contêiner. Se desejar disponibilizar os arquivos dentro do contêiner para todas as pessoas, você pode criar o contêiner e passar o nível de acesso público usando o seguinte código:

	blob_service.create_container('mycontainer', x_ms_blob_public_access='container') 

Como alternativa, você pode modificar um contêiner após você tê-lo criado usando o seguinte código:

	blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

Após essa alteração, qualquer pessoa na Internet pode ver blobs em um contêiner público, mas apenas você pode modificar ou excluí-los.

## Como: Carregar um blob em um contêiner

Para carregar dados em um blob, use os métodos **put_block_blob_from_path**, **put_block_blob_from_file**, **put_block_blob_from_bytes** ou **put_block_blob_from_text**. Esses são métodos de alto nível que realizam a separação por partes necessária quando o tamanho do arquivo excede 64 MB.

**put_block_blob_from_path** carrega o conteúdo de um arquivo do caminho especificado e **put_block_blob_from_file** carrega o conteúdo de um arquivo/fluxo já aberto. **put_block_blob_from_bytes** carrega uma matriz de bytes e **put_block_blob_from_text** carrega o valor de texto especificado usando a codificação especificada (usa UTF-8 como padrão).

O exemplo a seguir carrega o conteúdo do arquivo **sunset.png** no blob **myblob**.

	blob_service.put_block_blob_from_path(
        'mycontainer',
        'myblob',
        'sunset.png',
        x_ms_blob_content_type='image/png'
    )

## Como: Listar os blobs em um contêiner

Para listar os blobs em um contêiner, use o método **list_blobs** com um loop
**for** para exibir o nome de cada blob no contêiner. O código a seguir exibe o **nome** e a **url** de cada blob em um contêiner para o console.

	blobs = blob_service.list_blobs('mycontainer')
	for blob in blobs:
		print(blob.name)
		print(blob.url)

## Como: Baixar blobs

Para baixar dados de um blob, use **get_blob_to_path**, **get_blob_to_file**, **get_blob_to_bytes** ou **get_blob_to_text**. Esses são métodos de alto nível que realizam a separação por partes necessária quando o tamanho do arquivo excede 64 MB.

O exemplo a seguir demonstra o uso de **get_blob_to_path** para baixar o conteúdo do blob **myblob** e armazená-lo no arquivo **out-sunset.png**:

	blob_service.get_blob_to_path('mycontainer', 'myblob', 'out-sunset.png')

## Como: Excluir um blob

Finalmente, para excluir um blob, chame **delete_blob**.

	blob_service.delete_blob('mycontainer', 'myblob') 

## Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de blobs, siga estes links para saber mais sobre tarefas de armazenamento mais complexas.

-   Consulte a referência de MSDN: [Armazenando e acessando dados no Azure][]
-   Visite o [Blog da Equipe de Armazenamento do Azure][]

[Armazenando e acessando dados no Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Blog da equipe do Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Pacote do Python Azure]: https://pypi.python.org/pypi/azure  

<!--HONumber=49--> 