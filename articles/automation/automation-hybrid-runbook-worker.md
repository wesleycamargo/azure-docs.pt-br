---
title: "Hybrid Runbook Workers da Automação do Azure | Microsoft Docs"
description: "Este artigo fornece informações sobre como instalar e usar Hybrid Runbook Worker, que é um recurso da Automação do Azure que permite que você execute runbooks em máquinas no seu datacenter local ou provedor de nuvem."
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
ms.date: 08/05/2017
ms.author: magoedte;bwren
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 783fb22b0154915f2e3d8574ab95538dbd646705
ms.contentlocale: pt-br
ms.lasthandoff: 08/09/2017

---

# <a name="automate-resources-in-your-data-center-or-cloud-with-hybrid-runbook-worker"></a>Automatizar recursos em seu data center ou nuvem com Hybrid Runbook Worker
Os runbooks na Automação do Azure não podem acessar recursos em outras nuvens ou no seu ambiente local, já que eles são executados na nuvem do Azure.  O recurso Hybrid Runbook Worker da Automação do Azure permite executar runbooks diretamente no computador que hospeda a função e em recursos no ambiente para gerenciar esses recursos locais. Os runbooks são armazenados e gerenciados na Automação do Azure e entregues a um ou mais computadores designados.  

Essa funcionalidade está ilustrada na imagem a seguir:<br>  

![Visão geral do Runbook Worker Híbrido](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Para obter uma visão geral técnica das considerações sobre implantação e função do Hybrid Runbook Worker, consulte [Visão geral da arquitetura automação](automation-offering-get-started.md#automation-architecture-overview).    

## <a name="hybrid-runbook-worker-groups"></a>Grupos de Runbook Worker Híbrido
Cada Runbook Worker Híbrido é membro de um grupo de Runbook Worker Híbrido que você especifica ao instalar o agente.  Um grupo pode conter um único agente, mas você pode instalar vários agentes em um grupo para ter alta disponibilidade.

Quando você inicia um runbook em um Runbook Worker Híbrido, deve especificar o grupo no qual ele será executado.  Os membros do grupo determinarão qual trabalhador atenderá a solicitação.  Você não pode especificar um trabalhador específico.

## <a name="relationship-to-service-management-automation"></a>Relação com o Service Management Automation
O [SMA (Service Management Automation)](https://technet.microsoft.com/library/dn469260.aspx) permite que você execute os mesmos runbooks com suporte da Automação do Azure em seu data center local. O SMA geralmente é implantado junto com o Pacote do Microsoft Azure, pois este contém uma interface gráfica para gerenciamento de SMA. Ao contrário da Automação do Azure, o SMA requer uma instalação local que inclui servidores Web para hospedar a API, um banco de dados para conter runbooks e a configuração do SMA e Trabalhadores de Runbook para executar trabalhos de runbook. A Automação do Azure fornece esses serviços na nuvem e requer apenas que você mantenha os Runbook Workers Híbridos no seu ambiente local.

Se você for um usuário existente do SMA, pode mover seus runbooks para a Automação do Azure para serem usados com o Runbook Worker Híbrido sem alterações, supondo que eles executem sua própria autenticação aos recursos, como descrito em [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md).  Os runbooks em SMA são executados no contexto da conta de serviço no servidor trabalhador que pode fornecer a autenticação para os runbooks.

Você pode usar os critérios a seguir para determinar se a Automação do Azure com Runbook Worker Híbrido ou o Service Management Automation é mais adequado às suas necessidades.

* O SMA requer uma instalação local de seus componentes subjacentes que estão conectados ao Pacote do Microsoft Azure se uma interface de gerenciamento gráfico for necessária. Haverá necessidade de mais recursos locais, com custos mais altos de manutenção do que a Automação do Azure, que só precisa de um agente instalado nos trabalhos de runbook locais. Os agentes são gerenciados pelo Operations Management Suite, que reduz ainda mais seus custos de manutenção.
* A Automação do Azure armazena seus runbooks na nuvem e os entrega a Hybrid Runbooks Workers locais. Se a sua política de segurança não permite esse comportamento, você deve usar o SMA.
* O SMA está incluído no System Center; logo, ele exige uma licença do System Center 2012 R2. A Automação do Azure se baseia em um modelo de assinatura em camadas.
* A Automação do Azure tem recursos avançados, tais como runbooks gráficos, que não estão disponíveis no SMA.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Instalação do Hybrid Runbook Worker do Windows 

Para instalar e configurar um Hybrid Runbook Worker do Windows, há dois métodos disponíveis.  O método recomendado é usar um runbook de Automação para automatizar completamente o processo necessário para configurar um computador com Windows.  O segundo método é seguir um procedimento passo a passo para instalar e configurar a função manualmente.  

> [!NOTE]
> Para gerenciar a configuração de seus servidores que dão suporte à função Hybrid Runbook Worker com o DSC (Configuração de Estado Desejado), você precisará adicioná-los como nós DSC.  Para saber mais sobre a integração deles para gerenciamento com DSC, confira [Máquinas de integração para o gerenciamento pelo DSC de Automação do Azure](automation-dsc-onboarding.md).           
><br>
>Se você habilitar a [solução de Gerenciamento de atualização](../operations-management-suite/oms-solution-update-management.md), qualquer computador com Windows conectado ao seu espaço de trabalho do OMS será automaticamente configurado como um Hybrid Runbook Worker para oferecer suporte a runbooks incluídos nessa solução.  No entanto, ele não estará registrado com nenhum grupo Hybrid Worker que você já tenha definido em sua Conta de automação.  O computador pode ser adicionado a um grupo Hybrid Runbook Worker na sua Conta de automação para dar suporte a runbooks de automação enquanto você estiver usando a mesma conta para a solução e para a associação de grupo do Hybrid Runbook Worker.  Essa funcionalidade foi adicionada à versão 7.2.12024.0 do Hybrid Runbook Worker.  

Examine as seguintes informações sobre [requisitos de hardware e software](automation-offering-get-started.md#hybrid-runbook-worker) e [informações para preparar sua rede](automation-offering-get-started.md#network-planning) antes de começar a implantar um Hybrid Runbook Worker.  Depois de você implantar com êxito um runbook worker, leia [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para aprender a configurar seus runbooks para automatizar processos em seu datacenter local ou em outro ambiente de nuvem.  
 
### <a name="automated-deployment"></a>Implantação automatizada

Execute as seguintes etapas para automatizar a instalação e configuração da função do Hybrid Worker do Windows.  

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

    Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>

Você pode obter as informações necessárias para esse cmdlet na folha **Gerenciar Chaves** no portal do Azure.  Abra esta folha selecionando a opção **Chaves** na folha **Configurações** na sua conta de automação.

![Visão geral do Runbook Worker Híbrido](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **GroupName** é o nome do grupo de Runbook Worker Híbrido. Quando esse grupo já existe na conta de automação, o computador atual é adicionado a ele.  Se ele não existir, será adicionado.
* **EndPoint** é o campo **URL** na folha **Gerenciar Chaves**.
* **Token** é a **Chave de Acesso Primária** na folha **Gerenciar Chaves**.  

Use a opção **-Verbose** com **Add-HybridRunbookWorker** para receber informações detalhadas sobre a instalação.

#### <a name="5-install-powershell-modules"></a>5. Instalar módulos do PowerShell
Os Runbooks podem usar qualquer uma das atividades e cmdlets definidos nos módulos instalados em seu ambiente de Automação do Azure.  No entanto, esses módulos não são implantados automaticamente em computadores locais, portanto, você deve instalá-los manualmente.  A exceção é o módulo do Azure que é instalado por padrão, fornecendo acesso a cmdlets a todos os serviços e atividades do Azure da Automação do Azure.

Como a principal finalidade do recurso Runbook Worker Híbrido é gerenciar recursos locais, você provavelmente precisará instalar os módulos que dão suporte a esses recursos.  Veja [Instalar Módulos](http://msdn.microsoft.com/library/dd878350.aspx) para obter informações sobre como instalar os módulos do Windows PowerShell.  Os módulos instalados devem estar em um local referenciado pela variável de ambiente PSModulePath, para que eles sejam importados automaticamente pelo Hybrid Worker.  Para saber mais, veja [Modificando o caminho de instalação do PSModulePath](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx). 

## <a name="installing-linux-hybrid-runbook-worker"></a>Instalação do Hybrid Runbook Worker do Linux
Para instalar e configurar um Hybrid Runbook Worker no Linux, o procedimento para instalar e configurar manualmente a função é muito simples.  Requer a habilitação da solução **Hybrid Worker de Automação** em seu espaço de trabalho do OMS e, em seguida, a execução de um conjunto de comandos para registrar o computador como um trabalhador e adicioná-lo a um grupo novo ou existente. 

1.  Habilite a solução "Hybrid Worker de Automação" no OMS. Isso pode ser feito das seguintes maneiras:

   1. Na Galeria de Soluções no [portal do OMS](https://mms.microsoft.com), habilite a solução **Hybrid Worker de Automação**
   2. Execute o cmdlet a seguir:

        ```$null = Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```
2.  Run the following command with the proper parameters (endpoint and key can be taken from the portal from the automation account linked to the workspace used in the steps above):
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <OMSworkspaceId> -k <automationsharedkey> --groupname <hybridgroupname> -e <automationendpoint>


## Removing Hybrid Runbook Worker 
You can remove one or more Hybrid Runbook Workers from a group or you can remove the group, depending on your requirements.  To remove a Hybrid Runbook Worker from an on-premises computer, perform the following steps.

1. In the Azure portal, navigate to your Automation account.  
2. From the **Settings** blade, select **Keys** and note the values for field **URL** and **Primary Access Key**.  You need this information for the next step.
3. Open a PowerShell session in Administrator mode and run the following command - `Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>`.  Use the **-Verbose** switch for a detailed log of the removal process.

> [!NOTE]
> This does not remove the Microsoft Monitoring Agent from the computer, only the functionality and configuration of the Hybrid Runbook Worker role.  

## Remove Hybrid Worker groups
To remove a group, you first need to remove the Hybrid Runbook Worker from every computer that is a member of the group using the procedure shown earlier, and then you perform the following steps to remove the group.  

1. Open the Automation account in the Azure portal.
2. Select the **Hybrid Worker Groups** tile and in the **Hybrid Worker Groups** blade, select the group you wish to delete.  After selecting the specific group, the **Hybrid worker group** properties blade is displayed.<br> ![Hybrid Runbook Worker Group Blade](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)   
3. On the properties blade for the selected group, click **Delete**.  A message appears asking you to confirm this action, select **Yes** if you are sure you want to proceed.<br> ![Delete Group Confirmation Dialog](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)<br> This process can take several seconds to complete and you can track its progress under **Notifications** from the menu.  

## Troubleshooting 
The Hybrid Runbook Worker depends on the Microsoft Monitoring Agent to communicate with your Automation account to register the worker, receive runbook jobs, and report status. If  registration of the worker fails, here are some possible causes for the error:  

1. The hybrid worker is behind a proxy or firewall.  
    Verify the computer has outbound access to *.azure-automation.net on port 443.  

2. The computer the hybrid worker is running on has less than the minimum hardware [requirements](automation-offering-get-started.md#hybrid-runbook-worker).  
    Computers running the Hybrid Runbook Worker should meet the minimum hardware requirements before designating it to host this feature. Otherwise, depending on the resource utilization of other background processes and contention caused by runbooks during execution, the computer will become over utilized and cause runbook job delays or timeouts.
    Confirm the computer designated to run the Hybrid Runbook Worker feature meets the minimum hardware requirements.  If it does, monitor CPU and memory utilization to determine any correlation between the performance of Hybrid Runbook Worker processes and Windows.  If there is memory or CPU pressure, this may indicate the need to upgrade or add additional processors, or increase memory to address the resource bottleneck and resolve the error. Alternatively, select a different compute resource that can support the minimum requirements and scale when workload demands indicate an increase is necessary.
    
3. The Microsoft Monitoring Agent service is not running.  
    If the Microsoft Monitoring Agent Windows service is not running, this prevents the Hybrid Runbook Worker from communicating with Azure Automation.  Verify the agent is running by entering the following command in PowerShell: `get-service healthservice`.  If the service is stopped, enter the following command in PowerShell to start the service: `start-service healthservice`.  

4. In the **Application and Services Logs\Operations Manager** event log, you see event 4502  and EventMessage containing **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** with the following description:  *The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication. The article KB3126513 has additional troubleshooting information for connectivity issues.*
    This can be caused by your proxy or network firewall blockking communication to Microsoft Azure.  Verify the computer has outbound access to *.azure-automation.net on ports 443.

Logs are stored locally on each hybrid worker at C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes.  You can check if there are any warning or error events written to the **Application and Services Logs\Microsoft-SMA\Operations** and **Application and Services Logs\Operations Manager** event log that would indicate a connectivity or other issue affecting onboarding of the role to Azure Automation or issue while performing normal operations.  

## Next steps
Review [run runbooks on a Hybrid Runbook Worker](automation-hrw-run-runbooks.md) to learn how to configure your runbooks to automate processes in your on-premises datacenter or other cloud environment.

