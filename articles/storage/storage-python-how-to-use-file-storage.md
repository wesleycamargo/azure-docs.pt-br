---
title: Como usar o Armazenamento de Arquivos do Azure do Python | Microsoft Docs
description: Saiba como usar o Armazenamento de Arquivos do Azure do Python para carregar, listar, baixar e excluir arquivos.
services: storage
documentationcenter: python
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 12/08/2016
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: fefebeae665ccd14f15b0197241b30d33830fd09
ms.openlocfilehash: 9973da827ea5a9311904d7d6d4c22d59b5e2d0ce


---
# <a name="how-to-use-azure-file-storage-from-python"></a>Como usar o Armazenamento de Arquivos do Azure do Python
[!INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../includes/storage-try-azure-tools-files.md)]

## <a name="overview"></a>Visão geral
Este artigo mostra como executar cenários comuns usando o Armazenamento de Arquivos. Os exemplos são escritos em Python e usam o [Microsoft Azure Storage SDK for Python](SDK do Armazenamento do Microsoft Azure para Python). Os cenários abordados incluem carregamento, listagem, download e exclusão de arquivos.

[!INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-share"></a>Criar um compartilhamento
O objeto **FileService** permite que você trabalhe com compartilhamentos, diretórios e arquivos. O código a seguir cria um objeto **FileService** . Adicione o seguinte próximo à parte superior de qualquer arquivo Python no qual você deseja acessar o Armazenamento do Azure programaticamente.

```python
from azure.storage.file import FileService
```

O código a seguir cria um objeto **FileService** usando o nome da conta de armazenamento e a chave de conta.  Substitua “myaccount” e “mykey” pelo nome da sua conta e sua chave.

```python
file_service = **FileService** (account_name='myaccount', account_key='mykey')
```

No exemplo de código a seguir, será possível usar um objeto **FileService** para criar o compartilhamento, se ele não existir.

```python
file_service.create_share('myshare')
```

## <a name="upload-a-file-into-a-share"></a>Carregar um arquivo para um compartilhamento
O compartilhamento do armazenamento de arquivos do Azure contém, pelo menos, um diretório raiz onde os arquivos podem residir. Nesta seção, você aprenderá a carregar um arquivo do armazenamento local para o diretório raiz de um compartilhamento.

Para criar um arquivo e carregar dados, use os métodos **create\_file\_from\_path**, **create\_file\_from\_stream**, **create\_file\_from\_bytes** ou **create\_file\_from\_text**. Esses são métodos de alto nível que realizam a separação por partes necessária quando o tamanho do arquivo excede 64 MB.

**create\_file\_from\_path** carrega o conteúdo de um arquivo do caminho especificado e **create\_file\_from\_stream** carrega o conteúdo de um arquivo/fluxo já aberto. **create\_file\_from\_bytes** carrega uma matriz de bytes e **create\_file\_from\_text** carrega o valor do texto especificado usando a codificação especificada (padronizada para UTF-8).

O exemplo a seguir carrega o conteúdo do arquivo **sunset.png** no arquivo **myfile**.

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None, # We want to create this blob in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

## <a name="how-to-create-a-directory"></a>Coco criar um diretório
Você também pode organizar o armazenamento colocando arquivos em subdiretórios em vez de manter todos eles no diretório raiz. O serviço do Armazenamento de Arquivos do Azure permite que você crie tantos diretórios quanto a sua conta permitir. O código a seguir criará um subdiretório chamado **sampledir** no diretório raiz.

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="how-to-list-files-and-directories-in-a-share"></a>Como listar arquivos e diretórios em um compartilhamento
Para listar os arquivos e diretórios em um compartilhamento, use o método **list\_directories\_and\_files**. Esse método retorna um gerador. O código a seguir produz o **nome** de cada arquivo e diretório em um compartilhamento para o console.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="download-files"></a>Baixar arquivos
Para baixar dados de um arquivo, use **get\_file\_to\_path**, **get\_file\_to\_stream**, **get\_file\_to\_bytes** ou **get\_file\_to\_text**. Esses são métodos de alto nível que realizam a separação por partes necessária quando o tamanho do arquivo excede 64 MB.

O exemplo a seguir demonstra como usar **get\_file\_to\_path** para baixar o conteúdo do arquivo **myfile** e armazená-lo no arquivo **out-sunset.png**.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>Excluir um arquivo
Por fim, para excluir um arquivo, chame **delete_file**.

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu os conceitos básicos do armazenamento de Arquivos, siga estes links para saber mais.

* [Centro de desenvolvedores do Python](/develop/python/)
* [API REST de serviços de armazenamento do Azure](http://msdn.microsoft.com/library/azure/dd179355)
* [Blog da equipe de Armazenamento do Azure]
* [Microsoft Azure Storage SDK for Python]

[Blog da equipe de Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK for Python]: https://github.com/Azure/azure-storage-python



<!--HONumber=Nov16_HO3-->


