---
title: Restaurar chave e segredo do Cofre de Chaves para VMs criptografadas usando o Backup do Azure | Microsoft Docs
description: Saiba como restaurar a chave e o segredo do Cofre de Chaves no Backup do Azure usando o PowerShell
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 45214083-d5fc-4eb3-a367-0239dc59e0f6
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: pajosh
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0c5b2969ddc943b2b15826003f5a9e686e84e1c4


---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Restaurar chave e segredo do Cofre de Chaves para VMs criptografadas usando o Backup do Azure
Este artigo fala sobre usar o Backup da VM do Azure para executar a restauração de VMs do Azure criptografadas se a chave e o segredo não existirem no cofre de chaves. Essas etapas também poderão ser usadas se vocês quiser manter uma cópia separada da chave (Chave de Criptografia de Chave) e o segredo (Chave de Criptografia do BitLocker) para a VM restaurada.

## <a name="pre-requisites"></a>Pré-requisitos
1. **Backup de VMs criptografadas** – o backup de VMs do Azure criptografadas foi feito usando o Backup do Azure. Consulte o artigo [Gerenciar backup e restauração de VMs do Azure usando o PowerShell](backup-azure-vms-automation.md) para obter detalhes sobre como fazer o backup de VMs do Azure criptografadas.
2. **Configurar o Cofre de Chaves do Azure** – garanta que o cofre de chaves ao qual as chaves e segredos precisam ser restaurados já esteja presente. Consulte o artigo [Introdução ao Cofre de Chaves do Azure](../key-vault/key-vault-get-started.md) para obter detalhes sobre o gerenciamento do cofre de chaves.

## <a name="setup-recovery-services-vault"></a>Configurar cofre dos serviços de recuperação
Use as seguintes etapas para efetuar logon no PowerShell e definir o contexto do cofre de serviços de recuperação

### <a name="log-in-to-azure-powershell"></a>Fazer logon no Azure PowerShell
Faça logon na conta do Azure usando o seguinte cmdlet

```
PS C:\> Login-AzureRmAccount
```

### <a name="set-recovery-services-vault-context"></a>Definir contexto do cofre de serviços de recuperação
Depois de fazer logon, use o seguinte cmdlet para obter a lista de suas assinaturas disponíveis

```
PS C:\> Get-AzureRmSubscription
```

Selecione a assinatura em que recursos estão disponíveis

```
PS C:\> Set-AzureRmContext -SubscriptionId "<subscription-id>"
```

Defina o contexto do cofre usando o cofre de Serviços de Recuperação em que o backup foi habilitado para VMs criptografadas

```
PS C:\> Get-AzureRmRecoveryServicesVault -ResourceGroupName "<rg-name>" -Name "<rs-vault-name>" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="get-recovery-point"></a>Obter ponto de recuperação
Selecione o contêiner no cofre que representa a máquina virtual do Azure criptografada

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -Name "<vm-name>"
```

Usando esse contêiner, obtenha o item de backup para a máquina virtual correspondente

```
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
```

Obtenha uma matriz de pontos de recuperação para o item de backup selecionado no rp variável

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
```

## <a name="restore-encrypted-virtual-machine"></a>Restaurar máquina virtual criptografada
Use as seguintes etapas para restaurar a VM criptografada, sua chave e seu segredo.

### <a name="restore-key"></a>Restaurar chave
A matriz $rp acima é classificada em ordem inversa de tempo com o último ponto de recuperação no índice 0. Por exemplo: $rp[0] seleciona o último ponto de recuperação.

```
PS C:\> $rp1 = Get-AzureRmRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation "C:\Users\downloads"
```

> [!NOTE]
> Após a execução bem-sucedida desse cmdlet, um arquivo de blob é gerado na pasta especificada no computador em que ele foi executado. Esse arquivo de blob representa a Chave Criptografada da Chave em formato criptografado.
> 
> 

Restaure a chave de volta ao cofre de chaves usando o seguinte cmdlet. 

```
PS C:\> Restore-AzureKeyVaultKey -VaultName "contosokeyvault" -InputFile "C:\Users\downloads\key.blob"
```

### <a name="restore-secret"></a>Restaurar segredo
Restaurar dados de segredo do ponto de recuperação obtido acima

```
PS C:\> $rp1.KeyAndSecretDetails.SecretUrl

https://contosokeyvault.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/20aaae9eaa99996d89d99a29990d999a
```

> [!NOTE]
> O texto antes de vault.azure.net representa o nome do cofre de chaves original. O texto depois de secrets/ representa o nome do segredo. 
> 
> 

Obtenha o nome e o valor do segredo da saída do cmdlet executado acima, caso deseje usar o mesmo nome do segredo. Em outros casos, o $secretname abaixo deve ser atualizado para usar o novo nome do segredo. 

```
PS C:\> $secretname = "B3284AAA-DAAA-4AAA-B393-60CAA848AAAA"
PS C:\> $secretdata = $rp1.KeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
```

Defina marcações para o segredo, caso a VM precise ser restaurada também. Para a marcação DiskEncryptionKeyFileName, valor deve conter o nome do segredo que você planeja usar. 

```
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://contosokeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
```

> [!NOTE]
> O valor para DiskEncryptionKeyFileName é igual ao nome do segredo obtido anteriormente. O valor para DiskEncryptionKeyEncryptionKeyURL pode ser obtido do cofre de chaves depois de restaurar as chaves de volta e usar o cmdlet [Get-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868053.aspx)    
> 
> 

Definir o segredo de volta para o cofre de chaves

```
PS C:\> Set-AzureKeyVaultSecret -VaultName "contosokeyvault" -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  "Wrapped BEK"
```

### <a name="restore-virtual-machine"></a>Restaurar a máquina virtual
Os cmdlets do PowerShell acima ajudam a restaurar a chave e o segredo de volta para o cofre de chaves se você tiver feito backup da VM criptografado usando o Backup de VM do Azure. Depois de restaurá-los, consulte o artigo [Gerenciar backup e restauração de VMs do Azure usando o PowerShell](backup-azure-vms-automation.md) para restaurar VMs criptografadas.




<!--HONumber=Nov16_HO3-->


