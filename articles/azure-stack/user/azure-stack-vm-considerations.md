---
title: Diferenças e considerações para máquinas virtuais no Azure Stack | Microsoft Docs
description: Saiba mais sobre as diferenças e considerações ao trabalhar com máquinas virtuais no Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2018
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 12/19/2018
ms.openlocfilehash: 421e3bf4465f5aa9aafc4ad666af2178faedb7c3
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55245941"
---
# <a name="considerations-for-using-virtual-machines-in-azure-stack"></a>Considerações sobre o uso de máquinas virtuais no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

As máquinas virtuais de pilha do Azure fornecem recursos de computação sob demanda e escalonáveis. Antes de implantar máquinas virtuais (VMs), você deve entender as diferenças entre os recursos de máquina virtual disponíveis no Azure Stack e o Microsoft Azure. Este artigo descreve essas diferenças e identifica as principais considerações de planejamento de implantações de máquina virtual. Para saber mais sobre as diferenças de alto nível entre o Azure Stack e o Azure, consulte a [considerações da chave](azure-stack-considerations.md) artigo.

## <a name="cheat-sheet-virtual-machine-differences"></a>Folha de dados: Diferenças de máquina virtual

| Recurso | (Global) do Azure | Azure Stack |
| --- | --- | --- |
| Imagens de máquina virtual | O Azure Marketplace contém imagens que você pode usar para criar uma máquina virtual. Consulte a [do Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) página para exibir a lista de imagens que estão disponíveis no Azure Marketplace. | Por padrão, existem todas as imagens disponíveis no marketplace do Azure Stack. O administrador da nuvem do Azure Stack deve publicar ou baixe imagens no Marketplace do Azure Stack, antes que os usuários podem usá-los. |
| Tamanhos de máquina virtual | O Azure suporta uma ampla variedade de tamanhos para máquinas virtuais. Para saber mais sobre as opções e os tamanhos disponíveis, consulte o [tamanhos de máquinas virtuais do Windows](../../virtual-machines/virtual-machines-windows-sizes.md) e [tamanhos de máquina virtual Linux](../../virtual-machines/linux/sizes.md) tópicos. | O Azure Stack oferece suporte a um subconjunto de tamanhos de máquinas virtuais que estão disponíveis no Azure. Para exibir a lista de tamanhos com suporte, consulte o [tamanhos de máquina virtual](#virtual-machine-sizes) seção deste artigo. |
| Cotas da máquina virtual | [Limites de cota](../../azure-subscription-service-limits.md#service-specific-limits) são definidas pela Microsoft | O administrador da nuvem do Azure Stack deve atribuir cotas antes que eles oferecem as máquinas virtuais a seus usuários. |
| Extensões de máquina virtual |O Azure suporta uma ampla variedade de extensões de máquina virtual. Para saber mais sobre as extensões disponíveis, consulte o [recursos e extensões de máquina virtual](../../virtual-machines/windows/extensions-features.md) artigo.| O Azure Stack dá suporte a um subconjunto de extensões que estão disponíveis no Azure e da extensão possuem versões específicas. O administrador da nuvem do Azure Stack pode escolher quais extensões a serem disponibilizados para seus usuários. Para exibir a lista de extensões com suporte, consulte o [extensões de máquina virtual](#virtual-machine-extensions) seção deste artigo. |
| Rede de máquina virtual | Endereços IP públicos atribuídos à máquina virtual de locatário são acessíveis pela Internet.<br><br><br>Máquinas virtuais do Azure tem um nome DNS fixado | Endereços IP públicos atribuídos a uma máquina virtual de locatário são acessíveis no ambiente do Kit de desenvolvimento do Azure Stack apenas. Um usuário deve ter acesso para o Kit de desenvolvimento do Azure Stack por meio [RDP](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) ou [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) para se conectar a uma máquina virtual que é criada no Azure Stack.<br><br>As máquinas virtuais criadas em uma instância específica do Azure Stack ter um nome DNS com base no valor que é configurado pelo administrador de nuvem. |
| Armazenamento de máquina virtual | Dá suporte a [discos gerenciados.](../../virtual-machines/windows/managed-disks-overview.md) | Discos gerenciados têm suporte no Azure Stack com versão 1808 e posterior. |
| Desempenho de discos de máquina virtual | Depende do tamanho e tipo de disco. | Depende do tamanho VM da VM que os discos estão anexados para referir-se a [tamanhos de máquina Virtual com suporte no Azure Stack](azure-stack-vm-sizes.md) artigo.
| Versões de API | O Azure sempre tem as últimas versões de API para todos os recursos de máquina virtual. | O Azure Stack dá suporte a serviços específicos do Azure e as versões de API específicas para esses serviços. Para exibir a lista de versões de API com suporte, consulte o [as versões de API](#api-versions) seção deste artigo. |
| Serviço de Metadados de Instância do Azure | O Serviço de metadados de instância do Azure fornece informações sobre instâncias da máquina virtual em execução que podem ser usadas para gerenciar e configurar suas máquinas virtuais.  | Não há suporte para o serviço de metadados de instância no Azure Stack. |
|Conjuntos de disponibilidade de máquinas virtuais|Vários domínios de falha (2 ou 3 por região)<br>Vários domínios de atualização<br>Suporte a discos gerenciados|Vários domínios de falha (2 ou 3 por região)<br>Vários domínios de atualização (até 20)<br>Não há suporte de disco gerenciado|
|conjuntos de escala de máquina virtual|Dimensionamento automático com suporte|Dimensionamento automático não tem suportado.<br>Adicione mais instâncias para um conjunto de dimensionamento usando o portal, modelos do Resource Manager ou PowerShell.

## <a name="virtual-machine-sizes"></a>Tamanhos de máquina virtual

O Azure Stack impõe limites de recursos para evitar o consumo de recursos (servidor local e o nível de serviço). Esses limites melhoram a experiência de locatário, reduzindo o impacto de consumo de recursos por outros locatários.

- Para a saída de rede da VM, há limites de largura de banda em vigor. CAPS no Azure Stack são o mesmo que os limites no Azure.
- Para recursos de armazenamento do Azure Stack implementa limites de IOPS de armazenamento para evitar básico excesso de consumo de recursos por locatários para acesso de armazenamento.
- Para VMs com vários discos de dados anexados, a taxa de transferência máxima de cada disco de dados é 2300 IOPS para SSDs e HDDs de 500 IOPS.

A tabela a seguir lista as VMs que têm suporte no Azure Stack, juntamente com sua configuração:

| Type           | Tamanho          | Intervalo de tamanhos com suporte |
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
|Otimizado para memória|Série DSv2-  |[DS11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dsv2)    |

Tamanhos de máquina virtual e suas quantidades de recursos associado são consistentes entre o Azure Stack e o Azure. Isso inclui a quantidade de memória, o número de núcleos e o número/tamanho de discos de dados que podem ser criados. No entanto, o desempenho de VMs com o mesmo tamanho depende das características de subjacentes de um determinado ambiente do Azure Stack.

## <a name="virtual-machine-extensions"></a>Extensões de máquina virtual

 O Azure Stack inclui um pequeno conjunto de extensões. Atualizações e extensões adicionais estão disponíveis por meio de sindicalização do Marketplace.

Use o seguinte script do PowerShell para obter a lista de extensões de máquina virtual que estão disponíveis em seu ambiente do Azure Stack:

```powershell
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

## <a name="api-versions"></a>Versões de API

Recursos de máquina virtual no Azure Stack suportam as seguintes versões de API:

![Tipos de recursos VM](media/azure-stack-vm-considerations/vm-resoource-types.png)

Você pode usar o seguinte script do PowerShell para obter as versões de API para os recursos de máquina virtual que estão disponíveis em seu ambiente do Azure Stack:

```powershell
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like “Microsoft.compute”}
```

A lista de tipos de recursos com suporte e as versões de API pode variar se o operador de nuvem atualiza seu ambiente do Azure Stack para uma versão mais recente.

## <a name="windows-activation"></a>Ativação do Windows

Produtos do Windows devem ser usados de acordo com os direitos de uso do produto e os termos de licença do Microsoft. Usa o Azure Stack [ativação automática de VM](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v%3dws.11)) (AVMA) para ativar o Windows Server VMs (máquinas virtuais).

- Host de pilha do Azure ativa Windows com chaves AVMA para Windows Server 2016. Todas as VMs que executam o Windows Server 2012 R2 ou posterior são automaticamente ativadas.
- As VMs que executam o Windows Server 2012 ou anterior não são automaticamente ativadas e devem ser ativadas usando [ativação da MAK](https://technet.microsoft.com/library/ff793438.aspx). Para usar a ativação da MAK, você deve fornecer sua própria chave de produto.

Microsoft Azure usa a ativação do KMS para ativar máquinas virtuais do Windows. Se você mover uma VM do Azure Stack para Azure e encontrar problemas de ativação, consulte [problemas de ativação de máquina virtual de solução de problemas do Azure Windows](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-activation-problems). Informações adicionais podem ser encontradas na [falhas de ativação do Windows de solução de problemas em VMs do Azure](https://blogs.msdn.microsoft.com/mast/2017/06/14/troubleshooting-windows-activation-failures-on-azure-vms/) postagem do blog da equipe de suporte do Azure.

## <a name="next-steps"></a>Próximas etapas

[Criar uma máquina virtual do Windows com o PowerShell no Azure Stack](azure-stack-quick-create-vm-windows-powershell.md)
