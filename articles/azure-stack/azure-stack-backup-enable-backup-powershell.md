---
title: Habilitar o Backup para a pilha do Azure com o PowerShell | Microsoft Docs
description: "Habilite o serviço novamente de infraestrutura com o Windows PowerShell para que a pilha do Azure podem ser restaurada, se houver uma falha."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 7DFEFEBE-D6B7-4BE0-ADC1-1C01FB7E81A6
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: b4f48b7fd07c5fb590b6989e04e9084c86142d2a
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2018
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Habilitar o Backup para a pilha do Azure com o PowerShell

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Habilite o serviço novamente de infraestrutura com o Windows PowerShell para que a pilha do Azure podem ser restaurada, se houver uma falha. Você pode acessar os cmdlets do PowerShell para habilitar o backup, iniciar o backup e obter informações de backup por meio do ponto de extremidade de gerenciamento do operador.

## <a name="download-azure-stack-tools"></a>Baixar ferramentas de pilha do Azure

Instalar e PowerShell configurado para a pilha do Azure e as ferramentas de pilha do Azure. Consulte [colocar em funcionamento com o PowerShell no Azure pilha](https://review.docs.microsoft.com/en-us/azure/azure-stack/azure-stack-powershell-configure-quickstart).

##  <a name="load-the-connect-and-infrastructure-modules"></a>Carregar os módulos Connect e infraestrutura

Abra o Windows PowerShell com um prompt com privilégios elevados e execute os seguintes comandos:

   ```powershell
    cd C:\tools\AzureStack-Tools-master\Connect
    Import-Module .\AzureStack.Connect.psm1
    
    cd C:\tools\AzureStack-Tools-master\Infrastructure
    Import-Module .\AzureStack.Infra.psm1 
    
   ```

##  <a name="setup-rm-environment-and-log-into-the-operator-management-endpoint"></a>Ambiente de Rm e log de instalação para o ponto de extremidade de gerenciamento do operador

Na sessão do PowerShell, edite o seguinte script PowerShell adicionando as variáveis para o seu ambiente. Execute o script atualizado para configurar o ambiente do RM e de log para o ponto de extremidade de gerenciamento do operador.

| Variável    | DESCRIÇÃO |
|---          |---          |
| $TenantName | Nome do locatário do Active Directory do Azure. |
| Nome de conta do operador        | Seu nome de conta do operador de pilha do Azure. |
| Ponto de extremidade de Gerenciador de recursos do Azure | URL para o Gerenciador de recursos do Azure. |

   ```powershell
   # Specify Azure Active Directory tenant name
    $TenantName = "contoso.onmicrosoft.com"
    
    # Set the module repository and the execution policy
    Set-PSRepository `
      -Name "PSGallery" `
      -InstallationPolicy Trusted
    
    Set-ExecutionPolicy RemoteSigned `
      -force
    
    # Configure the Azure Stack operator’s PowerShell environment.
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint "https://adminmanagement.seattle.contoso.com"
    
    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience "https://graph.windows.net/"
    
    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName $TenantName `
      -EnvironmentName AzureStackAdmin
    
    # Sign-in to the operator's console.
    Login-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID 
    
   ```
## <a name="generate-a-new-encryption-key"></a>Gere uma nova chave de criptografia

Na sessão do PowerShell, execute os seguintes comandos:

   ```powershell
   $encryptionkey = New-EncryptionKeyBase64
   ```

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Forneça a chave de criptografia, credenciais e compartilhamento de backup para habilitar o backup

Na sessão do PowerShell, edite o seguinte script PowerShell adicionando as variáveis para o seu ambiente. Execute o script atualizado para fornecer a chave de criptografia, credenciais e compartilhamento de backup para o serviço de Backup de infraestrutura.

| Variável        | DESCRIÇÃO   |
|---              |---                                        |
| $username       | Tipo de **Username** usando o domínio e o nome de usuário para o local da unidade compartilhada. Por exemplo, `Contoso\administrator`. |
| $password       | Tipo de **senha** para o usuário. |
| $sharepath      | Digite o caminho para o **local de armazenamento de Backup**. Você deve usar uma cadeia de caracteres de convenção de nomenclatura Universal (UNC) para o caminho de um compartilhamento de arquivos hospedado em um dispositivo separado. Uma cadeia de caracteres UNC Especifica o local de recursos, como arquivos compartilhados ou dispositivos. Para garantir a disponibilidade dos dados de backup, o dispositivo deve estar em um local separado. |

   ```powershell
   $username = "domain\backupoadmin"
    $password = "password"
    $credential = New-Object System.Management.Automation.PSCredential($username, ($password| ConvertTo-SecureString -asPlainText -Force))  
    $location = Get-AzsLocation
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

Set-AzSBackupShare -Location $location -Path $sharepath -UserName $credential.UserName -Password $credential.GetNetworkCredential().password -EncryptionKey $encryptionkey 

   ```
##  <a name="confirm-backup-settings"></a>Confirme as configurações de backup

Na sessão do PowerShell, execute os seguintes comandos:

   ```powershell
   Get-AzsBackupLocation | Select-Object -Property Path, UserName, Password | ConvertTo-Json 
   ```

O resultado deve ser semelhante a seguinte saída JSON:

   ```json
      {
    "ExternalStoreDefault":  {
        "Path":  "\\\\serverIP\\AzSBackupStore\\contoso.com\\seattle",
        "UserName":  "domain\backupoadmin",
        "Password":  null
       }
   } 
   ```

## <a name="next-steps"></a>Próximas etapas

 - Saiba como executar um backup, consulte [Azure pilha](azure-stack-backup-back-up-azure-stack.md ).  
- Saiba como verificar se o backup foi executado, consulte [Confirmar backup concluído no portal de administração](azure-stack-backup-back-up-azure-stack.md ).
