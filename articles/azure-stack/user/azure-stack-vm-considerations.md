---
title: "As diferenças e considerações para máquinas virtuais no Azure pilha | Microsoft Docs"
description: "Saiba mais sobre as diferenças e considerações ao trabalhar com máquinas virtuais na pilha do Azure."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 6613946D-114C-441A-9F74-38E35DF0A7D7
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: mabrigg
ms.openlocfilehash: fe655facf4da99d951a430db8ce603cc0ec7f224
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="considerations-for-virtual-machines-in-azure-stack"></a>Considerações para máquinas virtuais na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Máquinas virtuais são uma sob demanda, recursos escalonáveis de computação oferecidos pela pilha do Azure. Quando você usar máquinas virtuais, você deve entender que há diferenças entre os recursos que estão disponíveis no Azure e a pilha do Azure. Este artigo fornece uma visão geral das considerações exclusivas para máquinas virtuais e seus recursos na pilha do Azure. Para saber mais sobre as diferenças de alto nível entre a pilha do Azure e o Azure, consulte o [chave considerações](azure-stack-considerations.md) artigo.

## <a name="cheat-sheet-virtual-machine-differences"></a>Roteiro: diferenças de máquina Virtual

| Recurso | Azure (global) | Azure Stack |
| --- | --- | --- |
| Imagens de máquinas virtuais | O Azure Marketplace contém imagens que você pode usar para criar uma máquina virtual. Consulte o [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) página para exibir a lista de imagens que estão disponíveis no Azure Marketplace. | Por padrão, não existem quaisquer imagens disponíveis no mercado de pilha do Azure. O administrador da nuvem do Azure pilha deve publicar ou baixar imagens Marketplace do Azure pilha antes que os usuários podem usá-los. |
| Tamanhos de máquina virtual | Azure oferece suporte a uma ampla variedade de tamanhos de máquinas virtuais. Para saber mais sobre as opções e os tamanhos disponíveis, consulte o [tamanhos de máquinas virtuais do Windows](../../virtual-machines/virtual-machines-windows-sizes.md) e [tamanhos de máquina virtual Linux](../../virtual-machines/linux/sizes.md) tópicos. | A pilha do Azure suporta um subconjunto de tamanhos de máquinas virtuais que estão disponíveis no Azure. Para exibir a lista de tamanhos com suporte, consulte o [tamanhos de máquina virtual](#virtual-machine-sizes) deste artigo. |
| Cotas da máquina virtual | [Limites de cota](../../azure-subscription-service-limits.md#service-specific-limits) são definidas pela Microsoft | O administrador da nuvem do Azure pilha deve atribuir cotas para máquinas virtuais oferecem aos usuários. |
| Extensões da máquina virtual |Azure oferece suporte a uma ampla variedade de extensões de máquina virtual. Para saber mais sobre as extensões disponíveis, consulte o [extensões de máquina virtual e recursos](../../virtual-machines/windows/extensions-features.md) artigo.| A pilha do Azure suporta um subconjunto de extensões que estão disponíveis no Azure e da extensão possuem versões específicas. O administrador da nuvem do Azure pilha pode escolher quais extensões a serem disponibilizados para seus usuários. Para exibir a lista de extensões com suporte, consulte o [extensões de máquina virtual](#virtual-machine-extensions) deste artigo. |
| Rede de máquina virtual | Endereços IP públicos atribuídos à máquina virtual são acessíveis pela Internet.<br><br><br>Máquinas virtuais do Azure tem um nome DNS fixado | Endereços IP públicos atribuídos a uma máquina virtual de locatário são acessíveis no ambiente somente do Kit de desenvolvimento de pilha do Azure. Um usuário deve ter acesso para o Kit de desenvolvimento de pilha do Azure por meio de [RDP](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) ou [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) para se conectar a uma máquina virtual que é criada na pilha do Azure.<br><br>Máquinas virtuais criadas em uma instância específica da pilha do Azure tem um nome DNS com base no valor que é configurado pelo administrador de nuvem. |
| Armazenamento de máquina virtual | Dá suporte a [discos gerenciado.](../../virtual-machines/windows/managed-disks-overview.md) | Discos gerenciados ainda não têm suporte na pilha do Azure. |
| Versões de API | O Azure sempre tem as versões de API mais recentes para todos os recursos de máquina virtual. | A pilha do Azure oferece suporte a serviços específicos do Azure e as versões de API específicas para esses serviços. Para exibir a lista de versões de API com suporte, consulte o [as versões de API](#api-versions) deste artigo. |
|Conjuntos de disponibilidade da máquina virtual|Vários domínios de falha (2 ou 3 por região)<br>Vários domínios de atualização<br>Suporte a disco gerenciado|Domínio de falha único<br>Domínio de atualização<br>Não há suporte de disco gerenciado|
|conjuntos de escala de máquina virtual|Suporte para dimensionamento automático|Dimensionamento automático não tem suportado.<br>Adicione mais instâncias em uma escala definida usando o portal, o Gerenciador de recursos de modelos ou o PowerShell.

## <a name="virtual-machine-sizes"></a>Tamanhos de máquina virtual 

O Kit de desenvolvimento de pilha do Azure suporta os seguintes tamanhos de: 

| Tipo | Tamanho | Intervalo de tamanhos com suporte |
| --- | --- | --- |
|Propósito geral |A Básico|A0 - A4|
|Propósito geral |Um padrão|A0 - A7|
|Propósito geral |Série D|D1 - D4|
|Propósito geral |Série Dv2|D1_v2 - D5_v2|
|Propósito geral |Série DS|DS1 - DS4|
|Propósito geral |Série DSv2|DS1_v2 - DS5_v2|
|Otimizado para memória|Série DS|DS11 - DS14|
|Otimizado para memória |Série DSv2|DS11_v2 - DS14_v2|

Tamanhos de máquina virtual e suas quantidades de recursos associado são consistentes entre a pilha do Azure e o Azure. Por exemplo, isso inclui a quantidade de memória, número de núcleos e o número ou o tamanho dos discos de dados que podem ser criadas. No entanto, o desempenho do mesmo tamanho VM na pilha do Azure depende das características subjacentes de um determinado ambiente da pilha do Azure.

## <a name="virtual-machine-extensions"></a>Extensões da máquina virtual 

 O Kit de desenvolvimento de pilha do Azure suporta as seguintes versões de extensão de máquina virtual:

![Extensões de VM](media/azure-stack-vm-considerations/vm-extensions.png)

Use o seguinte script do PowerShell para obter a lista de extensões de máquinas virtuais que estão disponíveis em seu ambiente de pilha do Azure:

```powershell 
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize 
```

## <a name="api-versions"></a>Versões de API 

Recursos de máquina virtual no Kit de desenvolvimento de pilha do Azure suportam as seguintes versões de API:

![Tipos de recurso VM](media/azure-stack-vm-considerations/vm-resoource-types.png)

Você pode usar o seguinte script do PowerShell para obter as versões de API para os recursos de máquina virtual que estão disponíveis em seu ambiente de pilha do Azure:

```powershell 
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like “Microsoft.compute”}
```
A lista de tipos de recursos com suporte e as versões de API pode variar se o operador de nuvem atualiza seu ambiente de pilha do Azure para uma versão mais recente.

## <a name="next-steps"></a>Próximas etapas

[Criar uma máquina virtual do Windows com o PowerShell na pilha do Azure](azure-stack-quick-create-vm-windows-powershell.md)
