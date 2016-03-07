<properties
	pageTitle="Como usar o Armazenamento de Arquivos do Azure do Python | Microsoft Azure"
	description="Saiba como usar o Armazenamento de Arquivos do Azure do Python para carregar, listar, baixar e excluir arquivos."
	services="storage"
	documentationCenter="python"
	authors="emgerner-msft"
	manager="wpickett"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="emgerner"/>

# Como usar o Armazenamento de Arquivos do Azure do Python

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]

## Visão geral

Este artigo mostra como executar cenários comuns usando o Armazenamento de Arquivos. Os exemplos são escritos em Python e usam o [SDK do Armazenamento do Microsoft Azure para Python]. Os cenários abordados incluem carregamento, listagem, download e exclusão de arquivos.

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Criar um compartilhamento

O objeto **FileService** permite que você trabalhe com compartilhamentos, diretórios e arquivos. O código a seguir cria um objeto **FileService**. Adicione o seguinte próximo à parte superior de qualquer arquivo Python no qual você deseja acessar o Armazenamento do Azure programaticamente.

	from azure.storage.file import FileService

O código a seguir cria um objeto **FileService** usando o nome da conta de armazenamento e a chave de conta. Substitua “myaccount” e “mykey” pelo nome da sua conta e sua chave.

	file_service = **FileService** (account_name='myaccount', account_key='mykey')

No exemplo de código a seguir, será possível usar um objeto **FileService** para criar o compartilhamento, se ele não existir.

	file_service.create_share('myshare')

## Carregar um arquivo para um compartilhamento

O compartilhamento do armazenamento de arquivos do Azure contém, pelo menos, um diretório raiz onde os arquivos podem residir. Nesta seção, você aprenderá a carregar um arquivo do armazenamento local para o diretório raiz de um compartilhamento.

Para criar um arquivo e carregar dados, use os métodos **create\_file\_from\_path**, **create\_file\_from\_stream**, **create\_file\_from\_bytes** ou **create\_file\_from\_text**. Esses são métodos de alto nível que realizam a separação por partes necessária quando o tamanho do arquivo excede 64 MB.

**create\_file\_from\_path** carrega o conteúdo de um arquivo do caminho especificado, **create\_file\_from\_stream** carrega o conteúdo de um arquivo/fluxo já aberto. **create\_file\_from\_bytes** carrega uma matriz de bytes, e **create\_file\_from\_text** carrega o valor de texto especificado usando a codificação especificada (UTF-8 é o padrão).

O exemplo a seguir carrega o conteúdo do arquivo **sunset.png** para o arquivo **myblob**.

	from azure.storage.file import ContentSettings
	file_service.create_file_from_path(
        'myshare',
				None, # We want to create this blob in the root directory, so we specify None for the directory_name
        'myfile',
        'sunset.png',
        content_settings=ContentSettings(content_type='image/png'))

## Coco criar um diretório

Você também pode organizar o armazenamento colocando arquivos em subdiretórios em vez de manter todos eles no diretório raiz. O serviço do Armazenamento de Arquivos do Azure permite que você crie tantos diretórios quanto a sua conta permitir. O código a seguir criará um subdiretório chamado **sampledir** no diretório raiz.

	file_service.create_directory('myshare', 'sampledir')

## Como listar arquivos e diretórios em um compartilhamento

Para listar os arquivos e diretórios em um compartilhamento, use o método **list\_directories\_and\_files**. Esse método retorna um gerador. O código a seguir produz o **nome** de cada arquivo e diretório em um compartilhamento para o console.

	generator = file_service.list_directories_and_files('myshare')
	for file_or_dir in generator:
		print(file_or_dir.name)

## Baixar arquivos

Para baixar dados de um arquivo, use **get\_file\_to\_path**, **get\_file\_to\_stream**, **get\_file\_to\_bytes** ou **get\_file\_to\_text**. Esses são métodos de alto nível que realizam a separação por partes necessária quando o tamanho do arquivo excede 64 MB.

O exemplo a seguir demonstra o uso de **get\_file\_to\_path** para baixar o conteúdo do arquivo **myfile** e armazená-lo no arquivo **out-sunset.png**.

	file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')

## Excluir um arquivo

Por fim, para excluir um arquivo e chame **delete\_file**.

	file_service.delete_file('myshare', None, 'myfile')

## Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de Arquivos, siga estes links para saber mais.

- [Centro de desenvolvedores do Python](/develop/python/)
- [API REST de serviços de armazenamento do Azure](http://msdn.microsoft.com/library/azure/dd179355)
- [Blog da equipe de Armazenamento do Azure]
- [SDK do Armazenamento do Microsoft Azure para Python]

[Blog da equipe de Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[SDK do Armazenamento do Microsoft Azure para Python]: https://github.com/Azure/azure-storage-python

<!---HONumber=AcomDC_0224_2016-->