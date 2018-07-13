---
title: Habilitar o Backup para o Azure Stack com o PowerShell | Microsoft Docs
description: Habilite o serviço de Backup de infraestrutura com o Windows PowerShell para que o Azure Stack pode ser restaurado se houver uma falha.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/10/2018
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 4fb40904e59e78e416d4598472a6adeb498e49f4
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968877"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Habilitar o Backup para o Azure Stack com o PowerShell

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Então, habilitar o serviço de Backup de infraestrutura com o Windows PowerShell faça backups periódicos do:
 - Certificado raiz e o serviço de identidade interna
 - Planos de usuário, ofertas, assinaturas
 - Segredos do Cofre de chaves
 - As políticas e funções de RBAC do usuário

Você pode acessar os cmdlets do PowerShell para habilitar o backup, iniciar o backup e obter informações de backup por meio do ponto de extremidade de gerenciamento do operador.

## <a name="prepare-powershell-environment"></a>Preparar o ambiente do PowerShell

Para obter instruções sobre como configurar o ambiente do PowerShell, consulte [instalar o PowerShell para Azure Stack ](azure-stack-powershell-install.md). Para entrar Azure Stack, consulte [configurar o ambiente de operador e entrar no Azure Stack](azure-stack-powershell-configure-admin.md).

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Forneça a chave de criptografia, credenciais e compartilhamento de backup para habilitar o backup

Na sessão do PowerShell, edite o seguinte script do PowerShell, adicionando as variáveis para o seu ambiente. Execute o script atualizado para fornecer a chave de criptografia, credenciais e compartilhamento de backup para o serviço de Backup de infraestrutura.

| Variável        | DESCRIÇÃO   |
|---              |---                                        |
| $username       | Tipo de **nome de usuário** usando o domínio e o nome de usuário para o local da unidade compartilhada com acesso suficiente para ler e gravar arquivos. Por exemplo, `Contoso\backupshareuser`. |
| $key            | Tipo de **chave de criptografia** usada para criptografar cada backup. |
| $password       | Tipo de **senha** para o usuário. |
| $sharepath      | Digite o caminho para o **local de armazenamento de Backup**. Você deve usar uma cadeia de caracteres de convenção de nomenclatura Universal (UNC) para o caminho para um compartilhamento de arquivos hospedado em um dispositivo separado. Uma cadeia de caracteres UNC Especifica o local dos recursos, como arquivos compartilhados ou dispositivos. Para garantir a disponibilidade dos dados de backup, o dispositivo deve estar em um local separado. |

   ```powershell
    $username = "domain\backupadmin"
   
    $Secure = Read-Host -Prompt ("Password for: " + $username) -AsSecureString
    $Encrypted = ConvertFrom-SecureString -SecureString $Secure
    $password = ConvertTo-SecureString -String $Encrypted
    
    $BackupEncryptionKeyBase64 = ""
    $tempEncryptionKeyString = ""
    foreach($i in 1..64) { $tempEncryptionKeyString += -join ((65..90) + (97..122) | Get-Random | % {[char]$_}) }
    $tempEncryptionKeyBytes = [System.Text.Encoding]::UTF8.GetBytes($tempEncryptionKeyString)
    $BackupEncryptionKeyBase64 = [System.Convert]::ToBase64String($tempEncryptionKeyBytes)
    $BackupEncryptionKeyBase64
    
    $Securekey = ConvertTo-SecureString -String $BackupEncryptionKeyBase64 -AsPlainText -Force
    $Encryptedkey = ConvertFrom-SecureString -SecureString $Securekey
    $key = ConvertTo-SecureString -String $Encryptedkey
    
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    Set-AzSBackupShare -BackupShare $sharepath -Username $username -Password $password -EncryptionKey $key
   ```
   
##  <a name="confirm-backup-settings"></a>Confirme as configurações de backup

Na sessão do PowerShell, execute os seguintes comandos:

   ```powershell
    Get-AzsBackupLocation | Select-Object -Property Path, UserName, AvailableCapacity
   ```

O resultado deve ser semelhante a seguinte saída:

   ```powershell
    Path                        : \\serverIP\AzSBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
   ```

## <a name="next-steps"></a>Próximas etapas

 - Aprenda a executar um backup, consulte [fazer backup do Azure Stack](azure-stack-backup-back-up-azure-stack.md ).  
 - Saiba como verificar se o backup foi executado, consulte [concluído no portal de administração de backup de confirmar](azure-stack-backup-back-up-azure-stack.md ).
