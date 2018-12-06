---
title: Validar um backup do Azure Stack usando o ASDK | Microsoft Docs
description: Como validar um backup de sistemas do Azure Stack integerated usando o ASDK.
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 09/05/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: 181f37fb72584e18cc963ba1ffde070379a1b0c6
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52961396"
---
# <a name="use-the-asdk-to-validate-an-azure-stack-backup"></a>Use o ASDK para validar um backup do Azure Stack
Depois de implantar o Azure Stack e provisionamento de recursos do usuário, como ofertas, planos, cotas e assinaturas, você deve [habilitar o backup de infraestrutura do Azure Stack](../azure-stack-backup-enable-backup-console.md). Agendamento e execução de backups regulares de infra-estrutura garantirá que os dados de gerenciamento de infraestrutura não seja perdidos se não houver um hardware catastrófico ou falha de serviço.

> [!TIP]
> É recomendável que você [executar um backup sob demanda](../azure-stack-backup-back-up-azure-stack.md) antes de iniciar este procedimento para garantir que você tenha uma cópia dos dados infrastrcuture mais recente disponíveis. Certifique-se de capturar a ID de backup depois que o backup for concluído com êxito. Essa ID será necessária durante a recuperação de nuvem. 

Backups de infraestrutura do Azure Stack contêm dados importantes sobre sua nuvem que pode ser restaurado durante a reimplantação do Azure Stack. Você pode usar o ASDK para validar esses backups sem afetar sua nuvem de produção. 

Validar backups em ASDK tem suporte para os seguintes cenários:

|Cenário|Finalidade|
|-----|-----|
|Valide os backups de infraestrutura de uma solução integrada.|Validação de vida útil curta que os dados no backup são válidos.|
|Aprenda o fluxo de trabalho de recuperação de ponta a ponta.|Use ASDK para validar todo o backup e restaurar a experiência.|
|     |     |

O cenário a seguir **não é** com suporte ao validar o ASDK backups:

|Cenário|Finalidade|
|-----|-----|
|Build ASDK para criar o backup e restaurar.|Restaure dados de backup de uma versão anterior da ASDK para uma versão mais recente.|
|     |     |


## <a name="cloud-recovery-deployment"></a>Implantação da recuperação de nuvem
Backups de infra-estrutura da sua implantação de sistemas integrados podem ser validados pela execução de uma implantação de recuperação de nuvem do ASDK. Esse tipo de implantação, os dados de serviço específico são restaurados do backup depois que o ASDK está instalado no computador host.



### <a name="cloud-recovery-prerequisites"></a>Pré-requisitos de recuperação de nuvem
Antes de iniciar uma implantação de recuperação de nuvem do ASDK, certifique-se de que você tenha as seguintes informações:

|Pré-requisito|DESCRIÇÃO|
|-----|-----|
|Caminho de compartilhamento de backup.|O caminho de compartilhamento de arquivo UNC do backup mais recente do Azure Stack que será usado para recuperar informações de infraestrutura do Azure Stack. Esse compartilhamento local será criado durante o processo de implantação da recuperação de nuvem.|
|Chave de criptografia de backup.|A chave de criptografia que foi usada para agendar backup de infraestrutura para ser executado usando o portal de administração do Azure Stack.|
|ID de backup para restaurar.|A ID de backup, na forma alfanumérica de "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx", que identifica o backup a serem restaurados durante a recuperação de nuvem.|
|IP do servidor de tempo.|Um IP do servidor de tempo válido, como 132.163.97.2, é necessário para a implantação do Azure Stack.|
|Senha do certificado externo.|A senha para o certificado externo usado pelo Azure Stack. O backup da CA contém certificados externos que precisam ser restaurados com essa senha.|
|     |     | 

## <a name="prepare-the-host-computer"></a>Preparar o computador host 
Como em uma implantação ASDK normal, o ambiente do sistema de host ASDK deve estar preparado para instalação. Quando o computador de host do kit de desenvolvimento foi preparado, ele será inicializado do disco rígido de máquina virtual CloudBuilder.vhdx para iniciar a implantação de ASDK.

No computador host ASDK, baixe um novo cloudbuilder.vhdx correspondente para a mesma versão do Azure Stack que foi feito o backup e siga as instruções para [preparar o computador de host ASDK](asdk-prepare-host.md).

Depois que o servidor do host for reiniciado do cloudbuilder.vhdx, você deve criar um compartilhamento de arquivos e copiar os dados de backup em. O compartilhamento de arquivos deve ser acessível para a conta executando a instalação; Administrador nesses comandos do PowerShell de exemplo: 

```powershell
$shares = New-Item -Path "c:\" -Name "Shares" -ItemType "directory"
$azsbackupshare = New-Item -Path $shares.FullName -Name "AzSBackups" -ItemType "directory"
New-SmbShare -Path $azsbackupshare.FullName -FullAccess ($env:computername + "\Administrator")  -Name "AzSBackups"
```

Em seguida, copie os arquivos de backup mais recente do Azure Stack para o compartilhamento criado recentemente. A estrutura de pasta no compartilhamento deve ser: `\\<ComputerName>\AzSBackups\MASBackup\<BackupID>\`.

## <a name="deploy-the-asdk-in-cloud-recovery-mode"></a>Implantar o ASDK no modo de recuperação de nuvem
O **InstallAzureStackPOC.ps1** script é usado para iniciar a recuperação de nuvem. 

> [!IMPORTANT]
> Instalação ASDK dá suporte a exatamente uma placa de interface de rede (NIC) de rede. Se você tiver várias NICs, certifique-se de que somente um está habilitado (e todos os outros estão desabilitados) antes de executar o script de implantação.

Modifique os seguintes comandos do PowerShell para o seu ambiente e executá-los para implantar o ASDK no modo de recuperação de nuvem:

```powershell
cd C:\CloudDeployment\Setup     
$adminPass = Get-Credential Administrator
$key = ConvertTo-SecureString "<Your backup encryption key>" -AsPlainText -Force ` 
$certPass = Read-Host -AsSecureString  

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
-BackupEncryptionKeyBase64 $key -BackupStoreCredential $adminPass -BackupId "<Backup ID to restore>" `
-TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

## <a name="restore-infrastructure-data-from-backup"></a>Restaurar dados de infraestrutura de backup
Depois de uma implantação de recuperação de nuvem bem-sucedido, você precisa concluir a restauração usando o **AzureStack restauração** cmdlet. 

Depois de fazer logon como o operador, o Azure Stack [instalar o PowerShell do Azure Stack](asdk-post-deploy.md#install-azure-stack-powershell) e, em seguida, substituindo sua ID de Backup para o `Name` parâmetro, execute o seguinte comando:

```powershell
Restore-AzsBackup -Name "<BackupID>"
```
Aguarde 60 minutos depois de chamar esse cmdlet para iniciar a verificação dos dados de backup na nuvem recuperado ASDK.

## <a name="next-steps"></a>Próximas etapas
[Registrar o Azure Stack](asdk-register.md)

