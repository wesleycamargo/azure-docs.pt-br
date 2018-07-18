---
title: As diferenças e considerações para máquinas virtuais no Azure pilha | Microsoft Docs
description: Saiba mais sobre as diferenças e considerações ao trabalhar com máquinas virtuais na pilha do Azure.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 6613946D-114C-441A-9F74-38E35DF0A7D7
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2018
ms.author: brenduns
ms.openlocfilehash: 324fa19aa97cead44f38d07a2fd0765048cd6238
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605381"
---
# <a name="considerations-for-using-virtual-machines-in-azure-stack"></a>Considerações para usar máquinas virtuais na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Máquinas de virtuais de pilha do Azure fornecem recursos de computação sob demanda e escalonáveis. Antes de implantar máquinas virtuais (VMs), você deve entender as diferenças entre os recursos de máquina virtual disponíveis na pilha do Azure e o Microsoft Azure. Este artigo descreve essas diferenças e identifica as principais considerações de planejamento de implantações de máquina virtual. Para saber mais sobre as diferenças de alto nível entre a pilha do Azure e o Azure, consulte o [chave considerações](azure-stack-considerations.md) artigo.

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
|Conjuntos de disponibilidade da máquina virtual|Vários domínios de falha (2 ou 3 por região)<br>Vários domínios de atualização<br>Suporte a disco gerenciado|Vários domínios de falha (2 ou 3 por região)<br>Vários domínios de atualização (até 20)<br>Não há suporte de disco gerenciado|
|conjuntos de escala de máquina virtual|Suporte para dimensionamento automático|Dimensionamento automático não tem suportado.<br>Adicione mais instâncias em uma escala definida usando o portal, o Gerenciador de recursos de modelos ou o PowerShell.

## <a name="virtual-machine-sizes"></a>Tamanhos de máquina virtual

A pilha do Azure impõe limites de recursos para evitar o consumo de recursos (servidor local e o nível de serviço). Esses limites melhoram a experiência de locatário, reduzindo o impacto de consumo de recursos por outros locatários.

- Para saída de rede da VM, há limites de largura de banda em vigor. Caps na pilha do Azure são iguais as tampas no Azure.
- Para recursos de armazenamento, a pilha do Azure implementa limites de IOPS de armazenamento para evitar excesso básico de recursos pelos locatários para acesso de armazenamento.
- Para VMs com vários discos de dados anexados, a taxa de transferência máxima de cada disco de dados é 500 IOPS para HHDs e IOPS 2300 SSDS.

A tabela a seguir lista as VMs que têm suporte na pilha do Azure junto com sua configuração:

| type           | Tamanho          | Intervalo de tamanhos com suporte |
| ---------------| ------------- | ------------------------ |
|Propósito geral |A Básico        |[A0 - A4](azure-stack-vm-sizes.md#basic-a)                   |
|Propósito geral |Um padrão     |[A0 - A7](azure-stack-vm-sizes.md#standard-a)              |
|Propósito geral |Série D       |[D1 - D4](azure-stack-vm-sizes.md#d-series)              |
|Propósito geral |Série Dv2     |[D1_v2 - D5_v2](azure-stack-vm-sizes.md#ds-series)        |
|Propósito geral |Série DS      |[DS1 - DS4](azure-stack-vm-sizes.md#dv2-series)            |
|Propósito geral |Série DSv2    |[DS1_v2 - DS5_v2](azure-stack-vm-sizes.md#dsv2-series)      |
|Otimizado para memória|Série D       |[D11 - D14](azure-stack-vm-sizes.md#mo-d)            |
|Otimizado para memória|Série DS      |[DS11 - DS14](azure-stack-vm-sizes.md#mo-ds)|
|Otimizado para memória|Série Dv2     |[D11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dv2)     |
|Otimizado para memória|Série de série DSv2-  |[DS11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dsv2)    |

Tamanhos de máquina virtual e suas quantidades de recursos associado são consistentes entre a pilha do Azure e o Azure. Isso inclui a quantidade de memória, o número de núcleos e o número ou o tamanho dos discos de dados que podem ser criadas. No entanto, o desempenho das VMs com o mesmo tamanho depende as características subjacentes de um determinado ambiente da pilha do Azure.

## <a name="virtual-machine-extensions"></a>Extensões da máquina virtual

 A pilha do Azure inclui um pequeno conjunto de extensões. Atualizações e extensões adicionais estão disponíveis por meio de agregação do Marketplace.

Use o seguinte script do PowerShell para obter a lista de extensões de máquinas virtuais que estão disponíveis em seu ambiente de pilha do Azure:

```powershell
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

## <a name="api-versions"></a>Versões de API

Recursos de máquina virtual na pilha do Azure suportam as seguintes versões de API:

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

## <a name="windows-activation"></a>Ativação do Windows

Produtos do Windows devem ser usados de acordo com direitos de uso do produto e os termos de licença da Microsoft. A pilha do Azure usa [ativação automática de VM](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v%3dws.11)) (AVMA) para ativar máquinas virtuais de Windows Server (VMs).

- Host de pilha do Azure ativa Windows com chaves AVMA para Windows Server 2016. Todas as VMs que executam o Windows Server 2012 ou posterior são automaticamente ativadas.
- Máquinas virtuais que execute o Windows Server 2008 R2 não serão ativados automaticamente e deve ser ativado usando [a ativação da MAK](https://technet.microsoft.com/library/ff793438.aspx). Para usar a ativação da MAK, você deve fornecer sua própria chave de produto.

Microsoft Azure usa a ativação do KMS para ativar máquinas virtuais do Windows. Se você mover uma VM da pilha do Azure para o Azure e ocorrência ativar problemas, consulte [problemas de ativação de máquina virtual de solução de problemas do Azure Windows](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-activation-problems). Informações adicionais podem ser encontradas no [falhas de ativação do Windows de solução de problemas em VMs do Azure](https://blogs.msdn.microsoft.com/mast/2017/06/14/troubleshooting-windows-activation-failures-on-azure-vms/) postagem no Blog de equipe de suporte do Azure.

## <a name="next-steps"></a>Próximas etapas

[Criar uma máquina virtual do Windows com o PowerShell na pilha do Azure](azure-stack-quick-create-vm-windows-powershell.md)