<properties
	pageTitle="Atualizar o script do PowerShell do projeto do Grupo de Recursos do Azure | Microsoft Azure"
	description="Descreve as etapas necessárias para atualizar manualmente o script do PowerShell que vem com um projeto de implantação do Grupo de Recursos do Azure."
	services="visual-studio-online"
	documentationCenter="na"
	authors="tfitzmac"
	manager="timlt"
	editor="" />

 <tags
	ms.service="azure-resource-manager"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="08/01/2016"
	ms.author="tomfitz" />

# Atualizar o script do PowerShell do projeto do Grupo de Recursos do Azure

O projeto de implantação do Grupo de Recursos do Azure no Visual Studio usa um script do Azure PowerShell para ajudar a preparar e implantar seus ativos no Azure a partir do Visual Studio. A versão do Azure PowerShell usada pelo script do projeto é 0.9.8. No entanto, uma nova versão do Azure PowerShell, a versão 1.0, foi lançada.

Embora a nova versão do Azure PowerShell tenha novos recursos e torne o uso do Gerenciador de Recursos do Azure uma experiência excelente, foram feitas alterações em várias funções de cmdlet que tornam a versão anterior do script incompatível. No entanto, você pode modificar o script para corrigir esses problemas e executar o script fora do Visual Studio em uma janela de comando do PowerShell. Este artigo fornece informações sobre quais correções fazer e onde. Além das correções específicas, uma cópia do script totalmente modificado é listada no final do artigo.

## Opções de solução

Depois de instalar o novo Azure PowerShell, os problemas podem manifestar-se de duas formas em seu projeto do Grupo de Recursos do Azure.

- Como a caixa de diálogo de implantação no Visual Studio não conseguirá localizar os novos cmdlets do Azure, você será solicitado a instalá-los. No entanto, como os módulos foram renomeados na nova versão dos cmdlets, o Visual Studio não conseguirá localizar os novos módulos. Como resultado, você poderá ficar preso em um loop se tentar instalá-los no Visual Studio.

- Se você executar o script fora do Visual Studio na janela de comando do PowerShell, poderá ver o erro:

	*O termo 'Switch-AzureMode' não é reconhecido como o nome de um cmdlet, função, arquivo de script ou programa operável. Verifique a ortografia do nome ou se um caminho foi incluído, verifique se ele está correto e tente novamente.*

Se o novo Azure PowerShell impedir a implantação de seu projeto do Grupo de Recursos do Azure, estas são as seguintes opções para corrigi-lo.

- Você pode desinstalar a versão mais recente do Azure PowerShell e reinstalar a versão anterior (0.9.8) usando o [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx).

- Você pode fazer alterações específicas e direcionadas no script do PowerShell do projeto de implantação para resolver os problemas e executar esse script manualmente na janela de comando do PowerShell. Você não poderá executar o script a partir do item de menu **Implantar** no Visual Studio. As etapas para atualizar o script do PowerShell são listadas posteriormente neste artigo, bem como uma cópia do script totalmente atualizado.

## Atualizar o script do Azure PowerShell
Estas instruções se referem aos números da linha. Para ativar a numeração da linha no Visual Studio, conira [Como: exibir os números da linha no editor](https://msdn.microsoft.com/library/ms165340.aspx).

1. Na linha 61, substitua o código a seguir:

	```
	if ($StorageAccountResourceGroupName) {
	        Switch-AzureMode AzureResourceManager
	        $StorageAccountKey = (Get-AzureStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1
	    }
	    else {
	        Switch-AzureMode AzureServiceManagement
	        $StorageAccountKey = (Get-AzureStorageKey -StorageAccountName $StorageAccountName).Primary
	    }
	$StorageAccountContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
	```

	por:

	```
	$StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1
	$StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context
	```

1. Na linha 87, substitua o código a seguir:

	```
	$ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission are
	```

	por:

	```
	$ArtifactsLocationSasToken = New-AzureRMStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
	```
1. Na linha 95, substitua este código:

	```
	Switch-AzureMode AzureResourceManager
	New-AzureResourceGroup
		-Name $ResourceGroupName `
		-Location $ResourceGroupLocation `
		-TemplateFile $TemplateFile `
		-TemplateParameterFile $TemplateParametersFile `
		@OptionalParameters `
		-Force –Verbose
	```

	pelo código a seguir:

	```
	New-AzureRMResourceGroup `
		-Name $ResourceGroupName `
		-Location $ResourceGroupLocation `
		-Verbose -Force -ErrorAction Stop

	New-AzureRMResourceGroupDeployment `
		-Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
		-ResourceGroupName $ResourceGroupName `
		-TemplateFile $TemplateFile `
		-TemplateParameterFile $TemplateParametersFile `
		@OptionalParameters `
		-Verbose -Force
	```

## Script atualizado
A seguir está o script modificado com todas as atualizações feitas mencionadas anteriormente.

```
#Requires -Version 3.0

Param(
  [string] [Parameter(Mandatory=$true)] $ResourceGroupLocation,
  [string] $ResourceGroupName = '$defaultResourceGroupName$',  
  [switch] $UploadArtifacts,
  [string] $StorageAccountName,
  [string] $StorageAccountResourceGroupName,
  [string] $StorageContainerName = $ResourceGroupName.ToLowerInvariant() + '-stageartifacts',
  [string] $TemplateFile = '..\Templates\$deployTemplateFileName$.json',
  [string] $TemplateParametersFile = '..\Templates\$deployTemplateFileName$.parameters.json',
  [string] $ArtifactStagingDirectory = '..\bin\Debug\staging',
  [string] $AzCopyPath = '..\Tools\AzCopy.exe',
  [string] $DSCSourceFolder = '..\DSC'
)

Import-Module Azure -ErrorAction SilentlyContinue

try {
  [Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("VSAzureTools-$UI$($host.name)".replace(" ","_"), "2.7.2")
} catch { }

Set-StrictMode -Version 3

$OptionalParameters = New-Object -TypeName Hashtable
$TemplateFile = [System.IO.Path]::Combine($PSScriptRoot, $TemplateFile)
$TemplateParametersFile = [System.IO.Path]::Combine($PSScriptRoot, $TemplateParametersFile)

if ($UploadArtifacts) {
    # Convert relative paths to absolute paths if needed
    $AzCopyPath = [System.IO.Path]::Combine($PSScriptRoot, $AzCopyPath)
    $ArtifactStagingDirectory = [System.IO.Path]::Combine($PSScriptRoot, $ArtifactStagingDirectory)
    $DSCSourceFolder = [System.IO.Path]::Combine($PSScriptRoot, $DSCSourceFolder)

    Set-Variable ArtifactsLocationName '_artifactsLocation' -Option ReadOnly
    Set-Variable ArtifactsLocationSasTokenName '_artifactsLocationSasToken' -Option ReadOnly

    $OptionalParameters.Add($ArtifactsLocationName, $null)
    $OptionalParameters.Add($ArtifactsLocationSasTokenName, $null)

    # Parse the parameter file and update the values of artifacts location and artifacts location SAS token if they are present
    $JsonContent = Get-Content $TemplateParametersFile -Raw | ConvertFrom-Json
    $JsonParameters = $JsonContent | Get-Member -Type NoteProperty | Where-Object {$_.Name -eq "parameters"}

    if ($JsonParameters -eq $null) {
        $JsonParameters = $JsonContent
    }
    else {
        $JsonParameters = $JsonContent.parameters
    }

    $JsonParameters | Get-Member -Type NoteProperty | ForEach-Object {
        $ParameterValue = $JsonParameters | Select-Object -ExpandProperty $_.Name

        if ($_.Name -eq $ArtifactsLocationName -or $_.Name -eq $ArtifactsLocationSasTokenName) {
            $OptionalParameters[$_.Name] = $ParameterValue.value
        }
    }

    $StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1

    $StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context

    # Create DSC configuration archive
    if (Test-Path $DSCSourceFolder) {
    Add-Type -Assembly System.IO.Compression.FileSystem
        $ArchiveFile = Join-Path $ArtifactStagingDirectory "dsc.zip"
        Remove-Item -Path $ArchiveFile -ErrorAction SilentlyContinue
        [System.IO.Compression.ZipFile]::CreateFromDirectory($DSCSourceFolder, $ArchiveFile)
    }

    # Generate the value for artifacts location if it is not provided in the parameter file
    $ArtifactsLocation = $OptionalParameters[$ArtifactsLocationName]
    if ($ArtifactsLocation -eq $null) {
        $ArtifactsLocation = $StorageAccountContext.BlobEndPoint + $StorageContainerName
        $OptionalParameters[$ArtifactsLocationName] = $ArtifactsLocation
    }

    # Use AzCopy to copy files from the local storage drop path to the storage account container
    & $AzCopyPath """$ArtifactStagingDirectory""", $ArtifactsLocation, "/DestKey:$StorageAccountKey", "/S", "/Y", "/Z:$env:LocalAppData\Microsoft\Azure\AzCopy\$ResourceGroupName"
    if ($LASTEXITCODE -ne 0) { return }

    # Generate the value for artifacts location SAS token if it is not provided in the parameter file
    $ArtifactsLocationSasToken = $OptionalParameters[$ArtifactsLocationSasTokenName]
    if ($ArtifactsLocationSasToken -eq $null) {
       # Create a SAS token for the storage container - this gives temporary read-only access to the container
       $ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
       $ArtifactsLocationSasToken = ConvertTo-SecureString $ArtifactsLocationSasToken -AsPlainText -Force
       $OptionalParameters[$ArtifactsLocationSasTokenName] = $ArtifactsLocationSasToken
    }
}

# Create or update the resource group using the specified template file and template parameters file
New-AzureRMResourceGroup `
	-Name $ResourceGroupName `
	-Location $ResourceGroupLocation `
	-Verbose -Force -ErrorAction Stop

New-AzureRMResourceGroupDeployment `
	-Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
	-ResourceGroupName $ResourceGroupName `
	-TemplateFile $TemplateFile `
	-TemplateParameterFile $TemplateParametersFile `
	@OptionalParameters `
	-Verbose -Force

```

<!---HONumber=AcomDC_0803_2016-->