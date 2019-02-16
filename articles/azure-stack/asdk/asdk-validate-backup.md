---
title: Validar um backup do Azure Stack usando o ASDK | Microsoft Docs
description: Como validar um backup de sistemas integrados do Azure Stack usando o ASDK.
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 02/15/2019
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 02/15/2019
ms.openlocfilehash: 6fdec992b19a5615a35955a46fd90102890cde16
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2019
ms.locfileid: "56329346"
---
# <a name="use-the-asdk-to-validate-an-azure-stack-backup"></a>Use o ASDK para validar um backup do Azure Stack
Depois de implantar o Azure Stack e provisionamento de recursos do usuário, como ofertas, planos, cotas e assinaturas, você deve [habilitar o backup de infraestrutura do Azure Stack](../azure-stack-backup-enable-backup-console.md). Agendamento e execução de backups regulares de infra-estrutura garantirá que os dados de gerenciamento de infraestrutura não seja perdidos se não houver um hardware catastrófico ou falha de serviço.

> [!TIP]
> É recomendável que você [executar um backup sob demanda](../azure-stack-backup-back-up-azure-stack.md) antes de iniciar este procedimento para garantir que você tenha uma cópia dos dados mais recentes de infraestrutura disponíveis. Certifique-se de capturar a ID de backup depois que o backup for concluído com êxito. Essa ID será necessária durante a recuperação de nuvem. 

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



### <a name="prereqs"></a>Pré-requisitos de recuperação de nuvem
Antes de iniciar uma implantação de recuperação de nuvem do ASDK, certifique-se de que você tenha as seguintes informações:

|Pré-requisito|DESCRIÇÃO|
|-----|-----|
|Caminho de compartilhamento de backup|O caminho de compartilhamento de arquivo UNC do backup mais recente do Azure Stack que será usado para recuperar informações de infraestrutura do Azure Stack. Esse compartilhamento local será criado durante o processo de implantação da recuperação de nuvem.|
|Chave de criptografia de backup|Opcional. Necessário apenas se você atualizou para a versão do Azure Stack 1901 ou posterior de uma versão anterior do Azure Stack com backup habilitado.|
|ID de backup para restaurar|A ID de backup, na forma alfanumérica de "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx", que identifica o backup a serem restaurados durante a recuperação de nuvem.|
|IP do servidor de tempo|Um IP do servidor de tempo válido, como 132.163.97.2, é necessário para a implantação do Azure Stack.|
|Senha do certificado externo|A senha do certificado autoassinado chave privada (. pfx) que foi usado para proteger o backup.|
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

### <a name="use-the-installer-to-deploy-the-asdk-in-recovery-mode"></a>Use o instalador para implantar o ASDK no modo de recuperação
As etapas nesta seção mostram como implantar o ASDK usando uma interface gráfica do usuário (GUI) fornecida pelo baixando e executando o **asdk installer.ps1** script do PowerShell.

> [!NOTE]
> A interface do usuário do instalador para o Kit de desenvolvimento do Azure Stack é um script de software livre baseado no WCF e do PowerShell.

1. Depois que o computador host é inicializada com êxito para a imagem CloudBuilder.vhdx, entre usando as credenciais de administrador especificado quando você [preparado o computador de host do kit de desenvolvimento](asdk-prepare-host.md) para instalação ASDK. Isso deve ser o mesmo que as credenciais de administrador local de host de kit de desenvolvimento.
2. Abra um console do PowerShell com privilégios elevados e execute o  **&lt;letra da unidade > \AzureStack_Installer\asdk-installer.ps1** script do PowerShell. O script agora pode ser em uma unidade C:\ na imagem CloudBuilder.vhdx diferente. Clique em **Recuperar**.

    ![Script do instalador ASDK](media/asdk-validate-backup/1.PNG) 

3. Insira suas informações de diretório do AD do Azure (opcionais) e a senha de administrador local para o computador host ASDK na página de credenciais e de provedor de identidade. Clique em **Avançar**.

    ![Página de credenciais e identidade](media/asdk-validate-backup/2.PNG) 

4. Selecione o adaptador de rede a ser usado pelo computador de host ASDK e clique em **próxima**. Todas as outras interfaces de rede serão desabilitadas durante a instalação do ASDK. 

    ![Interface do adaptador de rede](media/asdk-validate-backup/3.PNG) 

5. Na página de configuração de rede, forneça o servidor de tempo válido e endereços IP do encaminhador DNS. Clique em **Avançar**.

    ![Página de configuração de rede](media/asdk-validate-backup/4.PNG) 

6. Quando as propriedades de placa de interface de rede tiverem sido verificadas, clique em **próxima**. 

    ![Verificação de configurações de placa de rede](media/asdk-validate-backup/5.PNG) 

7. Forneça as informações necessárias, descritas anteriormente na [seção pré-requisitos](#prereqs) a página de configurações de Backup e o nome de usuário e senha a ser usada para acessar o compartilhamento. Clique em **próxima**: 

   ![Página Configurações de backup](media/asdk-validate-backup/6.PNG) 

8. Examine o script de implantação a ser usado para implantar o ASDK na página de resumo. Clique em **Deploy** para iniciar a implantação. 

    ![Página Resumo](media/asdk-validate-backup/7.PNG) 


### <a name="use-powershell-to-deploy-the-asdk-in-recovery-mode"></a>Usar o PowerShell para implantar o ASDK no modo de recuperação
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

Depois de fazer logon como o operador, o Azure Stack [instalar o Azure Stack PowerShell](asdk-post-deploy.md#install-azure-stack-powershell) e execute os seguintes comandos para especificar o certificado e a senha a ser usado ao restaurar do backup:

```powershell
Restore-AzsBackup -Name "<BackupID>"
```

Aguarde 60 minutos depois de chamar esse cmdlet para iniciar a verificação dos dados de backup na nuvem recuperado ASDK.

## <a name="next-steps"></a>Próximas etapas
[Registrar o Azure Stack](asdk-register.md)

