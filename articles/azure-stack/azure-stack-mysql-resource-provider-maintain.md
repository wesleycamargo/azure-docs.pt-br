---
title: Manter o provedor de recursos do MySQL na pilha do Azure | Microsoft Docs
description: Saiba como você pode manter o serviço de provedor de recursos do MySQL na pilha do Azure.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: bc1c96d2f027d459ca20fccb70cd94ac9e5cae94
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130131"
---
# <a name="mysql-resource-provider-maintenance-operations"></a>Operações de manutenção do provedor de recursos MySQL

O provedor de recursos do MySQL é executado em uma máquina virtual bloqueada. Para habilitar as operações de manutenção, é necessário atualizar a segurança da máquina virtual. Para fazer isso usando o princípio de privilégios mínimos, você pode usar o ponto de extremidade do PowerShell administração JEA (Just Enough) DBAdapterMaintenance. O pacote de instalação do provedor de recursos inclui um script para esta operação.

## <a name="update-the-virtual-machine-operating-system"></a>Atualizar o sistema operacional da máquina virtual

Como o provedor de recursos é executada em um *usuário* máquina virtual, você precisa aplicar as atualizações e patches necessários quando eles sejam liberados. Você pode usar os pacotes de atualização do Windows que são fornecidos como parte do ciclo de patch e atualização para aplicar as atualizações para a máquina virtual.

Atualize a máquina virtual do provedor usando um dos seguintes métodos:

- Instale o pacote de provedor de recursos mais recente usando uma imagem do Windows Server 2016 Core corrigida no momento.
- Instalar um pacote do Windows Update durante a instalação do ou atualize o provedor de recursos.

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>Atualize as definições do Windows Defender de máquina virtual

Para atualizar as definições de Defender, siga estas etapas:

1. Atualizar as definições do Windows Defender a partir de download [definição do Windows Defender](https://www.microsoft.com/en-us/wdsi/definitions).

    Na página de definições, role até "Baixar e instalar manualmente as definições de". Baixe o arquivo de 64 bits "Antivírus do Windows Defender para Windows 10 e Windows 8.1".

    Como alternativa, use [esse link direto](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64) download/executar o arquivo fpam fe.exe.

2. Abra uma sessão do PowerShell para o ponto de extremidade de manutenção de MySQL recurso provedor adaptador da máquina virtual.

3. Copie o arquivo de atualização de definições para o adaptador de provedor de recursos usando a sessão de ponto de extremidade de manutenção VM.

4. Na sessão do PowerShell de manutenção, execute o _DBAdapterWindowsDefenderDefinitions atualização_ comando.

5. Depois de instalar as definições, recomendamos que você exclua o arquivo de atualização de definições usando o _ItemOnUserDrive remover)_ comando.

**Exemplo de script do PowerShell para atualizar as definições.**

Você pode editar e executar o script a seguir para atualizar as definições de Defender. Substitua os valores no script com valores do seu ambiente.

```powershell
# Set credentials for the local admin on the resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Provide the public IP address for the adapter VM.
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions.  
Invoke-WebRequest -Uri 'https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64' `
    -Outfile $localPathToDefenderUpdate  

# Create a session to the maintenance endpoint.
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $vmLocalAdminCreds -ConfigurationName DBAdapterMaintenance

# Copy the defender update file to the adapter virtual machine.
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\"

# Install the update definitions.
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinition -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}

# Cleanup the definitions package file and session.
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession

```

## <a name="secrets-rotation"></a>Rotação de segredos

*Essas instruções se aplicam somente ao Azure pilha integrado sistemas versão 1804 e mais tarde. Não tente girar segredos em versões anteriores 1804 da pilha do Azure.*

Quando usar os provedores de recursos do SQL e MySQL com pilha Azure sistemas integrados, você pode girar os segredos de infraestrutura (implantação) a seguir:

- Certificado SSL externo [fornecido durante a implantação](azure-stack-pki-certs.md).
- A recurso provedor VM local senha da conta administrador fornecida durante a implantação.
- Senha de usuário de diagnóstico (dbadapterdiag) do provedor de recursos.

### <a name="powershell-examples-for-rotating-secrets"></a>Exemplos do PowerShell para girar segredos

**Altere todos os segredos ao mesmo tempo.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd `  
    -VMLocalCredential $localCreds

```

**Altere a senha de usuário de diagnóstico.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword  $passwd

```

**Altere a senha de conta de administrador local de VM.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds

```

**Altere a senha do certificado SSL.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd

```

### <a name="secretrotationmysqlproviderps1-parameters"></a>Parâmetros de SecretRotationMySQLProvider.ps1

|Parâmetro|DESCRIÇÃO|
|-----|-----|
|AzCredential|Credencial da conta de administrador de serviço de pilha do Azure.|
|CloudAdminCredential|Azure pilha nuvem domínio conta credenciais de administrador.|
|PrivilegedEndpoint|Ponto de extremidade com privilégios para acessar o Get-AzureStackStampInformation.|
|DiagnosticsUserPassword|Senha de conta de usuário de diagnóstico.|
|VMLocalCredential|A conta de administrador local na VM MySQLAdapter.|
|DefaultSSLCertificatePassword|Padrão de certificado SSL (* pfx) senha.|
|DependencyFilesLocalPath|Caminho de local de arquivos de dependência.|
|     |     |

### <a name="known-issues"></a>Problemas conhecidos

**Problema:**<br>
Os logs para rotação de segredos não são coletados automaticamente se o script de rotação secreta não quando ele é executado.

**Solução alternativa:**<br>
Use o cmdlet Get-AzsDBAdapterLogs para coletar todos os logs de provedor de recursos, incluindo AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log, salvo em c:\Logs.

## <a name="collect-diagnostic-logs"></a>Coletar logs de diagnóstico

Para coletar logs da máquina virtual bloqueada, você pode usar o ponto de extremidade do PowerShell administração JEA (Just Enough) DBAdapterDiagnostics. Esse ponto de extremidade fornece os seguintes comandos:

- **Get-AzsDBAdapterLog**. Este comando cria um pacote zip dos logs de diagnóstico de provedor de recursos e salva o arquivo na unidade de usuário da sessão. Você pode executar esse comando sem parâmetros e as últimas quatro horas de logs são coletadas.

- **Remover AzsDBAdapterLog**. Este comando remove os pacotes existentes do log no provedor de recurso VM.

### <a name="endpoint-requirements-and-process"></a>Requisitos de ponto de extremidade e processo

Quando um provedor de recursos é instalado ou atualizado, a conta de usuário dbadapterdiag é criada. Você usará essa conta para coletar logs de diagnóstico.

>[!NOTE]
>A senha da conta dbadapterdiag é o mesmo que a senha usada para o administrador local na máquina virtual que é criado durante uma implantação do provedor ou a atualização.

Para usar o _DBAdapterDiagnostics_ comandos, criar uma sessão remota do PowerShell para a máquina de virtual do provedor de recursos e execute o **AzsDBAdapterLog Get** comando.

Definir o período de tempo para coleta de log usando o **FromDate** e **ToDate** parâmetros. Se você não especificar um ou ambos os parâmetros, os seguintes padrões são usados:

* FromDate é de quatro horas antes da hora atual.
* ToDate é a hora atual.

**Exemplo de script do PowerShell para coletar logs.**

O script a seguir mostra como coletar logs de diagnóstico de VM do provedor de recursos.

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'
$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds
        -ConfigurationName DBAdapterDiagnostics

# Sample that captures logs from the previous hour.
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs to the user drive.
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup the logs.
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove-AzsDBAdapterLog}
# Close the session.
$session | Remove-PSSession

```

## <a name="next-steps"></a>Próximas etapas

[Remover o provedor de recursos MySQL](azure-stack-mysql-resource-provider-remove.md)
