---
title: Gerenciamento remoto do Windows por HTTPS para o Azure | Microsoft Docs
description: .
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: pbutlerm
ms.openlocfilehash: 99c41dc010ae589cc43c093646fd1c05c1333f7e
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265117"
---
# <a name="windows-remote-management-over-https"></a>Gerenciamento remoto do Windows por HTTPS

Esta seção explica como configurar uma VM hospedada no Azure, com base no Windows, para que ela possa ser gerenciada e implantada remotamente com o PowerShell.  Para habilitar a comunicação remota do PowerShell, a VM de destino precisa expor um ponto de extremidade HTTPS do WinRM (Gerenciamento Remoto do Windows).  Para obter mais informações sobre a comunicação remota do PowerShell, confira [Executando comandos remotos](https://docs.microsoft.com/powershell/scripting/core-powershell/running-remote-commands?view=powershell-6).  Para obter mais informações sobre o WinRM, confira [Gerenciamento Remoto do Windows](https://docs.microsoft.com/windows/desktop/WinRM/portal).

Se você tiver criado uma VM usando uma das abordagens "clássicas" do Azure – o portal do Azure Service Manager ou a [API de Gerenciamento de Serviços do Azure](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100)) preterida – ela estará configurada automaticamente com um ponto de extremidade do WinRM.  No entanto, se você criar uma VM usando qualquer uma das abordagens "modernas” do Azure, a VM *não* estará configurada para usar WinRM por HTTPS.  

- Usando o [portal do Azure](https://portal.azure.com/), normalmente de uma base aprovada, conforme descrito na seção [Criar um VHD compatível com o Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd)
- [Usando os modelos do Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)
- Usando o shell de comando do Azure PowerShell ou da CLI do Azure.  Para obter exemplos, confira [Início Rápido: criar uma máquina virtual do Windows no Azure com o PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell) e o [Início Rápido: criar uma máquina virtual do Linux com a CLI do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli).

Esse ponto de extremidade do WinRM também é necessário para executar o kit da ferramenta de certificação para a integração da VM, conforme descrito em [Certificar sua imagem de VM](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-certify-vm).

Por outro lado, normalmente, as VMs do Linux são gerenciadas remotamente usando a [CLI do Azure](https://docs.microsoft.com/cli/azure) ou os comandos do Linux em um console SSH.  O Azure também fornece vários métodos alternativos para [executar scripts em sua VM do Linux](https://docs.microsoft.com/azure/virtual-machines/linux/run-scripts-in-vm).  Para cenários mais complexos, há uma série de soluções de integração e automação disponíveis para VMs baseadas no Windows ou no Linux.


## <a name="configure-and-deploy-with-winrm"></a>Configurar e implantar com o WinRM

O ponto de extremidade do WinRM de uma VM baseada no Windows pode ser configurado durante dois estágios diferentes de seu desenvolvimento:

- Durante a criação – durante a implantação de uma VM em um VHD existente.  Essa é a abordagem preferencial para novas ofertas.  Essa abordagem exige a criação de um certificado do Azure, usando modelos do Azure Resource Manager fornecidos e executando scripts do PowerShell personalizados. 
- Após a implantação – em uma VM existente, hospedada no Azure.  Use essa abordagem se você já tiver uma solução de VM implantada no Azure e precisar habilitar o gerenciamento remoto do Windows para ela.  Essa abordagem requer alterações manuais no portal do Azure e a execução de um script na VM de destino. 


## <a name="next-steps"></a>Próximas etapas
Se você estiver criando uma VM, habilite o WinRM durante a [implantação da VM por meio de seus VHDs](./cpp-deploy-vm-vhd.md).  Caso contrário, o WinRM poderá ser habilitado em uma VM existente  
