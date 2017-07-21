---
title: "Hybrid Runbook Workers da Automação do Azure | Microsoft Docs"
description: "Este artigo fornece informações sobre como instalar e usar Runbook Worker Híbrido, que é um recurso da Automação do Azure que permite que você execute runbooks em máquinas no seu data center local."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 06227cda-f3d1-47fe-b3f8-436d2b9d81ee
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/29/2017
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 5cd863c3e357b67d281adb8484376295ad9099ec
ms.contentlocale: pt-br
ms.lasthandoff: 06/30/2017

---

# <a name="automate-resources-in-your-data-center-with-hybrid-runbook-worker"></a>Automatizar recursos em seu data center com Hybrid Runbook Worker
Os Runbooks na Automação do Azure não podem acessar recursos no seu data center local, já que eles são executados na nuvem do Azure.  O recurso Runbook Worker Híbrido da Automação do Azure permite executar runbooks em máquinas localizadas no seu data center para gerenciar recursos locais. Os runbooks são armazenados e gerenciados na Automação do Azure e entregues a uma ou mais máquinas locais.  

Essa funcionalidade está ilustrada na imagem a seguir:<br>  

![Visão geral do Runbook Worker Híbrido](media/automation-hybrid-runbook-worker/automation.png)

Você pode designar um ou mais computadores no seu data center para atuar como um Runbook Worker Híbrido e executar runbooks da Automação do Azure.  Cada trabalho exige o Agente de Gerenciamento da Microsoft com uma conexão ao Microsoft Operations Management Suite e o ambiente de runbook da Automação do Azure.  O Operations Management Suite é usado somente para instalar e manter o agente de gerenciamento, bem como para monitorar a funcionalidade do trabalho.  A entrega de runbooks e as instruções para executá-los são desempenhadas pela Automação do Azure.

Não há nenhum requisito de firewall de entrada para dar suporte a Runbook Workers Híbridos. O agente no computador local inicia todas as comunicações com a Automação do Azure na nuvem. Quando um runbook é iniciado, a Automação do Azure cria uma instrução que é recuperada pelo agente. O agente então puxa o runbook e todos os parâmetros antes de executá-lo.  Ele também vai recuperar os [ativos](http://msdn.microsoft.com/library/dn939988.aspx) usados pelo runbook da Automação do Azure.

> [!NOTE]
> Para gerenciar a configuração de seus servidores que dão suporte à função Hybrid Runbook Worker com o DSC (Configuração de Estado Desejado), você precisará adicioná-los como nós DSC.  Para saber mais sobre a integração deles para gerenciamento com DSC, confira [Máquinas de integração para o gerenciamento pelo DSC de Automação do Azure](automation-dsc-onboarding.md).           
><br>
>Se você habilitar a [solução de Gerenciamento de atualização](../operations-management-suite/oms-solution-update-management.md), qualquer computador com Windows conectado ao seu espaço de trabalho do OMS será automaticamente configurado como um Hybrid Runbook Worker para oferecer suporte a runbooks incluídos nessa solução.  No entanto, ele não estará registrado com nenhum grupo Hybrid Worker que você já tenha definido em sua Conta de automação.  O computador pode ser adicionado a um grupo Hybrid Runbook Worker na sua Conta de automação para dar suporte a runbooks de automação enquanto você estiver usando a mesma conta para a solução e para a associação de grupo do Hybrid Runbook Worker.  Essa funcionalidade foi adicionada à versão 7.2.12024.0 do Hybrid Runbook Worker.  

## <a name="hybrid-runbook-worker-groups"></a>Grupos de Runbook Worker Híbrido
Cada Runbook Worker Híbrido é membro de um grupo de Runbook Worker Híbrido que você especifica ao instalar o agente.  Um grupo pode conter um único agente, mas você pode instalar vários agentes em um grupo para ter alta disponibilidade.

Quando você inicia um runbook em um Runbook Worker Híbrido, deve especificar o grupo no qual ele será executado.  Os membros do grupo determinarão qual trabalhador atenderá a solicitação.  Você não pode especificar um trabalhador específico.

## <a name="hybrid-runbook-worker-requirements"></a>Requisitos do Hybrid Runbook Worker
Designe pelo menos um computador local para executar trabalhos de runbook híbrido.  Esse computador deve ter o seguinte:

* Windows Server 2012 ou posterior
* Windows PowerShell 4.0 ou posterior.  É recomendável instalar o Windows PowerShell 5.0 no computador para maior confiabilidade. Você pode baixar a versão mais recente no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=50395)
* Mínimo de 2 núcleos e 4 GB de RAM

Considere as seguintes recomendações para hybrid workers:

* Designe vários hybrid workers em cada grupo para alta disponibilidade.  
* Hybrid workers podem coexistir com os servidores de runbook do Service Management Automation ou do System Center Orchestrator.
* Pense na possibilidade de usar um computador fisicamente localizado ou próximo da região da sua Conta de automação, uma vez que os dados do trabalho são enviados de volta à Automação do Azure quando um trabalho é concluído.

### <a name="configure-proxy-and-firewall-settings"></a>Configurar o proxy e as definições do firewall
Para que o Hybrid Runbook Worker local se conecte e se registre no serviço OMS (Microsoft Operations Management Suite), ele deve ter acesso ao número da porta e às URLs descritas abaixo.  Isso é além das [portas e URLs necessárias para que o Microsoft Monitoring Agent](../log-analytics/log-analytics-windows-agents.md#network) se conecte ao OMS. Se você usar um servidor proxy para comunicação entre o agente e o serviço do OMS, será necessário garantir que os recursos apropriados estejam acessíveis. Se você usar um firewall para restringir o acesso à Internet, precisará configurar o firewall para permitir o acesso.

As informações a seguir listam a porta e as URLs que são necessárias para que o Hybrid Runbook Worker se comunique com a Automação.

* Porta: somente a TCP 443 é necessária para acesso de Internet de saída
* URL global: *.azure-automation.net

Se você tem uma conta de Automação definida para uma região específica e deseja restringir a comunicação com o datacenter regional, a tabela a seguir fornece o registro de DNS para cada região.

| **Região** | **Registro DNS** |
| --- | --- |
| Centro-Sul dos Estados Unidos |scus-jobruntimedata-prod-su1.azure-automation.net |
| Leste dos EUA 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| Centro-Oeste dos EUA | wcus-jobruntimedata-prod-su1.azure-automation.net |
| Europa Ocidental |we-jobruntimedata-prod-su1.azure-automation.net |
| Norte da Europa |ne-jobruntimedata-prod-su1.azure-automation.net |
| Canadá Central |cc-jobruntimedata-prod-su1.azure-automation.net |
| Sudeste da Ásia |sea-jobruntimedata-prod-su1.azure-automation.net |
| Índia Central |cid-jobruntimedata-prod-su1.azure-automation.net |
| Leste do Japão |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Sudeste da Austrália |ase-jobruntimedata-prod-su1.azure-automation.net |
| Sul do Reino Unido | uks-jobruntimedata-prod-su1.azure-automation.net |
| Gov. dos EUA – Virgínia | usge-jobruntimedata-prod-su1.azure-automation.us |

Para obter uma lista de endereços IP em vez de nomes, baixe e leia o arquivo xml [Endereço IP de Datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653) no Centro de Download da Microsoft. 

> [!NOTE]
> Esse arquivo contém intervalos de endereços IP (inclusive intervalos de Computação, SQL e Armazenamento) usados em Datacenters do Microsoft Azure. Um arquivo atualizado é postado semanalmente que reflete os intervalos atualmente implantados e quaisquer alterações futuras para os intervalos de IP. Novos intervalos que aparecem no arquivo não serão usados em datacenters por pelo menos uma semana. Baixe o novo arquivo xml semanalmente e faça as alterações necessárias no seu site para identificar corretamente os serviços em execução no Azure. Os usuários do Express Route podem observar esse arquivo usado para atualizar o anúncio de BGP de espaço do Azure na primeira semana de cada mês. 
> 

## <a name="installing-hybrid-runbook-worker"></a>Instalando o Runbook Worker Híbrido

Para instalar e configurar o Hybrid Runbook Worker, há dois métodos disponíveis.  O método recomendado é usar um runbook de Automação para automatizar completamente o processo necessário para configurar um computador com Windows.  O segundo método é seguir um procedimento passo a passo para instalar e configurar a função manualmente.  

### <a name="automated-deployment"></a>Implantação automatizada

Execute as seguintes etapas para automatizar a instalação e configuração da função do Hybrid Worker.  

1. Baixe o script *New-OnPremiseHybridWorker.ps1* da [Galeria do PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.0/DisplayScript) diretamente do computador que executa a função Hybrid Runbook Worker ou de outro computador no seu ambiente e copie-o para o trabalho.  

    O script *New-OnPremiseHybridWorker.ps1* requer os seguintes parâmetros durante a execução:

  * *AutomationAccountName* (obrigatório) ‑ o nome de sua conta de Automação.  
  * *ResourceGroupName* (obrigatório) ‑ o nome do grupo de recursos associado à conta de Automação.  
  * *HybridGroupName* (obrigatório) - o nome de um grupo de do Hybrid Runbook Worker que você especificará como um destino para os runbooks com suporte neste cenário. 
  *  *SubscriptionID* (obrigatório) - a Id da Assinatura do Azure que contém sua conta de automação.
  *  *WorkspaceName* (opcional) ‑ o nome de espaço de trabalho do OMS.  Se você não tiver um espaço de trabalho do OMS, o script criará e configurará um.  

     > [!NOTE]
     > No momento, as únicas regiões de Automação com suporte para integração com o OMS são: **Sudeste da Austrália**, **Leste dos EUA 2**, **Sudeste Asiático** e **Europa Ocidental**.  Se sua conta de Automação não estiver em uma dessas regiões, o script criará o espaço de trabalho do OMS, mas ele avisará que não é possível vinculá-los.
     > 
2. Em seu computador, abra o **Windows PowerShell** na tela **Inicial** no modo de Administrador.  
3. No shell de linha de comando do PowerShell, navegue até a pasta que contém o script baixado e execute-o alterando os valores dos parâmetros *-AutomationAccountName*, *-ResourceGroupName*, *-HybridGroupName*, *-SubscriptionId* e *-WorkspaceName*.

     > [!NOTE] 
     > Você deverá se autenticar com o Azure depois de executar o script.  Você **deve** entrar com uma conta que seja membro da função Administradores de Assinatura e coadministrador da assinatura.  
     >  
    
        .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> `
        -ResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
        -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfOMSWorkspace>

4. Será solicitado que você concorde em instalar o **NuGet** e se autentique as credenciais do Azure.<br><br> ![Execução do script New-OnPremiseHybridWorker](media/automation-hybrid-runbook-worker/new-onpremisehybridworker-scriptoutput.png)

5. Depois que o script for concluído, a folha dos Grupos do Hybrid Worker mostrará o novo grupo e o número de membros ou, se for um grupo existente, o número de membros será aumentado.  Você pode selecionar o grupo da lista na folha **Grupos do Hybrid Worker** e no bloco **Hybrid Workers**.  Na folha **Hybrid Workers**, você verá cada membro do grupo listado.  

### <a name="manual-deployment"></a>Implantação manual 
Execute as duas primeiras etapas uma vez para seu ambiente de Automação e depois repita as etapas restantes para cada computador de trabalho.

#### <a name="1-create-operations-management-suite-workspace"></a>1. Criar o espaço de trabalho do Operations Management Suite
Se você ainda não tiver um espaço de trabalho do Operations Management Suite, crie um usando as instruções em [Gerenciar seu espaço de trabalho](../log-analytics/log-analytics-manage-access.md). Você pode usar um espaço de trabalho existente se já tiver um.

#### <a name="2-add-automation-solution-to-operations-management-suite-workspace"></a>2. Adicionar solução de Automação ao espaço de trabalho do Operations Management Suite
As soluções adicionam funcionalidade ao Operations Management Suite.  A solução de Automação adiciona a funcionalidade da Automação do Azure, incluindo o suporte ao Hybrid Runbook Worker.  Quando você adicionar a solução ao espaço de trabalho, ele enviará automaticamente os componentes de trabalho ao computador do agente que você instalará na próxima etapa.

Siga as instruções em [Para adicionar uma solução usando a Galeria de Soluções](../log-analytics/log-analytics-add-solutions.md) para adicionar a solução **Automação** ao espaço de trabalho do Operations Management Suite.

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Instalar o Microsoft Monitoring Agent
O Microsoft Monitoring Agent conecta os computadores ao Operations Management Suite.  Quando você instala o agente no computador local e o conecta ao espaço de trabalho, ele baixa automaticamente os componentes necessários para o Hybrid Runbook Worker.

Siga as instruções em [Conectar computadores com Windows ao Log Analytics](../log-analytics/log-analytics-windows-agents.md) para instalar o agente no computador local.  Você poderá repetir esse processo em vários computadores para adicionar vários trabalhos ao ambiente.

Quando o agente tiver se conectado com êxito ao Operations Management Suite, ele será listado na guia **Fontes Conectadas** do painel **Configurações** do Operations Management Suite.  Você pode verificar se o agente baixou corretamente a solução de Automação quando ele tiver uma pasta chamada **AzureAutomationFiles** em C:\Arquivos de Programas\Microsoft Monitoring Agent\Agent.  Para confirmar a versão do Hybrid Runbook Worker, você pode navegar até C:\Arquivos de Programa\Microsoft Monitoring Agent\Agent\AzureAutomation\ e anotar a subpasta da \\*versão*.   

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Instalar o ambiente de runbook e conectá-lo à Automação do Azure
Quando você adiciona um agente ao Operations Management Suite, a solução de Automação envia por push o módulo **HybridRegistration** do PowerShell que contém o cmdlet **Add-HybridRunbookWorker**.  Você pode usar esse cmdlet para instalar o ambiente de runbook no computador e registrá-lo na Automação do Azure.

Abra uma sessão do PowerShell no modo de Administrador e execute os comandos a seguir para importar o módulo.

    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module HybridRegistration.psd1

Em seguida, execute o cmdlet **Add-HybridRunbookWorker** usando a seguinte sintaxe:

    Add-HybridRunbookWorker –Name <String> -EndPoint <Url> -Token <String>

Você pode obter as informações necessárias para esse cmdlet na folha **Gerenciar Chaves** no portal do Azure.  Abra esta folha selecionando a opção **Chaves** na folha **Configurações** na sua conta de automação.

![Visão geral do Runbook Worker Híbrido](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **Name** é o nome do Grupo de Runbook Worker Híbrido. Quando esse grupo já existe na conta de automação, o computador atual é adicionado a ele.  Se ele não existir, será adicionado.
* **EndPoint** é o campo **URL** na folha **Gerenciar Chaves**.
* **Token** é a **Chave de Acesso Primária** na folha **Gerenciar Chaves**.  

Use a opção **-Verbose** com **Add-HybridRunbookWorker** para receber informações detalhadas sobre a instalação.

#### <a name="5-install-powershell-modules"></a>5. Instalar módulos do PowerShell
Os Runbooks podem usar qualquer uma das atividades e cmdlets definidos nos módulos instalados em seu ambiente de Automação do Azure.  No entanto, esses módulos não são implantados automaticamente em computadores locais, portanto, você deve instalá-los manualmente.  A exceção é o módulo do Azure que é instalado por padrão, fornecendo acesso a cmdlets a todos os serviços e atividades do Azure da Automação do Azure.

Como a principal finalidade do recurso Runbook Worker Híbrido é gerenciar recursos locais, você provavelmente precisará instalar os módulos que dão suporte a esses recursos.  Veja [Instalar Módulos](http://msdn.microsoft.com/library/dd878350.aspx) para obter informações sobre como instalar os módulos do Windows PowerShell.  Os módulos instalados devem estar em um local referenciado pela variável de ambiente PSModulePath, para que eles sejam importados automaticamente pelo Hybrid Worker.  Para saber mais, veja [Modificando o caminho de instalação do PSModulePath](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx). 

## <a name="removing-hybrid-runbook-worker"></a>Removendo o Hybrid Runbook Worker 
Você pode remover um ou mais Trabalhadores de Runbook Híbridos de um grupo ou remover o grupo, dependendo dos seus requisitos.  Para remover um Hybrid Runbook Worker de um computador local, execute as etapas a seguir.

1. No portal do Azure, abra sua Conta de Automação.  
2. Na folha **Configurações**, selecione **Teclas** e anote os valores para o campo **URL** e **Tecla de Acesso Primária**.  Você precisará dessas informações para a próxima etapa.
3. Abra uma sessão do PowerShell no modo de Administrador e execute o comando a seguir ‑ `Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>`.  Use a opção **-Verbose** para obter um log detalhado do processo de remoção.

> [!NOTE]
> Isso não remove o Microsoft Monitoring Agent do computador, apenas a funcionalidade e a configuração da função do Hybrid Runbook Worker.  

## <a name="remove-hybrid-worker-groups"></a>Remover grupos do Hybrid Worker
Para remover um grupo, primeiro você precisa remover o Hybrid Runbook Worker de cada computador membro do grupo usando o procedimento mostrado anteriormente e, em seguida, executar as seguintes etapas para remover o grupo.  

1. Abra a conta de Automação no Portal do Azure.
2. Selecione o bloco **Grupos Hybrid Worker** e, na folha **Grupos Hybrid Worker**, selecione o grupo que deseja excluir.  Depois de selecionar o grupo específico, a folha de propriedades do **grupo Hybrid Worker** é exibida.<br> ![Folha do grupo Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)   
3. Na folha de propriedades do grupo selecionado, clique em **Excluir**.  Uma mensagem será exibida solicitando que você confirme a ação. Selecione **Sim** se tiver certeza de que deseja continuar.<br> ![Caixa de diálogo de confirmação de exclusão de grupo](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)<br> Esse processo pode levar vários segundos e você pode acompanhar o progresso no menu **Notificações**.  

## <a name="starting-runbooks-on-hybrid-runbook-worker"></a>Iniciar runbooks no Runbook Worker Híbrido
[Como iniciar um Runbook na Automação do Azure](automation-starting-a-runbook.md) descreve métodos diferentes para se iniciar um runbook.  O Runbook Worker Híbrido adiciona uma opção **RunOn** em que você pode especificar o nome de um Grupo de Runbook Worker Híbrido.  Se um grupo for especificado, o runbook é recuperado e executado pelos trabalhadores nesse grupo.  Se essa opção não for especificada, ele é executado na Automação do Azure como de costume.

Ao iniciar um runbook no portal do Azure, a opção **Executar em** ficará disponível e você poderá selecionar **Azure** ou **Hybrid Worker**.  Se você selecionar **Worker Híbrido**, pode selecionar o grupo de uma lista suspensa.

Use o parâmetro **RunOn**.  Você pode usar o comando a seguir para iniciar um runbook denominado Runbook de Teste em um Grupo Hybrid Runbook Worker chamado MyHybridGroup usando o Windows PowerShell.

    Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"

> [!NOTE]
> O parâmetro **RunOn** foi adicionado ao cmdlet **Start-AzureAutomationRunbook** na versão 0.9.1 do Microsoft Azure PowerShell.  Você deve [baixar a versão mais recente](https://azure.microsoft.com/downloads/) se tiver uma anterior instalada.  Você só precisa instalar essa versão em uma estação de trabalho em que vá iniciar o runbook do Windows PowerShell.  Você não precisa instalá-lo no computador de trabalho, a menos que pretenda iniciar runbooks desse computador.  Atualmente, você não pode iniciar um runbook em um Runbook Worker Híbrido de outro runbook, pois isso exigiria que a versão mais recente do Powershell do Azure estivesse instalada em sua Conta de automação.  A versão mais recente será automaticamente atualizada na Automação do Azure e enviada aos trabalhadores em breve.
>
>

## <a name="runbook-permissions"></a>Permissões de runbook
Os runbooks em execução em um Hybrid Runbook Worker não podem usar o mesmo método que é normalmente usado para autenticação de runbooks nos recursos do Azure, já que eles acessarão recursos fora do Azure.  O runbook pode fornecer sua própria autenticação aos recursos locais, ou você pode especificar uma conta RunAs para fornecer um contexto de usuário a todos os runbooks.

### <a name="runbook-authentication"></a>Autenticação de runbook
Por padrão, os runbooks serão executados no contexto da conta de Sistema local no computador local; portanto, eles devem fornecer sua própria autenticação aos recursos que acessarão.  

Você pode usar ativos de [Credencial](http://msdn.microsoft.com/library/dn940015.aspx) e [Certificado](http://msdn.microsoft.com/library/dn940013.aspx) no runbook com cmdlets que permitem a especificação das credenciais para autenticar com recursos diferentes.  O exemplo a seguir mostra uma parte de um runbook que reinicia um computador.  Ele recupera as credenciais de um ativo de credencial e o nome do computador de um ativo variável, para então usar esses valores com o cmdlet Restart-Computer.

    $Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -Name "MyCredential"
    $Computer = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" -Name  "ComputerName"

    Restart-Computer -ComputerName $Computer -Credential $Cred

Você também pode aproveitar o [InlineScript](automation-powershell-workflow.md#inlinescript), que permitirá a execução de blocos de código em outro computador com as credenciais especificadas pelo [parâmetro comum PSCredential](http://technet.microsoft.com/library/jj129719.aspx).

### <a name="runas-account"></a>Conta RunAs
Em vez de os runbooks fornecerem sua própria autenticação aos recursos locais, você pode especificar uma conta **RunAs** para um grupo Hybrid Worker.  Você especifica um [ativo de credencial](automation-credentials.md) que tenha acesso aos recursos locais e todos os runbooks serão executados sob essas credenciais ao serem executados em um Hybrid Runbook Worker no grupo.  

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
Como parte do processo de compilação automatizado para a implantação de recursos no Azure, você talvez precise interrogar os sistemas no local para dar suporte a uma tarefa ou um conjunto de etapas na sequência de implantação.  Para dar suporte à autenticação no Azure usando a conta Executar como, você precisa instalar o certificado da conta Executar como.  

O runbook do PowerShell a seguir, *Export-RunAsCertificateToHybridWorker*, exporta o certificado de Executar como de sua conta de Automação do Azure e baixa e importa para o repositório de certificados do computador local em um Hybrid Worker conectado à mesma conta.  Quando essa etapa for concluída, ele verificará que o Worker pode ser autenticado com sucesso no Azure usando a conta Executar como.

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

    [OutputType([string])] 

    # Set the password used for this certificate
    $Password = "YourStrongPasswordForTheCert"

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
    
    Add-AzureRmAccount `
      -ServicePrincipal `
      -TenantId $RunAsConnection.TenantId `
      -ApplicationId $RunAsConnection.ApplicationId `
      -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

    Set-AzureRmContext -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

    # List automation accounts to confirm Azure Resource Manager calls are working
    Get-AzureRmAutomationAccount | Select AutomationAccountName

Salve o runbook *Export-RunAsCertificateToHybridWorker* no seu computador com uma extensão `.ps1`.  Importe-o para sua conta de Automação e edite o runbook, alterando o valor da variável `$Password` pela sua própria senha.  Publique e, em seguida, execute o runbook direcionando o grupo Hybrid Worker que executa e autentica runbooks usando a conta Executar como.  O fluxo de trabalho informa a tentativa de importar o certificado para o armazenamento do computador local e vem com várias linhas, dependendo de quantas contas de Automação são definidas em sua assinatura e se a autenticação tiver sido bem-sucedida.  

## <a name="creating-runbooks-for-hybrid-runbook-worker"></a>Criando runbooks para Runbook Worker Híbrido
Não há nenhuma diferença na estrutura de runbooks executados na Automação do Azure e daqueles que executam em um Runbook Worker Híbrido. Os runbooks que você usar com cada um provavelmente serão bem diferentes uns dos outros, já que os runbooks para um Runbook Worker Híbrido normalmente gerenciam recursos locais em seu data center, enquanto runbooks na Automação do Azure normalmente gerenciam recursos na nuvem do Azure.

Você pode editar um runbook para Runbook Worker Híbrido na Automação do Azure, mas pode ter problemas se tentar testá-lo no editor.  Os módulos do PowerShell que acessam os recursos locais podem não estar instalados em seu ambiente de Automação do Azure. Nesse caso, o teste falhará.  Se você instalar os módulos necessários, o runbook será executado, mas não será capaz de acessar recursos locais para um teste completo.

## <a name="troubleshooting-runbooks-on-hybrid-runbook-worker"></a>Solucionando problemas de runbooks no Hybrid Runbook Worker
[A saída e as mensagens do runbook](automation-runbook-output-and-messages.md) são enviadas à Automação do Azure do Hybrid Workers assim como os trabalhos do runbook são executados na nuvem.  Também é possível habilitar os fluxos Verbose e Progress da mesma forma que você faria para outros runbooks.  

Os logs são armazenados localmente em cada hybrid worker, em C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes.

Se seus runbooks não forem concluídos com êxito e o resumo do trabalho mostrar um status **Suspenso**, leia o artigo de solução de problemas [Hybrid Runbook Worker: um trabalho de runbook termina com o status Suspenso](automation-troubleshooting-hrw-runbook-terminates-suspended.md).   

## <a name="relationship-to-service-management-automation"></a>Relação com o Service Management Automation
O [SMA (Service Management Automation)](https://technet.microsoft.com/library/dn469260.aspx) permite que você execute os mesmos runbooks com suporte da Automação do Azure em seu data center local. O SMA geralmente é implantado junto com o Pacote do Microsoft Azure, pois este contém uma interface gráfica para gerenciamento de SMA. Ao contrário da Automação do Azure, o SMA requer uma instalação local que inclui servidores Web para hospedar a API, um banco de dados para conter runbooks e a configuração do SMA e Trabalhadores de Runbook para executar trabalhos de runbook. A Automação do Azure fornece esses serviços na nuvem e requer apenas que você mantenha os Runbook Workers Híbridos no seu ambiente local.

Se você for um usuário existente do SMA, pode mover seus runbooks para a Automação do Azure para serem usados com o Runbook Worker Híbrido sem alterações, supondo que eles executem sua própria autenticação aos recursos, como descrito em [Criar runbooks para Runbook Worker Híbrido](#creating-runbooks-for-hybrid-runbook-worker).  Os runbooks em SMA são executados no contexto da conta de serviço no servidor trabalhador que pode fornecer a autenticação para os runbooks.

Você pode usar os critérios a seguir para determinar se a Automação do Azure com Runbook Worker Híbrido ou o Service Management Automation é mais adequado às suas necessidades.

* O SMA requer uma instalação local de seus componentes subjacentes que estão conectados ao Pacote do Microsoft Azure se uma interface de gerenciamento gráfico for necessária. Haverá necessidade de mais recursos locais, com custos mais altos de manutenção do que a Automação do Azure, que só precisa de um agente instalado nos trabalhos de runbook locais. Os agentes são gerenciados pelo Operations Management Suite, que reduz ainda mais seus custos de manutenção.
* A Automação do Azure armazena seus runbooks na nuvem e os entrega a Hybrid Runbooks Workers locais. Se a sua política de segurança não permite esse comportamento, você deve usar o SMA.
* O SMA está incluído no System Center; logo, ele exige uma licença do System Center 2012 R2. A Automação do Azure se baseia em um modelo de assinatura em camadas.
* A Automação do Azure tem recursos avançados, tais como runbooks gráficos, que não estão disponíveis no SMA.

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre os diferentes métodos que podem ser usados para iniciar um runbook, confira [Como iniciar um Runbook na Automação do Azure](automation-starting-a-runbook.md).  
* Para entender os diferentes procedimentos para trabalhar com runbooks do PowerShell e de Fluxo de Trabalho do PowerShell na Automação do Azure usando o editor de texto, confira [Editar um Runbook na Automação do Azure](automation-edit-textual-runbook.md)

