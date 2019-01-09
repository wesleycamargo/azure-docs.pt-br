---
title: Habilitar Azure Disk Encryption para VMs de IaaS do Windows
description: Este artigo fornece instruções sobre como habilitar as VMs da IaaS do Microsoft Azure Disk Encryption para Windows.
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 12/17/2018
ms.custom: seodec18
ms.openlocfilehash: 0051c7ca66d30730e6fc25b8b9d3edec91c43f07
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548639"
---
# <a name="enable-azure-disk-encryption-for-windows-iaas-vms"></a>Habilitar Azure Disk Encryption para VMs de IaaS do Windows

Você pode habilitar muitos cenários de criptografia de disco, e as etapas podem variar de acordo com o cenário. As seções a seguir abordam os cenários com mais detalhes para VMs da IaaS do Windows. Antes de poder usar a criptografia de disco, os [pré-requisitos do Azure Disk Encryption](../security/azure-security-disk-encryption-prerequisites.md) devem ser cumpridos. 

Tire um [instantâneo](../virtual-machines/windows/snapshot-copy-managed-disk.md) e/ou faça backup antes que os discos sejam criptografados. Os backups garantem que uma opção de recuperação seja possível, no caso de uma falha inesperada durante a criptografia. VMs com discos gerenciados exigem um backup antes que a criptografia ocorra. Depois que um backup é feito, você poderá usar o cmdlet Set-AzureRmVMDiskEncryptionExtension para criptografar discos gerenciados, especificando o parâmetro -skipVmBackup. Para obter mais informações sobre como fazer backup e restaurar VMs criptografadas, consulte o artigo [Backup do Microsoft Azure](../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
> - Se você já tiver usado o [Azure Disk Encryption com aplicativo do Microsoft Azure Active Directory](azure-security-disk-encryption-prerequisites-aad.md) para criptografar essa máquina virtual, precisará continuar a usar essa opção para criptografar a sua VM. Não é possível usar o [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) nessa máquina virtual criptografada pois esse cenário não é suportado, o que significa abandonar o aplicativo Azure Active Directory nessa VM criptografada que ainda não tem suporte. 
> - O Azure Disk Encryption precisa que o cofre de chaves e as VMs estejam localizados na mesma região. Crie e use um cofre de chaves que esteja na mesma região da VM a ser criptografada. 


## <a name="bkmk_RunningWinVM"></a> Habilitar criptografia em VMs do Windows da IaaS existentes ou em execução
Nesse cenário, é possível habilitar a criptografia usando um modelo, cmdlets do PowerShell ou comandos da CLI. As seções a seguir explicam com mais detalhes como habilitar o Azure Disk Encryption. Se você precisar de informações de esquema para a extensão de máquina virtual, consulte o [Azure Disk Encryption para extensão do artigo Windows](../virtual-machines/extensions/azure-disk-enc-windows.md).

>[!IMPORTANT]
 >É obrigatório tirar um instantâneo e/ou fazer backup de uma instância de VM baseada em disco gerenciado fora do Azure Disk Encryption e antes de habilitá-lo. Um instantâneo do disco gerenciado pode ser obtido do portal ou pode ser usado o [Backup do Azure](../backup/backup-azure-vms-encryption.md). Backups asseguram que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a criptografia. Depois que um backup é feito, o cmdlet Set-AzureRmVMDiskEncryptionExtension pode ser usado para criptografar Managed Disks especificando o parâmetro -skipVmBackup. O comando Set-AzureRmVMDiskEncryptionExtension falhará nas VMs baseadas em disco gerenciado até que um backup seja feito e esse parâmetro tenha sido especificado. 
>
>Criptografar ou desabilitar a criptografia pode fazer com que a VM seja reiniciada. 
>

### <a name="bkmk_RunningWinVMPSH"></a> Habilitar criptografia em VMs existentes ou em execução com Azure PowerShell 
Use o cmdlet [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) para habilitar a criptografia em uma máquina virtual da IaaS em execução no Azure. 

-  **Criptografar uma VM em execução:** o script abaixo inicializa as variáveis e executa o cmdlet Set-AzureRmVMDiskEncryptionExtension. O grupo de recursos, a VM e o cofre de chaves já devem ter sido criados como pré-requisitos. Substitua MySecureRg, MySecureVM e MySecureVault por seus valores.

     ```azurepowershell-interactive
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Criptografar uma VM em execução usando KEK:** 

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > A sintaxe para o valor do parâmetro diskvacryption-keyvault é a cadeia de caracteres do identificador completo:/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro key-encryption-key é o URI completo para KEK, como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Verificar se os discos estão criptografados:** para verificar o status de criptografia de uma VM IaaS, use o cmdlet [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus). 
     ```azurepowershell-interactive
     Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName 'MySecureRg' -VMName 'MySecureVM'
     ```
    
- **Desabilitar criptografia de disco:** para desabilitar a criptografia, use o cmdlet [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption). Desabilitar a criptografia de disco de dados na máquina virtual do Windows quando discos de dados e sistema operacional tem sido criptografados não funciona conforme o esperado. Desabilite a criptografia em todos os discos em vez disso.

     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
     ```

### <a name="bkmk_RunningWinVMCLI"></a>Habilitar criptografia em VMs existentes ou em execução com CLI do Azure
Use o comando [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) para habilitar a criptografia em uma máquina virtual da IaaS em execução no Azure.

-  **Criptografar uma VM em execução:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Criptografar uma VM em execução usando KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > A sintaxe para o valor do parâmetro diskvacryption-keyvault é a cadeia de caracteres do identificador completo:/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> A sintaxe para o valor do parâmetro key-encryption-key é o URI completo para KEK, como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Verificar se os discos estão criptografados:** Para verificar o status de criptografia de uma VM IaaS, use o comando [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show). 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
     ```

- **Desabilitar criptografia:** para desabilitar a criptografia, use o comando [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). Desabilitar a criptografia de disco de dados na máquina virtual do Windows quando discos de dados e sistema operacional tem sido criptografados não funciona conforme o esperado. Desabilite a criptografia em todos os discos em vez disso.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type [ALL, DATA, OS]
     ```
 
 > [!NOTE]
 >É obrigatório tirar um instantâneo e/ou fazer backup de uma instância de VM baseada em disco gerenciado fora do Azure Disk Encryption e antes de habilitá-lo. Um instantâneo do disco gerenciado pode ser obtido do portal ou pode ser usado o [Backup do Azure](../backup/backup-azure-vms-encryption.md). Backups asseguram que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a criptografia. Depois que um backup é feito, o cmdlet Set-AzureRmVMDiskEncryptionExtension pode ser usado para criptografar Managed Disks especificando o parâmetro -skipVmBackup. Esse comando falhará nas VMs baseadas em disco gerenciado até que um backup seja feito e esse parâmetro tenha sido especificado. 
>
>Criptografar ou desabilitar a criptografia pode fazer com que a VM seja reiniciada. 

### <a name="bkmk_RunningWinVMwRM"> </a>Usar o modelo do Resource Manager
É possível habilitar a criptografia de disco em VMs do Windows da IaaS em execução ou existentes no Azure usando o [modelo do Resource Manager para criptografar uma VM do Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).


1. No modelo de início rápido do Azure, clique em **Implantar no Azure**.

2. Selecione a assinatura, o grupo de recursos, o local, as configurações, os termos legais e o contrato. Clique em **Comprar** para habilitar a criptografia na VM da IaaS em execução ou existente.

A tabela a seguir lista os parâmetros de modelo do Resource Manager existentes para as VMs em execução ou existentes:

| Parâmetro | DESCRIÇÃO |
| --- | --- |
| vmName | Nome da VM para executar a operação de criptografia. |
| keyVaultName | Nome do cofre de chaves no qual a chave do BitLocker deve ser carregada. É possível obtê-lo, usando o cmdlet `(Get-AzureRmKeyVault -ResourceGroupName <MyResourceGroupName>). Vaultname` ou o comando da CLI do Azure `az keyvault list --resource-group "MySecureGroup" |ConvertFrom-JSON`|
| keyVaultResourceGroup | Nome do grupo de recursos que contém o cofre de chaves|
|  keyEncryptionKeyURL | URL da chave de criptografia de chaves que é usada para criptografar a chave gerada do BitLocker. Esse parâmetro será opcional se você selecionar **nokek** na lista suspensa UseExistingKek. Se selecionar **kek** na lista suspensa UseExistingKek, você deverá inserir o valor _keyEncryptionKeyURL_. |
| volumeType | Tipo de volume em que a operação de criptografia é executada. Os valores válidos são _OS_, _Data_ e _All_. 
| forceUpdateTag | Passe um valor exclusivo como um GUID sempre que a execução da operação precise ser forçada. |
| resizeOSDisk | A partição do SO deve ser redimensionada para ocupar o VHD do SO completo antes de dividir o volume do sistema. |
| location | Local de todos os recursos. |

## <a name="encrypt-virtual-machine-scale-sets"></a>Criptografar os conjuntos de dimensionamento de máquinas virtuais

Os [conjuntos de dimensionamento de máquinas virtuais do Azure](../virtual-machine-scale-sets/overview.md) permitem criar e gerenciar um grupo de VMs idênticas e com balanceamento de carga. O número de instâncias de VM pode aumentar ou diminuir automaticamente em resposta à demanda ou a um agendamento definido. Use a CLI ou o Azure PowerShell para criptografar conjuntos de dimensionamento de máquinas virtuais.


### <a name="register-for-disk-encryption-preview-using-azure-powershell"></a>Registre-se para visualização de criptografia de disco usando o Azure PowerShell

O Azure Disk Encryption para a versão prévia de conjuntos de dimensionamento de máquinas virtuais requer que você faça o registro da sua própria assinatura com [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature). Você só precisará executar as seguintes etapas na primeira vez que você usar o recurso de visualização de criptografia de disco:

```azurepowershell-interactive
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

Pode levar até 10 minutos para que a solicitação registro seja propagada. É possível verificar o status do registro com [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). Quando o `RegistrationState` relatar *Registrado*, registre novamente o provedor *Microsoft.Compute* com [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider):

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="encrypt-virtual-machine-scale-sets-with-azure-powershell"></a>Criptografar conjuntos de dimensionamento de máquinas virtuais com o Azure PowerShell

Use o cmdlet [Set-AzureRmVmssDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmssdiskencryptionextension) para habilitar a criptografia em um conjunto de dimensionamento de máquinas virtuais do Windows. O grupo de recursos, a VM e o cofre de chaves já devem ter sido criados como pré-requisitos.

-  **Criptografar um conjunto de dimensionamento de máquinas virtuais em execução**:
    ```azurepowershell-interactive
     $rgName= "MySecureRg";
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgName;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;


-  **Encrypt a running virtual machine scale set using KEK to wrap the key**:

    ```azurepowershell-interactive
     $rgName= "MySecureRg";
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $keyEncryptionKeyName = "MyKeyEncryptionKey";
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgName;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
    ```

- **Obter o status de criptografia de um conjunto de dimensionamento de máquinas virtuais:** Use o cmdlet [Get-AzureRmVmssVMDiskEncryption](/powershell/module/azurerm.compute/get-azurermvmssvmdiskencryption).
    
    ```azurepowershell-interactive
    get-AzureRmVmssVMDiskEncryption -ResourceGroupName "MySecureRG" -VMScaleSetName "MySecureVmss"
    ```

- **Desabilitar a criptografia em um conjunto de dimensionamento de máquinas virtuais**: Use o cmdlet [Disable-AzureRmVmssDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmssdiskencryption). 

    ```azurepowershell-interactive
    Disable-AzureRmVmssDiskEncryption -ResourceGroupName "MySecureRG" -VMScaleSetName "MySecureVmss"
    ```

### <a name="register-for-disk-encryption-preview-using-azure-cli"></a>Registre-se para visualização de criptografia de disco usando a CLI do Azure

O Azure Disk Encryption para a versão prévia de conjuntos de dimensionamento de máquinas virtuais requer que você faça o registro da sua própria assinatura com [az feature register](/cli/azure/feature#az-feature-register). Você só precisará executar as seguintes etapas na primeira vez que você usar o recurso de visualização de criptografia de disco:

```azurecli-interactive
az feature register --name UnifiedDiskEncryption --namespace Microsoft.Compute
```

Pode levar até 10 minutos para que a solicitação registro seja propagada. É possível verificar o status do registro com [az feature show](/cli/azure/feature#az-feature-show). Quando `State` reportar *Registrado*, registre novamente o *provedor Microsoft.Compute* com [registro do provedor de sincronização az](/cli/azure/provider#az-provider-register):

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```



###  <a name="encrypt-virtual-machine-scale-sets-with-azure-cli"></a>Criptografar conjuntos de dimensionamento de máquinas virtuais com a CLI do Azure

Use [az vmss encryption enable](/cli/azure/vmss/encryption#az-vmss-encryption-enable) para habilitar a criptografia em um conjunto de dimensionamento de máquinas virtuais do Windows. Se você definir a política de atualização no conjunto de dimensionamento como manual, inicie a criptografia com [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances). O grupo de recursos, a VM e o cofre de chaves já devem ter sido criados como pré-requisitos.

-  **Criptografar um conjunto de dimensionamento de máquinas virtuais em execução**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MySecureRG" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" 
    ```

-  **Criptografar um conjunto de dimensionamento de máquinas virtuais em execução usando KEK para encapsular a chave**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MySecureRG" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" --key-encryption-key "MyKEK" --key-encryption-keyvault "MySecureVault" 

     ```
- **Obter o status de criptografia de um conjunto de dimensionamento de máquinas virtuais:** use [az vmss encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show)

    ```azurecli-interactive
     az vmss encryption show --resource-group "MySecureRG" --name "MySecureVmss"
    ```

- **Desabilitar a criptografia em um conjunto de dimensionamento de máquinas virtuais**: use [az vmss encryption disable](/cli/azure/vmss/encryption#az-vmss-encryption-disable)
    ```azurecli-interactive
     az vmss encryption disable --resource-group "MySecureRG" --name "MySecureVmss"
    ```

### <a name="azure-resource-manager-templates-for-windows-virtual-machine-scale-sets"></a>Modelos do Azure Resource Manager para conjuntos de dimensionamento de máquinas virtuais do Windows

Para criptografar ou descriptografar conjuntos de dimensionamento de máquinas virtuais do Windows, use os modelos do Azure Resource Manager e as instruções abaixo:

- [Habilitar a criptografia em um conjunto de dimensionamento de máquinas virtuais do Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)
- [Implantar um conjunto de dimensionamento de VMs do Windows com um jumpbox e habilitar a criptografia no conjunto de dimensionamento de VMs do Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)
- [Desabilitar a criptografia em um conjunto de dimensionamento de VMs do Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

     1. Clique em **Implantar no Azure**.
     2. Preencha os campos obrigatórios e concorde com os termos e condições.
     3. Clique em **Comprar** para implantar o modelo.

## <a name="bkmk_VHDpre"> </a> Novas VMs da IaaS criadas a partir de chaves de criptografia e VHD criptografado pelo cliente

Nesse cenário, é possível habilitar a criptografia usando cmdlets do PowerShell ou comandos da CLI. 

Use as instruções no apêndice para preparar imagens previamente criptografadas que podem ser usadas no Azure. Depois que a imagem for criada, você poderá usar as etapas na próxima seção para criar uma VM do Azure criptografada.

* [Preparar um VHD do Windows previamente criptografado](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Preparar um VHD Linux previamente criptografado](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >É obrigatório tirar um instantâneo e/ou fazer backup de uma instância de VM baseada em disco gerenciado fora do Azure Disk Encryption e antes de habilitá-lo. Um instantâneo do disco gerenciado pode ser obtido do portal ou pode ser usado o [Backup do Azure](../backup/backup-azure-vms-encryption.md). Backups asseguram que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a criptografia. Depois que um backup é feito, o cmdlet Set-AzureRmVMDiskEncryptionExtension pode ser usado para criptografar Managed Disks especificando o parâmetro -skipVmBackup. O comando Set-AzureRmVMDiskEncryptionExtension falhará nas VMs baseadas em disco gerenciado até que um backup seja feito e esse parâmetro tenha sido especificado. 
>
>Criptografar ou desabilitar a criptografia pode fazer com que a VM seja reiniciada. 


### <a name="bkmk_VHDprePSH"> </a> Criptografar VMs com VHDs previamente criptografados com Azure PowerShell
É possível habilitar a criptografia de disco no VHD criptografado usando o cmdlet do PowerShell [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk#examples). O exemplo abaixo fornece alguns parâmetros comuns. 

```azurepowershell-interactive
$VirtualMachine = New-AzureRmVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzureRmVM -VM $VirtualMachine -ResourceGroupName "MySecureRG"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Habilitar criptografia em um disco de dados adicionado recentemente
É possível [adicionar um novo disco a uma VM do Windows usando o PowerShell ](../virtual-machines/windows/attach-disk-ps.md) ou [por meio do portal do Azure](../virtual-machines/windows/attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Habilitar criptografia em um disco adicionado recentemente com Azure PowerShell
 Ao usar o Powershell para criptografar um novo disco para VMs do Windows, uma nova versão da sequência deverá ser especificada. A versão da sequência deverá ser exclusiva. O script abaixo gera um GUID para a versão da sequência. Em alguns casos, um disco de dados adicionado recentemente pode ser criptografado automaticamente pela extensão do Azure Disk Encryption. A criptografia automática geralmente ocorre quando a VM é reinicializada depois que o novo disco fica online. Isso geralmente é causado porque "All" foi especificado para o tipo de volume quando a criptografia de disco foi executada anteriormente na VM. Se a criptografia automática ocorrer em um disco de dados recém-adicionado, recomendamos a execução do cmdlet Set-AzureRmVmDiskEncryptionExtension novamente com a nova versão de sequência. Se o seu novo disco de dados for criptografado automaticamente e você não quiser ser criptografado, primeiro descriptografe todas as unidades e criptografe novamente com uma nova versão de sequência, especificando o SO para o tipo de volume. 
  
 

-  **Criptografar uma VM em execução:** o script abaixo inicializa as variáveis e executa o cmdlet Set-AzureRmVMDiskEncryptionExtension. O grupo de recursos, a VM e o cofre de chaves já devem ter sido criados como pré-requisitos. Substitua MySecureRg, MySecureVM e MySecureVault por seus valores. Este exemplo usa "All" para o parâmetro -VolumeType, que inclui ambos os volumes de Dados e SO. Se você quiser apenas criptografar o volume do SO, use "OS" para o parâmetro -VolumeType. 

     ```azurepowershell-interactive
      $sequenceVersion = [Guid]::NewGuid();
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;
    ```
- **Criptografar uma VM em execução usando KEK:** Este exemplo usa "All" para o parâmetro -VolumeType, que inclui ambos os volumes de Dados e SO. Se você quiser apenas criptografar o volume do SO, use "OS" para o parâmetro -VolumeType.

     ```azurepowershell-interactive
     $sequenceVersion = [Guid]::NewGuid();
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > A sintaxe para o valor do parâmetro diskvacryption-keyvault é a cadeia de caracteres do identificador completo:/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro key-encryption-key é o URI completo para KEK, como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Habilitar criptografia em um disco adicionado recentemente com CLI do Azure
 O comando da CLI do Azure fornecerá automaticamente uma nova versão da sequência quando você executar o comando para habilitar a criptografia. Os exemplos usam "All" para o parâmetro do tipo de volume. Talvez seja necessário alterar o parâmetro do tipo de volume para disco do SO. Em contraste com a sintaxe do Powershell, a CLI não exige que o usuário forneça uma versão de sequência exclusiva ao habilitar a criptografia. A CLI gera e usa automaticamente o próprio valor de versão de sequência exclusivo.   

-  **Criptografar uma VM em execução:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Criptografar uma VM em execução usando KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>Desabilitar criptografia
É possível desabilitar a criptografia usando o Azure PowerShell, a CLI do Azure ou com um modelo do Resource Manager. Desabilitar a criptografia de disco de dados na máquina virtual do Windows quando discos de dados e sistema operacional tem sido criptografados não funciona conforme o esperado. Desabilite a criptografia em todos os discos em vez disso.

- **Desabilitar a criptografia de disco com o Azure PowerShell:** para desabilitar a criptografia, use o cmdlet [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption). 
     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Desabilitar a criptografia com a CLI do Azure:** para desabilitar a criptografia, use o comando [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type "all"
     ```
- **Desabilitar criptografia com um modelo do Resource Manager:** 

    1. Clique em **Implantar no Azure** no modelo [Desabilitar criptografia de disco na VM do Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad).
    2. Selecione a assinatura, o grupo de recursos, o local, a VM, o tipo de volume, os termos legais e o contrato.
    3.  Clique em **Comprar** para desabilitar a criptografia de disco em uma VM do Windows em execução. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Habilitar o Azure Disk Encryption para Linux](azure-security-disk-encryption-linux.md)
