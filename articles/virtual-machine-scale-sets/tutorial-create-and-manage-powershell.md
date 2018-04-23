---
title: Tutorial – Criar um conjunto de dimensionamento de máquinas virtuais do Azure | Microsoft Docs
description: Saiba como usar o Azure PowerShell para criar um conjunto de dimensionamento de máquinas virtuais e algumas tarefas de gerenciamento comuns, por exemplo, como iniciar e parar uma instância ou alterar a capacidade do conjunto de dimensionamento.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 80ff76f98e563df14feacafab2420a48611d5828
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/19/2018
---
# <a name="tutorial-create-and-manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Tutorial: Criar e gerenciar um conjunto de dimensionamento da máquinas virtuais usando o Azure PowerShell
Um conjunto de dimensionamento de máquinas virtuais permite implantar e gerenciar um conjunto de máquinas virtuais idênticas de dimensionamento automático. Durante todo o ciclo de vida do conjunto de dimensionamento de uma máquina virtual, você poderá precisar executar uma ou mais tarefas de gerenciamento. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar e se conectar a um conjunto de dimensionamento de máquinas virtuais
> * Selecionar e usar imagens de VM
> * Exibir e usar tamanhos específicos de instância de VM
> * Dimensionar manualmente um conjunto de dimensionamento
> * Executar tarefas comuns de gerenciamento de conjunto de dimensionamento

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 5.6.0 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzureRmAccount` para criar uma conexão com o Azure. 


## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Você deve criar um grupo de recursos antes de criar um conjunto de dimensionamento de máquinas virtuais. Crie um grupo de recursos com o comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Neste exemplo, um grupo de recursos denominado *myResourceGroup* é criado na região *EastUS*. 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Location "EastUS"
```
O nome do grupo de recursos é especificado quando você cria ou modifica um conjunto de dimensionamento ao longo deste tutorial.


## <a name="create-a-scale-set"></a>Criar um conjunto de escala
Primeiro, defina o nome de usuário e a senha de um administrador para as instâncias de VM com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Agora, crie um conjunto de dimensionamento de máquinas virtuais com [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). Para distribuir o tráfego para as instâncias de VM individuais, um balanceador de carga também é criado. O balanceador de carga inclui regras para distribuir o tráfego na porta TCP 80, além de permitir o tráfego de área de trabalho remota na porta TCP 3389 e comunicação remota do PowerShell na porta TCP 5985:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -Location "EastUS" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -Credential $cred
```

Leva alguns minutos para criar e configurar todos os recursos e as instâncias de VM do conjunto de dimensionamento.


## <a name="view-the-vm-instances-in-a-scale-set"></a>Exibir as instâncias de VM em um conjunto de dimensionamento
Para exibir uma lista de instância de VM em um conjunto de dimensionamento, use [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) da seguinte forma:

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

A saída de exemplo abaixo mostra duas instâncias de VM no conjunto de dimensionamento:

```powershell
ResourceGroupName         Name Location             Sku InstanceID ProvisioningState
-----------------         ---- --------             --- ---------- -----------------
MYRESOURCEGROUP   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUP   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

Para exibir informações adicionais sobre uma instância específica de VM, adicione o parâmetro `-InstanceId` a [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm). O exemplo abaixo exibe informações sobre a instância de VM *1*:

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


## <a name="list-connection-information"></a>Listar informações de conexão
Um endereço IP público é atribuído ao balanceador de carga que direciona o tráfego para as instâncias de VM individuais. Por padrão, as regras de Conversão de endereços de rede (NAT) são adicionadas ao balanceador de carga do Azure, que encaminha o tráfego de conexão remota para cada VM em determinada porta. Para conectar as instâncias de VM em um conjunto de dimensionamento, crie uma conexão remota para um endereço IP público e número de porta atribuídos.

Para listar as portas NAT que devem se conectar a instâncias de VM em um conjunto de dimensionamento, primeiro obtenha o objeto do balanceador de carga com [Get-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancer). Em seguida, exiba as regras NAT de entrada com [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig):

```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzureRmLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzureRmLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort
```

A saída de exemplo a seguir mostra o nome da instância, o endereço IP público do balanceador de carga e o número de porta para onde as regras de NAT encaminham o tráfego:

```powershell
Name             Protocol FrontendPort BackendPort
----             -------- ------------ -----------
myScaleSet3389.0 Tcp             50001        3389
myScaleSet5985.0 Tcp             51001        5985
myScaleSet3389.1 Tcp             50002        3389
myScaleSet5985.1 Tcp             51002        5985
```

O *Nome* da regra está de acordo com o nome da instância de VM conforme mostrado em um comando [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) anterior. Por exemplo, para se conectar à instância de VM *0*, use *myScaleSet3389.0* e conecte-se à porta *50001*. Para se conectar à instância de VM *1*, use o valor de *myScaleSet3389.1* e conecte-se à porta *50002*. Para usar a comunicação remota do PowerShell, conecte-se à regra de instância de VM apropriada para a porta *TCP* *5985*.

Exiba o endereço IP público do balanceador de carga com [Get-AzureRmPublicIPAddress](/powershell/module/AzureRM.Network/Get-AzureRmPublicIpAddress):

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" -Name "myPublicIPAddress" | Select IpAddress
```

Saída de exemplo:

```powershell
IpAddress
---------
52.168.121.216
```

Crie uma conexão remota para sua primeira instância de VM. Especifique o endereço IP público e o número da porta da instância de VM necessária, conforme mostrado com os comandos anteriores. Quando solicitado, insira as credenciais usadas quando você criou o conjunto de dimensionamento (por padrão,nos comandos de exemplo: *azureuser* e  *P@ssw0rd!*). Se você usa o Azure Cloud Shell, execute esta etapa de um prompt do PowerShell local ou do Cliente Área de Trabalho Remota. O exemplo abaixo se conecta a uma instância de VM em *1*:

```powershell
mstsc /v 52.168.121.216:50001
```

Depois de conectado à instância de VM, você poderá executar algumas alterações de configuração manualmente, caso seja necessário. Por enquanto, feche a conexão remota.


## <a name="understand-vm-instance-images"></a>Entender imagens de instância de VM
Quando você definiu a configuração de um conjunto de dimensionamento com [Set-AzureRmVmssStorageProfile](/powershell/module/AzureRM.Compute/Set-AzureRmVmssStorageProfile) em uma etapa anterior, usou uma imagem do Windows Server 2016 Datacenter. O Azure Marketplace inclui muitas imagens que podem ser usadas para criar instâncias de VM. Para ver uma lista de fornecedores disponíveis, use o comando [Get-AzureRmVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher).

```azurepowershell-interactive
Get-AzureRmVMImagePublisher -Location "EastUS"
```

Para exibir uma lista de imagens de determinado publicador, use [Get-AzureRmVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku). A lista de imagens também pode ser filtrada por `-PublisherName` ou `–Offer`. No exemplo a seguir, a lista é filtrada para todas as imagens com o nome do fornecedor *MicrosoftWindowsServer* e uma oferta que corresponde a *WindowsServer*:

```azurepowershell-interactive
Get-AzureRmVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

A saída de exemplo abaixo mostra todas as imagens disponíveis do Windows Server:

```powershell
Skus                                  Offer         PublisherName          Location
----                                  -----         -------------          --------
2008-R2-SP1                           WindowsServer MicrosoftWindowsServer eastus
2008-R2-SP1-smalldisk                 WindowsServer MicrosoftWindowsServer eastus
2012-Datacenter                       WindowsServer MicrosoftWindowsServer eastus
2012-Datacenter-smalldisk             WindowsServer MicrosoftWindowsServer eastus
2012-R2-Datacenter                    WindowsServer MicrosoftWindowsServer eastus
2012-R2-Datacenter-smalldisk          WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter                       WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-Server-Core           WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-Server-Core-smalldisk WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-smalldisk             WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-with-Containers       WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-with-RDSH             WindowsServer MicrosoftWindowsServer eastus
2016-Nano-Server                      WindowsServer MicrosoftWindowsServer eastus
```

Quando você criou um conjunto de dimensionamento no início do tutorial, uma imagem de VM padrão do *Windows Server 2016 DataCenter* foi fornecida para as instâncias de VM. Você pode especificar uma imagem de VM diferente com base na saída de [Get-AzureRmVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku). O exemplo a seguir cria um conjunto de dimensionamento com o parâmetro `-ImageName` para especificar uma imagem de VM do *MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest*. Como demora alguns minutos para criar e configurar todos os recursos do conjunto de dimensionamento e as instâncias de VM, você não precisa implantar o seguinte conjunto de dimensionamento:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup2" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet2" `
  -VirtualNetworkName "myVnet2" `
  -SubnetName "mySubnet2" `
  -PublicIpAddressName "myPublicIPAddress2" `
  -LoadBalancerName "myLoadBalancer2" `
  -UpgradePolicy "Automatic" `
  -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
  -Credential $cred
```


## <a name="understand-vm-instance-sizes"></a>Entender tamanhos de instância de VM
Um tamanho de instância de VM, ou *SKU*, determina a quantidade de recursos de computação, como memória, CPU e GPU que estão disponíveis para a instância de VM. As instâncias de VM em um conjunto de dimensionamento precisam ser dimensionadas apropriadamente para a carga de trabalho esperada.

### <a name="vm-instance-sizes"></a>Tamanhos de instância de VM
A tabela a seguir categoriza tamanhos de VMs comuns para determinados casos de uso.

| type                     | Tamanhos comuns           |    DESCRIÇÃO       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Propósito geral](../virtual-machines/windows/sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7| CPU/memória equilibrados. Ideal para desenvolvimento/teste e para aplicativos de pequeno a médio porte e soluções de dados.  |
| [Computação otimizada](../virtual-machines/windows/sizes-compute.md)   | Fs, F             | Relação de CPU/memória alta. Boa para aplicativos de tráfego médio, dispositivos de rede e processos em lote.        |
| [Memória otimizada](../virtual-machines/windows/sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Relação de memória/núcleo alta. Ótima para banco de dados relacionais, caches médios a grandes e análises na memória.                 |
| [Armazenamento otimizado](../virtual-machines/windows/sizes-storage.md)      | Ls                | Alta taxa de transferência de disco e de E/S. Ideal para Big Data, SQL e bancos de dados NoSQL.                                                         |
| [GPU](../virtual-machines/windows/sizes-gpu.md)          | NV, NC            | VMs especializadas, destinadas para renderização gráfica e edição de vídeo pesadas.       |
| [Alto desempenho](../virtual-machines/windows/sizes-hpc.md) | H, A8-11          | Nossas VMs de CPU mais potentes com adaptadores de rede de alto rendimento (RDMA) opcionais. 

### <a name="find-available-vm-instance-sizes"></a>Localizar tamanhos de instância de VM disponíveis
Para ver uma lista de tamanhos de instância de VM disponíveis em uma região específica, use o comando [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize). 

```azurepowershell-interactive
Get-AzureRmVMSize -Location "EastUS"
```

O resultado é semelhante ao exemplo condensado abaixo, que mostra os recursos atribuídos a cada tamanho de VM:

```powershell
Name                   NumberOfCores MemoryInMB MaxDataDiskCount OSDiskSizeInMB ResourceDiskSizeInMB
----                   ------------- ---------- ---------------- -------------- --------------------
Standard_DS1_v2                    1       3584                4        1047552                 7168
Standard_DS2_v2                    2       7168                8        1047552                14336
[...]
Standard_A0                        1        768                1        1047552                20480
Standard_A1                        1       1792                2        1047552                71680
[...]
Standard_F1                        1       2048                4        1047552                16384
Standard_F2                        2       4096                8        1047552                32768
[...]
Standard_NV6                       6      57344               24        1047552               389120
Standard_NV12                     12     114688               48        1047552               696320
```

Quando você criou um conjunto de dimensionamento no início do tutorial, um SKU de VM padrão *Standard_DS1_v2* foi fornecido para as instâncias de VM. Você pode especificar um tamanho de instância de VM diferente com base na saída de [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize). O exemplo a seguir cria um conjunto de dimensionamento com o parâmetro `-VmSize` para especificar um tamanho de instância de VM *Standard_F1*. Como demora alguns minutos para criar e configurar todos os recursos do conjunto de dimensionamento e as instâncias de VM, você não precisa implantar o seguinte conjunto de dimensionamento:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup3" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet3" `
  -VirtualNetworkName "myVnet3" `
  -SubnetName "mySubnet3" `
  -PublicIpAddressName "myPublicIPAddress3" `
  -LoadBalancerName "myLoadBalancer3" `
  -UpgradePolicy "Automatic" `
  -VmSize "Standard_F1" `
  -Credential $cred
```


## <a name="change-the-capacity-of-a-scale-set"></a>Alterar a capacidade de um conjunto de dimensionamento
Quando você criou um conjunto de dimensionamento, solicitou duas instâncias de VM. Para aumentar ou diminuir o número de instâncias de VM no conjunto de dimensionamento, você pode alterar a capacidade manualmente. O conjunto de dimensionamento cria ou remove o número necessário de instâncias de VM e configura o balanceador de carga para distribuir o tráfego.

Primeiro, crie um objeto de conjunto de dimensionamento com [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss), em seguida, especifique um novo valor para `sku.capacity`. Para aplicar a alteração de capacidade, use [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). O seguinte exemplo aumenta o número de instâncias de VM no conjunto de dimensionamento para *3*:

```azurepowershell-interactive
# Get current scale set
$vmss = Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 3
Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss 
```

Demora alguns minutos para atualizar a capacidade do seu conjunto de dimensionamento. Para ver o número de instâncias que você tem agora no conjunto de dimensionamento, use [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss):

```azurepowershell-interactive
Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

A seguinte saída de exemplo mostra que a capacidade do conjunto de dimensionamento agora é *3*:

```powershell
Sku        :
  Name     : Standard_DS2
  Tier     : Standard
  Capacity : 3
```


## <a name="common-management-tasks"></a>Tarefas comuns de gerenciamento
Agora você pode criar um conjunto de dimensionamento, listar as informações de conexão e se conectar a instâncias de VM. Você aprendeu como pode usar uma imagem de sistema operacional diferente para suas instâncias de VM, selecionar um tamanho de VM diferente ou dimensionar o número de instâncias manualmente. Como parte do gerenciamento diário, talvez seja necessário parar, iniciar ou reiniciar as instâncias de VM em seu conjunto de dimensionamento.

### <a name="stop-and-deallocate-vm-instances-in-a-scale-set"></a>Parar e desalocar instâncias de VM em um conjunto de dimensionamento
Para parar uma ou mais VMs em um conjunto de dimensionamento, use [Stop-AzureRmVmss](/powershell/module/azurerm.compute/stop-azurermvmss). O parâmetro `-InstanceId` permite que você especifique uma ou mais VMs para parar. Se você não especificar uma ID de instância, todas as VMs no conjunto de dimensionamento são paradas. O exemplo abaixo interrompe a instância *1*:

```azurepowershell-interactive
Stop-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```

Por padrão, as VMs paradas são desalocadas e não incorrem em encargos de computação. Se desejar que a VM permaneça em um estado de provisionamento quando interrompido, adicione o parâmetro `-StayProvisioned` ao comando anterior. VMs paradas que permanecem provisionadas incorrem em encargos de computação regulares.

### <a name="start-vm-instances-in-a-scale-set"></a>Iniciar instâncias de VM em um conjunto de dimensionamento
Para iniciar uma ou mais VMs em um conjunto de dimensionamento, use [Start-AzureRmVmss](/powershell/module/azurerm.compute/start-azurermvmss). O parâmetro `-InstanceId` permite que você especifique uma ou mais VMs a serem iniciadas. Se você não especificar uma ID de instância, todas as VMs no conjunto de dimensionamento são iniciadas. O exemplo abaixo inicia a instância *1*:

```azurepowershell-interactive
Start-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```

### <a name="restart-vm-instances-in-a-scale-set"></a>Reiniciar instâncias de VM em um conjunto de dimensionamento
Para reiniciar uma ou mais VMs em um conjunto de dimensionamento, use [Restart-AzureRmVmss](/powershell/module/azurerm.compute/restart-azurermvmss). O parâmetro `-InstanceId` permite que você especifique uma ou mais VMs para reiniciar. Se você não especificar uma ID de instância, todas as VMs no conjunto de dimensionamento são reiniciadas. O exemplo abaixo reinicia a instância *1*:

```azurepowershell-interactive
Restart-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


## <a name="clean-up-resources"></a>Limpar recursos
Quando você excluir um grupo de recursos, todos os recursos contidos, como as instâncias de VM, as rede virtuais e os discos, também serão excluídos. O parâmetro `-Force` confirma que você deseja excluir os recursos sem um prompt adicional para fazer isso. O parâmetro `-AsJob` retorna o controle ao prompt sem aguardar a conclusão da operação.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a executar algumas tarefas básicas de criação e gerenciamento do conjunto de dimensionamento com o Azure PowerShell:

> [!div class="checklist"]
> * Criar e se conectar a um conjunto de dimensionamento de máquinas virtuais
> * Selecionar e usar imagens de VM
> * Exibir e usar tamanhos específicos de VM
> * Dimensionar manualmente um conjunto de dimensionamento
> * Executar tarefas comuns de gerenciamento de conjunto de dimensionamento

Avance para o próximo tutorial a fim de saber mais sobre discos de conjunto de dimensionamento.

> [!div class="nextstepaction"]
> [Usar discos de dados com conjuntos de dimensionamento](tutorial-use-disks-powershell.md)
