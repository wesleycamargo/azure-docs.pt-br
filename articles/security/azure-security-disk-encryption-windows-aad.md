---
title: Habilitar o Azure Disk Encryption com o aplicativo do Azure AD para VMs de IaaS do Windows (versão anterior) | Microsoft Docs
description: Este artigo fornece instruções sobre como habilitar as VMs da IaaS do Microsoft Azure Disk Encryption para Windows.
services: security
documentationcenter: na
author: mestew
manager: MBaldwin
ms.assetid: 30b1151b-3314-4526-ba4e-4f6b23b1b280
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2018
ms.author: mstewart
ms.openlocfilehash: 2ecc177f810c9a3fe2d096b71aed4886cf89d7b4
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42889077"
---
#  <a name="enable-azure-disk-encryption-for-windows-iaas-vms-previous-release"></a>Habilitar Azure Disk Encryption para VMs da IaaS do Windows (versão anterior)

**A nova versão do Azure Disk Encryption elimina a necessidade de fornecer um parâmetro de aplicativo do Azure AD para habilitar a criptografia de disco de VM. Com a nova versão, você não precisa fornecer credenciais do Azure AD durante a etapa de habilitação de criptografia. Todas as novas VMs devem ser criptografadas sem os parâmetros do aplicativo do Azure AD, usando a nova versão. Para exibir instruções para habilitar a criptografia de disco de VM usando a nova versão, veja [Azure Disk Encryption para VMs do Windows](azure-security-disk-encryption-windows.md). As VMs que já foram criptografadas com os parâmetros de aplicativo do Azure AD ainda têm suporte e devem continuar a ser mantidas com a sintaxe do AAD.**


Você pode habilitar muitos cenários de criptografia de disco, e as etapas podem variar de acordo com o cenário. As seções a seguir abordam os cenários com mais detalhes para VMs da IaaS do Windows. Antes de poder usar a criptografia de disco, os [pré-requisitos do Azure Disk Encryption](../security/azure-security-disk-encryption-prerequisites-aad.md) devem ser cumpridos. 

Tire um [instantâneo](../virtual-machines/windows/snapshot-copy-managed-disk.md) e/ou faça backup antes que os discos sejam criptografados. Os backups garantem que uma opção de recuperação seja possível, no caso de uma falha inesperada durante a criptografia. VMs com discos gerenciados exigem um backup antes que a criptografia ocorra. Depois que um backup é feito, você poderá usar o cmdlet Set-AzureRmVMDiskEncryptionExtension para criptografar discos gerenciados, especificando o parâmetro -skipVmBackup. Para obter mais informações sobre como fazer backup e restaurar VMs criptografadas, consulte o artigo [Backup do Microsoft Azure](../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
>Para garantir que os segredos de criptografia não ultrapassem os limites regionais, o Azure Disk Encryption precisa que o Key Vault e as VMs sejam colocados na mesma região. Crie e use um cofre de chaves que esteja na mesma região da VM a ser criptografada. 


## <a name="enable-encryption-on-new-iaas-vms-created-from-the-marketplace"></a>Habilitar criptografia em novas VMs da IaaS criadas a partir do Marketplace
É possível habilitar criptografia de disco na nova VM do Windows da IaaS a partir do Marketplace no Azure usando um modelo do Resource Manager. O modelo cria uma nova VM criptografada do Windows usando a imagem da galeria do Windows Server 2012.

1. No [modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image), cliquem em **Implantar no Azure**.

2. Selecione a assinatura, o grupo de recursos, o local do grupo de recursos, os parâmetros, os termos legais e o contrato. Clique em **Comprar** para implantar uma nova VM da IaaS onde a criptografia está habilitada.

3. Depois de implantar o modelo, verifique o status de criptografia da VM usando o método preferido:
     - Verifique com a CLI do Azure, usando o comando [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show). 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
         ```

     - Verifique com o Azure PowerShell, usando o cmdlet [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus). 

         ```azurepowershell-interactive
         Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName 'MySecureRg' -VMName 'MySecureVM'
         ```

     -  Selecione a VM e, em seguida, clique em **Discos** sob o cabeçalho **Configurações** para verificar o status da criptografia no portal. No gráfico em **Criptografia**, você verá se ela está habilitada. 
           ![Portal do Azure – Disk Encryption habilitado](./media/azure-security-disk-encryption/disk-encryption-fig2.png) A tabela a seguir lista os parâmetros de modelo do Resource Manager para novas VMs do cenário do Marketplace usando a ID do cliente do Azure AD:

| Parâmetro | DESCRIÇÃO | 
| --- | --- |
| adminUserName | Especifique um nome de usuário para a máquina virtual. |
| adminPassword | Senha de usuário administrador para a máquina virtual. |
| newStorageAccountName | Nome da conta de armazenamento para armazenar VHDs do sistema operacional e de dados. |
| vmSize | O tamanho da VM. Atualmente, há suporte somente para séries Standard A, D e G. |
| virtualNetworkName | Nome da VNet à qual a NIC da VM deve pertencer. |
| subnetName | Nome da sub-rede na VNet à qual a NIC da VM deve pertencer. |
| AADClientID | A ID do cliente do aplicativo Azure AD que tem permissões para gravar segredos no cofre de chaves. |
| AADClientSecret | Segredo do cliente do aplicativo AD do Azure que tem permissões para gravar segredos para o cofre de chaves. |
| keyVaultURL | URL do cofre de chaves no qual a chave do BitLocker deve ser carregada. É possível obtê-lo, usando o cmdlet `(Get-AzureRmKeyVault -VaultName,-ResourceGroupName).VaultURI` ou a CLI do Azure `az keyvault show --name "MySecureVault" --query properties.vaultUri` |
| keyEncryptionKeyURL | URL da chave de criptografia de chave que é usada para criptografar a chave gerada do BitLocker (opcional). </br> </br>KeyEncryptionKeyURL é um parâmetro opcional. Você pode usar seu próprio KEK para proteger ainda mais a chave de criptografia de dados (frase secreta) no cofre de chaves. |
| keyVaultResourceGroup | Grupo de recursos do cofre de chaves. |
| vmName | Nome da VM em que a operação de criptografia deve ser executada. |


## <a name="bkmk_RunningWinVM"></a> Habilitar criptografia em VMs do Windows da IaaS existentes ou em execução
Nesse cenário, é possível habilitar a criptografia usando um modelo, cmdlets do PowerShell ou comandos da CLI. As seções a seguir explicam com mais detalhes como habilitar o Azure Disk Encryption. 

>[!IMPORTANT]
 >É obrigatório tirar um instantâneo e/ou fazer backup de uma instância de VM baseada em disco gerenciado fora do Azure Disk Encryption e antes de habilitá-lo. Um instantâneo do disco gerenciado pode ser obtido do portal ou pode ser usado o [Backup do Azure](../backup/backup-azure-vms-encryption.md). Backups asseguram que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a criptografia. Depois que um backup é feito, o cmdlet Set-AzureRmVMDiskEncryptionExtension pode ser usado para criptografar Managed Disks especificando o parâmetro -skipVmBackup. O comando Set-AzureRmVMDiskEncryptionExtension falhará nas VMs baseadas em disco gerenciado até que um backup seja feito e esse parâmetro tenha sido especificado. 
>
>Criptografar ou desabilitar a criptografia pode fazer com que a VM seja reiniciada. 
>

### <a name="bkmk_RunningWinVMPSH"></a> Habilitar criptografia em VMs existentes ou em execução com Azure PowerShell 
Use o cmdlet [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) para habilitar a criptografia em uma máquina virtual da IaaS em execução no Azure. Para obter informações sobre como habilitar a criptografia com o Azure Disk Encryption usando cmdlets do PowerShell, confira as postagens de blog [Explorar Azure Disk Encryption com o Azure PowerShell - parte 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) e [Explorar Azure Disk Encryption com o Azure PowerShell - parte 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

-  **Criptografar uma VM em execução usando um segredo de cliente:** o script abaixo inicializa as variáveis e executa o cmdlet Set-AzureRmVMDiskEncryptionExtension. O grupo de recursos, VM, cofre de chaves, aplicativo AAD e segredo do cliente já devem ter sido criados como pré-requisitos. Substitua MySecureRg, MySecureVM, MySecureVault, My-AAD-client-ID e My-AAD-client-secret por seus valores.
     ```azurepowershell-interactive
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Criptografar uma VM em execução usando KEK para encapsular o segredo do cliente:** o Azure Disk Encryption permite que você especifique uma chave existente no cofre de chaves para encapsular segredos de criptografia de disco que foram gerados ao habilitar a criptografia. Quando uma chave de criptografia de chave é especificada, o Azure Disk Encryption usa essa chave para agrupar os segredos de criptografia antes de gravar no Key Vault. 

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = ‘MyExtraSecureVM’;
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > A sintaxe para o valor do parâmetro diskvacryption-keyvault é a cadeia de caracteres do identificador completo:/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro key-encryption-key é o URI completo para KEK, como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Verifique se os discos estão criptografados:** para verificar o status de criptografia de uma VM da IaaS, use o cmdlet [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus). 
     ```azurepowershell-interactive
     Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName 'MySecureRg' -VMName 'MySecureVM'
     ```
    
- **Desabilitar criptografia de disco:** para desabilitar a criptografia, use o cmdlet [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption). 
     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
     ```

### <a name="bkmk_RunningWinVMCLI"></a>Habilitar criptografia em VMs existentes ou em execução com CLI do Azure
Use o comando [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) para habilitar a criptografia em uma máquina virtual da IaaS em execução no Azure.

-  **Criptografar uma VM em execução usando um segredo do cliente:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Criptografar uma VM em execução usando KEK para encapsular o segredo do cliente:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > A sintaxe para o valor do parâmetro diskvacryption-keyvault é a cadeia de caracteres do identificador completo:/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> A sintaxe para o valor do parâmetro key-encryption-key é o URI completo para KEK, como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Verifique se os discos estão criptografados:** para verificar o status de criptografia de uma VM da IaaS, use o comando [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show). 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
     ```

- **Desabilitar criptografia:** para desabilitar a criptografia, use o comando [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type [ALL, DATA, OS]
     ```
 
 > [!NOTE]
 >É obrigatório tirar um instantâneo e/ou fazer backup de uma instância de VM baseada em disco gerenciado fora do Azure Disk Encryption e antes de habilitá-lo. Um instantâneo do disco gerenciado pode ser obtido do portal ou pode ser usado o [Backup do Azure](../backup/backup-azure-vms-encryption.md). Backups asseguram que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a criptografia. Depois que um backup é feito, o cmdlet Set-AzureRmVMDiskEncryptionExtension pode ser usado para criptografar Managed Disks especificando o parâmetro -skipVmBackup. Esse comando falhará nas VMs baseadas em disco gerenciado até que um backup seja feito e esse parâmetro tenha sido especificado. 
>
>Criptografar ou desabilitar a criptografia pode fazer com que a VM seja reiniciada. 

### <a name="bkmk_RunningWinVMwRM"> </a>Usar o modelo do Resource Manager
É possível habilitar a criptografia de disco em VMs do Windows da IaaS em execução ou existentes no Azure usando o [modelo do Resource Manager para criptografar uma VM do Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).


1. No modelo de início rápido do Azure, clique em **Implantar no Azure**.

2. Selecione a assinatura, o grupo de recursos, o local do grupo de recursos, os parâmetros, os termos legais e o contrato. Clique em **Comprar** para habilitar a criptografia na VM da IaaS em execução ou existente.

A tabela a seguir lista os parâmetros de modelo do Gerenciador de Recursos existente ou VMs em execução que usam uma ID de cliente do Azure AD:

| Parâmetro | DESCRIÇÃO |
| --- | --- |
| AADClientID | ID do cliente do aplicativo Azure AD que tem permissões para gravar segredos no cofre de chaves. |
| AADClientSecret | Segredo do cliente do aplicativo Azure AD que tem permissões para gravar segredos no cofre de chaves. |
| keyVaultName | Nome do cofre de chaves no qual a chave do BitLocker deve ser carregada. É possível obtê-lo, usando o cmdlet `(Get-AzureRmKeyVault -ResourceGroupName <MyResourceGroupName>). Vaultname` ou o comando da CLI do Azure `az keyvault list --resource-group "MySecureGroup" |Convertfrom-JSON`|
|  keyEncryptionKeyURL | URL da chave de criptografia de chaves que é usada para criptografar a chave gerada do BitLocker. Esse parâmetro será opcional se você selecionar **nokek** na lista suspensa UseExistingKek. Se selecionar **kek** na lista suspensa UseExistingKek, você deverá inserir o valor _keyEncryptionKeyURL_. |
| volumeType | Tipo de volume em que a operação de criptografia é executada. Os valores válidos são _OS_, _Data_ e _All_. |
| sequenceVersion | Versão de sequência da operação de BitLocker. Aumente esse número de versão sempre que uma operação de criptografia de disco for executada na mesma VM. |
| vmName | Nome da VM em que a operação de criptografia deve ser executada. |


## <a name="bkmk_VHDpre"> </a>Novas VMs da IaaS criadas a partir de chaves de criptografia e VHD criptografado pelo cliente
Nesse cenário, você pode habilitar a criptografia usando o modelo do Resource Manager, cmdlets do PowerShell ou comandos da CLI. As seções a seguir explicam detalhadamente o modelo do Gerenciador de Recursos e comandos da CLI. 

Use as instruções no apêndice para preparar imagens previamente criptografadas que podem ser usadas no Azure. Depois que a imagem for criada, você poderá usar as etapas na próxima seção para criar uma VM do Azure criptografada.

* [Preparar um VHD do Windows previamente criptografado](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Preparar um VHD Linux previamente criptografado](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >É obrigatório tirar um instantâneo e/ou fazer backup de uma instância de VM baseada em disco gerenciado fora do Azure Disk Encryption e antes de habilitá-lo. Um instantâneo do disco gerenciado pode ser obtido do portal ou pode ser usado o [Backup do Azure](../backup/backup-azure-vms-encryption.md). Backups asseguram que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a criptografia. Depois que um backup é feito, o cmdlet Set-AzureRmVMDiskEncryptionExtension pode ser usado para criptografar Managed Disks especificando o parâmetro -skipVmBackup. O comando Set-AzureRmVMDiskEncryptionExtension falhará nas VMs baseadas em disco gerenciado até que um backup seja feito e esse parâmetro tenha sido especificado. 
>
>Criptografar ou desabilitar a criptografia pode fazer com que a VM seja reiniciada. 



### <a name="bkmk_VHDprePSH"> </a> Criptografar VMs com VHDs previamente criptografados com Azure PowerShell
É possível habilitar a criptografia de disco no VHD criptografado usando o cmdlet do PowerShell [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk#examples). O exemplo abaixo fornece alguns parâmetros comuns. 

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzureRmVM -VM $VirtualMachine -ResouceGroupName "MySecureRG"
```


### <a name="bkmk_VHDpreRM"> </a>Criptografar VMs de IaaS com VHDs previamente criptografados com um modelo do Resource Manager 
Você pode habilitar a criptografia de disco em seu VHD criptografado usando o [modelo do Gerenciador de Recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm).

1. No modelo de início rápido do Azure, clique em **Implantar no Azure**.

2. Selecione a assinatura, o grupo de recursos, o local do grupo de recursos, os parâmetros, os termos legais e o contrato. Clique em **Criar** para habilitar a criptografia na nova VM da IaaS.

A tabela a seguir lista os parâmetros de modelo do Gerenciador de Recursos para seu VHD criptografado:

| Parâmetro | DESCRIÇÃO |
| --- | --- |
| newStorageAccountName | Nome da conta de armazenamento para armazenar o VHD de sistema operacional. Esta conta de armazenamento já deve ter sido criada no mesmo grupo de recursos e no mesmo local da VM. |
| osVhdUri | URI do VHD do sistema operacional da conta de armazenamento. |
| osType | Tipo de produto do sistema operacional (Windows/Linux). |
| virtualNetworkName | Nome da VNet à qual a NIC da VM deve pertencer. O nome já deve ter sido criado no mesmo grupo de recursos e no mesmo local que a VM. |
| subnetName | O nome da sub-rede na VNet à qual a VM NIC deve pertencer. |
| vmSize | O tamanho da VM. Atualmente, há suporte somente para séries Standard A, D e G. |
| keyVaultResourceID | O ResourceID que identifica o recurso de cofre de chaves no Azure Resource Manager. É possível obtê-lo, usando o cmdlet do PowerShell `(Get-AzureRmKeyVault -VaultName &lt;MyKeyVaultName&gt; -ResourceGroupName &lt;MyResourceGroupName&gt;).ResourceId` ou usando o comando da CLI do Azure `az keyvault show --name "MySecureVault" --query id`|
| keyVaultSecretUrl | URL da chave de criptografia de disco que é configurada no cofre de chaves. |
| keyVaultKekUrl | URL da chave de criptografia de chave para criptografar a chave de criptografia de disco gerada. |
| vmName | Nome da VM IaaS. |

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Habilitar criptografia em um disco de dados adicionado recentemente
É possível [adicionar um novo disco a uma VM do Windows usando o PowerShell ](../virtual-machines/windows/attach-disk-ps.md) ou [por meio do portal do Azure](../virtual-machines/windows/attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Habilitar criptografia em um disco adicionado recentemente com Azure PowerShell
 Ao usar o Powershell para criptografar um novo disco para VMs do Windows, uma nova versão da sequência deverá ser especificada. A versão da sequência deverá ser exclusiva. O script abaixo gera um GUID para a versão da sequência. Em alguns casos, um disco de dados adicionado recentemente pode ser criptografado automaticamente pela extensão do Azure Disk Encryption. Se isso ocorrer, é recomendável executar o cmdlet Set-AzureRmVmDiskEncryptionExtension novamente com a nova versão da sequência.
 

-  **Criptografar uma VM em execução usando um segredo de cliente:** o script abaixo inicializa as variáveis e executa o cmdlet Set-AzureRmVMDiskEncryptionExtension. O grupo de recursos, VM, cofre de chaves, aplicativo AAD e segredo do cliente já devem ter sido criados como pré-requisitos. Substitua MySecureRg, MySecureVM, MySecureVault, My-AAD-client-ID e My-AAD-client-secret por seus valores. O parâmetro -VolumeType é configurado para discos de dados e não para o disco de SO. 

     ```azurepowershell-interactive
      $sequenceVersion = [Guid]::NewGuid();
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId  –SequenceVersion $sequenceVersion;
    ```
- **Criptografar uma VM em execução usando KEK para encapsular o segredo do cliente:** o Azure Disk Encryption permite que você especifique uma chave existente no cofre de chaves para encapsular segredos de criptografia de disco que foram gerados ao habilitar a criptografia. Quando uma chave de criptografia de chave é especificada, o Azure Disk Encryption usa essa chave para agrupar os segredos de criptografia antes de gravar no Key Vault. Talvez seja necessário adicionar o parâmetro -VolumeType se você estiver criptografando discos de dados e não o disco de SO. 

     ```azurepowershell-interactive
     $sequenceVersion = [Guid]::NewGuid();
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > A sintaxe para o valor do parâmetro diskvacryption-keyvault é a cadeia de caracteres do identificador completo:/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro key-encryption-key é o URI completo para KEK, como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Habilitar criptografia em um disco adicionado recentemente com CLI do Azure
 O comando da CLI do Azure fornecerá automaticamente uma nova versão da sequência quando você executar o comando para habilitar a criptografia. 
-  **Criptografar uma VM em execução usando um segredo do cliente:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Criptografar uma VM em execução usando KEK para encapsular o segredo do cliente:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault"--key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```


## <a name="enable-encryption-using-azure-ad-client-certificate-based-authentication"></a>Habilitar criptografia usando a autenticação baseada no certificado de cliente do Azure AD.
É possível usar a autenticação de certificado de cliente com ou sem KEK. Os scripts exigem que os [pré-requisitos do Azure Disk Encryption](azure-security-disk-encryption-prerequisites-aad.md) sejam cumpridos. Antes de usar os scripts do PowerShell, você já deve ter o certificado carregado no cofre de chaves e implantado na VM. Se você também estiver usando KEK, o KEK já deverá existir. Para obter mais informações, consulte a seção [Autenticação baseada em certificado do Azure AD](azure-security-disk-encryption-prerequisites-aad.md#bkmk_Cert) do artigo de pré-requisitos.


### <a name="enable-encryption-using-certificate-based-authentication-with-azure-powershell"></a>Habilitar a criptografia usando a autenticação baseada em certificado com Azure PowerShell

```powershell
## Fill in 'MySecureRg', 'My-AAD-client-ID', 'MySecureVault, and ‘MySecureVM’.

$rgName = 'MySecureRg';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = ‘MySecureVM’;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

# Fill in the certificate path and the password so the thumbprint can be set as a variable. 

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint

Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
```
  
### <a name="enable-encryption-using-certificate-based-authentication-and-a-kek-with-azure-powershell"></a>Habilitar criptografia usando autenticação baseada em certificado e um KEK com o Azure PowerShell

```powershell
# Fill in 'MySecureRg', 'My-AAD-client-ID', 'MySecureVault,, 'MySecureVM’, and "KEKName.

$rgName = 'MySecureRg';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$keyEncryptionKeyName ='KEKName';
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

## Fill in the certificate path and the password so the thumbprint can be read and set as a variable. 

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint and a KEK

Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

## <a name="disable-encryption"></a>Desabilitar criptografia
É possível desabilitar a criptografia usando o Azure PowerShell, a CLI do Azure ou com um modelo do Resource Manager. 

- **Desabilitar criptografia de disco com Azure PowerShell:** para desabilitar a criptografia, use o cmdlet [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption). 
     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
     ```

- **Desabilitar criptografia com a CLI do Azure:** para desabilitar a criptografia, use o comando [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type [ALL, DATA, OS]
     ```
- **Desabilitar criptografia com um modelo do Resource Manager:** 

    1. Clique em **Implantar no Azure** no modelo [Desabilitar criptografia na VM do Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm).
    2. Selecione a assinatura, o grupo de recursos, o local, a VM, os termos legais e o contrato.
    3.  Clique em **Comprar** para desabilitar a criptografia de disco em uma VM do Windows em execução. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Habilitar o Azure Disk Encryption para Linux](azure-security-disk-encryption-linux-aad.md)
