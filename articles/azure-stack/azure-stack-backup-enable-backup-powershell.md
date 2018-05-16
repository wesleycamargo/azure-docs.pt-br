---
title: Habilitar o Backup para a pilha do Azure com o PowerShell | Microsoft Docs
description: Habilite a infraestrutura de serviço de Backup com o Windows PowerShell para que a pilha do Azure podem ser restaurada, se houver uma falha.
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
ms.openlocfilehash: 4faa6930c37f9d491a3efa4b34519dbb13761a9d
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2018
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Habilitar o Backup para a pilha do Azure com o PowerShell

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Habilitar a infraestrutura de serviço de Backup com o Windows PowerShell para executar backups periódicos do:
 - Certificado de raiz e o serviço de identidade interna
 - Planos de usuário, ofertas, assinaturas
 - Keyvault segredos
 - As políticas e funções de RBAC do usuário

Você pode acessar os cmdlets do PowerShell para habilitar o backup, iniciar o backup e obter informações de backup por meio do ponto de extremidade de gerenciamento do operador.

## <a name="prepare-powershell-environment"></a>Preparar o ambiente do PowerShell

Para obter instruções sobre como configurar o ambiente do PowerShell, consulte [instalar o PowerShell para Azure pilha ](azure-stack-powershell-install.md).

## <a name="generate-a-new-encryption-key"></a>Gere uma nova chave de criptografia

Instalar e PowerShell configurado para a pilha do Azure e as ferramentas de pilha do Azure.
 - Consulte [colocar em funcionamento com o PowerShell no Azure pilha](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart).
 - Consulte [ferramentas baixar Azure pilha do GitHub](azure-stack-powershell-download.md)

Abra o Windows PowerShell com um prompt com privilégios elevados e execute os seguintes comandos:
   
   ```powershell
    cd C:\tools\AzureStack-Tools-master\Infrastructure
    Import-Module .\AzureStack.Infra.psm1 
   ```
   
Na sessão do PowerShell, execute os seguintes comandos:

   ```powershell
   $encryptionkey = New-EncryptionKeyBase64
   ```

> [!Warning]  
> Você deve usar as ferramentas de AzureStack para gerar a chave.

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Forneça a chave de criptografia, credenciais e compartilhamento de backup para habilitar o backup

Na sessão do PowerShell, edite o seguinte script PowerShell adicionando as variáveis para o seu ambiente. Execute o script atualizado para fornecer a chave de criptografia, credenciais e compartilhamento de backup para o serviço de Backup de infraestrutura.

| Variável        | DESCRIÇÃO   |
|---              |---                                        |
| $username       | Tipo de **Username** usando o domínio e o nome de usuário para o local da unidade compartilhada. Por exemplo, `Contoso\administrator`. |
| $password       | Tipo de **senha** para o usuário. |
| $sharepath      | Digite o caminho para o **local de armazenamento de Backup**. Você deve usar uma cadeia de caracteres de convenção de nomenclatura Universal (UNC) para o caminho para um compartilhamento de arquivo hospedado em um dispositivo separado. Uma cadeia de caracteres UNC Especifica o local de recursos, como arquivos compartilhados ou dispositivos. Para garantir a disponibilidade dos dados de backup, o dispositivo deve estar em um local separado. |

   ```powershell
    $username = "domain\backupoadmin"
    $password = "password"
    $credential = New-Object System.Management.Automation.PSCredential($username, ($password| ConvertTo-SecureString -asPlainText -Force))  
    $location = Get-AzsLocation
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"
    
    Set-AzSBackupShare -Location $location.Name -Path $sharepath -UserName $credential.UserName -Password $credential.GetNetworkCredential().password -EncryptionKey $encryptionkey
   ```
   
##  <a name="confirm-backup-settings"></a>Confirme as configurações de backup

Na sessão do PowerShell, execute os seguintes comandos:

   ```powershell
   Get-AzsBackupLocation | Select-Object -ExpandProperty externalStoreDefault | Select-Object -Property Path, UserName, Password | ConvertTo-Json
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