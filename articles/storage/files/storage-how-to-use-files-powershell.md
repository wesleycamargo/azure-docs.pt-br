---
title: Início Rápido para gerenciar compartilhamentos de arquivos do Azure com o Azure PowerShell
description: Use esse início rápido para saber como gerenciar compartilhamentos de arquivos do Azure usando o Azure PowerShell.
services: storage
author: wmgries
ms.service: storage
ms.topic: quickstart
ms.date: 10/18/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 16f557d48f8056d438d55fdd066395e7e36ed8a5
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945477"
---
# <a name="quickstart-create-and-manage-an-azure-file-share-with-azure-powershell"></a>Início Rápido: criar e gerenciar um compartilhamento de arquivos do Azure com o Azure PowerShell 
Este guia oferece orientações básicas sobre como trabalhar com [compartilhamentos de arquivos do Azure](storage-files-introduction.md) usando o PowerShell. Os compartilhamentos de arquivos do Azure são iguais a outros compartilhamentos de arquivos, mas são armazenados na nuvem e compatíveis com a plataforma do Azure. Os compartilhamentos de arquivos do Azure oferecem suporte ao protocolo SMB padrão do setor e habilita o compartilhamento de arquivos entre vários computadores, aplicativos e instâncias. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se você quiser instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 5.1.1 ou posterior. Para descobrir qual versão do módulo do Azure PowerShell está em execução, execute `Get-Module -ListAvailable AzureRM`. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Login-AzureRmAccount` para fazer logon na conta do Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Se você ainda não tiver um grupo de recursos do Azure, crie um novo com o cmdlet [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

O exemplo abaixo cria um grupo de recursos chamado *myResourceGroup* na região Leste dos EUA:

```azurepowershell-interactive
New-AzureRmResourceGroup `
    -Name myResourceGroup `
    -Location EastUS
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
Uma conta de armazenamento é um pool compartilhado de armazenamento que você pode usar para implantar compartilhamentos de arquivos do Azure ou outros recursos de armazenamento, como blobs ou filas. Uma conta de armazenamento pode conter um número ilimitado de compartilhamentos, e um compartilhamento pode conter um número ilimitado de arquivos, até os limites de capacidade da conta de armazenamento.

Este exemplo cria uma conta de armazenamento usando o cmdlet [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount). A conta de armazenamento é denominada *mystorageaccount<random number>*, e uma referência a essa conta de armazenamento na variável é armazenada na variável **$storageAcct**. Os nomes de conta de armazenamento devem ser exclusivo e, portanto, use `Get-Random` para acrescentar um número ao nome e torná-lo exclusivo. 

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

Os nomes dos compartilhamentos precisam ter todos letras minúsculas, números e hifens, mas não podem começar com um hífen. Para obter detalhes completos sobre como nomear arquivos e compartilhamentos de arquivos, confira [Nomenclatura e referência de compartilhamentos, diretórios, arquivos e metadados](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Use o compartilhamento de arquivos do Azure
Os Arquivos do Azure fornecem dois métodos para trabalhar com arquivos e pastas dentro do seu compartilhamento de arquivos do Azure: o padrão do setor [protocolo SMB (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) e o [protocolo REST de arquivo](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

Para montar um compartilhamento de arquivos com SMB, consulte o documento a seguir com base em seu sistema operacional:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Usar um compartilhamento de arquivos do Azure com o protocolo REST de arquivo 
É possível trabalhar diretamente com o protocolo REST de arquivo (ou seja, modelar manualmente as chamadas REST HTTP por conta própria), mas a maneira mais comum de usar o protocolo REST de arquivo é usar o módulo AzureRM PowerShell, a [CLI do Azure](storage-how-to-use-files-cli.md) ou um SDK de armazenamento do Azure, que fornecem um bom wrapper do protocolo REST de arquivo na linguagem de script/programação de sua escolha.  

Na maioria dos casos, você usará o compartilhamento de arquivos do Azure em vez do protocolo SMB, pois isso permite usar os aplicativos e as ferramentas existentes que você espera poder usar, mas há várias razões por que é vantajoso usar a API REST de arquivo em vez de SMB, a saber:

- Você está buscando seu compartilhamento de arquivos no Cloud Shell do PowerShell (o que não pode montar os compartilhamentos de arquivos via SMB).
- Você precisa executar um script ou aplicativo de um cliente que não consegue montar um compartilhamento de SMB, como clientes locais que não têm a porta 445 desbloqueada.
- Você está aproveitando as vantagens dos recursos sem servidor, como o [Azure Functions](../../azure-functions/functions-overview.md). 

Os exemplos a seguir mostram como usar o módulo do AzureRM PowerShell para manipular o compartilhamento de arquivos do Azure com o protocolo REST de arquivo. 

#### <a name="create-directory"></a>Criar diretório
Para criar um novo diretório chamado *myDirectory* na raiz do seu compartilhamento de arquivos do Azure, use o cmdlet [New-AzureStorageDirectory](/powershell/module/azurerm.storage/new-azurestoragedirectory).

```azurepowershell-interactive
New-AzureStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Path "myDirectory"
```

#### <a name="upload-a-file"></a>Carregar um arquivo
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

#### <a name="download-a-file"></a>Baixar um arquivo
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

#### <a name="copy-files"></a>Copiar arquivos
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

> [!div class="nextstepaction"]
> [O que Arquivos do Azure?](storage-files-introduction.md)