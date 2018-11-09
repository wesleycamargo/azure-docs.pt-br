---
title: Entender os resultados da verificação de agente no Gerenciamento de Atualizações do Azure
description: Saiba como solucionar problemas do agente de Gerenciamento de Atualizações.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: automation
ms.component: update-management
manager: carmonm
ms.openlocfilehash: 20323afe79ad3de1e3dfccd4752c4f7e28d22266
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50095364"
---
# <a name="understand-the-agent-check-results-in-update-management"></a>Entender os resultados da verificação de agente no Gerenciamento de Atualizações

Pode haver vários motivos pelos quais seu computador Não Azure não está mostrando **Pronto** no Gerenciamento de Atualizações. No Gerenciamento de Atualizações, você pode verificar a integridade de um agente de Hybrid Worker para determinar o problema subjacente. Este artigo descreve como executar a solução de problemas do Portal do Azure e em cenários offline.

## <a name="start-the-troubleshooter"></a>Iniciar a solução de problemas

Clicando no link de **Solução de problemas** na coluna **Preparação do Agente de Atualização** no portal, você inicia a página **Solucionar problemas do Agente de Atualização**. Essa página mostra problemas do agente e um link para este artigo para ajudá-lo a solucionar os problemas.

![vm list page](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> As verificações exigem que a VM esteja em execução. Se a VM não estiver em execução, você verá um botão para **Iniciar a VM**.

Na página **Solucionar problemas do Agente de Atualização**, clique em **Executar Verificações** para iniciar a solução de problemas. A solução de problemas usa o [comando Executar](../../virtual-machines/windows/run-command.md) para executar um script no computador e verificar as dependências que o agente tem. Quando a solução de problemas é concluída, retorna o resultado das verificações.

![Página Solucionar problemas](../media/update-agent-issues/troubleshoot-page.png)

Após a conclusão, os resultados são retornados na janela. As [seções de verificação](#pre-requisistes-checks) fornecem informações sobre o que cada verificação está procurando.

![Página Atualizar verificações de agente](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Verificações de pré-requisitos

### <a name="operating-system"></a>Sistema operacional

A verificação do sistema operacional verifica se o Hybrid Runbook Worker está executando um dos seguintes sistemas operacionais:

|Sistema operacional  |Observações  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Suporta apenas avaliações de atualização.         |
|Windows Server 2008 R2 SP1 e posterior     |É necessário o .NET Framework 4.5.1 ou posterior. ([Download do .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> Windows PowerShell 4.0 ou posterior é necessário. ([Baixar WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))<br/> Windows PowerShell 5.1é recomendado para maior confiabilidade.  ([Baixar WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |
|CentOS 6 (x86/x64) e 7 (x64)      | Os agentes do Linux devem ter acesso a um repositório de atualização. A aplicação de patch baseada em classificação requer 'yum' para retornar dados de segurança que não têm o CentOS fora da caixa.         |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Os agentes do Linux devem ter acesso a um repositório de atualização.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Os agentes do Linux devem ter acesso a um repositório de atualização.        |
|Ubuntu 14.04 LTS, 16.04 LTS e 18.04 LTS (x86/x64)      |Os agentes do Linux devem ter acesso a um repositório de atualização.         |

### <a name="net-451"></a>.NET 4.5.1

A verificação do .NET framework verifica se o sistema tem no mínimo o [.NET Framework 4.5.1](https://www.microsoft.com/download/details.aspx?id=30653) presente.

### <a name="wmf-51"></a>WMF 5.1

A verificação de WMF verifica se o sistema tem a versão necessária do Windows Management Framework. O [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) é a menor versão com suporte. É recomendável que você instale o [Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616) para aumentar a confiabilidade do Hybrid Runbook Worker.

### <a name="tls-12"></a>TLS 1.2

Essa verificação determina se você está usando o TLS 1.2 para criptografar as comunicações. O TLS 1.0 não tem mais suporte na plataforma. É recomendável que os clientes usem o TLS 1.2 para se comunicar com o Gerenciamento de Atualizações.

## <a name="connectivity-checks"></a>Verificações de conectividade

### <a name="registration-endpoint"></a>Ponto de extremidade de registro

Essa verificação determina se o agente pode se comunicar corretamente com o serviço do agente.

As configurações de proxy e firewall devem permitir que o agente do Hybrid Runbook Worker se comunique com o ponto de extremidade de registro. Para obter uma lista de endereços e portas a serem abertas, confira [Planejamento de rede para Hybrid Workers](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="operations-endpoint"></a>Ponto de extremidade de operações

Essa verificação determina se o agente pode se comunicar corretamente com o Serviço de Dados de Tempo de Execução do Trabalho.

As configurações de proxy e firewall devem permitir que o agente do Hybrid Runbook Worker se comunique com o Serviço de Dados de Tempo de Execução do Trabalho. Para obter uma lista de endereços e portas a serem abertas, confira [Planejamento de rede para Hybrid Workers](../automation-hybrid-runbook-worker.md#network-planning)

## <a name="vm-service-health-checks"></a>Verificações de integridade do serviço de VM

### <a name="monitoring-agent-service-status"></a>Monitoramento de status de serviço do agente

Essa verificação determina se o Microsoft Monitoring Agent `HealthService` está em execução no computador.

Para saber mais sobre a solução de problemas do serviço, confira [O Microsoft Monitoring Agent não está em execução](hybrid-runbook-worker.md#mma-not-running).

Para reinstalar o Microsoft Monitoring Agent, confira [Instalar e configurar o Microsoft Monitoring Agent](/log-analytics/log-analytics-concept-hybrid.md#install-and-configure-agent)

### <a name="monitoring-agent-service-events"></a>Monitoramento de eventos de serviço de agente

Essa verificação determina se houve eventos de `4502` no log do Operations Manager no computador nas últimas 24 horas.

Para saber mais sobre esse evento, confira o [guia de solução de problemas](hybrid-runbook-worker.md#event-4502) para este evento.

## <a name="access-permissions-checks"></a>Verificações de permissões de acesso

### <a name="machinekeys-folder-access"></a>Acesso à pasta MachineKeys

A verificação de Acesso à Pasta Crypto determina se a Conta do Sistema Local tem acesso a `C:\ProgramData\Microsoft\Crypto\RSA`

## <a name="troubleshoot-offline"></a>Solução de problemas offline

Você pode usar a solução de problemas offline em um Hybrid Runbook Worker executando o script localmente. O script [Solucionar problemas de WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration) pode ser encontrado na Galeria do PowerShell. Um exemplo da saída desse script é mostrado no seguinte exemplo:

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.1.7601 (Windows Server 2008 R2 SP1) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .Net Framework 4.5+
RuleGroupName               : Prerequisite Checks
RuleDescription             : .NET Framework version 4.5 or higher is required
CheckResult                 : Passed
CheckResultMessage          : .NET Framework version 4.5+ is found
CheckResultMessageId        : DotNetFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : WindowsManagementFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : WMF 5.1
RuleGroupName               : Prerequisite Checks
RuleDescription             : Windows Management Framework version 4.0 or higher is required (version 5.1 or higher is preferable)
CheckResult                 : Passed
CheckResultMessage          : Detected Windows Management Framework version: 5.1.17763.1
CheckResultMessageId        : WindowsManagementFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {5.1.17763.1}

RuleId                      : AutomationAgentServiceConnectivityCheck1
RuleGroupId                 : connectivity
RuleName                    : Registration endpoint
RuleGroupName               : connectivity
RuleDescription             : 
CheckResult                 : Failed
CheckResultMessage          : Unable to find Workspace registration information in registry
CheckResultMessageId        : AutomationAgentServiceConnectivityCheck1.Failed.NoRegistrationFound
CheckResultMessageArguments : {}

RuleId                      : AutomationJobRuntimeDataServiceConnectivityCheck
RuleGroupId                 : connectivity
RuleName                    : Operations endpoint
RuleGroupName               : connectivity
RuleDescription             : Proxy and firewall configuration must allow Automation Hybrid Worker agent to communicate with eus2-jobruntimedata-prod-su1.azure-automation.net
CheckResult                 : Passed
CheckResultMessage          : TCP Test for eus2-jobruntimedata-prod-su1.azure-automation.net (port 443) succeeded
CheckResultMessageId        : AutomationJobRuntimeDataServiceConnectivityCheck.Passed
CheckResultMessageArguments : {eus2-jobruntimedata-prod-su1.azure-automation.net}

RuleId                      : MonitoringAgentServiceRunningCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service status
RuleGroupName               : VM Service Health Checks
RuleDescription             : HealthService must be running on the machine
CheckResult                 : Failed
CheckResultMessage          : Microsoft Monitoring Agent service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Microsoft Monitoring Agent, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Microsoft Monitoring Agent service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Microsoft Monitoring Agent, Operations Manager, 4502}

RuleId                      : CryptoRsaMachineKeysFolderAccessCheck
RuleGroupId                 : permissions
RuleName                    : Crypto RSA MachineKeys Folder Access
RuleGroupName               : Access Permission Checks
RuleDescription             : SYSTEM account must have WRITE and MODIFY access to 'C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys'
CheckResult                 : Passed
CheckResultMessage          : Have permissions to access C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys
CheckResultMessageId        : CryptoRsaMachineKeysFolderAccessCheck.Passed
CheckResultMessageArguments : {C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys}

RuleId                      : TlsVersionCheck
RuleGroupId                 : prerequisites
RuleName                    : TLS 1.2
RuleGroupName               : Prerequisite Checks
RuleDescription             : Client and Server connections must support TLS 1.2
CheckResult                 : Passed
CheckResultMessage          : TLS 1.2 is enabled by default on the Operating System.
CheckResultMessageId        : TlsVersionCheck.Passed.EnabledByDefault
CheckResultMessageArguments : {}
```

## <a name="next-steps"></a>Próximas etapas

Para solucionar outros problemas de Hybrid Runbook Workers, confira [Solução de problemas ‒ Hybrid Runbook Workers](hybrid-runbook-worker.md)
