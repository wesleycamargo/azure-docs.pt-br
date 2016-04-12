<properties
    pageTitle="Configurar conta Executar como do Azure | Microsoft Azure"
    description="Tutorial que explica a criação, os testes e o uso de exemplo da autenticação de entidade de segurança na Automação do Azure."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="03/31/2016"
    ms.author="magoedte"/>

# Autenticar runbooks com uma conta Executar como do Azure
Este tópico mostra como configurar uma conta de Automação no portal do Azure usando o novo recurso de conta Executar como (também conhecido como uma entidade de serviço) para acessar recursos do Azure Resource Manager (ARM) em sua assinatura com runbooks de Automação. Quando você cria uma nova conta de Automação no portal do Azure, ele cria automaticamente uma nova entidade de serviço e é atribuída por padrão à função do controle de acesso baseado em função do Colaborador na assinatura. Isso simplifica o processo para você e o ajuda a começar a criar e a implantar rapidamente os runbooks para dar suporte às suas necessidades de automação.

Ao usar uma entidade de serviço, você pode:

* Fornecer uma maneira padronizada de autenticar com o Azure ao gerenciar recursos do Azure ARM usando runbooks
* Automatizar o uso de runbooks globais configurados em Alertas do Azure


>[AZURE.NOTE] O [recurso Integração de alerta](../azure-portal/insights-receive-alert-notifications.md) do Azure com Runbooks Globais de Automação exige uma conta de Automação configurada com uma entidade de serviço. Você pode selecionar uma conta de Automação que já tenha um usuário definido por entidade de serviço ou optar por criar um novo.



Nós mostraremos como criar a conta de Automação no portal do Azure e atualizar uma conta com uma conta Executar como usando o Azure PowerShell, além de mostrar como se autenticar com a entidade de serviço em seus runbooks.

## Criar uma nova conta de Automação do portal do Azure
Nesta seção, você executará as etapas a seguir para criar uma nova conta de Automação do Azure e a entidade de serviço no portal do Azure.

>[AZURE.NOTE] O usuário que executar essas etapas *deverá* ser membro da função Administradores de Assinatura.

1. Faça logon no portal do Azure como administrador de serviço para a assinatura do Azure que você deseja gerenciar.
2. Selecione **Contas de Automação**.
3. Na folha Contas de Automação, clique em **Adicionar**.<br>![Adicionar Conta de Automação](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties.png)
4. Na folha **Adicionar Conta de Automação**, na caixa **Nome**, digite um nome para a nova conta de Automação.
5. Se você tiver mais de uma assinatura, especifique a assinatura certa para a nova conta, bem como um **Grupo de recursos** novo ou existente e um **Local** de data center do Azure.
6. Verifique se o valor **Sim** está selecionado para a opção **Criar conta Executar como do Azure** e clique no botão **Criar**.  

    ![Aviso Adicionar Conta de Automação](media/automation-sec-configure-azure-runas-account/add-account-decline-create-runas-msg.png)

    >[AZURE.NOTE] Se você optar por não criar a conta Executar como ao selecionar a opção **Não**, verá uma mensagem de aviso na folha **Adicionar Conta de Automação**. Embora a conta seja criada e atribuída à função **Colaborador** na assinatura, ela não terá uma identidade de autenticação correspondente em seu serviço de diretório de assinaturas e, portanto, não haverá recursos de acesso em sua assinatura. Isso impedirá qualquer runbook que faça referência a essa conta seja capaz de se autenticar e de executar tarefas nos recursos do ARM.

    ![Aviso Adicionar Conta de Automação](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties-error.png)

    >[AZURE.NOTE] Se você receber uma mensagem de erro de permissão negada depois de clicar no botão **Criar**, sua conta não é um membro da função Administradores de assinatura.

7. Enquanto o Azure cria a conta de Automação, você poderá acompanhar o andamento em **Notificações** no menu.

Ao concluir, a conta de Automação será criada com um ativo Certificado denominado **AzureRunAsCertificate** com um tempo de vida de um ano e um ativo Conexão denominado **AzureRunAsConnection**.

## Atualizar uma Conta de automação usando o PowerShell
O procedimento abaixo atualiza a Conta de automação existente e cria a entidade de serviço usando o PowerShell. Este procedimento será necessário se você tiver criado uma conta sem criar a conta Executar como.

Antes de prosseguir, verifique o seguinte:

1. Você baixou e instalou o [Módulo do Azure Active Directory para Windows PowerShell (versão de 64 bits)](http://go.microsoft.com/fwlink/p/?linkid=236297)
2. Você criou uma conta de automação. Essa conta será referenciada como o valor para os parâmetros – AutomationAccountName e - ApplicationDisplayName no script abaixo.

O script do PowerShell irá configurar o seguinte:

* Um aplicativo do Azure AD que pode ser autenticado com o certificado autoassinado, uma conta de entidade de serviço para esse aplicativo no Azure AD e a atribuição da função Colaborador (você pode alterar isso para o proprietário ou para qualquer outra função) para essa conta em sua assinatura atual. Para saber mais, examine o artigo [Controle de acesso baseado em função na Automação do Azure](../automation/automation-role-based-access-control.md).  
* Um ativo de certificado de Automação na conta de automação especificada chamado **AzureRunAsCertificate**, que contém o certificado usado na entidade de serviço.
* Um ativo de conexão de Automação na conta de automação especificada chamado **AzureRunAsConnection**, que contém a applicationId, a tenantId, a subscriptionId e a impressão digital do certificado.  


### Executar o script do PowerShell

1. Salve o script a seguir em seu computador. Neste exemplo, salve-o com o nome de arquivo **New-AzureServicePrincipal.ps1**.  

    ```
    #Requires - RunAsAdministrator
    Param (
    [Parameter(Mandatory=$true)]
    [String] $ResourceGroup,

    [Parameter(Mandatory=$true)]
    [String] $AutomationAccountName,
   
    [Parameter(Mandatory=$true)]
    [String] $ApplicationDisplayName,
   
    [Parameter(Mandatory=$true)]
    [String] $CertPlainPassword,
   
    [Parameter(Mandatory=$false)]
    [int] $NoOfMonthsUntilExpired = 12
    )
   
    Login-AzureRmAccount
    Import-Module AzureRM.Resources
		
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
    $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $ServicePrincipalDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $keyCredential

    New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId | Write-Verbose
    Get-AzureRmADServicePrincipal | Where {$_.ApplicationId -eq $Application.ApplicationId} | Write-Verbose

    $NewRole = $null
    $Retries = 0;
    While ($NewRole -eq $null -and $Retries -le 2)
    {
      # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
      Sleep 5
      New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose
      Sleep 5
      $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
      $Retries++;
    }

    # Get the tenant id for this subscription
    $SubscriptionInfo = Get-AzureRmSubscription
    $TenantID = $SubscriptionInfo | Select TenantId -First 1

    # Create the automation resources
    New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name AzureRunAsCertificate -Password $CertPassword -Exportable | write-verbose

    # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    $ConnectionAssetName = "AzureRunAsConnection"
    $SubscriptionId = $SubscriptionInfo | Select SubscriptionId -First 1
    Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
    $ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId.SubscriptionId}
    New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
    ```
<br>
2. Em seu computador, inicie o **Windows PowerShell** na tela **Iniciar** com direitos de usuário elevados.
3. No shell da linha de comando elevado do PowerShell, navegue até a pasta que contém o script criado na Etapa 1 e execute o script alterando os valores dos parâmetros *–ResourceGroup*, *-AutomationAccountName*, *-ApplicationDisplayName* e *-CertPlainPassword*.<br> 

    ```
    .\New-AzureServicePrincipal.ps1 -ResourceGroup <ResourceGroupName> 
     -AutomationAccountName <NameofAutomationAccount> 
     -ApplicationDisplayName <DisplayNameofAutomationAccount> 
     -CertPlainPassword "<StrongPassword>"
    ```   
<br>

    >[AZURE.NOTE] Você deverá se autenticar com o Azure depois de executar o script. Você *deve* fazer logon com uma conta que seja um administrador de serviço na assinatura. <br> 
4. Depois que o script for concluído com êxito, vá para a próxima seção para testar e verificar a nova configuração de credencial.

### Verificar autenticação 
Em seguida, executaremos um pequeno teste para confirmar que você é capaz de se autenticar com êxito usando a nova entidade de serviço. Se não for possível se autenticar com êxito, vá para a Etapa 1 e confirme cada uma das etapas anteriores novamente.

1. No Portal do Azure, abra a conta de Automação criada anteriormente.  
2. Clique no bloco **Runbooks** para abrir a lista de runbooks.
3. Crie um novo runbook clicando no botão **Adicionar um runbook** e, na folha **Adicionar Runbook**, selecione **Criar um novo runbook**.
4. Dê ao runbook o nome *Test-SecPrin-Runbook* e selecione PowerShell como o **Tipo de Runbook**. Clique em **Criar** para criar o runbook.
5. Na folha **Editar Runbook do PowerShell**, cole o seguinte código na tela:<br>
  
    ```
     $Conn = Get-AutomationConnection -Name AzureRunAsConnection 
     Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID 
     -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```  
<br>
6. Salve o runbook clicando em **Salvar**.
7. Clique em **Painel de teste** para abrir a folha **Teste**.
8. Clique em **Iniciar** para iniciar o teste.
9. Um [trabalho de runbook](automation-runbook-execution.md) é criado e seu status é exibido no painel.  
10. O status do trabalho será iniciado como *Na fila*, indicando que ele está aguardando um runbook worker ficar disponível na nuvem. Ele mudará para *Iniciando* quando um runbook worker reivindicar o trabalho e para *Executando* quando o runbook realmente começar a ser executado.  
11. Quando o trabalho do runbook é concluído, sua saída é exibida. Em nosso caso, veremos um status **Concluído**.<br> ![Teste de runbook da entidade de segurança](media/automation-sec-configure-azure-runas-account/runbook-test-results.png)<br> 
12. Feche a folha **Teste** para retornar à tela.
13. Feche a folha **Editar Runbook do PowerShell**.
14. Feche a folha **Test-SecPrin-Runbook**.

O código acima, usado para verificar se a nova conta está configurada corretamente, é o que você usará em seus runbooks do PowerShell para se autenticar na Automação do Azure para gerenciar recursos do ARM. Obviamente, você pode continuar a se autenticar com a conta de Automação que usa no momento.

## Próximas etapas
- Para saber mais sobre Entidades de Serviço, veja [Objetos de aplicativo e objetos de entidade de serviço](../active-directory/active-directory-application-objects.md).
- Para saber mais sobre o Controle de Acesso baseado em Função na Automação do Azure, veja [Controle de acesso baseado em função na Automação do Azure](../automation/automation-role-based-access-control.md).

<!---HONumber=AcomDC_0406_2016-->