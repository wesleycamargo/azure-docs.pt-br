---
title: Executar runbooks no Hybrid Runbook Worker da Automação do Azure
description: Este artigo fornece informações sobre a execução de runbooks em computadores em seu datacenter local ou provedor de nuvem com a função de Hybrid Runbook Worker.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 07/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cd2578f2fd8217d513a693ef348a5c26a4b18623
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126500"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Executar runbooks em um Hybrid Runbook Worker

Não há nenhuma diferença na estrutura de runbooks executados na Automação do Azure e daqueles que executam em um Runbook Worker Híbrido. Os runbooks que você usar com cada um provavelmente serão bem diferentes uns dos outros, já que os runbooks direcionados a um Hybrid Runbook Worker normalmente gerenciam recursos no próprio computador local ou em recursos no ambiente local onde é implantado, enquanto runbooks na Automação do Azure normalmente gerenciam recursos na nuvem do Azure.

Ao criar runbooks para executar em um Hybrid Runbook Worker, é necessário editar e testar os runbooks no computador que hospeda o Hybrid Worker. O computador host possui todos os módulos e acesso à rede do PowerShell necessários para gerenciar e acessar os recursos locais. Depois que um runbook for editado e testado no computador do Hybrid Worker, você poderá carregá-lo no ambiente de Automação do Azure, onde estará disponível para execução no Hybrid Worker. É importante saber que os trabalhos executados na conta do Sistema Local para windows ou uma conta de usuário especial **nxautomation** no Linux, que podem introduzir diferenças sutis, ao criar registros para um Hybrid Runbook Worker, devem ser considerados.

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>Iniciar um runbook no Hybrid Runbook Worker

[Como iniciar um Runbook na Automação do Azure](automation-starting-a-runbook.md) descreve métodos diferentes para se iniciar um runbook. O Runbook Worker Híbrido adiciona uma opção **RunOn** em que você pode especificar o nome de um Grupo de Runbook Worker Híbrido. Se um grupo for especificado, o runbook será recuperado e executado por um dos trabalhadores nesse grupo. Se essa opção não for especificada, ele é executado na Automação do Azure como de costume.

Ao iniciar um runbook no portal do Azure, a opção **Executar em** ficará disponível e você poderá selecionar **Azure** ou **Hybrid Worker**. Se você selecionar **Worker Híbrido**, pode selecionar o grupo de uma lista suspensa.

Use o parâmetro **RunOn**. Você pode usar o comando a seguir para iniciar um runbook denominado Runbook de Teste em um Grupo Hybrid Runbook Worker chamado MyHybridGroup usando o Windows PowerShell.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> O parâmetro **RunOn** foi adicionado ao cmdlet **Start-AzureAutomationRunbook** na versão 0.9.1 do Microsoft Azure PowerShell. Você deve [baixar a versão mais recente](https://azure.microsoft.com/downloads/) se tiver uma anterior instalada. Você só precisa instalar essa versão em uma estação de trabalho em que vá iniciar o runbook do PowerShell. Você não precisa instalá-lo no computador de trabalho, a menos que pretenda iniciar runbooks desse computador"

## <a name="runbook-permissions"></a>Permissões de runbook

Os runbooks em execução em um Hybrid Runbook Worker não podem usar o mesmo método que é normalmente usado para autenticação de runbooks nos recursos do Azure, já que eles acessarão recursos fora do Azure. O runbook pode fornecer sua própria autenticação aos recursos locais, ou você pode especificar uma conta RunAs para fornecer um contexto de usuário a todos os runbooks.

### <a name="runbook-authentication"></a>Autenticação de runbook

Por padrão, os runbooks são executados no contexto da conta de Sistema Local para Windows e uma conta de usuário especial **nxautomation** para Linux no computador local, portanto, eles devem fornecer sua própria autenticação aos recursos que acessam.

Você pode usar ativos de [Credencial](automation-credentials.md) e [Certificado](automation-certificates.md) no runbook com cmdlets que permitem a especificação das credenciais para autenticar com recursos diferentes. O exemplo a seguir mostra uma parte de um runbook que reinicia um computador. Ele recupera as credenciais de um ativo de credencial e o nome do computador de um ativo variável, para então usar esses valores com o cmdlet Restart-Computer.

```azurepowershell-interactive
$Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -Name "MyCredential"
$Computer = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" -Name  "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Você também pode aproveitar o [InlineScript](automation-powershell-workflow.md#inlinescript), que permitirá a execução de blocos de código em outro computador com as credenciais especificadas pelo [parâmetro comum PSCredential](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="runas-account"></a>Conta RunAs

Por padrão o Hybrid Runbook Worker usa Sistema Local para Windows e uma conta de usuário especial **nxautomation** para Linux para executar runbooks. Em vez de os runbooks fornecerem sua própria autenticação aos recursos locais, você pode especificar uma conta **RunAs** para um grupo Hybrid Worker. Você especifica um [ativo de credencial](automation-credentials.md) que tenha acesso aos recursos locais e todos os runbooks serão executados sob essas credenciais ao serem executados em um Hybrid Runbook Worker no grupo.

O nome de usuário da credencial deve estar em um dos seguintes formatos:

* domínio\nome de usuário
* username@domain
* nome de usuário (para contas locais do computador local)

Use o procedimento a seguir para especificar uma conta RunAs para um grupo do Hybrid Worker:

1. Crie um [ativo de credencial](automation-credentials.md) com acesso a recursos locais.
2. Abra a conta de Automação no Portal do Azure.
3. Escolha o bloco **Grupos do Hybrid Worker** e selecione o grupo.
4. Escolha **Todas as configurações** e **Configurações do grupo do Hybrid Worker**.
5. Altere **Executar como** de **Padrão** para **Personalizado**.
6. Escolha a credencial e clique em **Salvar**.

### <a name="automation-run-as-account"></a>Conta de automação Executar como

Como parte do processo de build automatizado para a implantação de recursos no Azure, talvez você precise acessar os sistemas locais para dar suporte a uma tarefa ou um conjunto de etapas na sequência de implantação. Para dar suporte à autenticação no Azure usando a conta Executar como, você precisa instalar o certificado da conta Executar como.

O runbook do PowerShell a seguir, *Export-RunAsCertificateToHybridWorker*, exporta o certificado de Executar como de sua conta de Automação do Azure e baixa e importa para o repositório de certificados do computador local em um Hybrid Worker conectado à mesma conta. Quando essa etapa for concluída, ele verificará que o Worker pode ser autenticado com sucesso no Azure usando a conta Executar como.

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.
Run this runbook in the hybrid worker where you want the certificate installed.
This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running in the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
AUTHOR: Azure Automation Team
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test that authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzureRmContext -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm Azure Resource Manager calls are working
Get-AzureRmAutomationAccount | Select-Object AutomationAccountName
```

> [!IMPORTANT]
> **Connect-AzureRmAccount** agora é um alias para **Connect-AzureRMAccount**. Ao pesquisar sua biblioteca de itens, se você não vir **Connect-AzureRMAccount**, você pode usar **Connect-AzureRmAccount**, ou você pode atualizar seus módulos em sua Conta de Automação.

Salve o runbook *Export-RunAsCertificateToHybridWorker* no seu computador com uma extensão `.ps1`. Importe-o para sua conta de Automação e edite o runbook, alterando o valor da variável `$Password` pela sua própria senha. Publique e, em seguida, execute o runbook direcionando o grupo Hybrid Worker que executa e autentica runbooks usando a conta Executar como. O fluxo de trabalho informa a tentativa de importar o certificado para o armazenamento do computador local e vem com várias linhas, dependendo de quantas contas de Automação são definidas em sua assinatura e se a autenticação tiver sido bem-sucedida.

## <a name="job-behavior"></a>Comportamento do trabalho

Os trabalhos são tratados de forma ligeiramente diferente em Hybrid Runbook Workers do que quando são executados em áreas restritas do Azure. Uma diferença fundamental é que não há limite na duração do trabalho em Hybrid Runbook Workers. Os runbooks executados em áreas restritas do Azure são limitados a 3 horas devido à [fração justa](automation-runbook-execution.md#fair-share). Se você tiver um runbook de execução longa, será necessário assegurar que ele seja resiliente a um possível reinício, por exemplo, se o computador que hospeda o Hybrid Worker for reiniciado. Se o computador host do Hybrid Worker reiniciar, qualquer trabalho de runbook em execução será reiniciado desde o início ou do último ponto de verificação para runbooks do Fluxo de trabalho do PowerShell. Se um trabalho do runbook for reiniciado mais de 3 vezes, ele será suspenso.

## <a name="run-only-signed-runbooks"></a>Executar somente runbooks assinados

Os Hybrid Runbook Workers podem ser configurados para executar somente runbooks assinados com alguma configuração. A seção a seguir descreve como configurar os Hybrid Runbook Workers para executar runbooks assinados e como assinar os runbooks.

> [!NOTE]
> Após configurar um Hybrid Runbook Worker para executar somente runbooks assinados, os runbooks que **não** foram assinados não serão executados no trabalho.

### <a name="create-signing-certificate"></a>Criar certificado de assinatura

O exemplo a seguir cria um certificado autoassinado que pode ser usado para assinar runbooks. O exemplo cria o certificado e o exporta. O certificado será importado para os Hybrid Runbook Workers posteriormente. A impressão digital também será retornada e utilizada posteriormente para referenciar o certificado.

```powershell
# Create a self signed runbook that can be used for code signing
$SigningCert = New-SelfSignedCertificate -CertStoreLocation cert:\LocalMachine\my `
                                        -Subject "CN=contoso.com" `
                                        -KeyAlgorithm RSA `
                                        -KeyLength 2048 `
                                        -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
                                        -KeyExportPolicy Exportable `
                                        -KeyUsage DigitalSignature `
                                        -Type CodeSigningCert


# Export the certificate so that it can be imported to the hybrid workers
Export-Certificate -Cert $SigningCert -FilePath .\hybridworkersigningcertificate.cer

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Retrieve the thumbprint for later use
$SigningCert.Thumbprint
```

### <a name="configure-the-hybrid-runbook-workers"></a>Configurar os Hybrid Runbook Workers

Copie o certificado criado para cada Hybrid Runbook Worker em um grupo. Execute o script a seguir para importar o certificado e configure o Hybrid Worker para usar a validação de assinatura em runbooks.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Assine os Runbooks usando o certificado

Com os Hybrid Runbook Workers configurados para usar somente runbooks assinados. É necessário assinar os runbooks que devem ser usados no Hybrid Runbook Worker. Use o exemplo de PowerShell a seguir para assinar os runbooks.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Depois que o runbook for assinado, ele deverá ser importado para a Conta de Automação e publicado com o bloco de assinatura. Para saber como importar runbooks, consulte [Importar um runbook de um arquivo para a Automação do Azure](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-azure-automation).

## <a name="troubleshoot"></a>Solucionar problemas

Se os runbooks não estiverem sendo concluídos com êxito, revise o guia de solução de problemas em [falhas de execução do runbook](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os diferentes métodos que podem ser usados para iniciar um runbook, confira [Como iniciar um Runbook na Automação do Azure](automation-starting-a-runbook.md).
* Para entender os diferentes procedimentos para trabalhar com runbooks do PowerShell e de Fluxo de Trabalho do PowerShell na Automação do Azure usando o editor de texto, confira [Editar um Runbook na Automação do Azure](automation-edit-textual-runbook.md)
