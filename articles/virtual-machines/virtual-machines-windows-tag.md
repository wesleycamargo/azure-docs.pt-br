<properties
   pageTitle="Como Marcar uma VM | Microsoft Azure"
   description="Aprenda a marcar uma máquina virtual do Windows criada no Azure usando o modelo de implantação do Gerenciador de Recursos"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="11/10/2015"
   ms.author="dkshir;memccror"/>

# Como marcar uma máquina virtual do Windows no Azure


Este artigo descreve as diferentes maneiras de marcar uma máquina virtual do Windows no Azure por meio do Azure Resource Manager. As marcas são pares de chave/valor definidos pelo usuário que podem ser colocados diretamente em um recurso ou grupo de recursos. Atualmente, o Azure oferece suporte a até 15 marcas por recurso e grupo de recursos. As marcas podem ser colocadas em um recurso no momento da criação ou adicionadas a um recurso existente. Observe que as marcas têm suporte apenas a recursos criados por meio do Azure Resource Manager. Se quiser marcar uma máquina virtual Linux, consulte [Como marcar uma máquina virtual Linux no Azure](virtual-machines-linux-tag.md).

[AZURE.INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## Marcação com o PowerShell

Para criar, adicionar e excluir marcas pelo PowerShell, primeiramente, você precisa configurar o [ambiente do PowerShell com o Gerenciador de Recursos do Azure][]. Depois de concluir a instalação, você pode colocar marcas em recursos de Computação, Rede e Armazenamento na criação ou depois que o recurso for criado via PowerShell. Este artigo se concentra na exibição/edição de marcas colocadas nas Máquinas Virtuais.

Primeiramente, navegue para uma Máquina Virtual usando o cmdlet `Get-AzureVM`.

        PS C:\> Get-AzureVM -ResourceGroupName "MyResourceGroup" -Name "MyWindowsVM"

Se sua Máquina Virtual já contiver marcas, você verá todas elas no seu recurso:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Se desejar adicionar marcas por meio do PowerShell, você poderá usar o comando `Set-AzureResource`. Observe que, ao atualizar marcas pelo PowerShell, as marcas são atualizadas como um todo. Desse modo, se estiver adicionando uma marca a um recurso que já tenha marcas, você precisará incluir todas as marcas que deseja que sejam colocadas no recurso. Veja abaixo um exemplo de como adicionar mais marcas a um recurso usando cmdlets do PowerShell.

Este primeiro cmdlet define todas as marcas colocadas em *MyWindowsVM* para a variável *tags* usando as funções `Get-AzureResource` e `Tags`. Observe que o parâmetro `ApiVersion` é opcional; se não for especificado, a última versão de API do provedor de recursos é usada.

        PS C:\> $tags = (Get-AzureResource -Name MyWindowsVM -ResourceGroupName MyResourceGroup -ResourceType "Microsoft.Compute/virtualmachines" -ApiVersion 2015-05-01-preview).Tags

O segundo comando exibe as marcas para a variável fornecida.

        PS C:\> $tags

        Name		Value
        ----                           -----
        Value		MyDepartment
        Name		Department
        Value		MyApp1
        Name		Application
        Value		MyName
        Name		Created By
        Value		Production
        Name		Environment

O terceiro comando adiciona uma marca extra à variável *tags*. Observe o uso de **+=** para acrescentar o novo par de Chave/Valor à lista *tags*.

        PS C:\> $tags +=@{Name="Location";Value="MyLocation"}

O quarto comando define todas as marcas definidas na variável *tags* para o recurso determinado. Nesse caso, é MyWindowsVM.

        PS C:\> Set-AzureResource -Name MyWindowsVM -ResourceGroupName MyResourceGroup -ResourceType "Microsoft.Compute/VirtualMachines" -ApiVersion 2015-05-01-preview -Tag $tags

O quinto comando exibe todas as marcas no recurso. Como você pode ver, *Location* agora está definido como uma marca com *MyLocation* como valor.

        PS C:\> (Get-AzureResource -ResourceName "MyWindowsVM" -ResourceGroupName "MyResourceGroup" -ResourceType "Microsoft.Compute/VirtualMachines" -ApiVersion 2015-05-01-preview).Tags

        Name		Value
        ----                           -----
        Value		MyDepartment
        Name		Department
        Value		MyApp1
        Name		Application
        Value		MyName
        Name		Created By
        Value		Production
        Name		Environment
        Value		MyLocation
        Name		Location

Para saber mais sobre marcação usando o PowerShell, confira [Cmdlets de recursos do Azure][].

## Próximas etapas

* Para saber mais sobre como marcar seus recursos do Azure, consulte [Visão geral do Gerenciador de Recursos do Azure][] e [Usando marcas para organizar os Recursos do Azure][].
* Para ver como as marcas podem ajudá-lo a gerenciar seu uso de recursos do Azure, consulte [Noções básicas de sua fatura do Azure][] e [Obter informações sobre o consumo de recursos do Microsoft Azure][].

[ambiente do PowerShell com o Gerenciador de Recursos do Azure]: ../powershell-azure-resource-manager.md
[Cmdlets de recursos do Azure]: https://msdn.microsoft.com/library/azure/dn757692.aspx
[Visão geral do Gerenciador de Recursos do Azure]: ../resource-group-overview.md
[Usando marcas para organizar os Recursos do Azure]: ../resource-group-using-tags.md
[Noções básicas de sua fatura do Azure]: ../billing-understand-your-bill.md
[Obter informações sobre o consumo de recursos do Microsoft Azure]: ../billing-usage-rate-card-overview.md

<!-----------HONumber=AcomDC_0330_2016-->