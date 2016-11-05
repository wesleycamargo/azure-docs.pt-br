---
title: Sobre imagens de máquinas virtuais do Windows | Microsoft Docs
description: Saiba mais sobre como as imagens são usadas com máquinas virtuais do Windows no Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/21/2016
ms.author: cynthn

---
# Sobre imagens de máquinas virtuais do Windows
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[!INCLUDE [virtual-machines-common-classic-about-images](../../includes/virtual-machines-common-classic-about-images.md)]

## Trabalhando com imagens
Você pode usar o módulo do Azure PowerShell, para gerenciar as imagens disponíveis para sua assinatura do Azure. Você também pode usar o portal clássico do Azure para algumas tarefas de imagem, porém a linha de comando oferece mais opções.

* **Obter todas as imagens**:`Get-AzureVMImage`retorna uma lista de todas as imagens disponíveis na sua assinatura atual: as imagens, incluindo aquelas fornecidos pelo Azure ou parceiros. Isso significa que você pode obter uma lista grande. Os exemplos a seguir mostram como obter uma lista menor.
* **Obter as famílias de imagem**:`Get-AzureVMImage | select ImageFamily` obtém uma lista das famílias de imagem, mostrando as cadeias de caracteres da propriedade **ImageFamily**.
* **Obtenha todas as imagens em uma família específica**: `Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
* **Localizar imagens da VM**: `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` isso funciona com a filtragem da propriedade DataDiskConfiguration, que só se aplica a imagens de VM. Este exemplo também filtra a saída para apenas o nome do rótulo e da imagem.
* **Salvar uma imagem generalizada**: `Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
* **Salvar uma imagem especializada**: `Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`
  >[Azure.Tip] O parâmetro OSState é obrigatório se você quiser criar uma imagem VM, que inclui discos de dados e o disco do sistema operacional. Se você não usar o parâmetro, o cmdlet criará uma imagem do SO. O valor do parâmetro indica se a imagem é generalizada ou especializada, com base em se o disco do sistema operacional foi preparado para reutilização.
* **Excluir uma imagem**: `Remove-AzureVMImage –ImageName "MyOldVmImage"`

## Próximas etapas
Você também pode [criar uma máquina Windows usando o portal clássico](virtual-machines-windows-classic-tutorial.md)

<!---HONumber=AcomDC_0727_2016-->