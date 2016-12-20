---
title: Como marcar uma VM | Microsoft Docs
description: "Aprenda a marcar uma máquina virtual do Windows criada no Azure usando o modelo de implantação do Gerenciador de Recursos"
services: virtual-machines-windows
documentationcenter: 
author: mmccrory
manager: timlt
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
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 97efb86f31f75d2214e4169d88b792ab189354ed


---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Como marcar uma máquina virtual do Windows no Azure
Este artigo descreve as diferentes maneiras de marcar uma máquina virtual do Windows no Azure por meio do modelo de implantação do Resource Manager. As marcas são pares de chave/valor definidos pelo usuário que podem ser colocados diretamente em um recurso ou grupo de recursos. Atualmente, o Azure oferece suporte a até 15 marcas por recurso e grupo de recursos. As marcas podem ser colocadas em um recurso no momento da criação ou adicionadas a um recurso existente. Observe que as marcas tem suporte apenas para recursos criados por meio do modelo de implantação do Resource Manager. Se quiser marcar uma máquina virtual Linux, consulte [Como marcar uma máquina virtual Linux no Azure](virtual-machines-linux-tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Marcação com o PowerShell
Para criar, adicionar e excluir marcações pelo PowerShell, primeiramente, você precisa configurar o [ambiente do PowerShell com o Gerenciador de Recursos do Azure][ambiente do PowerShell com o Gerenciador de Recursos do Azure]. Depois de concluir a instalação, você pode colocar marcas em recursos de Computação, Rede e Armazenamento na criação ou depois que o recurso for criado via PowerShell. Este artigo se concentra na exibição/edição de marcas colocadas nas Máquinas Virtuais.

Primeiramente, navegue para uma Máquina Virtual usando o cmdlet `Get-AzureRmVM` .

        PS C:\> Get-AzureRmVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Se sua Máquina Virtual já contiver marcas, você verá todas elas no seu recurso:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Se desejar adicionar marcas por meio do PowerShell, você poderá usar o comando `Set-AzureRmResource` . Observe que, ao atualizar marcas pelo PowerShell, as marcas são atualizadas como um todo. Desse modo, se estiver adicionando uma marca a um recurso que já tenha marcas, você precisará incluir todas as marcas que deseja que sejam colocadas no recurso. Veja abaixo um exemplo de como adicionar mais marcas a um recurso usando cmdlets do PowerShell.

Este primeiro cmdlet define todas as marcações colocadas em *MyTestVM* para a variável *$tags* usando as propriedades `Get-AzureRmResource` e `Tags`.

        PS C:\> $tags = (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

O segundo comando exibe as marcas para a variável fornecida.

        PS C:\> $tags

        Name        Value
        ----                           -----
        Value        MyDepartment
        Name        Department
        Value        MyApp1
        Name        Application
        Value        MyName
        Name        Created By
        Value        Production
        Name        Environment

O terceiro comando adiciona uma marcação extra à variável *$tags* . Observe o uso de **+=** para acrescentar o novo par de chave/valor à lista *$tags* .

        PS C:\> $tags += @{Name="Location";Value="MyLocation"}

O quarto comando define todas as marcações definidas na variável *$tags* para o recurso determinado. Nesse caso, é MyTestVM.

        PS C:\> Set-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

O quinto comando exibe todas as marcas no recurso. Como você pode ver, *Location* agora está definido como uma marcação com *MyLocation* como valor.

        PS C:\> (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

        Name        Value
        ----                           -----
        Value        MyDepartment
        Name        Department
        Value        MyApp1
        Name        Application
        Value        MyName
        Name        Created By
        Value        Production
        Name        Environment
        Value        MyLocation
        Name        Location

Para saber mais sobre como marcar por meio do PowerShell, confira [Cmdlets de recursos do Azure][Cmdlets de recursos do Azure] (Cmdlets de recursos do Azure).

[!INCLUDE [virtual-machines-common-tag-usage](../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre a marcação dos recursos do Azure, confira [Visão geral do Azure Resource Manager][Visão geral do Azure Resource Manager] e [Usando marcas para organizar os recursos do Azure][Usando marcas para organizar os recursos do Azure] (Usando marcas para organizar os Recursos do Azure).
* Para ver como as marcações podem ajudar a gerenciar o uso de recursos do Azure, confira [Noções básicas de sua fatura do Azure][Noções básicas de sua fatura do Azure] (Noções básicas sobre a Cobrança do Azure) e [Obtenha informações sobre o consumo de recursos do Microsoft Azure][Obtenha informações sobre o consumo de recursos do Microsoft Azure].

[ambiente do PowerShell com o Gerenciador de Recursos do Azure]: ../powershell-azure-resource-manager.md
[Cmdlets de recursos do Azure]: https://msdn.microsoft.com/library/azure/dn757692.aspx
[Visão geral do Azure Resource Manager]: ../azure-resource-manager/resource-group-overview.md
[Usando marcas para organizar os recursos do Azure]: ../resource-group-using-tags.md
[Noções básicas de sua fatura do Azure]: ../billing/billing-understand-your-bill.md
[Obtenha informações sobre o consumo de recursos do Microsoft Azure]: ../billing-usage-rate-card-overview.md



<!--HONumber=Nov16_HO3-->


