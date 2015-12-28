<properties
	pageTitle="Como usar o Armazenamento de Blob do Azure do Python | Microsoft Azure"
	description="Saiba como usar o armazenamento de Blob do Azure no Python para carregar, baixar, listar e excluir blobs."
	services="storage"
	documentationCenter="python"
	authors="emgerner-msft"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="12/11/2015"
	ms.author="emgerner"/>

# Como usar o armazenamento de Blob do Azure no Python

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Visão geral

Este artigo mostra como executar cenários comuns usando o armazenamento de Blob. Os exemplos são escritos em Python e usam o [pacote de armazenamento do Python Azure][]. Os cenários abrangidos incluem carregar, listar, baixar e excluir blobs.

[AZURE.INCLUDE [armazenamento-blob-conceitos-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Criar um contêiner

> [AZURE.NOTE]Se você precisar instalar o Python ou o [pacote do Python Azure][], consulte o [Guia de instalação do Python](../python-how-to-install.md).

O objeto **serviço Blob** permite que você trabalhe com contêineres e blobs. O código a seguir cria um objeto **BlobService**. Adicione o seguinte próximo à parte superior de qualquer arquivo Python no qual você deseja acessar o Armazenamento do Azure programaticamente.

	from azure.storage.blob import BlobService

O código a seguir cria um objeto **Serviço de blob** usando o nome da conta de armazenamento e a chave da conta. Substitua 'Minha conta' e 'mykey' com a conta real e a chave.

	blob_service = BlobService(account_name='myaccount', account_key='mykey')

[AZURE.INCLUDE [armazenamento-contêiner-nomeando-regras-include](../../includes/storage-container-naming-rules-include.md)]

No exemplo de código a seguir, é possível usar uma **BlobService** objeto para criar o contêiner, se ele não existir.

	blob_service.create_container('mycontainer')

Por padrão, o novo contêiner é particular; portanto, você deve especificar sua chave de acesso de armazenamento (como anteriormente) para baixar blobs desse contêiner. Se desejar disponibilizar os arquivos dentro do contêiner para todas as pessoas, você pode criar o contêiner e passar o nível de acesso público usando o código a seguir.

	blob_service.create_container('mycontainer', x_ms_blob_public_access='container')

Como alternativa, você pode modificar um contêiner após você tê-lo criado usando o código a seguir.

	blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

Após essa alteração, qualquer pessoa na Internet pode ver blobs em um contêiner público, mas apenas você pode modificar ou excluí-los.

## Carregar um blob em um contêiner

Para carregar dados em um blob, use os métodos **put\_block\_blob\_from\_path**, **put\_block\_blob\_from\_file**, **put\_block\_blob\_from\_bytes** ou **put\_block\_blob\_from\_text**. Esses são métodos de alto nível que realizam a separação por partes necessária quando o tamanho do arquivo excede 64 MB.

**put\_block\_blob\_from\_path** carrega o conteúdo de um arquivo a partir do caminho especificado, **put\_block\_blob\_from\_file** carrega o conteúdo de um arquivo/fluxo já aberto. **put\_block\_blob\_from\_bytes** carrega uma matriz de bytes, e **put\_block\_blob\_from\_text** carrega o valor de texto especificado usando a codificação especificada (UTF-8 é o padrão).

O exemplo a seguir carrega o conteúdo do arquivo **sunset.png** no blob **myblob**.

	blob_service.put_block_blob_from_path(
        'mycontainer',
        'myblob',
        'sunset.png',
        x_ms_blob_content_type='image/png'
    )

## Listar os blobs em um contêiner

Para listar os blobs em um contêiner, use o método **list\_blobs**. Cada chamada para **list\_blobs** retornará um segmento de resultados. Para obter todos os resultados, verifique o **next\_marker** dos resultados e chame **list\_blobs** novamente conforme necessário. O código a seguir produz o **nome** de cada blob em um contêiner para o console.

	blobs = []
	marker = None
	while True:
		batch = blob_service.list_blobs('mycontainer', marker=marker)
		blobs.extend(batch)
		if not batch.next_marker:
			break
		marker = batch.next_marker
	for blob in blobs:
		print(blob.name)

## Baixar blobs

Cada segmento de resultados pode conter um número variável de blobs até no máximo 5000. Se **next\_marker** existir para um segmento específico, poderá haver mais blobs no contêiner.

Para baixar dados de um blob, use **get\_blob\_to\_path**, **get\_blob\_to\_file**, **get\_blob\_to\_bytes** ou **get\_blob\_to\_text**. Esses são métodos de alto nível que realizam a separação por partes necessária quando o tamanho do arquivo excede 64 MB.

O exemplo a seguir demonstra o uso de **get\_blob\_to\_path** para baixar o conteúdo do blob **myblob** e armazená-lo no arquivo **out-sunset.png**.

	blob_service.get_blob_to_path('mycontainer', 'myblob', 'out-sunset.png')

## Excluir um blob

Finalmente, para excluir um blob, chame **delete\_blob**.

	blob_service.delete_blob('mycontainer', 'myblob')

## Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de Blob, siga estes links para saber mais sobre tarefas de armazenamento mais complexas.

- Visite o [Blog da Equipe de Armazenamento do Azure][]
- [Transferir dados com o utilitário de linha de comando AzCopy](storage-use-azcopy)

Para obter mais informações, veja também o [Centro de Desenvolvedores do Python](/develop/python/).

[Blog da Equipe de Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[pacote do Python Azure]: https://pypi.python.org/pypi/azure
[pacote de armazenamento do Python Azure]: https://pypi.python.org/pypi/azure-storage

<!---HONumber=AcomDC_1217_2015-->