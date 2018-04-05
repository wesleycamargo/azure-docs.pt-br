---
title: Gerenciando compartilhamentos de arquivos do Azure usando a CLI do Azure
description: Saiba como usar a CLI do Azure para gerenciar os Arquivos do Azure.
services: storage
documentationcenter: na
author: wmgries
manager: jeconnoc
editor: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/26/2018
ms.author: wgries
ms.openlocfilehash: 066a43b553be18a5a0bc889fff441824df301b98
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2018
---
# <a name="managing-azure-file-shares-using-the-azure-cli"></a>Gerenciando compartilhamentos de arquivos do Azure usando a CLI do Azure
[Arquivos do Azure](storage-files-introduction.md) é o sistema de arquivos de nuvem fácil de usar da Microsoft. Os compartilhamentos de arquivos do Azure podem ser montados no Windows, no Linux e no macOS. Este guia percorre os fundamentos de trabalhar com compartilhamentos de arquivos do Azure usando a CLI do Azure. Saiba como: 

> [!div class="checklist"]
> * Criar um grupo de recursos e uma conta de armazenamento
> * Criar um compartilhamento de arquivos do Azure 
> * Criar um diretório
> * Carregar um arquivo 
> * Baixar um arquivo
> * Criar e usar um instantâneo de compartilhamento

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Caso opte por instalar e usar a CLI do Azure localmente, este guia exigirá que seja executada a CLI do Azure versão 2.0.4 ou posterior. Execute **az --version** para descobrir a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli). 

Por padrão, os comandos da CLI do Azure retornam JSON (JavaScript Object Notation), que é a maneira de enviar e receber mensagens de APIs REST na prática. Para facilitar o trabalho com as respostas em JSON, alguns dos exemplos neste guia usam o parâmetro de consulta nos comandos da CLI do Azure. Esse parâmetro usa a [linguagem de consulta JMESPath](http://jmespath.org/) para analisar o JSON. Você pode aprender mais sobre como manipular os resultados dos comandos da CLI do Azure seguindo o [Tutorial de JMESPath](http://jmespath.org/tutorial.html).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Se você ainda não tiver um grupo de recursos do Azure, poderá criar um com o comando [az group create](/cli/azure/group#create). 

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *Leste dos EUA*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
Uma conta de armazenamento é um pool compartilhado de armazenamento no qual você pode implantar o compartilhamento de arquivos do Azure ou outros recursos de armazenamento, como blobs ou filas. Uma conta de armazenamento pode conter um número ilimitado de compartilhamentos de arquivos, e um compartilhamento pode conter um número ilimitado de arquivos, até os limites de capacidade da conta de armazenamento.

Este exemplo cria uma conta de armazenamento denominada `mystorageaccount<random number>` usando o comando [az storage account create](/cli/azure/storage/account#create) e coloca o nome da conta de armazenamento na variável `$STORAGEACCT`. Os nomes de conta de armazenamento devem ser exclusivos. Use `$RANDOM` para acrescentar um número ao final do nome da conta de armazenamento e torná-lo exclusivo. 

```azurecli-interactive 
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

### <a name="get-the-storage-account-key"></a>Obter a chave da conta de armazenamento
Chaves de conta de armazenamento são usadas para controlar o acesso aos recursos em uma conta de armazenamento. Elas são criadas automaticamente quando você cria uma conta de armazenamento. Você pode obter as chaves da conta de armazenamento usando o comando [az storage account keys list](/cli/azure/storage/account/keys#list). 

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>Criar um compartilhamento de arquivos do Azure
Agora você pode criar seu primeiro compartilhamento de arquivos do Azure. Você pode criar compartilhamentos de arquivos usando o comando [az storage share create](/cli/azure/storage/share#create). Este exemplo cria um compartilhamento de arquivos do Azure chamado `myshare`. 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

> [!Important]  
> Os nomes de compartilhamento precisam ter somente letras em minúsculas, números e hifens, mas não podem começar com um hífen. Para obter detalhes completos sobre como nomear arquivos e compartilhamentos de arquivos, confira [Nomenclatura e referência de compartilhamentos, diretórios, arquivos e metadados](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="manipulating-the-contents-of-the-azure-file-share"></a>Manipulando o conteúdo do compartilhamento de arquivos do Azure
Agora que você criou um compartilhamento de arquivos do Azure, pode montar o compartilhamento de arquivos com SMB no [Windows](storage-how-to-use-files-windows.md), no [Linux](storage-how-to-use-files-linux.md) ou no [macOS](storage-how-to-use-files-mac.md). Como alternativa, você pode manipular o compartilhamento de arquivos do Azure com a CLI do Azure. Isso é vantajoso em vez de montar o compartilhamento de arquivos com SMB porque todas as solicitações feitas com a CLI do Azure são feitas com a API REST do arquivo, permitindo a você criar, modificar e excluir arquivos e diretórios em seu compartilhamento de arquivos de:

- O Bash Cloud Shell (que não pode montar os compartilhamentos de arquivos via SMB).
- Clientes que não podem montar compartilhamentos SMB, como clientes locais que não têm a porta 445 desbloqueada.
- Cenários sem servidor, como no [Azure Functions](../../azure-functions/functions-overview.md). 

### <a name="create-directory"></a>Criar diretório
Para criar um novo diretório chamado *myDirectory* na raiz do seu compartilhamento de arquivos do Azure, use o comando [`az storage directory create`](/cli/azure/storage/directory#az_storage_directory_create).

```azurecli-interactive
az storage directory create \
   --account-name $STORAGEACCT \
   --account-key $STORAGEKEY \
   --share-name "myshare" \
   --name "myDirectory" 
```

### <a name="upload-a-file"></a>Carregar um arquivo
Para demonstrar como carregar um arquivo usando o comando [`az storage file upload`](/cli/azure/storage/file#az_storage_file_upload), primeiro precisamos criar um arquivo dentro da unidade temporária do Cloud Shell da CLI do Azure para carregar. No exemplo a seguir, criamos e carregamos o arquivo.

```azurecli-interactive
date > ~/clouddrive/SampleUpload.txt

az storage file upload \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --source "~/clouddrive/SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Se você estiver executando a CLI do Azure localmente, substitua `~/clouddrive` por um caminho existente em seu computador.

Depois de carregar o arquivo, você pode usar o comando [`az storage file list`](/cli/azure/storage/file#az_storage_file_list) para verificar se o arquivo foi carregado no compartilhamento de arquivos do Azure.

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>Baixar um arquivo
Você pode usar o comando [`az storage file download`](/cli/azure/storage/file#az_storage_file_download) para baixar uma cópia do arquivo que você carregou na unidade temporária do Cloud Shell.

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before
rm -rf ~/clouddrive/SampleDownload.txt

az storage file download \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt" \
    --dest "~/clouddrive/SampleDownload.txt"
```

### <a name="copy-files"></a>Copiar arquivos
Uma tarefa comum é copiar arquivos de um compartilhamento de arquivos para outro ou de/para um contêiner do Armazenamento de Blobs do Azure. Para demonstrar essa funcionalidade, você pode criar um novo compartilhamento e copiar o arquivo carregado para esse novo compartilhamento usando o comando [az storage file copy](/cli/azure/storage/file/copy). 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare2"

az storage directory create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --name "myDirectory2"

az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-share "myshare" \
    --source-path "myDirectory/SampleUpload.txt" \
    --destination-share "myshare2" \
    --destination-path "myDirectory2/SampleCopy.txt"
```

Agora, se você listar os arquivos no novo compartilhamento, deverá ver o arquivo copiado.

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --output table
```

Embora o comando `az storage file copy start` seja conveniente para movimentações avulsas de arquivos entre compartilhamentos de arquivos do Azure e contêineres de armazenamento de Blobs do Azure, recomendamos AzCopy para movimentações maiores (em termos de quantidade ou tamanho de arquivos que estão sendo movidos). Saiba mais sobre [AzCopy para Linux](../common/storage-use-azcopy-linux.md) e [AzCopy para Windows](../common/storage-use-azcopy.md). O AzCopy deve ser instalado localmente; ele não está disponível no Cloud Shell 

## <a name="create-and-modify-share-snapshots"></a>Criar e modificar instantâneos de compartilhamento
Outra tarefa útil que você pode fazer com um compartilhamento de arquivos do Azure é criar instantâneos de compartilhamento. Um instantâneo preserva um ponto no tempo para um compartilhamento de arquivos do Azure. Os instantâneos de compartilhamento são semelhantes às tecnologias de sistema operacional que você talvez já conheça, como:
- Instantâneos do [LVM (Gerenciador de Volumes Lógicos)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) para sistemas Linux
- Instantâneos do [APFS (Sistema de arquivos da Apple)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) para macOS. 
- [VSS (Serviço de Cópias de Sombra de Volume)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636) para sistemas de arquivos Windows, como NTFS e ReFS

Você pode criar um instantâneo de compartilhamento usando o comando [`az storage share snapshot`](/cli/azure/storage/share#az_storage_share_snapshot).

```azurecli-interactive
SNAPSHOT=$(az storage share snapshot \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>Procurar conteúdo do instantâneo de compartilhamento
Você pode procurar o conteúdo de um instantâneo de compartilhamento passando o carimbo de data/hora do instantâneo de compartilhamento capturado na variável `$SNAPSHOT` para o comando `az storage file list`.

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --snapshot $SNAPSHOT \
    --output table
```

### <a name="list-share-snapshots"></a>Listar instantâneos de compartilhamento
Você pode ver a lista de instantâneos tirados para seu compartilhamento com o comando a seguir.

```azurecli-interactive
az storage share list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --include-snapshot \
    --query "[? name=='myshare' && snapshot!=null]" | tr -d '"'
```

### <a name="restore-from-a-share-snapshot"></a>Restaurar de um instantâneo de compartilhamento
Você pode restaurar um arquivo usando o comando `az storage file copy start` usado anteriormente. Primeiro, vamos excluir nosso arquivo `SampleUpload.txt` carregado para podermos restaurá-lo do instantâneo.

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt"

# Build the source URI for snapshot restore
URI=$(az storage account show \
    --resource-group "myResourceGroup" \
    --name $STORAGEACCT \
    --query "primaryEndpoints.file" | tr -d '"')

URI=$URI"myshare/myDirectory/SampleUpload.txt?sharesnapshot="$SNAPSHOT

# Restore SampleUpload.txt from the share snapshot
az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-uri $URI \
    --destination-share "myshare" \
    --destination-path "myDirectory/SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Excluir um instantâneo de compartilhamento
Você pode excluir um instantâneo de compartilhamento usando o comando [`az storage share delete`](/cli/azure/storage/share#az_storage_share_delete), com a variável que contém a referência `$SNAPSHOT` para o parâmetro `--snapshot`.

```azurecli-interactive
az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --snapshot $SNAPSHOT
```

## <a name="clean-up-resources"></a>Limpar recursos
Quando concluir, você poderá usar o comando [`az group delete`](/cli/azure/group#delete) para remover o grupo de recursos e todos os recursos relacionados. 

```azurecli-interactive 
az group delete --name "myResourceGroup"
```

Como alternativa, você pode remover recursos individualmente:
- Para remover os compartilhamentos de arquivos do Azure que criamos para este início rápido.

```azurecli-interactive
az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --delete-snapshots include

az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare2" \
    --delete-snapshots include
```

- Para remover a própria conta de armazenamento (isso removerá implicitamente os compartilhamentos de arquivos do Azure que criamos, bem como qualquer outro recurso de armazenamento criado, como um contêiner do Armazenamento de Blobs do Azure).

```azurecli-interactive
az storage account delete \
    --resource-group "myResourceGroup" \
    --name $STORAGEACCT \
    --yes
```

## <a name="next-steps"></a>Próximas etapas
- [Gerenciando compartilhamentos de arquivos com o Portal do Azure](storage-how-to-use-files-portal.md)
- [Gerenciar compartilhamentos de arquivos com o Azure PowerShell](storage-how-to-use-files-powershell.md)
- [Gerenciando compartilhamentos de arquivos com o Gerenciador de Armazenamento](storage-how-to-use-files-storage-explorer.md)
- [Planejando uma implantação de Arquivos do Azure](storage-files-planning.md)