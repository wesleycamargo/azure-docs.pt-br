---
title: "Exemplo de script do Azure PowerShell – Migrar blobs entre contas de armazenamento usando o AzCopy no Windows | Microsoft Docs"
description: "Usando o AzCopy, copia o conteúdo de Blob de uma Conta de Armazenamento do Azure para outra."
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 1/3/2018
ms.author: v-rogara
ms.openlocfilehash: 0902792b2367aa56285e7e074e184ffa35fca466
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2018
---
# <a name="migrate-blobs-across-storage-accounts-using-azcopy-on-windows"></a>Migrar blobs entre contas de armazenamento usando o AzCopy no Windows

Este exemplo copia todos os objetos de blob de uma conta de armazenamento de origem fornecida pelo usuário para uma conta de armazenamento de destino fornecida pelo usuário. 

Isso é feito usando o comando `Get-AzureStorageContainer`, que lista todos os contêineres em uma conta de armazenamento. O exemplo, em seguida, emite comandos do AzCopy, copiando cada contêiner da conta de armazenamento de origem para a conta de armazenamento de destino. Se ocorrerem falhas, o exemplo repetirá $retryTimes (o padrão é 3 e pode ser modificado com o parâmetro `-RetryTimes`). Se houver falha em cada tentativa, o usuário poderá executar o script novamente fornecendo o exemplo com o último contêiner copiado com êxito usando o parâmetro `-LastSuccessContainerName`. O exemplo, em seguida, continua copiando contêineres desse ponto.

Este exemplo exige o módulo do Armazenamento do Azure PowerShell versão **4.0.2** ou posterior. Você pode verificar a versão instalada usando `Get-Module -ListAvailable Azure.storage`. Se você precisa instalar ou atualizar, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Esse exemplo também requer a versão mais recente do [AzCopy no Windows](http://aka.ms/downloadazcopy). O diretório de instalação padrão é `C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\`

Este exemplo usa um nome e chave de conta de armazenamento de origem, um nome e chave de conta de armazenamento de destino e o caminho de arquivo completo do AzCopy.exe (caso não esteja instalado no diretório padrão).

A seguir estão exemplos de entrada para este exemplo:

Se o AzCopy estiver instalado no diretório padrão:
```PowerShell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
```

Se o AzCopy não estiver instalado no diretório padrão:

```Powershell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
AzCopyPath: C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy.exe
```

Se ocorrer falha e o exemplo precisar ser executado novamente de um contêiner específico: 

`.\copyScript.ps1 -LastSuccessContainerName myContainerName`

## <a name="sample-script"></a>Script de exemplo

```Powershell
# Run the script in a new open Powershell window, which has not run other cmdlets, or AzCopy performance could suffer .
# Install Azure PowerShell before runing the script: https://github.com/Azure/azure-powershell/releases
# Install AzCopy before runing the script: https://docs.microsoft.com/azure/storage/common/storage-use-azcopy
# Do not modify the Source or Destination accounts while the script is running

 param (
    [Parameter(Mandatory = $true, 
    HelpMessage= "Source Storage account name.")]
    [ValidatePattern("^[a-z0-9`]{3,24}$")]
    [String]$srcStorageAccountName,

    [Parameter(Mandatory = $true, 
    HelpMessage= "Source Storage account key.")]
    [String]$srcStorageAccountKey,
    
    [Parameter(Mandatory = $true, 
    HelpMessage= "Destination Storage account name.")]
    [ValidatePattern("^[a-z0-9`]{3,24}$")]
    [String]$DestStorageAccountName,

    [Parameter(Mandatory = $true, 
    HelpMessage= "Destination Storage account key.")]
    [String]$DestStorageAccountKey,

    [Parameter(Mandatory = $false, 
    HelpMessage= "Input the full filePath of the AzCopy.exe, e.g.: C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy.exe")]
    [String]$AzCopyPath = "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy.exe",

    [Parameter(Mandatory = $false, 
    HelpMessage='Sets the number of retries in an event of failure. Set to 0 for no retry, set -1 for infinite retry.')]
    [Int32]$RetryTimes = 3,

    [Parameter(Mandatory = $false, 
    ValueFromPipeline = $true, 
    HelpMessage='Used for resume operation. When provided, the script will copy containers that are alphabetically after $LastSuccessContainerName')]
    [String]$LastSuccessContainerName = $nullz
 )

 if( (Get-Item $AzCopyPath).Exists)
 {

    $FileItemVersion = (Get-Item $AzCopyPath).VersionInfo
    $FilePath = ("{0}.{1}.{2}.{3}" -f  $FileItemVersion.FileMajorPart,  $FileItemVersion.FileMinorPart,  $FileItemVersion.FileBuildPart,  $FileItemVersion.FilePrivatePart)

    if([version] $FilePath -lt "7.0.0.2")
    {
        $AzCopyPath = Read-Host "Version of AzCopy found at default install directory is of a lower, unsupported version. Please input the full filePath of the AzCopy.exe that is version 7.0.0.2 or higher, e.g.: C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy.exe"
    }
 }
 elseIf( (Get-Item $AzCopyPath).Exists -eq $false)
 {
    $AzCopyPath = Read-Host "Input the full filePath of the AzCopy.exe, e.g.: C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy.exe"
 }

# Create and check Storage context
$Error.Clear()
$srcCtx = New-AzureStorageContext -StorageAccountName $srcStorageAccountName -StorageAccountKey $srcStorageAccountKey
if ($srcCtx -eq $null)
{
    Write-Error "Script could not create source Storage Context, possibly due to invalid StorageAccountName or StorageAccount Key terminating: $Error[0]";
    return;
}
$destCtx = New-AzureStorageContext -StorageAccountName $destStorageAccountName -StorageAccountKey $destStorageAccountKey
if ($destCtx -eq $null)
{
    Write-Error "Script could not create destination storage context, possibly due to invalid StorageAccountName or StorageAccount Key terminating: $Error[0]";
    return;
}

#Check Source and Destination Storage account Connection
$Error.Clear()
$Containers = Get-AzureStorageContainer -MaxCount 1 -Context $srcCtx
if ($Error.Count -gt 0)
{
    Write-Error "Script failed to connect to source Storage account, terminating: $Error[0]";
    return;
}
$Containers = Get-AzureStorageContainer -MaxCount 1 -Context $destCtx
if ($Error.Count -gt 0)
{
    Write-Error "Script failed to connect to destination Storage account, terminating: $Error[0]";
    return;
}

#Check AzCopy Path
if((Test-Path $AzCopyPath) -eq $false)
{
    Write-Error "Script is terminating since the provided AzCopyPath does not exist: $AzCopyPath ";
    return;
}
elseif((Get-Item $AzCopyPath).BaseName -ne "AzCopy" )
{
    Write-Error "Script is terminating since the provided AzCopyPath does not refer to the AzCopy exe: $AzCopyPath ";
    return;
}
elseif((Get-Item $AzCopyPath).BaseName -eq "AzCopy")
{
    $FileItemVersion = (Get-Item $AzCopyPath).VersionInfo
    $FilePath = ("{0}.{1}.{2}.{3}" -f  $FileItemVersion.FileMajorPart,  $FileItemVersion.FileMinorPart,  $FileItemVersion.FileBuildPart,  $FileItemVersion.FilePrivatePart)

    if([version] $FilePath -lt "7.0.0.2")
    {
        $AzCopyPath = Read-Host "Version of AzCopy found at provided path is of a lower, unsupported version. Please input the full filePath of the AzCopy.exe that is version 7.0.0.2 or higher, e.g.: C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy.exe"
    }
}

$OutputLastSuccessContainer = $LastSuccessContainerName;
$HasReachedLastSuccessContainer = $false;

if ($LastSuccessContainerName -eq $null -or $LastSuccessContainerName -eq "")
{
    $HasReachedLastSuccessContainer = $true;
}

# For list container
$MaxReturn = 250
$Token = $Null

do{
    # List source containers
    $retry = 1
    $Error.Clear()
    $srcContainers = Get-AzureStorageContainer -MaxCount $MaxReturn -ContinuationToken $Token -Context $srcCtx

    # If list container fail, retry it
    while(($Error.Count -gt 0) -and ($RetryTimes -eq -1 -or $retry -le $retryTimes))
    {
        Write-Host "Retry List containers $retry"
        $Error.Clear()
        $srcContainers = Get-AzureStorageContainer -MaxCount $MaxReturn -ContinuationToken $Token -Context $srcCtx
        $retry++
    }

    # If list container fail after retry, break script
    if ($Error.Count -gt 0){
        Write-Error "Terminating the script since listing source containers failed: $Error[0]";
        $CopyContainerFail = $true
        break;
    }
    $Token = $srcContainers[$srcContainers.Count -1].ContinuationToken;

    # Transfer containers one by one
    $CopyContainerFail = $false
    foreach ($container in $srcContainers)
    {
        if (!$HasReachedLastSuccessContainer)
        {
            if ($container.Name -eq $LastSuccessContainerName)
            {
                $HasReachedLastSuccessContainer = $true;
            }
            Write-Host "Skipping container copy: $($container.Name)"
            Continue;
        }

        Write-Host "Start copying container: $($container.Name)"
        $retry = 1

        # Get AzCopy command for transfer one container
        $destContainer = $destCtx.StorageAccount.CreateCloudBlobClient().GetContainerReference($container.Name)
        $azCopyCmd = [string]::Format("""{0}"" /source:{1} /dest:{2} /sourcekey:""{3}"" /destkey:""{4}"" /snapshot /y /s",$AzCopyPath, $container.CloudBlobContainer.Uri.AbsoluteUri, $destContainer.Uri.AbsoluteUri, $srcStorageAccountKey, $DestStorageAccountKey)
    
        # Execute the AzCopy command first time
        Write-Host "$azCopyCmd"
        $result = cmd /c $azCopyCmd
        foreach($s in $result)
        {
            Write-Host $s 
        }

        # If transfer failed, retry it
        while(($LASTEXITCODE -ne 0) -and ($RetryTimes -eq -1 -or $retry -le $retryTimes))
        {
            Write-Host "Retry $retry : $azCopyCmd"
            $result = cmd /c $azCopyCmd
            foreach($s in $result)
            {
                Write-Host $s 
            }
            $retry++
        }

        # If tranfer failed after retry, print error
        if ($LASTEXITCODE -ne 0){
            Write-Error "Container copy failed: $($container.Name)";
            $CopyContainerFail = $true
            break;
        }
        else
        {
            Write-Host "Finished copying container: $($container.Name)"
            Write-Host ""
            $OutputLastSuccessContainer = $container.Name
        }
    }
    if($CopyContainerFail)
    {
        break;
    }
}
While ($Token -ne $Null)

# Summary the copy result
if ($CopyContainerFail)
{
    if(($OutputLastSuccessContainer -ne $null) -and ($OutputLastSuccessContainer -ne ""))
    {
        Write-Warning "To resume, rerun the script and append the parameter: ""-LastSuccessContainer $OutputLastSuccessContainer"""
        return $OutputLastSuccessContainer
    }
    else
    {
        Write-Warning "To resume, rerun the script."
        return $null
    }
}
else
{
    Write-Host "All Containers copied successfully."
    return ""
}
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para copiar dados de uma conta de armazenamento para outra. Cada item em que a tabela contém links para a documentação específica do comando.

| Get-Help | Observações |
|---|---|
| [Get-AzureStorageContainer](/powershell/module/azure.storage/Get-AzureStorageContainer) | Retorna os contêineres associados a essa conta de armazenamento. |
| [New-AzureStorageContext](/powershell/module/azure.storage/New-AzureStorageContext) | Cria um contexto do Armazenamento do Azure. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

Amostras adicionais de script do PowerShell de armazenamento podem ser encontradas em [Amostras do PowerShell para Armazenamento de Blobs do Azure](../blobs/storage-samples-blobs-powershell.md).
