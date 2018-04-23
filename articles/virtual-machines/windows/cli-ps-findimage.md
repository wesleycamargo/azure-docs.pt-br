---
title: Selecionar imagens de VM do Windows no Azure | Microsoft Docs
description: Saiba como usar o Azure PowerShell para determinar editor, oferta, SKU e versão de imagens de VM do Marketplace.
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/28/2018
ms.author: danlep
ms.openlocfilehash: 858b5976ef422ab410f42c44f94473aa4fd10970
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2018
---
# <a name="how-to-find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Como localizar imagens de VM do Windows no Azure Marketplace com o Azure PowerShell

Este tópico descreve como usar o Azure PowerShell para localizar imagens de VM no Azure Marketplace. Use essas informações para especificar uma imagem do Marketplace quando você cria uma VM programaticamente com o PowerShell, modelos do Gerenciador de Recursos ou outras ferramentas.

Verifique se você tem a versão mais recente do [módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps) instalada e configurada.

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="table-of-commonly-used-windows-images"></a>Tabela das imagens do Windows mais usadas
| Publicador | Oferta | Sku |
|:--- |:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-Server-Core |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-with-Containers |
| MicrosoftWindowsServer |WindowsServer |2016-Nano-Server |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2008-R2-SP1 |
| MicrosoftDynamicsNAV |DynamicsNAV |2017 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2016 |
| MicrosoftSQLServer |SQL2016-WS2016 |Enterprise |
| MicrosoftSQLServer |SQL2014SP2-WS2012R2 |Enterprise |
| MicrosoftWindowsServerHPCPack |WindowsServerHPCPack |2012R2 |
| MicrosoftWindowsServerEssentials |WindowsServerEssentials |WindowsServerEssentials |

## <a name="navigate-the-images"></a>Navegar pelas imagens

Outra maneira de localizar uma imagem em um local é executar os cmdlets [Get-AzureRMVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher), [Get-AzureRMVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer) e [Get-AzureRMVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) em sequência. Com esses comandos, você determina estes valores:

1. Liste os editores de imagem.
2. Para um determinado editor, liste suas ofertas.
3. Para uma determinada oferta, liste seus SKUs.

Em seguida, para uma SKU selecionada, execute [Get-AzureRMVMImage](/powershell/module/azurerm.compute/get-azurermvmimage) para listar as versões para implantar.

Primeiro, lista os editores com os seguintes comandos:

```powershell
$locName="<Azure location, such as West US>"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
```

Preencha o nome do editor escolhido e execute os seguintes comandos:

```powershell
$pubName="<publisher>"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Preencha o nome da oferta escolhida e execute os seguintes comandos:

```powershell
$offerName="<offer>"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

Preencha o nome da SKU escolhida e execute os seguintes comandos:

```powershell
$skuName="<SKU>"
Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Sku $skuName | Select Version
```

Na saída do comando `Get-AzureRMVMImage`, você pode selecionar uma imagem de versão para implantar uma nova máquina virtual.

Os comandos a seguir mostram um exemplo completo:

```powershell
$locName="West US"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName

```

Saída:

```
PublisherName
-------------
a10networks
aiscaler-cache-control-ddos-and-url-rewriting-
alertlogic
AlertLogic.Extension
Barracuda.Azure.ConnectivityAgent
barracudanetworks
basho
boxless
bssw
Canonical
...
```

Para o editor *MicrosoftWindowsServer*:

```powershell
$pubName="MicrosoftWindowsServer"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Saída:

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServerSemiAnnual
```

Para a oferta de *WindowsServer*:

```powershell
$offerName="WindowsServer"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

Saída:

```
Skus
----
2008-R2-SP1
2008-R2-SP1-smalldisk
2012-Datacenter
2012-Datacenter-smalldisk
2012-R2-Datacenter
2012-R2-Datacenter-smalldisk
2016-Datacenter
2016-Datacenter-Server-Core
2016-Datacenter-Server-Core-smalldisk
2016-Datacenter-smalldisk
2016-Datacenter-with-Containers
2016-Datacenter-with-RDSH
2016-Nano-Server
```

Em seguida, para a SKU *2016-Datacenter*:

```powershell
$skuName="2016-Datacenter"
Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Sku $skuName | Select Version
```

Agora você pode combinar o editor, a oferta, a SKU e a versão selecionados em um URN (valores separados por :). Passe esse URN com o parâmetro `--image` quando criar uma VM com o cmdlet [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Lembre-se de que é possível substituir, como opção, o número de versão no URN por "mais recente". Essa versão é sempre a versão mais recente da imagem. Você também pode usar o URN com o cmdlet do PowerShell [Set-AzureRMVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage). 

Se você implantar uma VM com um modelo do Gerenciador de Recursos, defina os parâmetros de imagem individualmente nas propriedades `imageReference`. Consulte a [referência de modelo](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]


### <a name="view-plan-properties"></a>Exibir propriedades de plano

Para exibir as informações do plano de compra de uma imagem, execute o cmdlet `Get-AzureRMVMImage`. Se a propriedade `PurchasePlan` na saída não for `null`, isso significa que a imagem tem termos que você precisa aceitar antes da implantação programática.  

Por exemplo, a imagem do Windows Server 2016 Datacenter não tem termos adicionais, porque as informações de `PurchasePlan` são `null`:

```powershell
$version = "2016.127.20170406"
Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Skus $skuName -Version $version
```

Saída:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/
                   Versions/2016.127.20170406
Location         : westus
PublisherName    : MicrosoftWindowsServer
Offer            : WindowsServer
Skus             : 2016-Datacenter
Version          : 2016.127.20170406
FilterExpression :
Name             : 2016.127.20170406
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : null
DataDiskImages   : []

```

Executar um comando semelhante para a imagem da Máquina Virtual de Ciência de Dados - Windows 2016 mostra as seguintes `PurchasePlan` propriedades: `name`, `product` e `publisher`. (Algumas imagens também têm uma propriedade `promotion code`.) Para implantar essa imagem, consulte as seções a seguir para aceitar os termos e habilitar a implantação programática.

```powershell
Get-AzureRMVMImage -Location "westus" -Publisher "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
```

Saída:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/microsoft-ads/ArtifactTypes/VMIma
                   ge/Offers/windows-data-science-vm/Skus/windows2016/Versions/0.2.02
Location         : westus
PublisherName    : microsoft-ads
Offer            : windows-data-science-vm
Skus             : windows2016
Version          : 0.2.02
FilterExpression :
Name             : 0.2.02
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : {
                     "publisher": "microsoft-ads",
                     "name": "windows2016",
                     "product": "windows-data-science-vm"
                   }
DataDiskImages   : []

```

### <a name="accept-the-terms"></a>Aceitar os termos

Para exibir os termos da licença, use o cmdlet [Get-AzureRmMarketplacetermsGet-AzureRmMarketplaceterms](/powershell/module/azurerm.marketplaceordering/get-azurermmarketplaceterms) e passe os parâmetros do plano de compra. A saída fornece um link para os termos da imagem do Marketplace e mostra se você aceitou os termos anteriormente. Por exemplo: 

```powershell
Get-AzureRmMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

Saída:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 2/23/2018 7:43:00 PM
```

Use o cmlet [Set-AzureRmMarketplaceterms](/powershell/module/azurerm.marketplaceordering/set-azurermmarketplaceterms) para aceitar ou rejeitar os termos. Você só precisa aceitar os termos uma vez por assinatura para a imagem. Por exemplo: 

```powershell

$agreementTerms=Get-AzureRmMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzureRmMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept

```

Saída:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : VNMTRJK3MNJ5SROEG2BYDA2YGECU33GXTD3UFPLPC4BAVKAUL3PDYL3KBKBLG4ZCDJZVNSA7KJWTGMDSYDD6KRLV3LV274DLBJSS4GQ
Accepted          : True
Signdate          : 2/23/2018 7:49:31 PM
```

### <a name="deploy-using-purchase-plan-parameters"></a>Implantar usando os parâmetros de plano de compra
Depois de aceitar os termos da imagem, você pode implantar uma VM na assinatura. Conforme mostrado no trecho a seguir, use o cmdlet [Set-AzureRmVMPlan](/powershell/module/azurerm.compute/set-azurermvmplan) para definir as informações do plano do Marketplace para o objeto da VM. Para obter um script completo criar configurações de rede para a VM e concluir a implantação, consulte os [exemplos de script do PowerShell](powershell-samples.md).

```powershell
...
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace plan information
$vmConfig = Set-AzureRmVMPlan -VM $vmConfig -Publisher "imagePlanPublisher" -Product "imagePlanProduct" -Name "imagePlanName"

$cred=Get-Credential

$vmConfig = Set-AzureRmVMOperatingSystem -Windows -VM $vmConfig -ComputerName "myVM" -Credential $cred

# Set the Marketplace image
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName "imagePublisher" -Offer "imageOffer" -Skus "imageSku" -Version "imageVersion"
...
```
Em seguida, você passa a configuração da VM com os objetos de configuração de rede para o cmdlet `New-AzureRmVM`.

## <a name="next-steps"></a>Próximas etapas
Para criar uma máquina virtual rapidamente com `New-AzureRmVM` usando as informações básicas da imagem, consulte [Criar uma máquina virtual do Windows com o PowerShell](quick-create-powershell.md).

Veja um exemplo de script do PowerShell para [Criar uma máquina virtual totalmente configurada](../scripts/virtual-machines-windows-powershell-sample-create-vm.md).


