---
title: Ferramentas para o armazenamento do Azure Stack | Microsoft Docs
description: Saiba mais sobre o armazenamento de dados do Azure Stack, ferramentas de transferência
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/03/2018
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: b907098f9d6471a7df5ca43f76cd9109ee4dea8e
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54263932"
---
# <a name="use-data-transfer-tools-for-azure-stack-storage"></a>Usar ferramentas de transferência de dados para o armazenamento do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

O Microsoft Azure Stack fornece um conjunto de serviços de armazenamento para discos, blobs, tabelas, filas e funções de gerenciamento de conta. Se você quiser gerenciar ou mover dados do armazenamento do Azure Stack, você pode usar um conjunto de ferramentas de armazenamento do Azure. Este artigo fornece uma visão geral das ferramentas disponíveis.

Seus requisitos de determinam quais das ferramentas a seguir funcionariam melhor para você:

* [AzCopy](#azcopy)

    Um utilitário de armazenamento específico, de linha de comando que você pode baixar para copiar dados de um objeto a outro objeto em sua conta de armazenamento ou entre contas de armazenamento.

* [PowerShell do Azure](#azure-powershell)

    Um shell de linha de comando, com base em tarefa e linguagem de script criado especialmente para administração do sistema.

* [CLI do Azure](#azure-cli)

    Uma ferramenta de código-fonte aberto, de plataforma cruzada que fornece um conjunto de comandos para trabalhar com as plataformas do Azure e o Azure Stack.

* [Gerenciador de armazenamento do Microsoft](#microsoft-azure-storage-explorer)

    Um aplicativo autônomo fácil de usar com uma interface do usuário.

* [Blobfuse ](#blobfuse)

    Um driver de sistema de arquivos virtual para o armazenamento de BLOBs do Azure, que permite que você acesse seus dados de blob de bloco existentes em sua conta de armazenamento por meio do sistema de arquivos Linux. 

Devido a diferenças de serviços de armazenamento entre o Azure e o Azure Stack, pode haver alguns requisitos específicos para cada ferramenta descritos nas seções a seguir. Para obter uma comparação entre o armazenamento do Azure Stack e o armazenamento do Azure, consulte [armazenamento do Azure Stack: Diferenças e considerações](azure-stack-acs-differences.md).

## <a name="azcopy"></a>AzCopy

O AzCopy é um utilitário de linha de comando projetado para copiar dados para e do armazenamento de blob e tabela do Microsoft Azure usando comandos simples com um desempenho ideal. Você pode copiar dados de um objeto para outro dentro de sua conta de armazenamento, ou entre contas de armazenamento.

### <a name="download-and-install-azcopy"></a>Baixar e instalar o AzCopy

Há duas versões do utilitário AzCopy: AzCopy no Windows e o AzCopy no Linux.

 - **AzCopy no Windows**
    - Baixe a versão com suporte do AzCopy para o Azure Stack. Você pode instalar e usar o AzCopy no Azure Stack da mesma forma que o Azure. Para obter mais informações, consulte [AzCopy no Windows](../../storage/common/storage-use-azcopy.md).
        - Para a atualização 1811, ou versões mais recentes, [baixar o AzCopy 7.3.0](https://aka.ms/azcopyforazurestack20171109).
        - Para versões anteriores (atualização 1802 para 1809), [baixar o AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417).

 - **AzCopy no Linux**

    - Você pode instalar e usar o AzCopy no Azure Stack da mesma forma que o Azure. Para obter mais informações, consulte [AzCopy no Linux](../../storage/common/storage-use-azcopy-linux.md).
    - Para versões anteriores (1802 para 1809 atualizações), consulte o [etapas de instalação para o AzCopy 7.1 e versões anteriores](../../storage/common/storage-use-azcopy-linux.md#installation-steps-for-azcopy-71-and-earlier-versions).

### <a name="azcopy-command-examples-for-data-transfer"></a>Exemplos de comando AzCopy para transferir dados

Os exemplos a seguir seguem os cenários típicos para cópia de dados de blobs do Azure Stack. Para obter mais informações, consulte [AzCopy no Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) e [AzCopy no Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux).

### <a name="download-all-blobs-to-a-local-disk"></a>Baixar todos os blobs em um disco local

**Windows**

```shell
AzCopy.exe /source:https://myaccount.blob.local.azurestack.external/mycontainer /dest:C:\myfolder /sourcekey:<key> /S
```

**Linux**

```bash
azcopy \
    --source https://myaccount.blob.local.azurestack.external/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

### <a name="upload-single-file-to-virtual-directory"></a>Carregar arquivo único no diretório virtual

**Windows**

```shell
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.local.azurestack.external/mycontainer/vd /DestKey:key /Pattern:abc.txt
```

**Linux**

```bash
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.local.azurestack.external/mycontainer/vd/abc.txt \
    --dest-key <key>
```

### <a name="move-data-between-azure-and-azure-stack-storage"></a>Mover dados entre o armazenamento do Azure e o Azure Stack

Transferência de dados assíncronos entre o armazenamento do Azure e o Azure Stack não tem suporte. Você precisa especificar a transferência com o **/SyncCopy** ou **– cópia sincronizada** opção.

**Windows**

```shell
Azcopy /Source:https://myaccount.blob.local.azurestack.external/mycontainer /Dest:https://myaccount2.blob.core.windows.net/mycontainer2 /SourceKey:AzSKey /DestKey:Azurekey /S /SyncCopy
```

**Linux**

```bash
azcopy \
    --source https://myaccount1.blob.local.azurestack.external/myContainer/ \
    --destination https://myaccount2.blob.core.windows.net/myContainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt" \
    --sync-copy
```

### <a name="azcopy-known-issues"></a>Problemas conhecidos do Azcopy

 - Qualquer operação do AzCopy em um armazenamento de arquivos não está disponível porque o armazenamento de arquivos ainda não está disponível no Azure Stack.
 - Transferência de dados assíncronos entre o armazenamento do Azure e o Azure Stack não tem suporte. Você pode especificar a transferência com o **/SyncCopy** opção para copiar os dados.
 - A versão do Azcopy Linux dá suporte a apenas 1802 atualização ou versões posteriores. E ele não dá suporte a serviço tabela.

## <a name="azure-powershell"></a>Azure PowerShell

O Azure PowerShell é um módulo que fornece cmdlets para gerenciar serviços no Azure e o Azure Stack. É um shell baseado em tarefa e de linha de comando e linguagem de script criado especialmente para administração do sistema.

### <a name="install-and-configure-powershell-for-azure-stack"></a>Instalar e configurar o PowerShell para o Azure Stack

O Azure Stack compatível com módulos do PowerShell do Azure são necessárias para trabalhar com o Azure Stack. Para obter mais informações, consulte [instalar o PowerShell para Azure Stack](azure-stack-powershell-install.md) e [configurar o ambiente do PowerShell do usuário do Azure Stack](azure-stack-powershell-configure-user.md) para saber mais.

### <a name="powershell-sample-script-for-azure-stack"></a>Script de exemplo do PowerShell para o Azure Stack 

Este exemplo pressupõem que você tenha com êxito [PowerShell instalado para o Azure Stack](azure-stack-powershell-install.md). Esse script ajudará você a concluir a configuração e solicitar credenciais para adicionar sua conta no ambiente local do PowerShell de seu locatário do Azure Stack. Em seguida, o script será definir o padrão de assinatura do Azure, crie uma nova conta de armazenamento no Azure, crie um novo contêiner nessa nova conta de armazenamento e carregará um arquivo de imagem existente (blob) para esse contêiner. Depois que o script lista todos os blobs nesse contêiner, ele irá criar um novo diretório de destino no computador local e baixar o arquivo de imagem.

1. Instale [módulos do Azure Stack-compatível com o Azure PowerShell](azure-stack-powershell-install.md).
2. Baixe o [as ferramentas necessárias para trabalhar com o Azure Stack](azure-stack-powershell-download.md).
3. Abra **Windows PowerShell ISE** e **executar como administrador**, clique em **arquivo** > **novo** para criar um novo arquivo de script.
4. Copie o script a seguir e cole no novo arquivo de script.
5. Atualize as variáveis de script com base em suas definições de configuração.
   > [!NOTE]
   > Esse script tem a ser executado no diretório raiz **AzureStack_Tools**.

```PowerShell  
# begin

$ARMEvnName = "AzureStackUser" # set AzureStackUser as your Azure Stack environment name
$ARMEndPoint = "https://management.local.azurestack.external" 
$GraphAudience = "https://graph.windows.net/" 
$AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com" 

$SubscriptionName = "basic" # Update with the name of your subscription.
$ResourceGroupName = "myTestRG" # Give a name to your new resource group.
$StorageAccountName = "azsblobcontainer" # Give a name to your new storage account. It must be lowercase.
$Location = "Local" # Choose "Local" as an example.
$ContainerName = "photo" # Give a name to your new container.
$ImageToUpload = "C:\temp\Hello.jpg" # Prepare an image file and a source directory in your local computer.
$DestinationFolder = "C:\temp\download" # A destination directory in your local computer.

# Import the Connect PowerShell module"
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
Import-Module .\Connect\AzureStack.Connect.psm1

# Configure the PowerShell environment
# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRmEnvironment -Name $ARMEvnName -ARMEndpoint $ARMEndPoint 

# Set the GraphEndpointResourceId value
Set-AzureRmEnvironment -Name $ARMEvnName -GraphEndpoint $GraphAudience

# Login
$TenantID = Get-AzsDirectoryTenantId -AADTenantName $AADTenantName -EnvironmentName $ARMEvnName
Add-AzureRmAccount -EnvironmentName $ARMEvnName -TenantId $TenantID 

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

A versão atual do módulo PowerShell do Azure compatível para o Azure Stack é 1.2.11 para as operações de usuário. Ele é diferente da versão mais recente do PowerShell do Azure. Essa diferença afeta a operação de serviços de armazenamento:

O formato do valor de retorno de `Get-AzureRmStorageAccountKey` na versão 1.2.11 tem duas propriedades: `Key1` e `Key2`, enquanto a versão atual do Azure retorna uma matriz que contém todas as chaves de conta.

```powershell
# This command gets a specific key for a storage account, 
# and works for Azure PowerShell version 1.4, and later versions.
(Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
-AccountName "MyStorageAccount").Value[0]

# This command gets a specific key for a storage account, 
# and works for Azure PowerShell version 1.3.2, and previous versions.
(Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
-AccountName "MyStorageAccount").Key1
```

Para obter mais informações, consulte [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/Get-AzureRmStorageAccountKey).

## <a name="azure-cli"></a>CLI do Azure

A CLI do Azure é a experiência de linha de comando do Azure para gerenciar recursos do Azure. Você pode instalá-lo no Windows, Linux e macOS e executá-lo da linha de comando.

CLI do Azure é otimizada para gerenciar e administrar os recursos do Azure da linha de comando e para a criação de scripts de automação que funcionam no Azure Resource Manager. Ele fornece muitas das mesmas funções encontradas no portal do Azure Stack, incluindo o acesso de dados avançados.

O Azure Stack requer a CLI do Azure versão 2.0 ou posterior. Para obter mais informações sobre como instalar e configurar a CLI do Azure com o Azure Stack, consulte [instalar e configurar a CLI do Azure Stack](azure-stack-version-profiles-azurecli2.md). Para obter mais informações sobre como usar a CLI do Azure para executar várias tarefas ao trabalhar com recursos em sua conta de armazenamento do Azure Stack, consulte [usando a CLI do Azure com o armazenamento do Azure](../../storage/storage-azure-cli.md)

### <a name="azure-cli-sample-script-for-azure-stack"></a>Script de exemplo da CLI do Azure para o Azure Stack

Depois de concluir a instalação da CLI e a configuração, você pode tentar as etapas a seguir para trabalhar com um pequeno exemplo script shell para interagir com recursos de armazenamento do Azure Stack. O script conclui as seguintes ações:

* Cria um novo contêiner na conta de armazenamento.
* Carrega um arquivo existente (como um blob) no contêiner.
* Lista todos os blobs no contêiner.
* Baixa o arquivo para um destino no computador local que você especificar.

Antes de executar esse script, certifique-se de que você pode com êxito se conectem e entrar no Azure Stack de destino.

1. Abra o editor de texto de sua preferência, e copie e cole o script anterior no editor.
2. Atualize as variáveis do script para refletir suas definições de configuração.
3. Depois de atualizar as variáveis necessárias, salve o script e saia do editor. As próximas etapas pressupõem que você nomeou seu script como **my_storage_sample.sh**.
4. Marque o script como executável, se necessário: `chmod +x my_storage_sample.sh`
5. Execute o script. Por exemplo, no Bash:`./my_storage_sample.sh`

```azurecli
#!/bin/bash
# A simple Azure Stack storage example script

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

## <a name="microsoft-azure-storage-explorer"></a>Gerenciador de armazenamento do Microsoft Azure

Gerenciador de armazenamento do Microsoft Azure é um aplicativo autônomo da Microsoft. Ele permite que você trabalhe facilmente com o armazenamento do Azure e o armazenamento do Azure Stack dados em computadores Linux, macOS e Windows. Se você quiser uma maneira fácil de gerenciar os dados do armazenamento do Azure Stack, considere usar o Gerenciador de armazenamento do Microsoft Azure.

* Para saber mais sobre como configurar o Gerenciador de armazenamento do Azure para trabalhar com o Azure Stack, consulte [conectar o Gerenciador de armazenamento para uma assinatura do Azure Stack](azure-stack-storage-connect-se.md).
* Para saber mais sobre o Gerenciador de armazenamento do Microsoft Azure, consulte [Introdução ao Gerenciador de armazenamento](../../vs-azure-tools-storage-manage-with-storage-explorer.md)

## <a name="blobfuse"></a>Blobfuse 

[Blobfuse](https://github.com/Azure/azure-storage-fuse) é um driver de sistema de arquivos virtual para o Armazenamento de Blobs do Azure que permite acessar os Dados do Blob de Blocos existentes em sua conta de Armazenamento através do sistema de arquivos Linux. Armazenamento de Blobs do Azure é um serviço de armazenamento de objeto e, portanto, não possui um namespace hierárquico. O Blobfuse fornece esse namespace usando o esquema de diretório virtual com o uso da barra `/` como um delimitador. O Blobfuse funciona no Azure e o Azure Stack. 

Para saber mais sobre como montar o armazenamento de BLOBs como um sistema de arquivos com Blobfuse no Linux, consulte [como armazenamento de blobs de montagem como um sistema de arquivos com Blobfuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux). 

Para o Azure Stack **blobEndpoint** precisa ser especificado além de accountName, accountKey/sasToken, containerName, ao configurar suas credenciais de conta de armazenamento na etapa de preparação para a montagem. 

No Kit de desenvolvimento do Azure Stack, o blobEndpoint deve ser `myaccount.blob.local.azurestack.external`. No sistema integrado do Azure Stack, entre em contato com seu administrador de nuvem se você não tiver certeza sobre o ponto de extremidade. 

Esteja ciente de que accountKey e sasToken só podem ser configurado um por vez. Quando a chave de conta de armazenamento for fornecido, o arquivo de configuração de credenciais é no seguinte formato: 

```
accountName myaccount 
accountKey myaccesskey== 
containerName mycontainer 
blobEndpoint myaccount.blob.local.azurestack.external
```

Quando recebe o token de acesso compartilhado, o arquivo de configuração de credenciais é no seguinte formato:

```  
accountName myaccount 
sasToken ?mysastoken 
containerName mycontainer 
blobEndpoint myaccount.blob.local.azurestack.external
```

## <a name="next-steps"></a>Próximas etapas

* [Conectar-se o Gerenciador de armazenamento para uma assinatura do Azure Stack](azure-stack-storage-connect-se.md)
* [Introdução ao Gerenciador de armazenamento](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Armazenamento consistente do Azure: diferenças e considerações](azure-stack-acs-differences.md)
* [Introdução ao armazenamento do Microsoft Azure](../../storage/common/storage-introduction.md)
