---
title: Gerenciar discos VM na pilha do Azure | Microsoft Docs
description: "Discos de provisionamento de máquinas virtuais para a pilha do Azure."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 4e5833cf-4790-4146-82d6-737975fb06ba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/14/2017
ms.author: brenduns
ms.reviewer: jiahan
ms.openlocfilehash: 0c36e2eaaf2d266842b2b7de0b0c8dc0ed1e0145
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2017
---
# <a name="virtual-machine-disk-storage-for-azure-stack"></a>Armazenamento de disco de máquina virtual para a pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

A pilha do Azure suporta o uso de [não gerenciado discos](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#unmanaged-disks) em uma máquina Virtual como um disco do sistema operacional (SO) e um disco de dados. Para usar discos não gerenciados, você cria um [conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) e, em seguida, armazenar os discos como blobs de página em contêineres na conta de armazenamento. Esses discos são referenciados como discos de VM.

Para melhorar o desempenho e reduzir o custo de gerenciamento do sistema de pilha do Azure, é recomendável que colocar cada disco VM em um contêiner separado. Um contêiner deve conter um disco do sistema operacional ou um disco de dados, mas não ambos ao mesmo tempo. No entanto, não há nenhuma restrição que impede colocando ambos no mesmo contêiner.

Se você adicionar um ou mais discos de dados para uma VM, planeje usar contêineres adicionais como um local para armazenar os discos. Como os discos de dados, o disco do sistema operacional para VMs adicionais também deve ser em seus próprios contêineres separados.

Quando você cria várias VMs, você pode reutilizar a mesma conta de armazenamento para cada nova VM. Somente os contêineres que você cria devem ser exclusivos.  

Para adicionar discos a uma máquina virtual, use o portal do usuário ou o PowerShell.

| Método | Opções
|-|-|
|[Portal do usuário](#use-the-portal-to-add-additional-disks-to-a-vm)|-Adicione novos discos de dados a uma VM que foi configurada anteriormente. Novos discos são criados pela pilha do Azure. </br> </br>-Adicione um arquivo. vhd existente como um disco em uma VM que foi configurada anteriormente. Para fazer isso, você deve primeiro preparar e carregue o arquivo. vhd no Azure pilha. |
|[PowerShell](#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm) | -Criar uma nova VM com um disco do sistema operacional e ao mesmo tempo, adicione um ou mais discos de dados para essa VM. |


## <a name="use-the-portal-to-add-additional-disks-to-a-vm"></a>Use o portal para adicionar discos adicionais a uma VM
Por padrão, quando você usar o portal para criar uma máquina virtual para a maioria dos itens do marketplace, apenas um disco do sistema operacional é criado. Discos criados pelo Azure são chamados de discos gerenciados.

Depois de provisionar uma máquina virtual, você pode usar o portal para adicionar um novo disco de dados ou um disco de dados existente para essa VM. Cada disco adicional deve ser colocado em um contêiner separado. Os discos que você adicionar a uma máquina virtual são chamados de discos não gerenciados.

### <a name="use-the-portal-to-attach-a-new-data-disk-to-a-vm"></a>Use o portal para anexar um novo disco de dados a uma VM

1.  No portal, clique em **máquinas virtuais**.    
    ![Exemplo: Painel de máquina virtual](media/azure-stack-manage-vm-disks/vm-dashboard.png)

2.  Selecione uma máquina virtual que foram provisionada anteriormente.   
    ![Exemplo: Selecione uma máquina virtual no painel](media/azure-stack-manage-vm-disks/select-a-vm.png)

3.  Para a máquina virtual, clique em **discos** > **anexar novos**.       
    ![Exemplo: Anexar um novo disco à vm](media/azure-stack-manage-vm-disks/Attach-disks.png)    

4.  No **anexar novo disco** painel, clique em **local**. Por padrão, o local é definido no mesmo contêiner que contém o disco do sistema operacional.      
    ![Exemplo: Definir o local do disco](media/azure-stack-manage-vm-disks/disk-location.png)

5.  Selecione o **conta de armazenamento** usar. Em seguida, selecione o **contêiner** onde você deseja colocar o disco de dados. Do **contêineres** página, você pode criar um novo contêiner se você quiser. Em seguida, você pode alterar o local para o novo disco ao seu próprio contêiner. Quando você usa um contêiner separado para cada disco, você pode distribuir o posicionamento do disco de dados que pode melhorar o desempenho. Clique em **selecione** para salvar a seleção.     
    ![Exemplo: Selecione um contêiner](media/azure-stack-manage-vm-disks/select-container.png)

6.  No **anexar novo disco** página, atualize o **nome**, **tipo**, **tamanho**, e **cache de Host** configurações do disco. Em seguida, clique em **Okey** para salvar a nova configuração de disco para a máquina virtual.  
    ![Exemplo: Anexação de disco completo](media/azure-stack-manage-vm-disks/complete-disk-attach.png)  

7.  Depois de pilha do Azure cria o disco e a anexa à máquina virtual, o novo disco está listado em configurações de disco da máquina virtual em **discos de dados**.   
    ![Exemplo: Disco de modo de exibição](media/azure-stack-manage-vm-disks/view-data-disk.png)


### <a name="attach-an-existing-data-disk-to-a-vm"></a>Anexar um disco de dados existente a uma VM
1.  [Preparar um arquivo. vhd](https://docs.microsoft.com/azure/virtual-machines/windows/classic/createupload-vhd) para uso como disco de dados para uma máquina virtual. Carregue o arquivo. vhd para uma conta de armazenamento que você pode usar com a máquina virtual que você deseja anexar o arquivo. vhd.

  Você planeja usar um contêiner diferente para armazenar o arquivo. vhd que o contêiner que mantém o disco do sistema operacional.   
  ![Exemplo: Carregar um arquivo VHD](media/azure-stack-manage-vm-disks/upload-vhd.png)

2.  Depois que o arquivo. vhd é carregado, você está pronto para anexar o VHD para uma máquina virtual. No menu à esquerda, clique em **máquinas virtuais**.  
 ![Exemplo: Selecione uma máquina virtual no painel](media/azure-stack-manage-vm-disks/vm-dashboard.png)

3.  Selecione a máquina virtual na lista.    
  ![Exemplo: Selecione uma máquina virtual no painel](media/azure-stack-manage-vm-disks/select-a-vm.png)

4.  Na página para a máquina virtual, clique em **discos** > **anexar existente**.   
  ![Exemplo: Anexar um disco existente](media/azure-stack-manage-vm-disks/attach-disks2.png)

5.  No **anexar o disco existente** , clique em **arquivo VHD**. O **contas de armazenamento** página será aberta.    
  ![Exemplo: Selecione um arquivo VHD](media/azure-stack-manage-vm-disks/select-vhd.png)

6.  Em **contas de armazenamento**, selecione a conta a ser usada e, em seguida, selecione um contêiner que contém o arquivo. vhd que você carregou anteriormente. Selecione o arquivo. vhd e, em seguida, clique em **selecione** para salvar a seleção.    
  ![Exemplo: Selecione um contêiner](media/azure-stack-manage-vm-disks/select-container2.png)

7.  Em **anexar o disco existente**, o arquivo selecionado é listado em **arquivo VHD**. Atualização de **cache de Host** configuração do disco e, em seguida, clique em **Okey** para salvar a nova configuração de disco para a máquina virtual.    
  ![Exemplo: Anexar o arquivo VHD](media/azure-stack-manage-vm-disks/attach-vhd.png)

8.  Depois de pilha do Azure cria o disco e a anexa à máquina virtual, o novo disco está listado em configurações de disco da máquina virtual em **discos de dados**.   
  ![Exemplo: Concluir o disco anexar](media/azure-stack-manage-vm-disks/complete-disk-attach.png)


## <a name="use-powershell-to-add-multiple-unmanaged-disks-to-a-vm"></a>Use o PowerShell para adicionar vários discos não gerenciados a uma VM
Você pode usar o PowerShell para provisionar uma VM e adicionar um novo disco de dados ou anexar um pré-existente **. vhd** arquivo como um disco de dados.

O **AzureRmVMDataDisk Add** adiciona um disco de dados para uma máquina virtual. Você pode adicionar um disco de dados quando você criar uma máquina virtual, ou você pode adicionar um disco de dados para uma máquina virtual existente. Especifique o **VhdUri** parâmetro para distribuir os discos para diferentes contêineres.

### <a name="add-data-disks-to-a-new-virtual-machine"></a>Adicionar discos de dados para uma nova máquina virtual
Os exemplos a seguir usam comandos do PowerShell para criar uma VM com três discos de dados, cada colocada em um contêiner diferente.

O primeiro comando cria um objeto de máquina virtual e, em seguida, armazena no *$VirtualMachine* variável. O comando atribui um nome e o tamanho para a máquina virtual.
  ```
  $VirtualMachine = New-AzureRmVMConfig -VMName "VirtualMachine" `
                                      -VMSize "Standard_A2"
  ```

Os próximos três comandos atribuam caminhos três discos de dados para o *$DataDiskVhdUri01*, *$DataDiskVhdUri02*, e *$DataDiskVhdUri03* variáveis. Defina um nome de caminho diferentes na URL para distribuir os discos para diferentes contêineres.     
  ```
  $DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
  ```

  ```
  $DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
  ```

  ```
  $DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
  ```

Os três comandos finais adicionam discos de dados para a máquina virtual armazenada na *$VirtualMachine*. Cada comando Especifica o nome, local e as propriedades adicionais do disco. O URI de cada disco é armazenado em *$DataDiskVhdUri01*, *$DataDiskVhdUri02*, e *$DataDiskVhdUri03*.
  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk1' `
                  -Caching 'ReadOnly' -DiskSizeInGB 10 -Lun 0 `
                  -VhdUri $DataDiskVhdUri01 -CreateOption Empty
  ```

  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk2' `
                 -Caching 'ReadOnly' -DiskSizeInGB 11 -Lun 1 `
                 -VhdUri $DataDiskVhdUri02 -CreateOption Empty
  ```

  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk3' `
                  -Caching 'ReadOnly' -DiskSizeInGB 12 -Lun 2 `
                  -VhdUri $DataDiskVhdUri03 -CreateOption Empty
  ```

Use os seguintes comandos do PowerShell para adicionar a configuração de rede e disco de sistema operacional para a máquina virtual e, em seguida, inicie a nova VM.
  ```
  #set variables
  $rgName = "myResourceGroup"
  $location = "local"
  #Set OS Disk
  $osDiskUri = "https://contoso.blob.local.azurestack.external/vhds/osDisk.vhd"
  $osDiskName = "osDisk"

  $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $osDiskName -VhdUri $osDiskUri `
      -CreateOption FromImage -Windows

  # Create a subnet configuration
  $subnetName = "mySubNet"
  $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24

  # Create a vnet configuration
  $vnetName = "myVnetName"
  $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
      -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet

  # Create a public IP
  $ipName = "myIP"
  $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
      -AllocationMethod Dynamic

  # Create a network security group cnfiguration
  $nsgName = "myNsg"
  $rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
      -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
      -SourceAddressPrefix Internet -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389
  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
      -Name $nsgName -SecurityRules $rdpRule

  # Create a NIC configuration
  $nicName = "myNicName"
  $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
  -Location $location -SubnetId $vnet.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id -PublicIpAddressId $pip.Id

  #Create the new VM
  $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName VirtualMachine | `
      Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
      -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
  New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $VirtualMachine
  ```



### <a name="add-data-disks-to-an-existing-virtual-machine"></a>Adicionar discos de dados para uma máquina virtual existente
Os exemplos a seguir usam comandos do PowerShell para adicionar três discos de dados a uma VM existente.
O primeiro comando obtém a máquina virtual chamada máquina virtual usando o **AzureRmVM Get** cmdlet. O comando armazena a máquina virtual na variável *$VirtualMachine* .
  ```
  $VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" `
                                  -Name "VirtualMachine"
  ```
Os próximos três comandos atribuam caminhos de três discos de dados para as variáveis $DataDiskVhdUri01, $DataDiskVhdUri02 e $DataDiskVhdUri03.  Os nomes de caminho diferentes a vhduri indicam diferentes contêineres para o posicionamento de disco.
  ```
  $DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
  ```
  ```
  $DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
  ```
  ```
  $DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
  ```


  Os próximos três comandos adicionam os discos de dados para a máquina virtual armazenada no *$VirtualMachine* variável. Cada comando Especifica o nome, local e as propriedades adicionais do disco. O URI de cada disco é armazenado em *$DataDiskVhdUri01*, *$DataDiskVhdUri02*, e *$DataDiskVhdUri03*.
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" `
                        -VhdUri $DataDiskVhdUri01 -LUN 0 `
                        -Caching ReadOnly -DiskSizeinGB 10 -CreateOption Empty
  ```
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk2" `
                        -VhdUri $DataDiskVhdUri02 -LUN 1 `
                        -Caching ReadOnly -DiskSizeinGB 11 -CreateOption Empty
  ```
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk3" `
                        -VhdUri $DataDiskVhdUri03 -LUN 2 `
                        -Caching ReadOnly -DiskSizeinGB 12 -CreateOption Empty
  ```


  O comando final atualizará o estado da máquina virtual armazenada no *$VirtualMachine* em -*ResourceGroupName*.
  ```
  Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
  ```
<!-- Pending scripts  

## Distribute the data disks of an existing VM
If you have a VM with more than one disk in the same container, the service operator of the Azure Stack deployment might ask you to redistribute the disks into individual containers.

To do so, use the scripts from the following location in GitHub. These scripts can be used to move the data disks to different containers.
-->

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre máquinas virtuais de pilha do Azure, consulte [considerações para máquinas virtuais no Azure pilha](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-considerations).
