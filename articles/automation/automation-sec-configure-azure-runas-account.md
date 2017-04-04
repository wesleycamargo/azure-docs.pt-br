---
title: Configurar uma conta Executar como do Azure | Microsoft Docs
description: "Este tutorial explica a criação, os testes e o uso de exemplo da autenticação de entidade de segurança na Automação do Azure."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
keywords: "nome principal do serviço, setspn, autenticação do azure"
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/27/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: fbca3d195290551d37606e231b997a40a602351f
ms.lasthandoff: 03/29/2017


---

# <a name="authenticate-runbooks-with-an-azure-run-as-account"></a>Autenticar runbooks com uma conta Executar como do Azure
Este artigo mostra como configurar uma conta de Automação do Azure no portal do Azure. Para fazer isso, você pode usar o recurso de conta Executar como para autenticar runbooks que gerenciam recursos no Azure Resource Manager ou Gerenciamento de Serviços do Azure.

Ao criar uma conta de Automação no portal do Azure, você cria automaticamente duas contas:

* Uma conta Executar como. Essa conta cria uma entidade de serviço no Azure AD (Azure Active Directory) e um certificado. Também atribui o RBAC (controle de acesso baseado em função) de Colaborador, que gerencia os recursos do Resource Manager por meio de runbooks.
* Uma conta Executar como clássica. Essa conta carrega um certificado de gerenciamento, que é usado para gerenciar o Gerenciamento de Serviços ou recursos clássicos usando runbooks.

Criar uma conta de Automação simplifica o processo para você e o ajuda a começar a criar e a implantar rapidamente os runbooks para dar suporte às suas necessidades de automação.

Com contas Executar como e Executar como Clássica, você pode:

* Fornecer uma maneira padronizada de se autenticar no Azure ao gerenciar recursos do Resource Manager ou do Gerenciamento de Serviços de runbooks no portal do Azure.
* Automatize o uso de runbooks globais, que você pode configurar nos Alertas do Azure.

> [!NOTE]
> O [Recurso de integração de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) do Azure com os runbooks globais de Automação exige uma conta de Automação configurada com uma conta dos tipos Executar Como e Executar Como Clássica. Você pode selecionar uma conta de Automação que já definiu contas Executar como e Executar como Clássica ou pode optar por criar uma nova conta de Automação.
>  

Este artigo mostra como criar uma conta de Automação no portal do Azure, atualizar uma conta de Automação usando o Azure PowerShell, gerenciar a configuração de conta e autenticar-se em seus runbooks.

Antes de começar a criar uma conta de Automação, é uma boa ideia entender e considerar o seguinte:

* Criar uma conta de Automação não afeta as contas de Automação que você talvez já tenha criado no modelo de implantação clássico ou do Resource Manager.
* O processo funciona apenas para contas de Automação que você cria no portal do Azure. A tentativa de criar uma conta no portal clássico do Azure não replica a configuração da conta Executar como.
* Se já tiver runbooks e ativos (como agendas ou variáveis) para gerenciar os recursos clássicos e desejar runbooks para autenticação com a nova conta Executar como Clássica, siga um destes procedimentos:

  * Para criar uma conta Executar como Clássica, siga as instruções na seção "Gerenciando a conta Executar como". 
  * Para atualizar a conta existente, use o script do PowerShell na seção "Atualizar a conta de Automação usando o PowerShell".
* Para autenticar usando a nova conta Executar como e a conta Executar como clássica da Automação, será necessário modificar seus runbooks existentes com o código de exemplo fornecido na seção [Autenticar exemplos de código](#authentication-code-examples).

    >[!NOTE]
    >A conta Executar como destina-se à autenticação em recursos do Resource Manager usando a entidade de serviço baseada em certificado. A conta Executar como Clássica destina-se à autenticação em recursos do Gerenciamento de Serviços com o certificado de gerenciamento.

## <a name="create-an-automation-account-from-the-azure-portal"></a>Criar uma conta de automação no Portal do Azure
Nesta seção, você pode criar uma conta de Automação do Azure no portal do Azure, que, por sua vez, cria uma conta Executar como e uma conta Executar como Clássica.

>[!NOTE]
>Para criar uma conta de Automação, você deve ser membro da função de Administradores de Serviço ou coadministrador da assinatura que está concedendo acesso à assinatura. Você também deve ser adicionado como um usuário a instância da assinatura padrão do Active Directory. A conta não precisa ser atribuída a uma função com privilégios.
>
>Se não for membro da instância do Active Directory da assinatura antes de ser adicionados à função de coadministrador da assinatura, você será adicionado ao Active Directory como convidado. Nesse caso, você receberá um aviso "Você não tem permissões para criar..." na folha **Adicionar Conta de Automação**.
>
>Os usuários adicionados à função de coadministrador primeiro podem ser removidos das assinaturas da instância do Active Directory e adicionados novamente para torná-los Usuários completos no Active Directory. Para verificar essa situação no painel **Azure Active Directory** no portal do Azure, selecione **Usuários e grupos**, selecione **Todos os usuários** e, depois de selecionar o usuário específico, selecione **Perfil**. O valor do atributo **Tipo de usuário** sob o perfil de usuários não deve ser igual a **Convidado**.
>

1. Conecte-se no Portal do Azure com uma conta que seja membro da função administradores da assinatura e coadministradora da assinatura.

2. Selecione **Contas de Automação**.

3. Na folha **Contas de Automação**, clique em **Adicionar**.
A folha **Adicionar Conta de Automação** será aberta.

 ![A folha "Adicionar Conta de Automação"](media/automation-sec-configure-azure-runas-account/create-automation-account-properties-b.png)

   > [!NOTE]
   > Se a conta não for membro da função de administradores de assinatura e coadministrador da assinatura, o seguinte aviso será exibido na folha **Adicionar Conta de Automação**:
   >
   >![Aviso Adicionar Conta de Automação](media/automation-sec-configure-azure-runas-account/create-account-without-perms.png)
   >
   >

4. Na folha **Adicionar Conta de Automação**, na caixa **Nome**, digite um nome para a nova conta de Automação.

5. Se tiver mais de uma assinatura, faça o seguinte:

    a. Em **Assinatura**, especifique uma para a nova conta.

    b. Em **Grupo de Recursos**, clique em **Criar novo** ou **Usar existente**.

    c. Em **Local**, especifique um datacenter do Azure.

6. Em **Criar conta Executar como do Azure**, selecione **Sim**e clique em **Criar**.

   > [!NOTE]
   > Se você optar por não criar a conta Executar como selecionando **Não**, será exibida uma mensagem de aviso na folha **Adicionar Conta de Automação**. Embora a conta seja criada no portal do Azure, não tem uma identidade de autenticação correspondente no serviço de diretório de assinatura do Resource Manager ou clássico. Consequentemente, a conta não tem acesso a recursos na assinatura. Este cenário impede que todos os runbooks que fazem referência a essa conta se autenticaquem e executem tarefas em recursos nesses modelos de implantação.
   >
   > ![Mensagem de aviso na folha "Adicionar Conta de Automação"](media/automation-sec-configure-azure-runas-account/create-account-decline-create-runas-msg.png)
   >
   > Além disso, como a entidade de serviço não é criada, a função de Colaborador não é atribuída.
   >

7. Enquanto o Azure cria a conta de Automação, você poderá acompanhar o andamento em **Notificações** no menu.

### <a name="resources"></a>Recursos
Quando a criação da conta de Automação tiver sido criada com êxito, vários recursos serão criados automaticamente para você. Os recursos estão resumidos nas duas seguintes tabelas:

#### <a name="run-as-account-resources"></a>Recursos da conta Executar como

| Recurso | Descrição |
| --- | --- |
| Runbook AzureAutomationTutorial | Um exemplo de runbook gráfico que demonstra como autenticar usando a conta Executar como e que obtém todos os recursos do Resource Manager. |
| Runbook do AzureAutomationTutorialScript | Um exemplo de runbook do PowerShell que demonstra como autenticar usando a conta Executar como e que obtém todos os recursos do Resource Manager. |
| AzureRunAsCertificate | O ativo de certificado é criado automaticamente quando você cria uma conta de Automação ou usa o script do PowerShell a seguir para uma conta existente. O certificado permite autenticar-se com o Azure para que você possa gerenciar recursos do Azure Resource Manager de runbooks. O certificado tem um tempo de vida de um ano. |
| AzureRunAsConnection | O ativo de conexão é criado automaticamente quando você cria uma conta de Automação ou usa o script do PowerShell para uma conta existente. |

#### <a name="classic-run-as-account-resources"></a>Recursos de conta Executar como clássica

| Recurso | Descrição |
| --- | --- |
| Runbook do AzureClassicAutomationTutorial | Um exemplo de runbook gráfico que obtém todas as VMs que são criadas usando o modelo de implantação clássico em uma assinatura usando a conta Executar como Clássica (certificado) e grava o nome da VM e o status. |
| Runbook do script AzureClassicAutomationTutorial | Um runbook do PowerShell de exemplo que obtém todas as VMs clássicas em uma assinatura usando a conta Executar como Clássica (certificado) e grava o nome e o status da VM. |
| AzureClassicRunAsCertificate | O ativo de certificado criado automaticamente que você usou para se autenticar no Azure para que possa gerenciar recursos clássicos do Azure por meio de runbooks. O certificado tem um tempo de vida de um ano. |
| AzureClassicRunAsConnection | O ativo de conexão criado automaticamente e usado para se autenticar no Azure para que você possa gerenciar recursos clássicos do Azure por meio de runbooks. |

## <a name="verify-run-as-authentication"></a>Verificar a autenticação de Executar como
Executaremos um pequeno teste para confirmar se você pode se autenticar com êxito usando a nova conta Executar como.

1. No portal do Azure, abra a conta de Automação criada anteriormente.

2. Clique no bloco **Runbooks** para abrir a lista de runbooks.

3. Selecione o runbook **AzureAutomationTutorialScript** clique em **Iniciar** para iniciar o runbook. Os seguintes eventos ocorrem:
 * Um [trabalho de runbook](automation-runbook-execution.md) é criado, a folha **Trabalho** é exibida e o status do trabalho é mostrado no bloco **Resumo do Trabalho**.
 * O status do trabalho é iniciado como **Na fila**, indicando que ele está aguardando até um runbook worker ficar disponível na nuvem.
 * O status se torna **Iniciando** quando um trabalhador reivindica o trabalho.
 * O status se torna **Em execução** quando o runbook começa a ser executado.
 * Quando o trabalho de runbook concluir a execução, você deverá ver o status **Concluído**.

       ![Security Principal Runbook Test](media/automation-sec-configure-azure-runas-account/job-summary-automationtutorialscript.png)
4. Para ver os resultados detalhados do runbook, clique no bloco **Saída**.  
A folha **Saída** é exibida, mostrando que o runbook foi autenticado com êxito e retornou uma lista de todos os recursos disponíveis no grupo de recursos.

5. Feche a folha **Saída** para voltar para a folha **Resumo do Trabalho**.

6. Feche a folha **Resumo do Trabalho** e a folha do runbook **AzureAutomationTutorialScript** correspondente.

## <a name="verify-classic-run-as-authentication"></a>Verificar a autenticação de Executar como Clássica
Executaremos um pequeno teste para confirmar se você pode se autenticar com êxito usando a nova conta Executar como Clássica.

1. No portal do Azure, abra a conta de Automação criada anteriormente.

2. Clique no bloco **Runbooks** para abrir a lista de runbooks.

3. Selecione o runbook **AzureClassicAutomationTutorialScript** clique em **Iniciar** para iniciar o runbook. Os seguintes eventos ocorrem:

 * Um [trabalho de runbook](automation-runbook-execution.md) é criado, a folha **Trabalho** é exibida e o status do trabalho é mostrado no bloco **Resumo do Trabalho**.
 * O status do trabalho é iniciado como **Na fila**, indicando que ele está aguardando até um runbook worker ficar disponível na nuvem.
 * O status se torna **Iniciando** quando um trabalhador reivindica o trabalho.
 * O status se torna **Em execução** quando o runbook começa a ser executado.
 * Quando o trabalho de runbook concluir a execução, você deverá ver o status **Concluído**.

    ![Teste de runbook da entidade de segurança](media/automation-sec-configure-azure-runas-account/job-summary-automationclassictutorialscript.png)<br>
4. Para ver os resultados detalhados do runbook, clique no bloco **Saída**.  
A folha **Saída** é exibida, mostrando que o runbook foi autenticado com êxito e retornou uma lista de todas as VMs clássicas na assinatura.

5. Feche a folha **Saída** para voltar para a folha **Resumo do Trabalho**.

6. Feche a folha **Resumo do Trabalho** e a folha do runbook **AzureAutomationTutorialScript** correspondente.

## <a name="managing-your-run-as-account"></a>Gerenciando a conta Executar como
Em algum momento antes que a conta de Automação expire, você precisará renovar o certificado. Se achar que a conta Executar como foi comprometida, você poderá excluí-la e recriá-la. Esta seção descreve como realizar essas operações.

### <a name="self-signed-certificate-renewal"></a>Renovação do certificado autoassinado
O certificado autoassinado que você criou para a conta Executar como expira um ano a contar da data de criação. Você pode renová-lo a qualquer momento antes que ele expire. Quando você o renova, o certificado atual é mantido para garantir que todos os runbooks que estão enfileiradas ou ativamente em execução e que se autenticam com a conta Executar como não sejam afetados negativamente. O certificado permanece válido até a data de expiração.

> [!NOTE]
> Se tiver configurado a conta Executar como da Automação para usar um certificado emitido por a autoridade de certificação corporativa e usar essa opção, o certificado da empresa será substituído por um certificado autoassinado.

Para renovar o certificado, faça o seguinte:

1. No Portal do Azure, abra a Conta de automação.

2. Na folha **Conta de Automação**, no painel **Propriedades da conta**, em **Configurações da Conta**, selecione **Contas Executar como**.

    ![Painel de propriedades da conta de Automação](media/automation-sec-configure-azure-runas-account/automation-account-properties-pane.png)
3. Na folha de propriedades **Contas Executar como**, selecione a conta Executar como ou a conta Executar como Clássica para a qual você deseja renovar o certificado.

4. Na folha **Propriedades** da conta selecionada, clique em **Renovar certificado**.

    ![Renovar o certificado da conta Executar como](media/automation-sec-configure-azure-runas-account/automation-account-renew-runas-certificate.png)

5. Enquanto o certificado está sendo renovado, você poderá acompanhar o andamento em **Notificações** no menu.

### <a name="delete-a-run-as-or-classic-run-as-account"></a>Excluir uma conta Executar como ou Executar como Clássica
Esta seção descreve como excluir e recriar uma conta Executar como ou Executar como Clássica. Quando você executa essa ação, a conta de Automação é mantida. Após excluir uma conta Executar como ou Executar como Clássica, você pode recriá-la no portal do Azure.

1. No Portal do Azure, abra a Conta de automação.

2. Na folha **Conta de Automação**, no painel de propriedades de conta, selecione **Contas Executar como**.

3. Na folha de propriedades de **Contas Executar como**, selecione a conta Executar como ou a conta Executar como Clássica que você deseja excluir. Em seguida, na folha **Propriedades** da conta selecionada, clique em **Excluir**.

 ![Excluir Conta Executar como](media/automation-sec-configure-azure-runas-account/automation-account-delete-runas.png)

4. Enquanto a conta está sendo excluída, você poderá acompanhar o andamento em **Notificações** no menu.

5. Depois que a conta for excluída, você poderá recriá-la na folha de propriedades **Contas Executar como** selecionando a opção de criação **Executar como Conta do Azure**.

 ![Recriar a conta de Automação Executar como](media/automation-sec-configure-azure-runas-account/automation-account-create-runas.png)

### <a name="misconfiguration"></a>Configuração incorreta
Alguns itens de configuração necessários para que a conta Executar como ou Executar como Clássica funcione corretamente podem foram excluídos ou criados incorretamente durante a instalação inicial. Os itens incluem:

* Ativo de certificado
* Ativo de conexão
* A Conta Executar como foi removida da função de Colaborador
* Entidade de serviço ou aplicativo no Azure AD

Nos casos anteriores e em outras instâncias de uma configuração incorreta, a conta de Automação detecta as alterações e exibe o status *Incompleto* na folha de propriedades **Contas Executar como** para a conta.

![Status incompleto de configuração de conta Executar como](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config.png)

Quando você selecionar a conta Executar como, o painel **Propriedades** da conta exibirá a seguinte mensagem de erro:

![Mensagem de aviso incompleto da configuração da conta Executar como](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config-msg.png).

Você pode resolver rapidamente esses problemas de conta Executar como excluindo e recriando a conta.

## <a name="update-your-automation-account-by-using-powershell"></a>Atualizar sua conta de Automação usando o PowerShell
Você poderá usar o PowerShell para atualizar a conta de automação existente se:

* Você criar uma conta de Automação, mas se recusar a criar a conta Executar como.
* Você já usa uma conta de Automação para gerenciar recursos do Resource Manager e quer atualizá-la para incluir a conta Executar como para autenticação de runbook.
* Você já usar uma conta de Automação para gerenciar os recursos clássicos e quiser atualizá-la para usar a conta Executar como Clássica em vez de criar uma nova conta e migrar seus runbooks e ativos para ela.   
* Você quiser criar uma conta Executar como e uma conta Clássica Executar como usando um certificado emitido por sua CA (Autoridade de Certificação).

O script tem os seguintes pré-requisitos:

* O script pode ser executado somente no Windows 10 e no Windows Server 2016 com módulos do Azure Resource Manager 2.01 e posterior. Não há suporte nas versões anteriores do Windows.
* Azure PowerShell 1.0 e posterior. Para obter informações sobre a versão 1.0 do PowerShell, confira [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Uma conta de Automação, que é referenciada como o valor para os parâmetros *–AutomationAccountName* e *-pplicationDisplayName* no script do PowerShell a seguir.

Para obter os valores para *SubscriptionID*, *ResourceGroup* e *AutomationAccountName*, que são parâmetros obrigatórios para os scripts, faça o seguinte:
1. No portal do Azure, selecione a conta de Automação na folha **Conta de Automação** e selecione **Todas as configurações**. 
2. Na folha **Todas as configurações**, em **Configurações de Conta**, selecione **Propriedades**. 
3. Observe os valores na folha **Propriedades**.

 ![A folha "Propriedades" da conta de Automação](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)  

### <a name="create-a-run-as-account-powershell-script"></a>Criar o script do PowerShell da conta Executar como
Este script do PowerShell inclui suporte para as seguintes configurações:

* Crie uma conta Executar como usando um certificado autoassinado.
* Crie uma conta Executar como e uma conta Executar como Clássica usando um certificado autoassinado.
* Crie uma conta Executar como e uma conta Executar como Clássica usando um certificado corporativo.
* Crie uma conta Executar como e uma conta Executar como Clássica usando um certificado autoassinado na nuvem do Azure Governamental.

Dependendo da opção de configuração que você selecionar, o script criará os itens a seguir.

**Para contas Executar como:**

* Cria um aplicativo do Azure AD para ser exportado com a chave pública autoassinada ou de certificado corporativo ou cria uma conta de entidade de serviço para o aplicativo no Azure AD e atribui a função de Colaborador à conta na assinatura atual. Você pode alterar essa configuração para Proprietário ou qualquer outra função. Para obter mais informações, confira [Controle de acesso baseado em função na Automação do Azure](automation-role-based-access-control.md).
* Cria um ativo de certificado de Automação chamado *AzureRunAsCertificate* na conta de Automação especificada. O ativo de certificado contém a chave privada do certificado que é usada pelo aplicativo do Azure AD.
* Cria um ativo de conexão de Automação chamado *AzureRunAsConnection* na conta de Automação especificada. O ativo de conexão contém applicationId, tenantId, subscriptionId e a impressão digital do certificado.

**Para contas Executar como clássicas:**

* Cria um ativo de certificado de Automação chamado *AzureClassicRunAsCertificate* na conta de Automação especificada. O ativo de certificado contém a chave privada do certificado usada pelo certificado de gerenciamento.
* Cria um ativo de conexão de Automação chamado *AzureClassicRunAsConnection* na conta de Automação especificada. O ativo de conexão contém o nome da assinatura, subscriptionId e o nome do ativo de certificado.

>[!NOTE]
> Se selecionar a opção para criar uma conta Executar como Clássica, depois que o script for executado, carregue o certificado público (extensão de nome de arquivo .cer) para o repositório de gerenciamento da assinatura em que a conta de Automação foi criada.
> 

Para executar o script e carregar o certificado, faça o seguinte:

1. Salve o script a seguir em seu computador. Neste exemplo, salve-o com o nome de arquivo *New-RunAsAccount.ps1*.

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
        [Boolean] $CreateClassicRunAsAccount,

        [Parameter(Mandatory=$true)]
        [String] $SelfSignedCertPlainPassword,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPathForRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPlainPasswordForRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPathForClassicRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

        [Parameter(Mandatory=$false)]
        [ValidateSet("AzureCloud","AzureUSGovernment")]
        [string]$EnvironmentName="AzureCloud",

        [Parameter(Mandatory=$false)]
        [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
        )

        function CreateSelfSignedCertificate([string] $keyVaultName, [string] $certificateName, [string] $selfSignedCertPlainPassword,
                                      [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
           -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
           -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired)

        $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
        }

        function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {  
        $CurrentDate = Get-Date
        $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
        $KeyId = (New-Guid).Guid

        $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $KeyCredential.StartDate = $CurrentDate
        $KeyCredential.EndDate= [DateTime]$PfxCert.GetExpirationDateString()
        $KeyCredential.KeyId = $KeyId
        $KeyCredential.CertValue  = $keyValue

        # Use key credentials and create an Azure AD application
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $KeyCredential
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6)
        {
           Sleep -s 10
           New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
           $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
           $Retries++;
        }
           return $Application.ApplicationId.ToString();
        }

        function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName,[string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force   
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
        }

        function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
        }

        Import-Module AzureRM.Profile
        Import-Module AzureRM.Resources

        $AzureRMProfileVersion= (Get-Module AzureRM.Profile).Version
        if (!(($AzureRMProfileVersion.Major -ge 2 -and $AzureRMProfileVersion.Minor -ge 1) -or ($AzureRMProfileVersion.Major -gt 2)))
        {
           Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
           return
        }

        Login-AzureRmAccount -EnvironmentName $EnvironmentName
        $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

        # Create a Run As account by using a service principal
        $CertifcateAssetName = "AzureRunAsCertificate"
        $ConnectionAssetName = "AzureRunAsConnection"
        $ConnectionTypeName = "AzureServicePrincipal"

        if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
        $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
        $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
        } else {
          $CertificateName = $AutomationAccountName+$CertifcateAssetName
          $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
          $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
          $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
          CreateSelfSignedCertificate $KeyVaultName $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create a service principal
        $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
        $ApplicationId=CreateServicePrincipal $PfxCert $ApplicationDisplayName

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

        # Populate the ConnectionFieldValues
        $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
        $TenantID = $SubscriptionInfo | Select TenantId -First 1
        $Thumbprint = $PfxCert.Thumbprint
        $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}


        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

        if ($CreateClassicRunAsAccount) {
            # Create a Run As account by using a service principal
            $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
            $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
            $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
            $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
                    "Log in to the Microsoft Azure Management portal (https://manage.windowsazure.com) and select Settings -> Management Certificates." + [Environment]::NewLine +
                    "Then click Upload and upload the .cer format of #CERT#"

             if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
             $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
             $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
             $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
        } else {
             $ClassicRunAsAccountCertificateName = $AutomationAccountName+$ClassicRunAsAccountCertifcateAssetName
             $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
             $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
             $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
             $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
             CreateSelfSignedCertificate $KeyVaultName $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.SubscriptionName
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red $UploadMessage
        }

2. No computador, clique em **Iniciar** e inicie o **Windows PowerShell** com direitos de usuário elevados.

3. Por meio do shell de linha de comando do PowerShell com privilégios elevados, acesse a pasta que contém o script que você criou na etapa 1.

4. Execute o script usando os valores de parâmetro para a configuração necessária.

    **Criar uma conta Executar como usando um certificado autoassinado**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false`

    **Criar uma conta Executar como e uma conta Executar como Clássica usando um certificado autoassinado**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true`

    **Criar uma conta Executar como e uma conta Executar como Clássica usando um certificado corporativo**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>`

    **Criar uma conta Executar como e uma conta Executar como Clássica usando um certificado autoassinado na nuvem do Azure Governamental**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment`

    > [!NOTE]
    > Depois que o script for executado, você deverá se autenticar no Azure. Ente com uma conta que seja membro da função de administradores de assinatura e coadministrador da assinatura.
    >
    >

Depois que o script for executado com êxito, observe o seguinte:
* Se você tiver criado uma conta Executar como Clássica com um certificado autoassinado público (arquivo .cer), o script a criará e salvará na pasta de arquivos temporários no computador sob o perfil de usuário *%USERPROFILE%\AppData\Local\Temp*, que é usado para executar a sessão do PowerShell.
* Se tiver criado uma conta Executar como Clássica com um certificado público corporativo (arquivo .cer), use este certificado. Siga as instruções para [carregar um certificado de API de gerenciamento no portal clássico do Azure](../azure-api-management-certs.md)e valide a configuração de credencial com recursos de gerenciamento de serviço usando o [código de exemplo para se autenticar com Recursos de Gerenciamento de Serviço](#sample-code-to-authenticate-with-service-management-resources). 
* Se *não* criou uma conta Executar como Clássica, autentique-se com os recursos do Resource Manager e valide a configuração de credenciais usando o [código de exemplo para autenticação com recursos de Gerenciamento de Serviços](#sample-code-to-authenticate-with-resource-manager-resources).

## <a name="sample-code-to-authenticate-with-resource-manager-resources"></a>Código de exemplo para autenticação com recursos Resource Manager
Você pode usar o código de exemplo atualizado a seguir, obtido do runbook *AzureAutomationTutorialScript* de exemplo, para autenticação usando a conta Executar como para gerenciar os recursos do Resource Manager com seus runbooks.

    $connectionName = "AzureRunAsConnection"
    $SubId = Get-AutomationVariable -Name 'SubscriptionId'
    try
    {
       # Get the connection "AzureRunAsConnection "
       $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

       "Signing in to Azure..."
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

Para ajudá-lo a trabalhar facilmente entre várias assinaturas, o script inclui duas linhas de código adicionais que dão suporte à referência a um contexto de assinatura. Um ativo de variável chamado *SubscriptionId* contém a ID da assinatura. Após a instrução de cmdlet `Add-AzureRmAccount`, o cmdlet [`Set-AzureRmContext`](https://msdn.microsoft.com/library/mt619263.aspx) é declarado com o conjunto de parâmetros *-SubscriptionId*. Se o nome da variável for muito genérico, você poderá revisá-lo para incluir um prefixo ou usar outra convenção de nomenclatura para torná-lo mais fácil de identificar. Como alternativa, você pode usar o conjunto de parâmetros *-SubscriptionName* em vez de *-SubscriptionId* com um ativo de variável correspondente.

O cmdlet que você usa para se autenticar no runbook, `Add-AzureRmAccount`, usa o conjunto de parâmetros *ServicePrincipalCertificate*. Realiza a autenticação usando o certificado de entidade de serviço, não as credenciais do usuário.

## <a name="sample-code-to-authenticate-with-service-management-resources"></a>Código de exemplo para autenticação em recursos do Gerenciamento de Serviços
Você pode usar o código de exemplo atualizado a seguir, obtido do runbook *AzureClassicAutomationTutorialScript* de exemplo, para autenticação usando a conta Executar como Clássica para gerenciar os recursos clássicos com seus runbooks.

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

## <a name="next-steps"></a>Próximas etapas
* [Objetos de aplicativo e entidade de serviço no Azure AD](../active-directory/active-directory-application-objects.md)
* [Controle de acesso com base em função na Automação do Azure](automation-role-based-access-control.md)
* [Visão geral sobre certificados para os Serviços de Nuvem do Azure](../cloud-services/cloud-services-certs-create.md)

