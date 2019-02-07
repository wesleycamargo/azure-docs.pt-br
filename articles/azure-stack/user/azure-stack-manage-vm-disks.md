---
title: Gerenciar discos de VM no Azure Stack | Microsoft Docs
description: Crie discos para máquinas virtuais no Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/18/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: b5254b45fcd71901876d9e77d1f29c0a0d40ce3f
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55813867"
---
# <a name="create-virtual-machine-disk-storage-in-azure-stack"></a>Criar o armazenamento de disco da máquina virtual no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Este artigo descreve como criar o armazenamento de disco da máquina virtual usando o portal do Azure Stack ou por meio do PowerShell.

## <a name="overview"></a>Visão geral

Começando com a versão 1808, o Azure Stack suporta o uso de discos gerenciados e não gerenciados em máquinas virtuais, como um sistema operacional (SO) e um disco de dados. Antes da versão 1808, há suporte para apenas discos não gerenciados. 

**[Discos gerenciados](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#managed-disks)**  simplificar o gerenciamento de disco para VMs IaaS do Azure, gerenciando as contas de armazenamento associadas com os discos de VM. Você só precisa especificar o tamanho do disco necessário e o Azure Stack cria e gerencia o disco para você.

**[Discos não gerenciados](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#unmanaged-disks)**, exigem que você crie uma [conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) para armazenar os discos. Os discos que você cria são chamados de discos de VM e são armazenados em contêineres na conta de armazenamento.

### <a name="best-practice-guidelines"></a>Diretrizes de práticas recomendadas

Para melhorar o desempenho e reduzir os custos gerais, recomendamos que você coloque cada disco VM em um contêiner separado. Um contêiner deve conter um disco do sistema operacional ou um disco de dados, mas não ambos ao mesmo tempo. (No entanto, há nada para impedir que você colocar os dois tipos de disco no mesmo contêiner.)

Se você adicionar um ou mais discos de dados a uma VM, use contêineres adicionais como um local para armazenar esses discos. O disco do SO para VMs adicionais também deve estar no seu próprio contêiner.

Quando você cria VMs, você pode reutilizar a mesma conta de armazenamento para cada nova máquina virtual. Somente os contêineres que você cria devem ser exclusivos.

### <a name="adding-new-disks"></a>Adição de novos discos

A tabela a seguir resume como adicionar discos usando o portal e por meio do PowerShell.

| Método | Opções
|-|-|
|Portal do usuário|-Adicione novos discos de dados a uma VM existente. Novos discos são criados pela pilha do Azure. </br> </br>-Adicione um arquivo de disco (. vhd) existente para uma VM criada anteriormente. Para fazer isso, você deve preparar o. vhd e, em seguida, carregue o arquivo para o Azure Stack. |
|[PowerShell](#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm) | -Criar uma nova VM com um disco do sistema operacional e ao mesmo tempo, adicione um ou mais discos de dados para essa VM. |

## <a name="use-the-portal-to-add-disks-to-a-vm"></a>Usar o portal para adicionar discos a uma VM

Por padrão, quando você usa o portal para criar uma VM para a maioria dos itens do marketplace, apenas o disco do sistema operacional é criado.

Depois de criar uma VM, você pode usar o portal para:
* Criar um novo disco de dados e anexá-lo à VM.
* Carregar um disco de dados existente e anexá-lo à VM.

Cada disco não gerenciado que você adicionar deve ser colocado em um contêiner separado.

>[!NOTE]  
>Os discos criados e gerenciados pelo Azure são chamados [discos gerenciados](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).

### <a name="use-the-portal-to-create-and-attach-a-new-data-disk"></a>Usar o portal para criar e anexar um disco de dados

1.  No portal, escolha **todos os serviços** > **máquinas virtuais**.    
    ![Exemplo: Painel da VM](media/azure-stack-manage-vm-disks/vm-dashboard.png)

2.  Selecione uma máquina virtual que criou anteriormente.   
    ![Exemplo: Selecione uma VM no painel de controle](media/azure-stack-manage-vm-disks/select-a-vm.png)

3.  Para a máquina virtual, selecione **discos** > **adicionar disco de dados**.       
    ![Exemplo: Anexar um novo disco à vm](media/azure-stack-manage-vm-disks/Attach-disks.png)    

4.  Para o disco de dados:
    -  Insira o **LUN**. O LUN deve ser um número válido.
    -  Selecione **criar um disco**.
    ![Exemplo: Anexar um novo disco à vm](media/azure-stack-manage-vm-disks/add-a-data-disk-create-disk.png)

5.  No criar gerenciado folha de disco:
    -  Insira o **nome** do disco.
    -  Selecione um existente ou crie um novo **grupo de recursos**.
    -  Selecione o **local**. Por padrão, o local é definido no mesmo contêiner que contém o disco do sistema operacional.
    -  Selecione o **tipo de conta**. 
        ![Exemplo: Anexar um novo disco à vm](media/azure-stack-manage-vm-disks/create-manage-disk.png)

        **Premium SSD**  
        Discos Premium (SSD) contam com unidades de estado sólido e oferecem desempenho consistente e baixa latência. Eles oferecem o melhor equilíbrio entre preço e desempenho e são ideais para aplicativos de e/S intensiva e cargas de trabalho de produção.
       
        **HDD padrão**  
        Discos padrão (HDD) contam com unidades magnéticas e são preferíveis para aplicativos em que os dados são acessados com pouca frequência. Discos com redundância de zona - são feitos por com redundância de armazenamento de zona (ZRS) que replica seus dados em várias zonas e estão disponíveis mesmo se uma única zona está inativo. 

    -  Selecione o **tipo de fonte**.

       Criar um disco de um instantâneo de outro disco, um blob em uma conta de armazenamento, ou criar um disco vazio.

        **Instantâneo**  
        Selecione um instantâneo, se ele estiver disponível. O instantâneo deve estar no disponíveis na assinatura e o local da VM.

        **Armazenamento de BLOBs**  
        - Adicione o URI do blob de armazenamento que contém a imagem de disco.  
        - Selecione **procurar** para abrir a folha de contas de armazenamento. Para obter instruções, consulte [adicionar um disco de dados de uma conta de armazenamento](#add-a-data-disk-from-a-storage-account).
        - Selecione o tipo de sistema operacional da imagem, ou **Windows**, **Linux**, ou **nenhum (disco de dados)**.

        **Nenhum (disco vazio)**

    -  Selecione o **tamanho (GiB)**.

       O custo de disco padrão aumentam com base no tamanho do disco. Premium disco desempenho e os custos aumentam com base no tamanho do disco. Para obter mais informações, consulte [preço do Managed Disks](https://go.microsoft.com/fwlink/?linkid=843142).

    -  Selecione **Criar**. O Azure Stack cria e valida o disco gerenciado.

5.  Depois que o Azure Stack cria o disco e o anexa à máquina virtual, o novo disco é listado nas configurações de disco da máquina virtual sob **discos de dados**.   

    ![Exemplo: Disco de modo de exibição](media/azure-stack-manage-vm-disks/view-data-disk.png)

### <a name="add-a-data-disk-from-a-storage-account"></a>Adicionar um disco de dados de uma conta de armazenamento

Para obter mais informações sobre como trabalhar com contas de armazenamento no Azure Stack, consulte [Introdução ao armazenamento do Azure Stack](azure-stack-storage-overview.md).

1. Selecione o **conta de armazenamento** usar.
2. Selecione o **contêiner** onde você deseja colocar o disco de dados. Dos **contêineres** folha, você pode criar um novo contêiner se desejar. Em seguida, você pode alterar o local para o novo disco para seu próprio contêiner. Quando você usa um contêiner separado para cada disco, você pode distribuir o posicionamento do disco de dados que pode melhorar o desempenho.
3. Escolher **selecionar** para salvar a seleção.

    ![Exemplo: Selecione um contêiner](media/azure-stack-manage-vm-disks/select-container.png)

## <a name="attach-an-existing-data-disk-to-a-vm"></a>Anexar um disco de dados existente a uma VM

1.  [Preparar um arquivo. vhd](https://docs.microsoft.com/azure/virtual-machines/windows/classic/createupload-vhd) para uso como disco de dados para uma VM. Carregue o arquivo. vhd para uma conta de armazenamento que você usa com a VM que você deseja anexar o arquivo. vhd.

    Você planeja usar um contêiner diferente para armazenar o arquivo. vhd que o contêiner que mantém o disco do sistema operacional.   
    ![Exemplo: Carregar um arquivo VHD](media/azure-stack-manage-vm-disks/upload-vhd.png)

2.  Depois que o arquivo. vhd é carregado, você está pronto para anexar o VHD em uma VM. No menu à esquerda, selecione **máquinas virtuais**.  
 ![Exemplo: Selecione uma VM no painel de controle](media/azure-stack-manage-vm-disks/vm-dashboard.png)

3.  Escolha a máquina virtual na lista.

    ![Exemplo: Selecione uma VM no painel de controle](media/azure-stack-manage-vm-disks/select-a-vm.png)

4.  Na página para a máquina virtual, selecione **discos** > **anexar existente**.   

    ![Exemplo: Anexar um disco existente](media/azure-stack-manage-vm-disks/attach-disks2.png)

5.  No **anexar disco existente** página, selecione **arquivo VHD**. O **contas de armazenamento** página será aberta.    

    ![Exemplo: Selecione um arquivo VHD](media/azure-stack-manage-vm-disks/select-vhd.png)

6.  Sob **contas de armazenamento**, selecione a conta a ser usada e, em seguida, escolha um contêiner que mantém o arquivo. vhd que você carregou anteriormente. Selecione o arquivo. vhd e, em seguida, escolha **selecionar** para salvar a seleção.    

    ![Exemplo: Selecione um contêiner](media/azure-stack-manage-vm-disks/select-container2.png)

7.  Sob **anexar disco existente**, o arquivo selecionado está listado em **arquivo VHD**. Atualizar o **cache de Host** configuração do disco e, em seguida, selecione **Okey** para salvar a nova configuração de disco para a VM.    

    ![Exemplo: Anexe o arquivo de VHD](media/azure-stack-manage-vm-disks/attach-vhd.png)

8.  Depois que o Azure Stack cria o disco e o anexa à máquina virtual, o novo disco é listado nas configurações de disco da máquina virtual sob **discos de dados**.   

    ![Exemplo: Anexação de disco completo](media/azure-stack-manage-vm-disks/complete-disk-attach.png)

## <a name="use-powershell-to-add-multiple-unmanaged-disks-to-a-vm"></a>Usar o PowerShell para adicionar vários discos não gerenciados a uma VM

Você pode usar o PowerShell para provisionar uma VM e adicione um novo disco de dados ou anexar um pré-existentes **. vhd** arquivo como um disco de dados.

O **Add-AzureRmVMDataDisk** cmdlet adiciona um disco de dados a uma máquina virtual. Você pode adicionar um disco de dados quando você cria uma máquina virtual, ou você pode adicionar um disco de dados para uma máquina virtual existente. Especifique o **VhdUri** parâmetro para distribuir os discos para diferentes contêineres.

### <a name="add-data-disks-to-a-new-virtual-machine"></a>Adicionar discos de dados para uma nova máquina virtual
Os exemplos seguintes usam comandos do PowerShell para criar uma VM com três discos de dados, cada colocada em um contêiner diferente.

O primeiro comando cria um objeto de máquina virtual e, em seguida, armazena-o na *$VirtualMachine* variável. O comando atribui um nome e um tamanho para a máquina virtual.

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName "VirtualMachine" `
                                    -VMSize "Standard_A2"
```

Os próximos três comandos atribuam caminhos de três discos de dados para o *US $DataDiskVhdUri01*, *US $DataDiskVhdUri02*, e *$DataDiskVhdUri03* variáveis. Defina um nome de caminho diferentes na URL para distribuir os discos para diferentes contêineres.

```powershell
$DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
```

```powershell
$DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
```

```powershell
$DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
```

Os três comandos finais adicionam discos de dados para a máquina virtual armazenada na *$VirtualMachine*. Cada comando Especifica o nome, local e as propriedades adicionais do disco. O URI de cada disco é armazenado no *US $DataDiskVhdUri01*, *US $DataDiskVhdUri02*, e *$DataDiskVhdUri03*.

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk1' `
                -Caching 'ReadOnly' -DiskSizeInGB 10 -Lun 0 `
                -VhdUri $DataDiskVhdUri01 -CreateOption Empty
```

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk2' `
                -Caching 'ReadOnly' -DiskSizeInGB 11 -Lun 1 `
                -VhdUri $DataDiskVhdUri02 -CreateOption Empty
```

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk3' `
                -Caching 'ReadOnly' -DiskSizeInGB 12 -Lun 2 `
                -VhdUri $DataDiskVhdUri03 -CreateOption Empty
```

Use os seguintes comandos do PowerShell para adicionar a configuração de rede e disco de sistema operacional para a máquina virtual e, em seguida, inicie a nova VM.

```powershell
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

# Create a network security group configuration
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
O primeiro comando obtém a máquina virtual chamada de máquina virtual usando o **Get-AzureRmVM** cmdlet. O comando armazena a máquina virtual na variável *$VirtualMachine* .

```powershell
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" `
                                -Name "VirtualMachine"
```
Os próximos três comandos atribuam caminhos de três discos de dados para as variáveis $DataDiskVhdUri01, DataDiskVhdUri02 $ e $DataDiskVhdUri03.  Os nomes de caminho diferentes a vhduri indicam diferentes contêineres para o posicionamento de disco.
```powershell
$DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
```
```powershell
$DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
```
```powershell
$DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
```


Os próximos três comandos adicionam os discos de dados para a máquina virtual armazenada na *$VirtualMachine* variável. Cada comando Especifica o nome, local e as propriedades adicionais do disco. O URI de cada disco é armazenado no *US $DataDiskVhdUri01*, *US $DataDiskVhdUri02*, e *$DataDiskVhdUri03*.

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" `
                      -VhdUri $DataDiskVhdUri01 -LUN 0 `
                      -Caching ReadOnly -DiskSizeinGB 10 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk2" `
                      -VhdUri $DataDiskVhdUri02 -LUN 1 `
                      -Caching ReadOnly -DiskSizeinGB 11 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk3" `
                      -VhdUri $DataDiskVhdUri03 -LUN 2 `
                      -Caching ReadOnly -DiskSizeinGB 12 -CreateOption Empty
```

O comando final atualiza o estado da máquina virtual armazenada no *$VirtualMachine* em –*ResourceGroupName*.

```powershell
Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre máquinas virtuais do Azure Stack, consulte [considerações para máquinas virtuais no Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-considerations).
