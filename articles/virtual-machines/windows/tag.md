---
title: Como marcar um recurso de máquina virtual do Windows no Azure | Microsoft Docs
description: Aprenda a marcar uma máquina virtual do Windows criada no Azure usando o modelo de implantação do Gerenciador de Recursos
services: virtual-machines-windows
documentationcenter: ''
author: mmccrory
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 56d17f45-e4a7-4d84-8022-b40334ae49d2
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
ms.openlocfilehash: b6c63a868b7697522c708a4db483395a537a82cf
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56823443"
---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Como marcar uma máquina virtual do Windows no Azure
Este artigo descreve as diferentes maneiras de marcar uma máquina virtual do Windows no Azure por meio do modelo de implantação do Resource Manager. As marcas são pares de chave/valor definidos pelo usuário que podem ser colocados diretamente em um recurso ou grupo de recursos. Atualmente, o Azure oferece suporte a até 15 marcas por recurso e grupo de recursos. As marcas podem ser colocadas em um recurso no momento da criação ou adicionadas a um recurso existente. Observe que as marcas tem suporte apenas para recursos criados por meio do modelo de implantação do Resource Manager. Se quiser marcar uma máquina virtual Linux, consulte [Como marcar uma máquina virtual Linux no Azure](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Marcação com o PowerShell
Para criar, adicionar e excluir marcas pelo PowerShell, primeiramente, você precisa configurar o [ambiente do PowerShell com o Azure Resource Manager][PowerShell environment with Azure Resource Manager]. Depois de concluir a instalação, você pode colocar marcas em recursos de Computação, Rede e Armazenamento na criação ou depois que o recurso for criado via PowerShell. Este artigo se concentra na exibição/edição de marcas colocadas nas Máquinas Virtuais.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

Primeiramente, navegue para uma Máquina Virtual usando o cmdlet `Get-AzVM` .

        PS C:\> Get-AzVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Se sua Máquina Virtual já contiver marcas, você verá todas elas no seu recurso:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Se desejar adicionar marcas por meio do PowerShell, você poderá usar o comando `Set-AzResource` . Observe que, ao atualizar marcas pelo PowerShell, as marcas são atualizadas como um todo. Desse modo, se estiver adicionando uma marca a um recurso que já tenha marcas, você precisará incluir todas as marcas que deseja que sejam colocadas no recurso. Veja abaixo um exemplo de como adicionar mais marcas a um recurso usando cmdlets do PowerShell.

Este primeiro cmdlet define todas as marcações colocadas em *MyTestVM* para a variável *$tags* usando as propriedades `Get-AzResource` e `Tags`.

        PS C:\> $tags = (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

O segundo comando exibe as marcas para a variável fornecida.

```
    PS C:\> $tags
    
    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
```

O terceiro comando adiciona uma marcação extra à variável *$tags* . Observe o uso de **+=** para acrescentar o novo par de chave/valor à lista *$tags* .

        PS C:\> $tags += @{Location="MyLocation"}

O quarto comando define todas as marcações definidas na variável *$tags* para o recurso determinado. Nesse caso, é MyTestVM.

        PS C:\> Set-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

O quinto comando exibe todas as marcas no recurso. Como você pode ver, *Location* agora está definido como uma marcação com *MyLocation* como valor.

```
    PS C:\> (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
    Location      MyLocation
```

Para saber mais sobre marcação usando o PowerShell, confira [Cmdlets de recursos do Azure][Azure Resource Cmdlets].

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre como marcar seus recursos do Azure, consulte [Visão geral do Azure Resource Manager][Azure Resource Manager Overview] e [Usando marcas para organizar os Recursos do Azure][Using Tags to organize your Azure Resources].
* Para ver como as marcas podem ajudá-lo a gerenciar seu uso de recursos do Azure, consulte [Noções básicas de sua fatura do Azure][Understanding your Azure Bill] e [Obtenha informações sobre o consumo de recursos do Microsoft Azure][Gain insights into your Microsoft Azure resource consumption].

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/manage-resources-powershell.md
[Azure Resource Cmdlets]: https://docs.microsoft.com/powershell/module/az.resources/
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
