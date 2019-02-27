---
title: Habilitar o Backup para o Azure Stack com o PowerShell | Microsoft Docs
description: Habilite o serviço de Backup de infraestrutura com o Windows PowerShell para que o Azure Stack pode ser restaurado se houver uma falha.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 38ab7b80e2f03176c3bedfd98a2d0e20fc02592b
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56865885"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Habilitar o Backup para o Azure Stack com o PowerShell

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Então, habilitar o serviço de Backup de infraestrutura com o Windows PowerShell faça backups periódicos do:
 - Certificado raiz e o serviço de identidade interna
 - Planos de usuário, ofertas, assinaturas
 - Computação, armazenamento e cotas de usuário de rede
 - Segredos do Cofre de chave de usuário
 - As políticas e funções de RBAC do usuário
 - Contas de armazenamento do usuário

Você pode acessar os cmdlets do PowerShell para habilitar o backup, iniciar o backup e obter informações de backup por meio do ponto de extremidade de gerenciamento do operador.

## <a name="prepare-powershell-environment"></a>Preparar o ambiente do PowerShell

Para obter instruções sobre como configurar o ambiente do PowerShell, consulte [instalar o PowerShell para Azure Stack](azure-stack-powershell-install.md). Para entrar Azure Stack, consulte [configurar o ambiente de operador e entrar no Azure Stack](azure-stack-powershell-configure-admin.md).

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Forneça a chave de criptografia, credenciais e compartilhamento de backup para habilitar o backup

Na sessão do PowerShell, edite o seguinte script do PowerShell, adicionando as variáveis para o seu ambiente. Execute o script atualizado para fornecer a chave de criptografia, credenciais e compartilhamento de backup para o serviço de Backup de infraestrutura.

| Variável        | DESCRIÇÃO   |
|---              |---                                        |
| $username       | Tipo de **nome de usuário** usando o domínio e o nome de usuário para o local da unidade compartilhada com acesso suficiente para ler e gravar arquivos. Por exemplo, `Contoso\backupshareuser`. |
| $password       | Tipo de **senha** para o usuário. |
| $sharepath      | Digite o caminho para o **local de armazenamento de Backup**. Você deve usar uma cadeia de caracteres de convenção de nomenclatura Universal (UNC) para o caminho para um compartilhamento de arquivos hospedado em um dispositivo separado. Uma cadeia de caracteres UNC Especifica o local dos recursos, como arquivos compartilhados ou dispositivos. Para garantir a disponibilidade dos dados de backup, o dispositivo deve estar em um local separado. |
| $frequencyInHours | A frequência em horas determina a frequência com que os backups são criados. O valor padrão é 12. O Agendador dá suporte a um máximo de 12 e um mínimo de 4.|
| $retentionPeriodInDays | O período de retenção em dias determina quantos dias de backups são preservados em local externo. O valor padrão é 7. O Agendador dá suporte a um máximo de 14 e um mínimo de 2. Backups mais antigos que o período de retenção sejam excluídos automaticamente do local externo.|
| $encryptioncertpath | O caminho do certificado de criptografia Especifica o caminho de arquivo para o. Arquivo CER com a chave pública usada para criptografia de dados. |
|     |     |

```powershell
    # Example username:
    $username = "domain\backupadmin"
 
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString

    # Create a self-signed certificate using New-SelfSignedCertificate, export the public key portion and save it locally.

    $cert = New-SelfSignedCertificate `
        -DnsName "www.contoso.com" `
        -CertStoreLocation "cert:\LocalMachine\My" 

    New-Item -Path "C:\" -Name "Certs" -ItemType "Directory" 

    #make sure to export the PFX format of the certificate with the public and private keys and then delete the certifcate from the local certificate store of the machine where you created the certificate
    
    Export-Certificate `
        -Cert $cert `
        -FilePath c:\certs\AzSIBCCert.cer 

    # Set the backup settings with the name, password, share, and CER certificate file.
    Set-AzsBackupConfiguration -BackupShare $sharepath -Username $username -Password $password -EncryptionCertPath "c:\temp\cert.cer"
```
   
##  <a name="confirm-backup-settings"></a>Confirme as configurações de backup

Na sessão do PowerShell, execute os seguintes comandos:

   ```powershell
    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName
   ```

O resultado deve ser semelhante a saída de exemplo a seguir:

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
   ```

## <a name="update-backup-settings"></a>Atualizar as configurações de backup
Na sessão do PowerShell, você pode atualizar os valores padrão para o período de retenção e frequência de backups. 

   ```powershell
    #Set the backup frequency and retention period values.
    $frequencyInHours = 10
    $retentionPeriodInDays = 5

    Set-AzsBackupConfiguration -BackupFrequencyInHours $frequencyInHours -BackupRetentionPeriodInDays $retentionPeriodInDays

    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName, AvailableCapacity, BackupFrequencyInHours, BackupRetentionPeriodInDays
   ```

O resultado deve ser semelhante a saída de exemplo a seguir:

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
    BackupFrequencyInHours      : 10
    BackupRetentionPeriodInDays : 5
   ```

###<a name="azure-stack-powershell"></a>Azure Stack PowerShell 
O cmdlet do PowerShell para configurar o backup de infraestrutura é AzsBackupConfiguration conjunto. Em versões anteriores, o cmdlet foi AzsBackupShare conjunto. Esse cmdlet requer o fornecimento de um certificado. Se o backup de infraestrutura é configurado com uma chave de criptografia, você não pode atualizar a chave de criptografia ou exibir a propriedade. Você precisará usar a versão 1.6 do PowerShell de administrador. 

Se o backup de infraestrutura foi configurado antes de atualizar para 1901, você pode usar a versão 1.6 do PowerShell de administrador para definir e exibir a chave de criptografia. Versão 1.6 não permitirá a atualização da chave de criptografia para um arquivo de certificado.
Consulte a [instalar o Azure Stack PowerShell](azure-stack-powershell-install.md) para obter mais informações sobre como instalar a versão correta do módulo. 


## <a name="next-steps"></a>Próximas etapas

Aprenda a executar um backup, consulte [fazer backup do Azure Stack](azure-stack-backup-back-up-azure-stack.md)

Saiba como verificar se o backup foi executado, consulte [backup confirmar concluído no portal de administração](azure-stack-backup-back-up-azure-stack.md)
