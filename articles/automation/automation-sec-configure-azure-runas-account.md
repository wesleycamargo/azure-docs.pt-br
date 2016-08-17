<properties
    pageTitle="Configurar a conta Executar como do Azure | Microsoft Azure"
    description="Tutorial que explica a criação, os testes e o uso de exemplo da autenticação de entidade de segurança na Automação do Azure."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""
	keywords="nome principal do serviço, setspn, autenticação do azure"/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/29/2016"
    ms.author="magoedte"/>

# Autenticar runbooks com uma conta Executar como do Azure

Este tópico mostrará como configurar uma conta de Automação do Azure no portal do Azure usando o recurso de conta Executar como para autenticar runbooks que gerenciam recursos no Azure Resource Manager ou no Gerenciamento de Serviços do Azure.

O processo que cria a nova conta de Automação no portal do Azure também faz isto automaticamente:

- Cria uma conta Executar como que:
    - Cria uma nova entidade de serviço no Azure Active Directory.
    - Cria um certificado.
    - Atribui o controle de acesso baseado em função de Colaborador (RBAC), que será usado para gerenciar os recursos do Resource Manager por meio de runbooks.
- Cria uma conta Executar como Clássica ao carregar um certificado de gerenciamento, que será usado para gerenciar o Gerenciamento de Serviços do Azure ou recursos clássicos usando runbooks.

Isso simplifica o processo para você e o ajuda a começar a criar e a implantar rapidamente os runbooks para dar suporte às suas necessidades de automação.

Ao usar uma conta Executar como e uma conta Executar como Clássica, você poderá:

- Fornecer uma maneira padronizada de se autenticar no Azure ao gerenciar recursos do Azure Resource Manager ou do Gerenciamento de Serviços do Azure de runbooks no portal do Azure.
- Automatize o uso de runbooks globais configurados nos Alertas do Azure.


>[AZURE.NOTE] O [recurso Integração de alerta](../azure-portal/insights-receive-alert-notifications.md) do Azure com Runbooks Globais de Automação exige uma conta de Automação configurada com uma conta Executar como e uma conta Executar como Clássica. Você pode selecionar uma conta de Automação que já tenha uma conta Executar como e uma conta Executar como Clássica definidas ou pode criar uma nova.

Nós mostraremos como criar a conta de Automação no portal do Azure, como atualizar uma conta de Automação usando o PowerShell, além de como se autenticar em seus runbooks.

Antes de fazermos isso, há algumas coisas que você deve entender e considerar:

- Isso não afeta as contas de automação existentes já criadas no modelo de implantação clássico ou do Resource Manager.
- Isso só funcionará para contas de Automação criadas por meio do portal do Azure. Se você tentar criar uma conta no portal clássico do Azure, a conta não replicará a configuração da conta Executar como.
- Você pode ter runbooks e ativos (por exemplo, agendas e variáveis) que foram criados anteriormente para gerenciar os recursos clássicos e convém que esses runbooks se autentiquem usando a nova conta Executar como Clássica. Você precisará migrar os runbooks para a nova conta de Automação ou atualizar sua conta existente usando o script do PowerShell posteriormente neste artigo.
- Para se autenticar usando a nova conta Executar como e a conta Executar como Clássica da Automação, você precisará modificar seus runbooks existentes com o código de exemplo posteriormente neste artigo.

    >[AZURE.NOTE] A conta Executar como destina-se à autenticação em recursos do Resource Manager usando a entidade de serviço baseada em certificado. A conta Executar como Clássica destina-se à autenticação em recursos do Gerenciamento de Serviços usando o certificado de gerenciamento.


## Criar uma nova conta de Automação do portal do Azure

Nesta seção, você criará uma nova conta de Automação do Azure usando o portal do Azure. Isso cria a conta Executar como e a conta Executar como Clássica.

>[AZURE.NOTE] Para executar estas etapas, você *deverá* ser um membro da função de Administradores de Assinatura e o coadministrador da assinatura que está concedendo acesso à assinatura para o usuário. Você também deve adicionar o usuário como um usuário padrão do Azure AD (Azure Active Directory) da assinatura. A conta não precisa ser atribuída a uma função com privilégios.

1. Entre no portal do Azure com uma conta que seja membro da função Administradores de Assinatura e um coadministrador da assinatura.
2. Selecione **Contas de Automação**.
3. Na folha Contas de Automação, clique em **Adicionar**.<br>![Adicionar Conta de Automação](media/automation-sec-configure-azure-runas-account/create-automation-account-properties.png)

    >[AZURE.NOTE] Se você vir o seguinte aviso na folha **Adicionar Conta de Automação**, sua conta não é um membro da função Administradores de Assinatura e um coadministrador da assinatura.<br>![Aviso Adicionar Conta de Automação](media/automation-sec-configure-azure-runas-account/create-account-without-perms.png)

4. Na folha **Adicionar Conta de Automação**, na caixa **Nome**, digite um nome para a nova conta de Automação.
5. Se você tiver mais de uma assinatura, especifique a assinatura certa para a nova conta, e um **Grupo de recursos** novo ou existente e um **Local** de data center do Azure.
6. Verifique se o valor **Sim** está selecionado para a opção **Criar conta Executar como do Azure** e clique no botão **Criar**.

    >[AZURE.NOTE] Se você optar por não criar a conta Executar como ao selecionar a opção **Não**, verá uma mensagem de aviso na folha **Adicionar Conta de Automação**. Embora a conta seja criada no portal do Azure, ela não terá uma identidade de autenticação correspondente em seu serviço de diretório de assinaturas clássico ou do Resource Manager. Portanto, a conta não terá acesso a recursos em sua assinatura. Isso impedirá qualquer runbook que faça referência a essa conta seja capaz de se autenticar e de executar tarefas nos recursos nesses modelos de implantação.

    >![Aviso Adicionar Conta de Automação](media/automation-sec-configure-azure-runas-account/create-account-decline-create-runas-msg.png)<br> Enquanto a entidade de serviço não for criada, a função Colaborador não será atribuída.


7. Enquanto o Azure cria a conta de Automação, você poderá acompanhar o andamento em **Notificações** no menu.

### Recursos incluídos

Quando a criação da conta de Automação tiver sido criada com êxito, vários recursos serão criados automaticamente para você. A tabela a seguir resume os recursos para a conta Executar como.<br>

Recurso|Descrição
----|----
Runbook AzureAutomationTutorial|Um exemplo de runbook do PowerShell que demonstra como autenticar usando a conta Executar como e que obtém todos os recursos do Resource Manager.
Runbook do AzureAutomationTutorialScript|Um exemplo de runbook do PowerShell que demonstra como autenticar usando a conta Executar como e que obtém todos os recursos do Resource Manager.
AzureRunAsCertificate|Um ativo de certificado criado se você tiver optado pela criação da conta Executar como durante a criação da conta de Automação ou usando o script do PowerShell posteriormente neste artigo para uma conta existente. Com o certificado, você pode se autenticar no Azure para poder gerenciar recursos do Azure Resource Manager desde os runbooks. Esse certificado tem um tempo de vida de um ano.
AzureRunAsConnection|Um ativo de conexão criado se você tiver optado pela criação da conta Executar como durante a criação da conta de Automação ou usando o script do PowerShell posteriormente neste artigo para uma conta existente.

A tabela a seguir resume os recursos para a conta Executar como Clássica.<br>

Recurso|Descrição
----|----
Runbook do AzureClassicAutomationTutorial|Um runbook de exemplo que obtém todas as máquinas virtuais (VMs) clássicas em uma assinatura usando a conta Executar como Clássica (certificado) e então retorna o nome e o status da VM.
Runbook do script AzureClassicAutomationTutorial|Um runbook de exemplo que obtém todas as VMs clássicas em uma assinatura usando a conta Executar como Clássica (certificado) e então retorna o nome e o status da VM.
AzureClassicRunAsCertificate|Um ativo de certificado criado automaticamente e usado para autenticar no Azure para que você possa gerenciar recursos clássicos do Azure de runbooks. Esse certificado tem um tempo de vida de um ano.
AzureClassicRunAsConnection|Um ativo de conexão criado automaticamente e usado para autenticar no Azure para que você possa gerenciar recursos clássicos do Azure de runbooks.  

## Verificar a autenticação de Executar como

Em seguida, executaremos um pequeno teste para confirmar se você pode se autenticar com êxito usando a nova conta Executar como.

1. No portal do Azure, abra a conta de Automação criada anteriormente.
2. Clique no bloco **Runbooks** para abrir a lista de runbooks.
3. Selecione o runbook **AzureAutomationTutorialScript** e clique em **Iniciar** para iniciar o runbook. Será mostrado um aviso, que verifica se você deseja iniciar o runbook.
4. Um [trabalho de runbook](automation-runbook-execution.md) é criado e a folha **Trabalho** mostra o status do trabalho no bloco **Resumo do Trabalho**. O status do trabalho será iniciado como *Na fila* para indicar que o trabalho está aguardando um runbook worker ficar disponível na nuvem. O status mudará para *Iniciando* quando um runbook worker reivindicar o trabalho e para *Em Execução* quando o runbook realmente começar a ser executado. Quando o trabalho de runbook for concluído, o status será **Concluído**.<br> ![Teste de runbook da entidade de segurança](media/automation-sec-configure-azure-runas-account/job-summary-automationtutorialscript.png)<br>
5. Para ver os resultados detalhados do runbook, clique no bloco **Saída**.<br> Na folha **Saída**, você deverá ver que a conta foi autenticada e que retornou uma lista de todos os recursos disponíveis no grupo de recursos.
6. Feche a folha **Saída** para retornar à folha **Resumo do Trabalho**.
7. Feche o **Resumo do Trabalho** e a folha do runbook **AzureAutomationTutorialScript** correspondente.

## Verificar a autenticação de Executar como Clássica

Em seguida, executaremos um pequeno teste para confirmar se você pode se autenticar com êxito usando a nova conta Executar como Clássica.

1. No portal do Azure, abra a conta de Automação criada anteriormente.
2. Clique no bloco **Runbooks** para abrir a lista de runbooks.
3. Selecione o runbook **AzureClassicAutomationTutorialScript** e clique em **Iniciar** para iniciar o runbook. Será mostrado um aviso, que verifica se você deseja iniciar o runbook.
4. Um [trabalho de runbook](automation-runbook-execution.md) é criado, a folha Trabalho é aberta e o status do trabalho é mostrado no bloco **Resumo do Trabalho**. O status do trabalho será iniciado como *Na fila* para indicar que o trabalho está aguardando um runbook worker ficar disponível na nuvem. Ele mudará para *Iniciando* quando um runbook worker reivindicar o trabalho e para *Em Execução* quando o runbook realmente começar a ser executado. Quando o trabalho de runbook for concluído, deveremos ver um status **Concluído**.<br> ![Teste de runbook da entidade de segurança](media/automation-sec-configure-azure-runas-account/job-summary-automationclassictutorialscript.png)<br>
5. Para ver os resultados detalhados do runbook, clique no bloco **Saída**.<br> Na folha **Saída**, você deverá ver que a conta foi autenticada e que retornou uma lista de todas as VMs clássicas na assinatura.
6. Feche a folha **Saída** para retornar à folha **Resumo do Trabalho**.
7. Feche o **Resumo do Trabalho** e a folha do runbook **AzureClassicAutomationTutorialScript** correspondente.

## Atualizar uma conta de Automação usando o PowerShell

Aqui podemos fornecer a opção de usar o PowerShell para atualizar sua conta de Automação existente se:

- Você tiver criado uma conta de Automação, mas se recusou a criar a conta Executar como.
- Você já tiver uma conta de Automação para gerenciar recursos do Resource Manager e quiser atualizá-la para incluir a conta Executar como para autenticação de runbook.
- Você já tiver uma conta de Automação para gerenciar os recursos clássicos e quiser atualizá-la para usar a conta Executar como Clássica em vez de criar uma nova conta e migrar seus runbooks e ativos para ela.

Antes de prosseguir, verifique o seguinte:

- Você baixou e instalou o [Windows Management Framework (WMF) 4.0](https://www.microsoft.com/download/details.aspx?id=40855) se estiver executando o Windows 7. Se você estiver executando o Windows Server 2012 R2, o Windows Server 2012, o Windows Server 2008 R2, o Windows 8.1 ou o Windows 7 SP1, o [Windows Management Framework 5.0](https://www.microsoft.com/download/details.aspx?id=50395) estará disponível para instalação.
- Você instalou os cmdlets do Azure PowerShell 1.0. Para saber mais sobre esta versão e como instalá-la, veja [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).
- Você criou uma conta de automação. Essa conta será mencionada como o valor para os parâmetros *–AutomationAccountName* e *-ApplicationDisplayName* nos scripts nas seções a seguir.

Para obter os valores para *SubscriptionID*, *ResourceGroup* e *AutomationAccountName*, que são parâmetros obrigatórios para os scripts, no portal do Azure, selecione sua conta de Automação na folha **Conta de Automação** e selecione **Todas as configurações**. Na folha **Todas as configurações**, em **Configurações de Conta**, selecione **Propriedades**. Na folha **Propriedades**, você pode observar esses valores.<br> ![Propriedades da Conta de Automação](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)

### Criar o script do PowerShell da conta Executar como

O script do PowerShell a seguir irá configurar:

- Um aplicativo do Azure AD que será autenticado com o certificado autoassinado, cria uma conta de entidade de serviço para esse aplicativo no Azure AD e atribui a função Colaborador para essa conta em sua assinatura atual. Você pode alterar o Colaborador para Proprietário ou para qualquer outra função. Para saber mais, confira o artigo [Controle de acesso baseado em função na Automação do Azure](../automation/automation-role-based-access-control.md).
- Um ativo de certificado de Automação na conta de automação especificada chamado AzureRunAsCertificate. O ativo tem o certificado que a entidade de serviço utiliza.
- Um ativo de conexão de Automação na conta de automação especificada chamado AzureRunAsConnection. O ativo contém a ID do aplicativo, a ID do locatário, a ID da assinatura e a impressão digital do certificado.

As etapas a seguir orientarão você pelo procedimento de execução do script.

1. Salve o script a seguir em seu computador. Neste exemplo, salve-o com o nome de arquivo **New-AzureServicePrincipal.ps1**.

        #Requires -RunAsAdministrator
        Param (
        [Parameter(Mandatory=$true)]
        [String] $ResourceGroup,

        [Parameter(Mandatory=$true)]
        [String] $AutomationAccountName,

        [Parameter(Mandatory=$true)]
        [String] $ApplicationDisplayName,

        [Parameter(Mandatory=$true)]
        [String] $SubscriptionId,

        [Parameter(Mandatory=$true)]
        [String] $CertPlainPassword,

        [Parameter(Mandatory=$false)]
        [int] $NoOfMonthsUntilExpired = 12
        )

        Login-AzureRmAccount
        Import-Module AzureRM.Resources
        Select-AzureRmSubscription -SubscriptionId $SubscriptionId

        $CurrentDate = Get-Date
        $EndDate = $CurrentDate.AddMonths($NoOfMonthsUntilExpired)
        $KeyId = (New-Guid).Guid
        $CertPath = Join-Path $env:TEMP ($ApplicationDisplayName + ".pfx")

        $Cert = New-SelfSignedCertificate -DnsName $ApplicationDisplayName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"

        $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my" + $Cert.Thumbprint) -FilePath $CertPath -Password $CertPassword -Force | Write-Verbose

        $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate -ArgumentList @($CertPath, $CertPlainPassword)
        $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

        $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $KeyCredential.StartDate = $CurrentDate
        $KeyCredential.EndDate= $EndDate
        $KeyCredential.KeyId = $KeyId
        $KeyCredential.Type = "AsymmetricX509Cert"
        $KeyCredential.Usage = "Verify"
        $KeyCredential.Value = $KeyValue

        # Use Key credentials
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $ApplicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $keyCredential

        New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId | Write-Verbose
        Get-AzureRmADServicePrincipal | Where {$_.ApplicationId -eq $Application.ApplicationId} | Write-Verbose

        $NewRole = $null
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6)
        {
           # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
           Sleep 5
           New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
           Sleep 10
           $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
           $Retries++;
        }

        # Get the tenant id for this subscription
        $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
        $TenantID = $SubscriptionInfo | Select TenantId -First 1

        # Create the automation resources
        New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name AzureRunAsCertificate -Password $CertPassword -Exportable | write-verbose

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        $ConnectionAssetName = "AzureRunAsConnection"
        Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
        $ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues

2. Em seu computador, inicie o **Windows PowerShell** na tela **Iniciar** com direitos de usuário elevados.
3. Do shell de linha de comando do PowerShell com privilégios elevados, vá para a pasta que contém o script criado na Etapa 1 e execute o script. Altere os valores dos parâmetros *–ResourceGroup*, *-AutomationAccountName*, *-ApplicationDisplayName*, *-SubscriptionId* e *-CertPlainPassword*.<br>

    >[AZURE.NOTE] Você deverá se autenticar com o Azure depois de executar o script. Você deve entrar com uma conta que seja membro da função Administradores de Assinatura e coadministrador da assinatura.

        .\New-AzureServicePrincipal.ps1 -ResourceGroup <ResourceGroupName>
        -AutomationAccountName <NameofAutomationAccount> `
        -ApplicationDisplayName <DisplayNameofAutomationAccount> `
        -SubscriptionId <SubscriptionId> `
        -CertPlainPassword "<StrongPassword>"  
<br>

Depois que o script for concluído com êxito, consulte o [código de exemplo](#sample-code-to-authenticate-with-resource-manager-resources) para se autenticar nos recursos do Resource Manager e validar a configuração de credenciais.

### Criar um script do PowerShell da conta Executar como Clássica

O script do PowerShell a seguir irá configurar:

- Um ativo de certificado de Automação na conta de automação especificada chamado **AzureClassicRunAsCertificate**. O ativo contém o certificado usado para autenticar seus runbooks.
- Um ativo de conexão de Automação na conta de automação especificada chamado **AzureClassicRunAsConnection**. O ativo contém o nome da assinatura, a ID da assinatura e o nome de ativo de certificado.

O script criará um certificado de gerenciamento autoassinado e o salvará na pasta de arquivos temporários em seu computador sob o perfil do usuário usado para executar a sessão do PowerShell: *%USERPROFILE%\\AppData\\Local\\Temp*. Após a execução do script, você precisará carregar o certificado de gerenciamento do Azure no repositório de gerenciamento para a assinatura em que foi criada a conta de Automação. As etapas a seguir orientarão você pelo procedimento de execução do script e de carregamento do certificado.

1. Salve o script a seguir em seu computador. Neste exemplo, salve-o com o nome de arquivo **New-AzureClassicRunAsAccount.ps1**.

        #Requires -RunAsAdministrator
        Param (
        [Parameter(Mandatory=$true)]
        [String] $ResourceGroup,

        [Parameter(Mandatory=$true)]
        [String] $AutomationAccountName,

        [Parameter(Mandatory=$true)]
        [String] $ApplicationDisplayName,

        [Parameter(Mandatory=$true)]
        [String] $SubscriptionId,

        [Parameter(Mandatory=$true)]
        [String] $CertPlainPassword,

        [Parameter(Mandatory=$false)]
        [int] $NoOfMonthsUntilExpired = 12
        )

        Login-AzureRmAccount
        Import-Module AzureRM.Resources
        $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId
        $SubscriptionName = $subscription.Subscription.SubscriptionName

        $CurrentDate = Get-Date
        $EndDate = $CurrentDate.AddMonths($NoOfMonthsUntilExpired)
        $KeyId = (New-Guid).Guid
        $CertPath = Join-Path $env:TEMP ($ApplicationDisplayName + ".pfx")
        $CertPathCer = Join-Path $env:TEMP ($ApplicationDisplayName + ".cer")

        $Cert = New-SelfSignedCertificate -DnsName $ApplicationDisplayName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"

        $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my" + $Cert.Thumbprint) -FilePath $CertPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my" + $Cert.Thumbprint) -FilePath $CertPathCer -Type CERT | Write-Verbose

        # Create the automation resources
        $ClassicCertificateAssetName = "AzureClassicRunAsCertificate"
        New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name $ClassicCertificateAssetName  -Password $CertPassword -Exportable | write-verbose

        # Create an Automation connection asset named AzureClassicRunAsConnection in the Automation account. This connection uses ClassicCertificateAssetName.
        $ConnectionAssetName = "AzureClassicRunAsConnection"
        Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
        $ConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicCertificateAssetName}
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureClassicCertificate -ConnectionFieldValues $ConnectionFieldValues

        Write-Host -ForegroundColor red "Please upload the cert $CertPathCer to the Management store by following the steps below."
        Write-Host -ForegroundColor red "Log in to the Microsoft Azure Management portal (https://manage.windowsazure.com) and select Settings -> Management Certificates."
        Write-Host -ForegroundColor red "Then click Upload and upload the certificate $CertPathCer"

2. Em seu computador, inicie o **Windows PowerShell** na tela **Iniciar** com direitos de usuário elevados.
3. Do shell de linha de comando do PowerShell com privilégios elevados, vá para a pasta que contém o script criado na Etapa 1 e execute o script. Altere os valores dos parâmetros *–ResourceGroup*, *-AutomationAccountName*, *-ApplicationDisplayName*, *-SubscriptionId* e *-CertPlainPassword*.<br>

    >[AZURE.NOTE] Você deverá se autenticar com o Azure depois de executar o script. Você deve entrar com uma conta que seja membro da função Administradores de Assinatura e coadministrador da assinatura.

        .\New-AzureClassicRunAsAccount.ps1 -ResourceGroup <ResourceGroupName>
        -AutomationAccountName <NameofAutomationAccount> `
        -ApplicationDisplayName <DisplayNameofAutomationAccount> `
        -SubscriptionId <SubscriptionId> `
        -CertPlainPassword "<StrongPassword>"

Depois que o script for concluído com êxito, você precisará copiar o certificado criado na pasta **Temp** do seu perfil de usuário. Siga as etapas para [carregar um certificado de API de gerenciamento](../azure-api-management-certs.md) no portal clássico do Azure e consulte o [código de exemplo](#sample-code-to-authenticate-with-service-management-resources) para validar a configuração de credencial com recursos do Gerenciamento de Serviços.

## Código de exemplo para autenticação com recursos Resource Manager

Você pode usar o código de exemplo atualizado a seguir, obtido do runbook **AzureAutomationTutorialScript** de exemplo, para autenticação usando a conta Executar como para gerenciar os recursos do Resource Manager com seus runbooks.

    $connectionName = "AzureRunAsConnection"
    $SubId = Get-AutomationVariable -Name 'SubscriptionId'
    try
    {
       # Get the connection "AzureRunAsConnection "
       $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

       "Logging in to Azure..."
       Add-AzureRmAccount `
         -ServicePrincipal `
         -TenantId $servicePrincipalConnection.TenantId `
         -ApplicationId $servicePrincipalConnection.ApplicationId `
         -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
	   "Setting context to a specific subscription"	 
	   Set-AzureRmContext -SubscriptionId $SubId	 		 
    }
    catch {
        if (!$servicePrincipalConnection)
        {
           $ErrorMessage = "Connection $connectionName not found."
           throw $ErrorMessage
         } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
         }
    }


O script inclui duas linhas adicionais de código para oferecer suporte à referência a um contexto de assinatura para que você possa trabalhar facilmente entre várias assinaturas. O ativo de variável *SubscriptionId* contém a ID da assinatura. Após a instrução de cmdlet Add-AzureRmAccount, o [cmdlet Set-AzureRmContext](https://msdn.microsoft.com/library/mt619263.aspx) é declarado com o conjunto de parâmetros *-SubscriptionId*. Se o nome da variável for muito genérico, você poderá revisar o nome da variável para incluir um prefixo ou outra convenção de nomenclatura para facilitar a identificação para suas finalidades. Como alternativa, você pode usar o conjunto de parâmetros *-SubscriptionName* em vez de *-SubscriptionId* com um ativo de variável correspondente.

Observe que o cmdlet usado para autenticar o runbook, **Add-AzureRmAccount**, usa o conjunto de parâmetros *ServicePrincipalCertificate*. Ele se autentica usando o certificado de entidade de serviço em vez das credenciais.

## Código de exemplo para autenticação em recursos do Gerenciamento de Serviços

Você pode usar o código de exemplo atualizado a seguir, obtido do runbook **AzureClassicAutomationTutorialScript** de exemplo, para autenticação usando a conta Executar como Clássica para gerenciar os recursos clássicos com seus runbooks.

    $ConnectionAssetName = "AzureClassicRunAsConnection"
    # Get the connection
    $connection = Get-AutomationConnection -Name $connectionAssetName        

    # Authenticate to Azure with certificate
    Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
    if ($Conn -eq $null)
    {
       throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
    }

    $CertificateAssetName = $Conn.CertificateAssetName
    Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
    $AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
    if ($AzureCert -eq $null)
    {
       throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
    }

    Write-Verbose "Authenticating to Azure with certificate." -Verbose
    Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert
    Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID


## Próximas etapas

- Para saber mais sobre Entidades de Serviço, veja [Objetos de aplicativo e objetos de entidade de serviço](../active-directory/active-directory-application-objects.md).
- Para saber mais sobre o Controle de Acesso baseado em Função na Automação do Azure, consulte [Controle de acesso baseado em função na Automação do Azure](../automation/automation-role-Based-access-control.md).
- Para saber mais sobre certificados e serviços do Azure, confira [Visão geral dos certificados de Serviços de Nuvem do Azure](../cloud-services/cloud-services-certs-create.md).

<!---HONumber=AcomDC_0810_2016-->