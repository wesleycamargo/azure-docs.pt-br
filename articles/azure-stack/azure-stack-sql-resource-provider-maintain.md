---
title: Manter o provedor de recursos do SQL na pilha do Azure | Microsoft Docs
description: Saiba como você pode manter o serviço de provedor de recursos do SQL na pilha do Azure.
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
ms.date: 06/11/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: e7ddbe1235b3957a1e0cb7693ee728bfdbf9db6b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295652"
---
# <a name="maintenance-operations"></a>Operações de manutenção 
O provedor de recursos do SQL é um bloqueado para a máquina virtual. Atualização de segurança da máquina do recurso provedor virtual pode ser feito por meio do ponto de extremidade do PowerShell administração JEA (Just Enough) _DBAdapterMaintenance_. Um script é fornecido com o pacote de instalação do RP para facilitar a essas operações.

## <a name="patching-and-updating"></a>Aplicação de patch e atualização
O provedor de recursos do SQL não será processado como parte da pilha do Azure como um componente complementar. Microsoft fornecerá atualizações para o provedor de recursos do SQL conforme necessário. O provedor de recursos do SQL é instanciado em um _usuário_ máquina virtual sob a assinatura do provedor padrão. Portanto, é necessário fornecer patches do Windows, as assinaturas de antivírus, etc. O Windows update pacotes que são fornecidos como parte do ciclo de atualização e patch pode ser usado para aplicar atualizações a VM do Windows. Quando um adaptador atualizado é liberado, um script é fornecido para aplicar a atualização. Esse script cria uma nova VM RP e migra qualquer estado que você já tiver.

 ## <a name="backuprestoredisaster-recovery"></a>Backup/restauração/recuperação de desastre
 O provedor de recursos do SQL não é feito como parte do processo de continuidade de negócios de pilha do Azure-DR, conforme ele é um componente complementar. Scripts serão fornecidos para facilitar a:
- Fazendo backup das informações de estado necessárias (armazenadas em uma conta de armazenamento do Azure pilha)
- Restaurando o RP no caso de uma recuperação de pilha completa é necessária.
Servidores de banco de dados devem ser recuperados pela primeira vez (se necessário), antes do recurso de provedor é restaurado.

## <a name="updating-sql-credentials"></a>Atualizando as credenciais do SQL
Você é responsável pela criação e manutenção de contas de administrador do sistema em seus servidores SQL. O provedor de recursos precisa de uma conta com esses privilégios para gerenciar bancos de dados em nome dos usuários - não precisa de acesso aos dados nesses bancos de dados. Se você precisar atualizar as senhas de sa em seus servidores SQL, você pode usar o recurso de atualização da interface do administrador do provedor de recursos para alterar a senha armazenada usada pelo provedor de recursos. Essas senhas são armazenadas em um cofre de chaves em sua instância de pilha do Azure.

Para modificar as configurações, clique em **procurar** &gt; **recursos ADMINISTRATIVOS** &gt; **servidores de hospedagem SQL** &gt; **Logons do SQL Server** e selecione um nome de logon. A alteração deve ser feita primeiro na instância do SQL (e todas as réplicas, se necessário). No **configurações** do painel, clique em **senha**.

![Atualize a senha de administrador](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)

## <a name="secrets-rotation"></a>Rotação de segredos 
*Essas instruções se aplicam somente ao Azure pilha integrado sistemas versão 1804 e mais tarde. Não tente secreta rotação em versões de pilha do pré-1804 do Azure.*

Quando usar os provedores de recursos do SQL e MySQL com pilha Azure sistemas integrados, você pode girar os segredos de infraestrutura (implantação) a seguir:
- Certificado SSL externo [fornecido durante a implantação](azure-stack-pki-certs.md).
- A recurso provedor VM local senha da conta administrador fornecida durante a implantação.
- Senha de usuário de diagnóstico (dbadapterdiag) do provedor de recursos.

### <a name="powershell-examples-for-rotating-secrets"></a>Exemplos do PowerShell para girar segredos

**Alterar todos os segredos ao mesmo tempo**
```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd  `
    -VMLocalCredential $localCreds
```

**Alterar a senha de usuário de diagnóstico somente**
```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword  $passwd 
```

**Alterar a senha da conta de administrador local VM**
```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds
```

**Alterar o certificado SSL**
```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd 
```

### <a name="secretrotationsqlproviderps1-parameters"></a>Parâmetros de SecretRotationSQLProvider.ps1

|Parâmetro|DESCRIÇÃO|
|-----|-----|
|AzCredential|Credencial da conta de administrador de serviço de pilha do Azure.|
|CloudAdminCredential|Azure pilha nuvem domínio conta credenciais de administrador.|
|PrivilegedEndpoint|Ponto de extremidade com privilégios para acessar o Get-AzureStackStampInformation.|
|DiagnosticsUserPassword|Senha de usuário de diagnóstico.|
|VMLocalCredential|A conta de administrador local da VM MySQLAdapter.|
|DefaultSSLCertificatePassword|Padrão de certificado SSL (* pfx) senha.|
|DependencyFilesLocalPath|Caminho de Local de arquivos de dependência.|
|     |     |

### <a name="known-issues"></a>Problemas conhecidos
**Problema**: os logs para rotação de segredos não são coletados automaticamente se o script de rotação secreta personalizado falhar quando ele é executado.

**Solução alternativa**: Use o cmdlet Get-AzsDBAdapterLogs para coletar todos os logs do provedor de recursos, incluindo AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log em c:\Logs.

## <a name="update-the-virtual-machine-operating-system"></a>Atualizar o sistema operacional da máquina virtual
Há várias maneiras de atualizar a VM do Windows Server:
- Instalar o pacote de provedor de recursos mais recente usando uma imagem do Windows Server 2016 Core corrigida no momento
- Instalar um pacote do Windows Update durante a instalação ou atualização do RP

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>Atualize as definições do Windows Defender de máquina virtual
Siga estas etapas para atualizar as definições de Defender:

1. Atualizar as definições do Windows Defender a partir de download [definição do Windows Defender](https://www.microsoft.com/en-us/wdsi/definitions).

    Nessa página, em "Baixar e instalar manualmente as definições de" download "antivírus do Windows Defender para Windows 10 e o arquivo de 64 bits do Windows 8.1". 
    
    Link direto: https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64.

2. Criar uma sessão do PowerShell para o ponto de extremidade da máquina SQL RP adaptador virtual manutenção
3. Copie o arquivo de atualização de definições para a máquina de adaptador de banco de dados usando a sessão de ponto de extremidade de manutenção
4. O PowerShell de manutenção de sessão invocar o _DBAdapterWindowsDefenderDefinitions atualização_ comando
5. Após a instalação, é recomendável remover o arquivo de atualização de definições usadas. Ele pode ser removido na sessão de manutenção usando o _ItemOnUserDrive remover)_ comando.


Aqui está um exemplo de script para atualizar as definições de Defender (substitua o endereço ou nome da máquina virtual com o valor real):

```powershell
# Set credentials for the RP VM local admin user
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Public IP Address of the DB adapter machine
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions. 
Invoke-WebRequest -Uri 'https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64' `
    -Outfile $localPathToDefenderUpdate 

# Create session to the maintenance endpoint
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $vmLocalAdminCreds -ConfigurationName DBAdapterMaintenance
# Copy defender update file to the db adapter machine
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\"
# Install the update file
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinition -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession 
```


## <a name="collect-diagnostic-logs"></a>Coletar logs de diagnóstico
O provedor de recursos do SQL é um bloqueado para a máquina virtual. Se for necessário coletar logs da máquina virtual, um ponto de extremidade do PowerShell administração JEA (Just Enough) _DBAdapterDiagnostics_ é fornecido para essa finalidade. Há dois comandos a esse ponto de extremidade:

- **Get-AzsDBAdapterLog**. Prepara um pacote zip que contém os logs de diagnóstico do RP e o coloca na unidade de usuário de sessão. O comando pode ser chamado sem parâmetros e coletará as últimas quatro horas de logs.
- **Remover AzsDBAdapterLog**. Limpa os pacotes existentes do log no provedor de recursos do VM

Uma conta de usuário chamado **dbadapterdiag** é criada durante a implantação de RP ou atualização para se conectar ao ponto de extremidade de diagnóstico para a extração de logs do RP. A senha dessa conta é o mesmo que a senha fornecida para a conta de administrador local durante a implantação/atualização.

Para usar esses comandos, você precisará criar uma sessão remota do PowerShell para a máquina de virtual do provedor de recursos e invocar o comando. Opcionalmente, você pode fornecer parâmetros FromDate e ToDate. Se você não especificar um ou ambos, a FromDate será quatro horas antes da hora atual e o ToDate será a hora atual.

Esse script de exemplo demonstra o uso desses comandos:

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
        -ConfigurationName DBAdapterDiagnostics

# Sample captures logs from the previous one hour
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup logs
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session
$session | Remove-PSSession
```

## <a name="next-steps"></a>Próximas etapas
[Adicionar servidores de hospedagem do SQL Server](azure-stack-sql-resource-provider-hosting-servers.md)
