---
title: Gerenciando compartilhamentos de arquivos com o Azure PowerShell
description: Saiba como gerenciar compartilhamentos de arquivos do Azure usando o Azure PowerShell.
services: storage
documentationcenter: ''
author: wmgries
manager: aungoo
editor: tamram
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/26/2018
ms.author: wgries
ms.openlocfilehash: 8fcc654b7f5068037ec0818e19d8dff09bc07537
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38635081"
---
# <a name="managing-azure-file-shares-with-azure-powershell"></a>Gerenciando compartilhamentos de arquivos com o Azure PowerShell 
[Arquivos do Azure](storage-files-introduction.md) é o sistema de arquivos de nuvem de fácil acesso da Microsoft. Os compartilhamentos de arquivos do Azure podem ser montados no Windows, no Linux e no macOS. Este guia percorre os fundamentos de trabalhar com compartilhamentos de arquivos do Azure usando o PowerShell. Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar um grupo de recursos e uma conta de armazenamento
> * Criar um compartilhamento de arquivos do Azure 
> * Criar um diretório
> * Carregar um arquivo 
> * Baixar um arquivo
> * Criar e usar um instantâneo de compartilhamento

Se você não tiver uma assinatura do Azure, poderá criar uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se você quiser instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 5.1.1 ou posterior. Para descobrir qual versão do módulo do Azure PowerShell está em execução, execute `Get-Module -ListAvailable AzureRM`. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzureRmAccount` para criar uma conexão com o Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Se você ainda não tiver um grupo de recursos do Azure, crie um novo com o cmdlet [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

O exemplo abaixo cria um grupo de recursos chamado *myResourceGroup* na região Leste dos EUA:

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
Uma conta de armazenamento é um pool compartilhado de armazenamento que você pode usar para implantar compartilhamentos de arquivos do Azure ou outros recursos de armazenamento, como blobs ou filas. Uma conta de armazenamento pode conter um número ilimitado de compartilhamentos, e um compartilhamento pode conter um número ilimitado de arquivos, até os limites de capacidade da conta de armazenamento.

Este exemplo cria uma conta de armazenamento denominada `mystorageacct<random number>` e coloca uma referência a essa conta de armazenamento na variável **$storageAcct**. Os nomes de conta de armazenamento devem ser exclusivo e, portanto, use `Get-Random` para acrescentar um número ao nome e torná-lo exclusivo. 

```azurepowershell-interactive 
$storageAcct = New-AzureRmStorageAccount `
                  -ResourceGroupName "myResourceGroup" `
                  -Name "mystorageacct$(Get-Random)" `
                  -Location eastus `
                  -SkuName Standard_LRS 
```

## <a name="create-an-azure-file-share"></a>Criar um compartilhamento de arquivos do Azure
Agora você pode criar seu primeiro compartilhamento de arquivos do Azure. Você pode criar um compartilhamento de arquivos usando o cmdlet [New-AzureStorageShare](/powershell/module/azurerm.storage/new-azurestorageshare). Esse exemplo cria um compartilhamento chamado `myshare`.

```azurepowershell-interactive
New-AzureStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

> [!Important]  
> Os nomes de compartilhamento precisam ter somente letras em minúsculas, números e hifens, mas não podem começar com um hífen. Para obter detalhes completos sobre como nomear arquivos e compartilhamentos de arquivos, confira [Nomenclatura e referência de compartilhamentos, diretórios, arquivos e metadados](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="work-with-the-contents-of-the-azure-file-share"></a>Trabalhar com o conteúdo do compartilhamento de arquivos do Azure
Agora que você criou um compartilhamento de arquivos do Azure, pode montar o compartilhamento de arquivos com SMB no [Windows](storage-how-to-use-files-windows.md), no [Linux](storage-how-to-use-files-linux.md) ou no [macOS](storage-how-to-use-files-mac.md). Como alternativa, você pode trabalhar com o compartilhamento de arquivos do Azure com o módulo do Azure PowerShell. Isso é vantajoso em vez de montar o compartilhamento de arquivos com SMB porque todas as solicitações feitas com o PowerShell são feitas com a API REST do arquivo, permitindo a você criar, modificar e excluir arquivos e diretórios em seu compartilhamento de arquivos de:

- Cloud Shell do PowerShell (que não pode montar os compartilhamentos de arquivos via SMB).
- Clientes que não podem montar compartilhamentos SMB, como clientes locais que não têm a porta 445 desbloqueada.
- Cenários sem servidor, como no [Azure Functions](../../azure-functions/functions-overview.md). 


### <a name="create-directory"></a>Criar diretório
Para criar um novo diretório chamado *myDirectory* na raiz do seu compartilhamento de arquivos do Azure, use o cmdlet [New-AzureStorageDirectory](/powershell/module/azurerm.storage/new-azurestoragedirectory).


```azurepowershell-interactive
New-AzureStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Path "myDirectory"
```

### <a name="upload-a-file"></a>Carregar um arquivo
Para demonstrar como carregar um arquivo usando o cmdlet [Set-AzureStorageFileContent](/powershell/module/azure.storage/set-azurestoragefilecontent), primeiro precisamos criar um arquivo dentro da unidade temporária do Cloud Shell do PowerShell para upload. 

Este exemplo coloca a data e a hora atuais em um novo arquivo na unidade temporária e o carrega no compartilhamento de arquivos.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
Get-Date | Out-File -FilePath "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzureStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Source "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

Se você estiver executando o PowerShell localmente, substitua `C:\Users\ContainerAdministrator\CloudDrive\` por um caminho existente em seu computador.

Depois de carregar o arquivo, você pode usar o cmdlet [Get-AzureStorageFile](/powershell/module/Azure.Storage/Get-AzureStorageFile) para verificar se o arquivo foi carregado no compartilhamento de arquivos do Azure. 

```azurepowershell-interactive
Get-AzureStorageFile -Context $storageAcct.Context -ShareName "myshare" -Path "myDirectory" 
```

### <a name="download-a-file"></a>Baixar um arquivo
Você pode usar o cmdlet [Get-AzureStorageFileContent](/powershell/module/azure.storage/get-azurestoragefilecontent) para baixar uma cópia do arquivo que você acabou de carregar na unidade temporária do Cloud Shell.

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item 
    `-Path "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt" `
     -Force `
     -ErrorAction SilentlyContinue

Get-AzureStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt" ` 
    -Destination "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt"
```

Depois de baixar o arquivo, você pode usar o `Get-ChildItem` para ver se o arquivo foi baixado na unidade temporária do Cloud Shell do PowerShell.

```azurepowershell-interactive
Get-ChildItem -Path "C:\Users\ContainerAdministrator\CloudDrive"
``` 

### <a name="copy-files"></a>Copiar arquivos
Uma tarefa comum é copiar arquivos de um compartilhamento de arquivos para outro ou de/para um contêiner do Armazenamento de Blobs do Azure. Para demonstrar essa funcionalidade, você pode criar um novo compartilhamento e copiar o arquivo recém-carregado para esse novo compartilhamento usando o cmdlet [Start-AzureStorageFileCopy](/powershell/module/azure.storage/start-azurestoragefilecopy). 

```azurepowershell-interactive
New-AzureStorageShare `
    -Name "myshare2" `
    -Context $storageAcct.Context
  
New-AzureStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare2" `
   -Path "myDirectory2"

Start-AzureStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName "myshare" `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName "myshare2" `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

Agora, se você listar os arquivos no novo compartilhamento, deverá ver o arquivo copiado.

```azurepowershell-interactive
Get-AzureStorageFile -Context $storageAcct.Context -ShareName "myshare2" -Path "myDirectory2" 
```

Embora o cmdlet `Start-AzureStorageFileCopy` seja conveniente para movimentações avulsas de arquivos entre compartilhamentos de arquivos do Azure e contêineres de armazenamento de Blobs do Azure, recomendamos AzCopy para movimentações maiores (em termos de quantidade ou tamanho de arquivos que estão sendo movidos). Saiba mais sobre [AzCopy para Windows](../common/storage-use-azcopy.md) e [AzCopy para Linux](../common/storage-use-azcopy-linux.md). O AzCopy deve ser instalado localmente; ele não está disponível no Cloud Shell. 

## <a name="create-and-modify-share-snapshots"></a>Criar e modificar instantâneos de compartilhamento
Outra tarefa útil que você pode fazer com um compartilhamento de arquivos do Azure é criar instantâneos de compartilhamento. Um instantâneo preserva um ponto no tempo para um compartilhamento de arquivos do Azure. Os instantâneos de compartilhamento são semelhantes às tecnologias de sistema operacional que você talvez já conheça, como:
- [VSS (Serviço de Cópias de Sombra de Volume)](https://docs.microsoft.com/en-us/windows/desktop/VSS/volume-shadow-copy-service-portal) para sistemas de arquivos Windows, como NTFS e ReFS
- Instantâneos do [LVM (Gerenciador de Volumes Lógicos)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) para sistemas Linux
- Instantâneos do [APFS (Sistema de arquivos da Apple)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) para macOS. 

Você pode criar um instantâneo de compartilhamento para um compartilhamento usando o método `Snapshot` no objeto do PowerShell para um compartilhamento de arquivo, que é recuperado com o cmdlet [Get-AzureStorageShare](/powershell/module/azure.storage/get-azurestorageshare). 

```azurepowershell-interactive
$share = Get-AzureStorageShare -Context $storageAcct.Context -Name "myshare"
$snapshot = $share.Snapshot()
```

### <a name="browse-share-snapshots"></a>Procurar instantâneos de compartilhamento
Você pode procurar o conteúdo do instantâneo de compartilhamento passando a referência do instantâneo (`$snapshot`) para o parâmetro `-Share` do cmdlet `Get-AzureStorageFile`.

```azurepowershell-interactive
Get-AzureStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>Listar instantâneos de compartilhamento
Você pode ver a lista de instantâneos tirados para seu compartilhamento com o comando a seguir.

```azurepowershell-interactive
Get-AzureStorageShare -Context $storageAcct.Context | Where-Object { $_.Name -eq "myshare" -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>Restaurar de um instantâneo de compartilhamento
Você pode restaurar um arquivo usando o comando `Start-AzureStorageFileCopy` usado anteriormente. Para fins deste início rápido, iremos primeiro excluir nosso arquivo `SampleUpload.txt` carregado anteriormente para que possa ser restaurado do instantâneo.

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzureStorageFile `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt"

# Restore SampleUpload.txt from the share snapshot
Start-AzureStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName "myshare" `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Excluir um instantâneo de compartilhamento
Você pode excluir um instantâneo de compartilhamento usando o cmdlet [Remove-AzureStorageShare](/powershell/module/azure.storage/remove-azurestorageshare), com a variável que contém a referência `$snapshot` para o parâmetro `-Share`.

```azurepowershell-interactive
Remove-AzureStorageShare -Share $snapshot
```

## <a name="clean-up-resources"></a>Limpar recursos
Quando tiver concluído, você poderá usar o cmdlet [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos e todos os recursos relacionados. 

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

Como alternativa, você pode remover recursos individualmente:

- Para remover os compartilhamentos de arquivos do Azure que criamos para este início rápido.
```azurepowershell-interactive
Get-AzureStorageShare -Context $storageAcct.Context | Where-Object { $_.IsSnapshot -eq $false } | ForEach-Object { 
    Remove-AzureStorageShare -Context $storageAcct.Context -Name $_.Name
}
```

- Para remover a própria conta de armazenamento (isso removerá implicitamente os compartilhamentos de arquivos do Azure que criamos, bem como qualquer outro recurso de armazenamento criado, como um contêiner do Armazenamento de Blobs do Azure).
```azurepowershell-interactive
Remove-AzureRmStorageAccount -ResourceGroupName $storageAcct.ResourceGroupName -Name $storageAcct.StorageAccountName
```

## <a name="next-steps"></a>Próximas etapas
- [Gerenciando compartilhamentos de arquivos com o Portal do Azure](storage-how-to-use-files-portal.md)
- [Gerenciando compartilhamentos de arquivos com a CLI do Azure](storage-how-to-use-files-cli.md)
- [Gerenciando compartilhamentos de arquivos com o Gerenciador de Armazenamento](storage-how-to-use-files-storage-explorer.md)
- [Planejando uma implantação de Arquivos do Azure](storage-files-planning.md)
