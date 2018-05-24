---
title: Hybrid Runbook Worker do Windows de Automação do Azure
description: Este artigo fornece informações sobre como instalar um Hybrid Runbook Worker da Automação do Azure que permita executar runbooks em computadores Windows no seu datacenter local ou ambiente de nuvem.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 30eda7683a1e8c27eb117b92744bf90eae3fd5d9
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34193825"
---
# <a name="how-to-deploy-a-windows-hybrid-runbook-worker"></a>Como implantar um Hybrid Runbook Worker Windows

O recurso Hybrid Runbook Worker da Automação do Azure permite executar runbooks diretamente no computador que hospeda a função e em recursos no ambiente para gerenciar esses recursos locais. Os runbooks são armazenados e gerenciados na Automação do Azure e entregues a um ou mais computadores designados. Este artigo descreve como instalar o Hybrid Runbook Worker em um computador Windows.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Instalação do Hybrid Runbook Worker do Windows

Para instalar e configurar um Hybrid Runbook Worker do Windows, há dois métodos disponíveis. O método recomendado é usar um runbook de Automação para automatizar completamente o processo necessário para configurar um computador com Windows. O segundo método é seguir um procedimento passo a passo para instalar e configurar a função manualmente.

> [!NOTE]
> Para gerenciar a configuração de seus servidores que dão suporte à função Hybrid Runbook Worker com o DSC (Configuração de Estado Desejado), você precisará adicioná-los como nós DSC.

A seguir estão os requisitos mínimos para um Hybrid Runbook Worker do Windows.

* Windows Server 2012 ou posterior.
* O Windows PowerShell 4.0 ou superior é necessário ([faça o download do WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855)). Windows PowerShell 5.1 ([faça o download do WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)) é recomendado para aumentar a confiabilidade.
* .NET Framework 4.6.2 ou posterior.
* Um mínimo de dois núcleos.
* Um mínimo de 4 GB de RAM.
* Porta 443 (saída)

Para ver os requisitos de rede adicionais para o Hybrid Runbook Worker, consulte [Configurar sua rede](automation-hybrid-runbook-worker.md#network-planning).

Para saber mais sobre a integração deles para gerenciamento com DSC, confira [Máquinas de integração para o gerenciamento pelo DSC de Automação do Azure](automation-dsc-onboarding.md).
Se você habilitar a [solução de Gerenciamento de Atualizações](../operations-management-suite/oms-solution-update-management.md), qualquer computador com Windows conectado ao espaço de trabalho do Log Analytics será automaticamente configurado como um Hybrid Runbook Worker para dar suporte a runbooks incluídos nessa solução. No entanto, ele não estará registrado com nenhum grupo Hybrid Worker que você já tenha definido em sua Conta de automação. O computador pode ser adicionado a um grupo Hybrid Runbook Worker na sua Conta de automação para dar suporte a runbooks de automação enquanto você estiver usando a mesma conta para a solução e para a associação de grupo do Hybrid Runbook Worker. Essa funcionalidade foi adicionada à versão 7.2.12024.0 do Hybrid Runbook Worker.

Depois de você implantar com êxito um runbook worker, leia [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para aprender a configurar seus runbooks para automatizar processos em seu datacenter local ou em outro ambiente de nuvem.

### <a name="automated-deployment"></a>Implantação automatizada

Execute as seguintes etapas para automatizar a instalação e configuração da função do Hybrid Worker do Windows.

1. Baixe o script *New-OnPremiseHybridWorker.ps1* da [Galeria do PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/DisplayScript) diretamente do computador que executa a função Hybrid Runbook Worker ou de outro computador no seu ambiente e copie-o para o trabalho.

   O script *New-OnPremiseHybridWorker.ps1* requer os seguintes parâmetros durante a execução:

   * *AutomationAccountName* (obrigatório) ‑ O nome de sua conta de Automação.
   * *AAResourceGroupName* (obrigatório) ‑ O nome do grupo de recursos associado à conta de Automação
   * *OMSResourceGroupName* (opcional) - O nome do grupo de recursos para o espaço de trabalho do OMS. Se não for especificado, o AAResourceGroupName será usado.
   * *HybridGroupName* (obrigatório) - O nome de um grupo de do Hybrid Runbook Worker que você especificará como um destino para os runbooks com suporte neste cenário.
   * *SubscriptionID* (obrigatório) - A ID da Assinatura do Azure que contém sua conta de automação.
   * *WorkspaceName* (opcional) ‑ O nome de espaço de trabalho do Log Analytics. Se você não tiver um espaço de trabalho do Log Analytics, o script criará e configurará um.

     > [!NOTE]
     > No momento, as únicas regiões de Automação com suporte para integração com o Log Analytics são: **Sudeste da Austrália**, **Leste dos EUA 2**, **Sudeste Asiático** e **Europa Ocidental**. Se sua conta de Automação não estiver em uma dessas regiões, o script criará o espaço de trabalho do Log Analytics, mas ele avisará que não é possível vinculá-los.

1. Em seu computador, abra o **Windows PowerShell** na tela **Inicial** no modo de Administrador.
1. No shell de linha de comando do PowerShell, navegue até a pasta que contém o script baixado e execute-o alterando os valores dos parâmetros *-AutomationAccountName*, *-AAResourceGroupName*, *-OMSResourceGroupName*, *-HybridGroupName*, *-SubscriptionId* e *-WorkspaceName*.

     > [!NOTE]
     > Você deverá se autenticar com o Azure depois de executar o script. Você **deve** entrar com uma conta que seja membro da função Administradores de Assinatura e coadministrador da assinatura.

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

1. Será solicitado que você concorde em instalar o **NuGet** e se autentique as credenciais do Azure.

1. Depois que o script for concluído, a página dos Grupos do Hybrid Worker mostrará o novo grupo e o número de membros ou, se for um grupo existente, o número de membros será aumentado. Você pode selecionar o grupo da lista na página **Grupos do Hybrid Worker** e no bloco **Hybrid Workers**. Na página **Hybrid Workers**, você verá cada membro do grupo listado.

### <a name="manual-deployment"></a>Implantação manual

Execute as duas primeiras etapas uma vez para seu ambiente de Automação e depois repita as etapas restantes para cada computador de trabalho.

#### <a name="1-create-log-analytics-workspace"></a>1. Criar espaço de trabalho do Log Analytics

Se você ainda não tiver um espaço de trabalho do Log Analytics, crie um usando as instruções em [Gerenciar seu espaço de trabalho](../log-analytics/log-analytics-manage-access.md). Você pode usar um espaço de trabalho existente se já tiver um.

#### <a name="2-add-automation-solution-to-log-analytics-workspace"></a>2. Adicionar solução de Automação ao espaço de trabalho do Log Analytics

As soluções acrescentam funcionalidades ao Log Analytics. A solução de Automação adiciona a funcionalidade da Automação do Azure, incluindo o suporte ao Hybrid Runbook Worker. Quando você adicionar a solução ao espaço de trabalho, ele enviará automaticamente os componentes de trabalho ao computador do agente que você instalará na próxima etapa.

Siga as instruções em [Para adicionar uma solução usando a Galeria de Soluções ](../log-analytics/log-analytics-add-solutions.md) para adicionar a solução **Automação** ao espaço de trabalho do Log Analytics.

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Instalar o Microsoft Monitoring Agent

O Microsoft Monitoring Agent conecta computadores ao Log Analytics. Quando você instala o agente no computador local e o conecta ao espaço de trabalho, ele baixa automaticamente os componentes necessários para o Hybrid Runbook Worker.

Siga as instruções em [Conectar computadores com Windows ao Log Analytics](../log-analytics/log-analytics-windows-agent.md) para instalar o agente no computador local. Você poderá repetir esse processo em vários computadores para adicionar vários trabalhos ao ambiente.

Quando o agente for conectado com êxito ao Log Analytics, ele será listado na guia **Fontes Conectadas** da página **Configurações** do Log Analytics. Você pode verificar se o agente baixou corretamente a solução de Automação quando ele tiver uma pasta chamada **AzureAutomationFiles** em C:\Arquivos de Programas\Microsoft Monitoring Agent\Agent. Para confirmar a versão do Hybrid Runbook Worker, você pode navegar até C:\Arquivos de Programa\Microsoft Monitoring Agent\Agent\AzureAutomation\ e anotar a subpasta da \\*versão*.

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Instalar o ambiente de runbook e conectá-lo à Automação do Azure

Ao adicionar um agente ao Log Analytics, a solução de Automação efetua push do módulo **HybridRegistration** do PowerShell, que contém o cmdlet **Add-HybridRunbookWorker**. Você pode usar esse cmdlet para instalar o ambiente de runbook no computador e registrá-lo na Automação do Azure.

Abra uma sessão do PowerShell no modo de Administrador e execute os comandos a seguir para importar o módulo.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module HybridRegistration.psd1
```

Em seguida, execute o cmdlet **Add-HybridRunbookWorker** usando a seguinte sintaxe:

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Você pode obter as informações necessárias para esse cmdlet na página **Gerenciar Chaves** no portal do Azure. Abra esta página selecionando a opção **Chaves** na página **Configurações** na sua conta de automação.

![Visão geral do Runbook Worker Híbrido](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **GroupName** é o nome do grupo de Runbook Worker Híbrido. Quando esse grupo já existe na conta de automação, o computador atual é adicionado a ele. Se ele não existir, será adicionado.
* **EndPoint** é o campo **URL** na página **Gerenciar Chaves**.
* **Token** é a **Chave de Acesso Primária** na página **Gerenciar Chaves**.

Use a opção **-Verbose** com **Add-HybridRunbookWorker** para receber informações detalhadas sobre a instalação.

#### <a name="5-install-powershell-modules"></a>5. Instalar módulos do PowerShell

Os Runbooks podem usar qualquer uma das atividades e cmdlets definidos nos módulos instalados em seu ambiente de Automação do Azure. No entanto, esses módulos não são implantados automaticamente em computadores locais, portanto, você deve instalá-los manualmente. A exceção é o módulo do Azure que é instalado por padrão, fornecendo acesso a cmdlets a todos os serviços e atividades do Azure da Automação do Azure.

Como a principal finalidade do recurso Runbook Worker Híbrido é gerenciar recursos locais, você provavelmente precisará instalar os módulos que dão suporte a esses recursos. Veja [Instalar Módulos](http://msdn.microsoft.com/library/dd878350.aspx) para obter informações sobre como instalar os módulos do Windows PowerShell. Os módulos instalados devem estar em um local referenciado pela variável de ambiente PSModulePath, para que eles sejam importados automaticamente pelo Hybrid Worker. Para obter mais informações, consulte [Modificando o caminho de instalação do PSModulePath](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx).

## <a name="troubleshooting"></a>solução de problemas

O Hybrid Runbook Worker depende do Microsoft Monitoring Agent para se comunicar com sua conta de Automação para registrar o worker, receber trabalhos de runbook e relatar status. Se o registro do worker falhar, aqui estão algumas das possíveis causas do erro:

### <a name="the-microsoft-monitoring-agent-is-not-running"></a>O Microsoft Monitoring Agent não está em execução

Se o serviço do Windows Microsoft Monitoring Agent não estiver em execução, impedirá o Hybrid Runbook Worker de se comunicar com a Automação do Azure. Verifique se o agente está em execução digitando o seguinte comando no PowerShell: `Get-Service healthservice`. Se o serviço for interrompido, digite o seguinte comando no PowerShell para iniciar o serviço: `Start-Service healthservice`.

### <a name="event-4502-in-operations-manager-log"></a>Evento 4502 no log do Operations Manager

No log de eventos **Logs de Aplicativos e Serviços\Operations Manager**, você verá evento 4502 e EventMessage contendo **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** com a seguinte descrição: *O certificado apresentado pelo serviço \<wsid\>.oms.opinsights.azure.com não foi emitido por uma autoridade de certificação usada para serviços da Microsoft. Entre em contato com seu administrador de rede para ver se há um proxy em execução que intercepte a comunicação TLS/SSL. O artigo KB3126513 tem informações adicionais sobre solução de problemas de conectividade.*

Isso poderá ocorrer se seu proxy ou firewall de rede estiver bloqueando a comunicação com o Microsoft Azure. Verifique se o computador tem acesso de saída para *.azure-automation.net na porta 443.

Os logs são armazenados localmente em cada hybrid worker, em C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Você pode verificar se há algum aviso ou eventos de erro gravados nos logs de eventos **Logs de Aplicativos e Serviços\Microsoft-SMA\Operações** e **Logs de Aplicativos e Serviços\Operations Manager** que indiquem um problema de conectividade ou outro problema que esteja afetando a integração da função de Automação do Azure ou um problema ao executar operações normais.

Para obter etapas adicionais sobre como solucionar problemas com o Gerenciamento de Atualizações, consulte [Gerenciamento de Atualizações - solução de problemas](automation-update-management.md#troubleshooting)

## <a name="next-steps"></a>Próximas etapas

* Leia [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para aprender a configurar seus runbooks para automatizar processos em seu datacenter local ou em outro ambiente de nuvem.
* Para obter instruções sobre como remover os Hybrid Runbook Workers, consulte [Remover Hybrid Runbook Workers da Automação do Azure](automation-hybrid-runbook-worker.md#removing-hybrid-runbook-worker)