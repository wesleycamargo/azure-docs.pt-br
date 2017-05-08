---
title: Migrar VMs do AWS para o Azure | Microsoft Docs
description: "Migre uma instância EC2 do AWS (Amazon Web Services) para Máquinas Virtuais do Azure. Esse cenário usa o Managed Disks para simplificar o armazenamento em nuvem."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: a6549999003d4b1c8e2b8a8e2a2fafef942bce43
ms.lasthandoff: 04/27/2017


---

# <a name="migrate-from-amazon-web-services-aws-to-azure-managed-disks"></a>Migrar do AWS (Amazon Web Services) para o Managed Disks do Azure

Você pode migrar uma instância EC2 do AWS (Amazon Web Services) para o Azure carregando o VHD. Se quiser criar múltiplas VMs no Azure usando a mesma imagem, primeiramente, você deverá generalizar a VM e depois exportar o VHD generalizado para um diretório local. Depois que o VHD for carregado, você poderá criar uma nova VM do Azure que use o [Managed Disks](../../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para armazenamento. Com o Managed Disks do Azure, não é preciso gerenciar as contas de armazenamento para VMs IaaS do Azure. Você só precisa especificar o tipo (Premium ou Standard) e o tamanho do disco necessário, e o Azure vai criar e gerenciar o disco para você. 

Antes de iniciar esse processo, não se esqueça de ler [Plan for the migration to Managed Disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) (Planejar para a migração no Managed Disks).

Antes de carregar qualquer VHD no Azure, você deve seguir as etapas em [Preparar um VHD ou VHDX do Windows para carregar no Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="before-you-begin"></a>Antes de começar
Caso use o PowerShell, verifique se você tem a versão mais recente do módulo AzureRM.Compute do PowerShell. Execute o comando a seguir para instalá-lo.

```powershell
Install-Module AzureRM.Compute -MinimumVersion 2.6.0
```
Para saber mais, confira [Azure PowerShell Versioning](/powershell/azure/overview) (Controle de versão do Azure PowerShell).


## <a name="generalize-the-windows-vm-using-sysprep"></a>Generalizar a VM Windows usando Sysprep

Generalizar uma VM usando Sysprep remove todas as informações específicas à máquina, bem como informações de conta pessoal do VHD e a prepara para ser usada como uma imagem. Para obter detalhes sobre o Sysprep, consulte [Como usar o Sysprep: uma introdução](http://technet.microsoft.com/library/bb457073.aspx).

Verifique se as funções de servidor em execução no computador são suportadas pelo Sysprep. Para obter mais informações, consulte [Suporte do Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Se você estiver executando o Sysprep antes de carregar o VHD para o Azure pela primeira vez, verifique se você [preparou sua VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) antes de executar o Sysprep. 
> 
> 

1. Entre na máquina virtual Windows.
2. Abra uma janela de prompt de comando como administrador. Altere o diretório para **%windir%\system32\sysprep** e, a seguir, execute`sysprep.exe`.
3. Na caixa de diálogo **Ferramenta de Preparação do Sistema**, selecione **Entrar na Configuração Inicial pelo Usuário do Sistema (OOBE)** e verifique se a caixa de seleção **Generalizar** está marcada.
4. Em **Opções de Desligamento**, selecione **Desligar**.
5. Clique em **OK**.
   
    ![Inicie o Sysprep](./media/aws-to-azure/sysprepgeneral.png)
6. Quando o Sysprep for concluído, desligará a máquina virtual. Não reinicie a VM.



## <a name="export-the-vhd-from-an-ec2-instance"></a>Exportar o VHD de uma instância EC2

1.    Se você estiver usando o AWS (Amazon Web Services), exporte a instância EC2 para um VHD em um bucket do Amazon S3. Execute as etapas descritas na documentação do Amazon para Exportar instâncias do Amazon EC2, a fim de instalar a ferramenta de interface de linha de comando (CLI) do Amazon EC2. Em seguida, execute o comando create-instance-export-task para exportar a instância do EC2 para um arquivo VHD. Certifique-se de usar o VHD para a variável DISK_IMAGE_FORMAT ao executar o comando create-instance-export-task. O arquivo VHD exportado é salvo no depósito do Amazon S3 designado durante esse processo.

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT '
    --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2.    Baixe o arquivo VHD do depósito S3. Selecione o arquivo VHD e **Ações** > **Baixar**.



## <a name="log-in-to-azure"></a>Fazer logon no Azure
Se você ainda não tiver a versão do PowerShell instalada, leia [How to install and configure Azure PowerShell](/powershell/azure/overview) (Como instalar e configurar o Azure PowerShell).

1. Abra o Azure PowerShell e conecte-se à sua conta do Azure. Uma janela pop-up é aberta para inserir as credenciais da conta do Azure.
   
    ```powershell
    Login-AzureRmAccount
    ```
2. Obtenha as IDs de assinatura das assinaturas disponíveis.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Defina a assinatura correta usando a ID da assinatura. Substitua `<subscriptionID>` com a ID da assinatura correta.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="get-the-storage-account"></a>Obter a conta de armazenamento
Você precisa de uma conta de armazenamento no Azure para armazenar a imagem da VM carregada. Você pode usar uma conta de armazenamento existente ou criar uma nova. 

Se for o usar o VHD para criar um disco gerenciado para uma VM, o local da conta de armazenamento deverá ser o mesmo local em que você criará a VM.

Para exibir as contas de armazenamento disponíveis, digite:

```powershell
Get-AzureRmStorageAccount
```

Se você quiser usar uma conta de armazenamento existente, vá para a seção [Carregar a imagem da VM](#upload-the-vm-vhd-to-your-storage-account).

Se você precisa criar uma conta de armazenamento, siga estas etapas:

1. Você precisa do nome do grupo de recursos no qual a conta de armazenamento deve ser criada. Para saber quais são todos os grupos de recursos que estão em sua assinatura, digite:
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    Para criar um grupo de recursos denominado **myResourceGroup** na região **Oeste dos EUA**, digite:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Crie uma conta de armazenamento com o nome **mystorageaccount** neste grupo de recursos usando o cmdlet [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount):
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
    Os valores válidos para -SkuName são:
   
   * **Standard_LRS** – Armazenamento com redundância local. 
   * **Standard_ZRS** – Armazenamento com redundância de zona.
   * **Standard_GRS** – Armazenamento com redundância geográfica. 
   * **Standard_RAGRS** – Armazenamento com redundância geográfica com acesso de leitura. 
   * **Premium_LRS** – Armazenamento com redundância local Premium. 

## <a name="upload-the-vhd-to-your-storage-account"></a>Carregar o VHD na sua conta de armazenamento

Use o cmdlet [Add-AzureRmVhd](/powershell/module/azurerm.compute/add-azurermvhd) para carregar o VHD em um contêiner na conta de armazenamento. Este exemplo carrega o arquivo **myVHD.vhd** de `"C:\Users\Public\Documents\Virtual hard disks\"` em uma conta de armazenamento denominada **mystorageaccount** no grupo de recursos **myResourceGroup**. O arquivo será colocado em um contêiner chamado **mycontainer** e o novo nome do arquivo será **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Se o comando tiver êxito, você receberá uma resposta semelhante a esta:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

Dependendo da conexão de rede e do tamanho do arquivo VHD, esse comando pode demorar um pouco para ser concluído

Salve o caminho do **URI de Destino** para usá-lo posteriormente caso queira criar um disco gerenciado ou uma nova VM usando o VHD carregado.

### <a name="other-options-for-uploading-a-vhd"></a>Outras opções para carregar um VHD

Você também pode carregar um VHD na sua conta de armazenamento usando um dos seguintes métodos:

-   [API do Blob da Cópia de Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd894037.aspx)

-   [Carregamento de Blobs do Gerenciador de Armazenamento do Azure](https://azurestorageexplorer.codeplex.com/)

-   [Referência de API REST do Serviço de Importação/Exportação do Armazenamento](https://msdn.microsoft.com/library/dn529096.aspx)

    É recomendável usar o Serviço de Importação/Exportação se o tempo de carregamento estimado é de mais de sete dias. Você pode usar [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) para estimar o tempo com base no tamanho dos dados e na unidade de transferência. 

    A Importação/Exportação pode ser usada para copiar para a conta de armazenamento standard. Você precisará copiar do armazenamento standard para a conta de armazenamento premium usando uma ferramenta como o AzCopy.

## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Criar uma imagem gerenciada usando o VHD carregado 

Crie uma imagem gerenciada usando o VHD do SO generalizado.


1.  Primeiro, defina os parâmetros comuns:

    ```powershell
    $rgName = "myResourceGroupName"
    $vmName = "myVM"
    $location = "West Central US" 
    $imageName = "yourImageName"
    $osVhdUri = "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd"
    ```

4.  Crie a imagem usando o VHD do SO generalizado.

    ```powershell
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

## <a name="setup-some-variables-for-the-image"></a>Configurar algumas variáveis para a imagem

Primeiramente, precisamos coletar informações básicas sobre a imagem e criar uma variável para ela. Este exemplo usa uma imagem de VM gerenciada chamada **myImage**, que está no grupo de recursos **myResourceGroup** no local **Centro-Oeste dos EUA**. 

```powershell
$rgName = "myResourceGroup"
$location = "West Central US"
$imageName = "myImage"
$image = Get-AzureRMImage -ImageName $imageName -ResourceGroupName $rgName
```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual
Crie a vNet e a sub-rede da [rede virtual](../../virtual-network/virtual-networks-overview.md).

1. Crie a sub-rede. Este exemplo cria uma sub-rede chamada **mySubnet** com um prefixo de endereço de **10.0.0.0/24**.  
   
    ```powershell
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Crie a rede virtual. Este exemplo cria uma rede virtual chamada **myVnet** com o prefixo de endereço de **10.0.0.0/16**.  
   
    ```powershell
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-network-interface"></a>Criar um endereço IP público e um adaptador de rede

Para habilitar a comunicação com a máquina virtual na rede virtual, são necessários um [endereço IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) e um adaptador de rede.

1. Criar um endereço IP público. Este exemplo cria um endereço IP público chamado **myPip**. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Crie a NIC. Este exemplo cria uma NIC chamada **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Criar o grupo de segurança de rede e uma regra RDP

Para fazer logon em sua VM usando RDP, é preciso ter uma NSG (regra de segurança de rede) que permita acesso de RDP na porta 3389. 

Este exemplo cria um NSG chamado **myNsg** que contém uma regra chamada **myRdpRule** que permite tráfego RDP pela porta 3389. Para obter mais informações sobre NSGs, consulte [Abrir portas para uma VM no Azure usando PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"
$ruleName = "myRdpRule"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name $ruleName -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


## <a name="create-a-variable-for-the-virtual-network"></a>Criar uma variável para a rede virtual

Crie uma variável para a rede virtual concluída. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName

```

## <a name="get-the-credentials-for-the-vm"></a>Obter as credenciais para a VM

O cmdlet a seguir abrirá uma janela na qual você vai inserir um novo nome de usuário e senha para usar como a conta de administrador local para acessar remotamente a VM. 

```powershell
$cred = Get-Credential
```

## <a name="set-variables-for-the-vm-name-computer-name-and-the-size-of-the-vm"></a>Definir variáveis para o nome da VM, o nome do computador e o tamanho da VM

1. Crie variáveis para o nome da VM e do computador. Este exemplo define o nome da VM como **myVM** e o nome do computador como **myComputer**.

    ```powershell
    $vmName = "myVM"
    $computerName = "myComputer"
    ```
2. Defina o tamanho da máquina virtual. Este exemplo cria a VM de tamanho **Standard_DS1_v2**. Veja a documentação sobre os [tamanhos de VM](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) para obter mais informações.

    ```powershell
    $vmSize = "Standard_DS1_v2"
    ```

3. Adicione o nome e o tamanho da VM à configuração da VM.

```powershell
$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
```

## <a name="set-the-vm-image-as-source-image-for-the-new-vm"></a>Definir a imagem da VM como imagem de origem para a nova VM

Defina a imagem de origem usando a ID da imagem da VM gerenciada.

```powershell
$vm = Set-AzureRmVMSourceImage -VM $vm -Id $image.Id
```

## <a name="set-the-os-configuration-and-add-the-nic"></a>Defina a configuração do SO e adicione a NIC.

Insira o tipo de armazenamento (PremiumLRS ou StandardLRS) e o tamanho do disco do SO. Este exemplo define o tipo de conta como **PremiumLRS**, o tamanho do disco como **128 GB** e o cache do disco como **ReadWrite**.

```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm  -ManagedDiskStorageAccountType PremiumLRS -DiskSizeInGB 128 `
-CreateOption FromImage -Caching ReadWrite

$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $computerName `
-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

## <a name="create-the-vm"></a>Criar a VM

Crie a nova VM usando a configuração que criamos e armazenamos na variável **$vm**.

```powershell
New-AzureRmVM -VM $vm -ResourceGroupName $rgName -Location $location
```

## <a name="verify-that-the-vm-was-created"></a>Verificar se a VM foi criada
Ao concluir, você deverá ver a VM recém-criada no [portal do Azure](https://portal.azure.com) em **Navegar** > **Máquinas virtuais** ou usando os seguintes comandos do PowerShell:

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Próximas etapas

Para entrar em sua nova máquina virtual, navegue até a VM no [portal](https://portal.azure.com), clique em **Conectar**e abra o arquivo RDP da Área de Trabalho Remota. Use as credenciais da conta da máquina virtual original para entrar na nova máquina virtual. Para obter mais informações, veja [Como se conectar e fazer logon em uma máquina virtual do Azure executando o Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
 

