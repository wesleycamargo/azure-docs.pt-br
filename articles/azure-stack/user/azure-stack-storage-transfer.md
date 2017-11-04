---
title: Ferramentas para o armazenamento do Azure pilha
description: "Saiba mais sobre os dados de armazenamento do Azure pilha ferramentas de transferência"
services: azure-stack
documentationcenter: 
author: xiaofmao
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: xiaofmao
ms.openlocfilehash: 9799498a11449a9ed496d0fdb40312603eda064e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tools-for-azure-stack-storage"></a>Ferramentas para o armazenamento do Azure de pilha

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

A pilha do Microsoft Azure fornece um conjunto de serviços de armazenamento de discos, blobs, tabelas, filas e funcionalidade de gerenciamento de conta. Se você deseja gerenciar ou mover dados para ou do armazenamento de pilha do Azure, você pode usar um conjunto de ferramentas do armazenamento do Azure. Este artigo fornece uma visão geral das ferramentas disponíveis.

A ferramenta que funciona melhor para você depende de seus requisitos:
* [AzCopy](#azcopy)

    Um utilitário de linha de comando específicas de armazenamento que você pode baixar para copiar dados de um objeto para outro dentro de sua conta de armazenamento, ou entre contas de armazenamento.

* [PowerShell do Azure](#azure-powershell)

    Um shell de linha de comando baseado em tarefa e linguagem de script criado especialmente para a administração do sistema.

* [CLI do Azure](#azure-cli)

    Uma ferramenta de software livre, multiplataforma que fornece um conjunto de comandos para trabalhar com as plataformas do Azure e a pilha do Azure.

* [Gerenciador de armazenamento do Microsoft (visualização)](#microsoft-azure-storage-explorer)

    Um aplicativo autônomo fácil de usar com uma interface do usuário.

Devido a diferenças de serviços de armazenamento entre o Azure e a pilha do Azure, pode haver alguns requisitos específicos para cada ferramenta descritos nas seções a seguir. Para obter uma comparação entre o armazenamento de pilha do Azure e armazenamento do Azure, consulte [Azure pilha de armazenamento: diferenças e considerações](azure-stack-acs-differences.md).


## <a name="azcopy"></a>AzCopy
AzCopy é um utilitário de linha de comando projetado para copiar dados para e de BLOBs do Microsoft Azure e armazenamento de tabela usando comandos simples com um desempenho ideal. Você pode copiar dados de um objeto para outro dentro de sua conta de armazenamento, ou entre contas de armazenamento. Há duas versões do AzCopy: AzCopy no Windows e AzCopy no Linux. A pilha do Azure suporta apenas a versão do Windows. 
 
### <a name="download-and-install-azcopy"></a>Baixar e instalar o AzCopy 
[Baixar](https://aka.ms/azcopyforazurestack) a versão do Windows com suporte do AzCopy para a pilha do Azure. Você pode instalar e usar AzCopy na pilha do Azure da mesma forma que o Azure. Para obter mais informações, consulte [transferir dados com o utilitário de linha de comando AzCopy](../../storage/common/storage-use-azcopy.md). 

### <a name="azcopy-command-examples-for-data-transfer"></a>Exemplos de comandos do AzCopy para transferência de dados
Os exemplos a seguir demonstram alguns cenários típicos para copiar dados para e de blobs do Azure pilha. Para obter mais informações, consulte [transferir dados com o utilitário de linha de comando AzCopy](../../storage/storage-use-azcopy.md). 
#### <a name="download-all-blobs-to-local-disk"></a>Baixar todos os blobs no disco local
```azcopy
AzCopy.exe /source:https://myaccount.blob.local.azurestack.external/mycontainer /dest:C:\myfolder /sourcekey:<key> /S
```
#### <a name="upload-single-file-to-virtual-directory"></a>Carregar arquivo único no diretório virtual 
```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.local.azurestack.external/mycontainer/vd /DestKey:key /Pattern:abc.txt
```
#### <a name="move-data-between-azure-and-azure-stack-storage"></a>Mover dados entre o Azure e a pilha de armazenamento do Azure 
Não há suporte para a transferência de dados assíncrona entre o armazenamento do Azure e a pilha do Azure. Você precisa especificar a transferência com o `/SyncCopy` opção. 
```azcopy 
Azcopy /Source:https://myaccount.blob.local.azurestack.external/mycontainer /Dest:https://myaccount2.blob.core.windows.net/mycontainer2 /SourceKey:AzSKey /DestKey:Azurekey /S /SyncCopy
```

### <a name="azcopy-known-issues"></a>Problemas conhecidos de Azcopy
* Qualquer operação AzCopy no armazenamento de arquivo não está disponível porque o arquivo de armazenamento ainda não está disponível na pilha do Azure.
* Não há suporte para a transferência de dados assíncrona entre o armazenamento do Azure e a pilha do Azure. Você pode especificar a transferência com o `/SyncCopy` opção para copiar os dados.
* Não há suporte para a versão do Linux do Azcopy para o armazenamento de pilha do Azure. 

## <a name="azure-powershell"></a>Azure PowerShell
PowerShell do Azure é um módulo que fornece cmdlets para gerenciar serviços no Azure e a pilha do Azure. Trata-se de uma linguagem de scripts e shell de linha de comando baseada em tarefas projetada especialmente para administração do sistema.

### <a name="install-and-configure-powershell-for-azure-stack"></a>Instalar e configurar o PowerShell para a pilha do Azure
Pilha do Azure compatíveis módulos do PowerShell do Azure são necessárias para trabalhar com a pilha do Azure. Para obter mais informações, consulte [instalar o PowerShell para Azure pilha](azure-stack-powershell-install.md) e [configurar o ambiente do PowerShell do usuário do Azure pilha](azure-stack-powershell-configure-user.md) para saber mais.

### <a name="powershell-sample-script-for-azure-stack"></a>Script de exemplo do PowerShell para Azure pilha 
Este exemplo, suponha que foi [instalar o PowerShell para Azure pilha](azure-stack-powershell-install.md). Esse script será ajuda conplete a configuração e peça ao seu locatário do Azure pilha credenciais para adicionar sua conta para o seu de ambiente local do PowerShell. Em seguida, o script será definir o padrão de assinatura do Azure, crie uma nova conta de armazenamento no Azure, criar um novo contêiner nessa nova conta de armazenamento e carregar um arquivo de imagem existente (blob) para esse contêiner. Depois que o script listar todos os blobs nesse contêiner, ele criará um novo diretório de destino no computador local e baixará o arquivo de imagem.

1. Instalar [módulos do PowerShell do Azure pilha-compatível com o Azure](azure-stack-powershell-install.md).  
2. Baixe o [ferramentas necessárias para trabalhar com o Azure pilha](azure-stack-powershell-download.md).  
3. Abra **o Windows PowerShell ISE** e **executar como administrador**, clique em **arquivo** > **novo** para criar um novo arquivo de script.
4. Copie o script a seguir e cole o novo arquivo de script.
5. Atualize as variáveis de script com base em suas definições de configuração. 
6. Observação: este script deve ser executado sob a raiz do baixado **AzureStack_Tools**. 

```PowerShell 
# begin

$ARMEvnName = "AzureStackUser" # set AzureStackUser as your Azure Stack environemnt name
$ARMEndPoint = "https://management.local.azurestack.external" 
$GraphAudiance = "https://graph.windows.net/" 
$AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com" 

$SubscriptionName = "basic" # Update with the name of your subscription.
$ResourceGroupName = "myTestRG" # Give a name to your new resource group.
$StorageAccountName = "azsblobcontainer" # Give a name to your new storage account. It must be lowercase!
$Location = "Local" # Choose "Local" as an example.
$ContainerName = "photo" # Give a name to your new container.
$ImageToUpload = "C:\temp\Hello.jpg" # Prepare an image file and a source directory in your local computer.
$DestinationFolder = "C:\temp\downlaod" # A destination directory in your local computer.

# Import the Connect PowerShell module"
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
Import-Module .\Connect\AzureStack.Connect.psm1

# Configure the PowerShell environment
# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRmEnvironment -Name $ARMEvnName -ARMEndpoint $ARMEndPoint 

# Set the GraphEndpointResourceId value
Set-AzureRmEnvironment -Name $ARMEvnName -GraphEndpoint $GraphAudiance

# Login
$TenantID = Get-AzsDirectoryTenantId -AADTenantName $AADTenantName -EnvironmentName $ARMEvnName
Login-AzureRmAccount -EnvironmentName $ARMEvnName -TenantId $TenantID 

# Set a default Azure subscription.
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Create a new Resource Group 
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Create a new storage account.
New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS

# Set a default storage account.
Set-AzureRmCurrentStorageAccount -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName 

# Create a new container.
New-AzureStorageContainer -Name $ContainerName -Permission Off

# Upload a blob into a container.
Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload

# List all blobs in a container.
Get-AzureStorageBlob -Container $ContainerName

# Download blobs from the container:
# Get a reference to a list of all blobs in a container.
$blobs = Get-AzureStorageBlob -Container $ContainerName

# Create the destination directory.
New-Item -Path $DestinationFolder -ItemType Directory -Force  

# Download blobs into the local destination directory.
$blobs | Get-AzureStorageBlobContent –Destination $DestinationFolder

# end
```

### <a name="powershell-known-issues"></a>Problemas conhecidos do PowerShell 
A versão atual do módulo Azure PowerShell compatível para a pilha do Azure é 1.2.10. É diferente da versão mais recente do PowerShell do Azure. Essa diferença afeta a operação de serviços de armazenamento:

* O formato do valor de retorno de `Get-AzureRmStorageAccountKey` versão 1.2.10 tem duas propriedades: `Key1` e `Key2`, enquanto a versão atual do Azure retorna uma matriz que contém todas as chaves de conta.
   ```
   # This command gets a specific key for a Storage account, 
   # and works for Azure PowerShell version 1.4, and later versions.
   (Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
   -AccountName "MyStorageAccount").Value[0]

   # This command gets a specific key for a Storage account, 
   # and works for Azure PowerShell version 1.3.2, and previous versions.
   (Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
   -AccountName "MyStorageAccount").Key1

   ```
   Para obter mais informações, consulte [Get-AzureRmStorageAccountKey](https://docs.microsoft.com/powershell/module/azurerm.storage/Get-AzureRmStorageAccountKey?view=azurermps-4.1.0).

## <a name="azure-cli"></a>CLI do Azure
A CLI do Azure é a experiência de linha de comando do Azure para gerenciar recursos do Azure. Você pode instalá-lo no Windows, Linux e macOS e executá-lo a partir da linha de comando. 

CLI do Azure é otimizado para gerenciar e administrar os recursos do Azure da linha de comando e para a criação de scripts de automação que funcionam com o Gerenciador de recursos do Azure. Ele fornece muitas das mesmas funções encontradas no portal do Azure pilha, incluindo o acesso de dados avançados.

A pilha do Azure requer CLI do Azure versão 2.0. Para obter mais informações sobre como instalar e configurar CLI do Azure com a pilha do Azure, consulte [instalar e configurar o Azure pilha CLI](azure-stack-connect-cli.md). Para obter mais informações sobre como usar o 2.0 do CLI do Azure para executar várias tarefas de trabalhar com recursos em sua conta de armazenamento de pilha do Azure, consulte [usando o Azure CLI2.0 com armazenamento do Azure](../../storage/storage-azure-cli.md)

### <a name="azure-cli-sample-script-for-azure-stack"></a>Script de exemplo CLI do Azure para a pilha do Azure 
Depois de concluir a instalação de CLI e configuração, você pode tentar as etapas a seguir para trabalhar com um exemplo de script de shell pequeno para interagir com os recursos de armazenamento do Azure pilha. O script primeiro cria um novo contêiner na sua conta de armazenamento, em seguida, carrega um arquivo existente (como um blob) para esse contêiner, lista todos os blobs no contêiner e por fim, baixa o arquivo para um destino no computador local que você especificar. Antes de executar este script, verifique se você se conectar com êxito e o logon para o destino de pilha do Azure. 
1. Abra o editor de texto de sua preferência, e copie e cole o script anterior no editor.
2. Atualize as variáveis do script para refletir as configurações. 
3. Depois de atualizar as variáveis necessárias, salve o script e sair do editor. As próximas etapas pressupõem nomeados my_storage_sample.sh seu script.
4. Marque o script como executável, se necessário: `chmod +x my_storage_sample.sh`
5. Execute o script. Por exemplo, no Bash:`./my_storage_sample.sh`

```bash
#!/bin/bash
# A simple Azure Stack Storage example script

export AZURESTACK_RESOURCE_GROUP=<resource_group_name>
export AZURESTACK_RG_LOCATION="local"
export AZURESTACK_STORAGE_ACCOUNT_NAME=<storage_account_name>
export AZURESTACK_STORAGE_CONTAINER_NAME=<container_name>
export AZURESTACK_STORAGE_BLOB_NAME=<blob_name>
export FILE_TO_UPLOAD=<file_to_upload>
export DESTINATION_FILE=<destination_file>

echo "Creating the resource group..."
az group create --name $AZURESTACK_RESOURCE_GROUP --location $AZURESTACK_RG_LOCATION

echo "Creating the storage account..."
az storage account create --name $AZURESTACK_STORAGE_ACCOUNT_NAME --resource-group $AZURESTACK_RESOURCE_GROUP --account-type Standard_LRS

echo "Creating the blob container..."
az storage container create --name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Uploading the file..."
az storage blob upload --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --file $FILE_TO_UPLOAD --name $AZURESTACK_STORAGE_BLOB_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Listing the blobs..."
az storage blob list --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --output table

echo "Downloading the file..."
az storage blob download --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --name $AZURESTACK_STORAGE_BLOB_NAME --file $DESTINATION_FILE --output table

echo "Done"
```

## <a name="microsoft-azure-storage-explorer"></a>Gerenciador do Armazenamento do Microsoft Azure

Microsoft Azure Storage Explorer é um aplicativo autônomo da Microsoft. Ele permite trabalhar facilmente com o armazenamento do Azure e dados de armazenamento de pilha do Azure no Windows e Linux e macOS. Se você quiser uma maneira fácil de gerenciar os dados de armazenamento de pilha do Azure, considere usar o Microsoft Azure Storage Explorer.

Para obter mais informações sobre como configurar o Azure Storage Explorer para trabalhar com a pilha do Azure, consulte [conectar o Gerenciador de armazenamento para uma assinatura do Azure pilha](azure-stack-storage-connect-se.md).

Para obter mais informações sobre o Gerenciador de armazenamento do Microsoft Azure, consulte [Introdução ao Gerenciador de armazenamento (visualização)](../../vs-azure-tools-storage-manage-with-storage-explorer.md)

## <a name="next-steps"></a>Próximas etapas
* [Conecte-se o Gerenciador de armazenamento para uma assinatura de pilha do Azure](azure-stack-storage-connect-se.md)
* [Introdução ao Gerenciador de armazenamento (visualização)](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Armazenamento do Azure consistente: diferenças e considerações](azure-stack-acs-differences.md)
* [Introdução ao Armazenamento do Microsoft Azure](../../storage/common/storage-introduction.md)

