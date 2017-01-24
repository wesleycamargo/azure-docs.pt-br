---
title: "Automação da implantação de aplicativos com extensões de máquina virtual | Microsoft Docs"
description: "Tutorial principal de DotNet da máquina virtual do Azure"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 79c91304-6c1b-4354-a185-fecc129b139d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/21/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: b4fb534cf18fd17f636e88cc31d6c997a9f09e45
ms.openlocfilehash: e72afd857025773b3aadc3de124b4e79ec6cd512


---
# <a name="application-deployment-with-azure-resource-manager-templates"></a>Implantação de aplicativos com modelos do Azure Resource Manager
Depois que todos os requisitos de infraestrutura do Azure foram identificados e convertidos em um modelo de implantação, a implantação real do aplicativo precisa ser resolvida. Implantação de aplicativo aqui se refere a instalar os binários do aplicativo real nos recursos do Azure. Para o exemplo de Loja de Música, o .Net Core e o IIS precisam ser instalados e configurados em cada máquina virtual. Os binários da Loja de Música precisam ser instalados na máquina virtual e o banco de dados da Loja de Música criado previamente.

Este documento detalha como extensões de Máquina Virtual podem automatizar a implantação de aplicativos e configuração de máquinas virtuais do Azure. Todas as dependências e configurações exclusivas são realçadas. Para obter a melhor experiência, pré-implante uma instância da solução em sua assinatura do Azure e trabalhe com o modelo do Azure Resource Manager. O modelo completo pode ser encontrado aqui – [Implantação de Loja de Música no Windows](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="configuration-script"></a>Script de configuração
Extensões de máquina virtual são programas especializados executadas em máquinas virtuais para fornecer automação da configuração. As extensões estão disponíveis para várias finalidades específicas, como antivírus, configuração de registro e configuração do Docker. A extensão de Script personalizado pode ser usada para executar qualquer script em uma máquina virtual. Com o exemplo de Loja de Música, cabe à extensão de script personalizado configurar as máquinas virtuais do Windows e instalar o aplicativo de Loja de Música.

Antes de detalhar como extensões de máquina virtual são declaradas em um modelo do Azure Resource Manager, examine o script que é executado. Esse script configura a máquina virtual do Windows para hospedar o aplicativo de Loja de Música. Quando executado, o script instala todo software necessário, instala o aplicativo de Loja de Música do controle do código-fonte e prepara o banco de dados. 

> Este exemplo é para fins de demonstração.

```PowerShell
<#
    .SYNOPSIS
        Downloads and configures .Net Core Music Store application sample across IIS and Azure SQL DB.
#>

Param (
    [string]$user,
    [string]$password,
    [string]$sqlserver
)

# Firewall
netsh advfirewall firewall add rule name="http" dir=in action=allow protocol=TCP localport=80

# Folders
New-Item -ItemType Directory c:\temp
New-Item -ItemType Directory c:\music

# Install iis
Install-WindowsFeature web-server -IncludeManagementTools

# Install dot.net core sdk
Invoke-WebRequest http://go.microsoft.com/fwlink/?LinkID=615460 -outfile c:\temp\vc_redistx64.exe
Start-Process c:\temp\vc_redistx64.exe -ArgumentList '/quiet' -Wait
Invoke-WebRequest https://go.microsoft.com/fwlink/?LinkID=809122 -outfile c:\temp\DotNetCore.1.0.0-SDK.Preview2-x64.exe
Start-Process c:\temp\DotNetCore.1.0.0-SDK.Preview2-x64.exe -ArgumentList '/quiet' -Wait
Invoke-WebRequest https://go.microsoft.com/fwlink/?LinkId=817246 -outfile c:\temp\DotNetCore.WindowsHosting.exe
Start-Process c:\temp\DotNetCore.WindowsHosting.exe -ArgumentList '/quiet' -Wait

# Download music app
Invoke-WebRequest  https://github.com/Microsoft/dotnet-core-sample-templates/raw/master/dotnet-core-music-windows/music-app/music-store-azure-demo-pub.zip -OutFile c:\temp\musicstore.zip
Expand-Archive C:\temp\musicstore.zip c:\music

# Azure SQL connection sting in environment variable
[Environment]::SetEnvironmentVariable("Data:DefaultConnection:ConnectionString", "Server=$sqlserver;Database=MusicStore;Integrated Security=False;User Id=$user;Password=$password;MultipleActiveResultSets=True;Connect Timeout=30",[EnvironmentVariableTarget]::Machine)

# Pre-create database
$env:Data:DefaultConnection:ConnectionString = "Server=$sqlserver;Database=MusicStore;Integrated Security=False;User Id=$user;Password=$password;MultipleActiveResultSets=True;Connect Timeout=30"
Start-Process 'C:\Program Files\dotnet\dotnet.exe' -ArgumentList 'c:\music\MusicStore.dll'

# Configure iis
Remove-WebSite -Name "Default Web Site"
Set-ItemProperty IIS:\AppPools\DefaultAppPool\ managedRuntimeVersion ""
New-Website -Name "MusicStore" -Port 80 -PhysicalPath C:\music\ -ApplicationPool DefaultAppPool
& iisreset
```

## <a name="vm-script-extension"></a>Extensão de script da VM
Extensões de VM podem ser executadas em uma máquina virtual no momento do build incluindo o recurso de extensão no modelo do Azure Resource Manager. A extensão pode ser adicionada usando o Assistente para Adicionar Recursos do Visual Studio ou inserindo JSON válido no modelo. O recurso de extensão do script é aninhado dentro do recurso de máquina virtual; isso pode ser visto no exemplo a seguir.

Siga este link para ver o exemplo JSON no modelo do Resource Manager – [Extensão de script da VM](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L339). 

Observe que no JSON a seguir o script é armazenado no GitHub. Esse script também pode ser armazenado no Armazenamento de Blobs do Azure. Além disso, os modelos do Azure Resource Manager permitem a criação da cadeia de caracteres de execução de script de forma que os valores de parâmetros de modelo podem ser usados como parâmetros para execução de script. Nesse caso, os dados são fornecidos ao implantar os modelos e, em seguida, esses valores podem ser usados ao executar o script.

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
  }
}
```

Para obter mais informações sobre como usar a extensão de script personalizado, consulte [Extensões de script personalizado com modelos do Resource Manager](virtual-machines-windows-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-step"></a>Próxima etapa
<hr>

[Explorar mais modelos do Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates)




<!--HONumber=Nov16_HO4-->


