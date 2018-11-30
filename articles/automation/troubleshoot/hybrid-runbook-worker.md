---
title: Solução de problemas - Trabalhadores do Runbook Híbrido de Automação do Azure
description: Este artigo fornece informações sobre solução de problemas de trabalhadores do Runbook Híbrido de Automação do Azure
services: automation
ms.service: automation
ms.component: ''
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 38e2589365c2f1c88145fbf068d3ed267d4a4621
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52284551"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Solucionar problemas de trabalhadores de runbooks híbridos

Este artigo fornece informações sobre como solucionar problemas com os trabalhadores de runbook híbridos.

## <a name="general"></a>Geral

O operador de Runbook híbrido depende de um agente para se comunicar com sua conta de automação para registrar o trabalho, recebe trabalhos de runbook e status de relatórios. Para o Windows, esse agente é o Microsoft Monitoring Agent. Para Linux, é o Agente do OMS para Linux.

### <a name="runbook-execution-fails"></a>Cenário: a execução do Runbook falha

#### <a name="issue"></a>Problema

A execução do runbook falha e você recebe o seguinte erro:

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Seu runbook foi suspenso logo depois de tentar executá-lo três vezes. Há condições que podem interromper a execução com êxito do runbook e a mensagem de erro relacionada não incluir nenhuma informação adicional que indique o motivo.

#### <a name="cause"></a>Causa

A seguir estão possíveis causas possíveis:

* Os runbooks não podem autenticar com recursos locais

* O Hybrid Worker está usando um proxy ou firewall

* Os runbooks não podem autenticar com recursos locais

* O computador designado para executar o recurso Hybrid Runbook Worker atende aos requisitos mínimos de hardware.

#### <a name="resolution"></a>Resolução

Verifique se o computador tem acesso de saída para *.azure automation.net na porta 443.

Os computadores que executam o Hybrid Runbook Worker devem atender aos requisitos mínimos de hardware antes de serem designados para hospedar esse recurso. Caso contrário, dependendo da utilização de recursos de outros processos em segundo plano e da contenção provocada por runbooks durante a execução, o computador fica sobrecarregados e causará atrasos de trabalho de runbook ou tempos limite.

Confirme se o computador designado para executar o recurso Hybrid Runbook Worker atende aos requisitos mínimos de hardware. Se isso acontecer, monitore a utilização de CPU e memória para determinar qualquer correlação entre o desempenho de processos do Hybrid Runbook Worker e o Windows. Se não houver memória ou pressão da CPU, isso pode indicar a necessidade de atualizar ou adicionar mais processadores ou aumentar a memória para eliminar o gargalo de recursos e resolver o erro. Como alternativa, selecione um recurso de computação diferente que consiga dar suporte aos requisitos mínimos e ajuste a escala quando a demanda da carga de trabalho indicar que um aumento é necessário.

Verifique o log de eventos do **Microsoft SMA** para ver um evento correspondente com descrição *Processo Win32 Encerrado com o código [4294967295]*. A causa desse erro é que você ainda não configurou a autenticação em seus runbooks ou especificou as credenciais Executar como para o grupo do Hybrid Worker. Analise as [Permissões de runbook](../automation-hrw-run-runbooks.md#runbook-permissions) para confirmar que a autenticação dos runbooks está configurada corretamente.

## <a name="linux"></a>Linux

O Hybrid Runbook Worker do Linux depende do Agente do OMS para Linux para se comunicar com sua conta de Automação para registrar o worker, receber trabalhos de runbook e relatar status. Se o registro do trabalhador falhar, aqui estão algumas das possíveis causas do erro:

### <a name="oms-agent-not-running"></a>Cenário: O Agente do OMS para Linux não está em execução

Se o Agente do OMS para Linux não estiver em execução, isso impedirá o Hybrid Runbook Worker do Linux de se comunicar com a Automação do Azure. Verifique se o agente está em execução digitando o seguinte comando: `ps -ef | grep python`. Você deverá ver uma saída semelhante à seguinte, os processos de python com conta de usuário **nxautomation**. Se as soluções de Gerenciamento de Atualizações ou de Automação do Azure não estiverem ativadas, nenhum dos processos a seguir estará em execução.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

A lista a seguir mostra os processos que são iniciados para um Hybrid Runbook Worker do Linux. Eles estão localizados no diretório `/var/opt/microsoft/omsagent/state/automationworker/`.

* **oms.conf** - Este é o processo do gerenciador de trabalho, que é iniciado diretamente no DSC.

* **worker.conf** -Este processo é o processo do Hybrid Worker Registrado Automaticamente, ele é iniciado pelo gerenciador de trabalho. Esse processo é usado pelo Gerenciamento de Atualizações e é transparente para o usuário. Este processo não está presente se a solução de Gerenciamento de Atualizações não estiver ativada na máquina.

* **diy/worker.conf** - Este processo é o processo do Hybrid Worker DIY. O processo do Hybrid Worker DIY é usado para executar runbooks de usuário no Hybrid Runbook Worker. Ele é diferente apenas do processo do Hybrid Worker Registrado Automaticamente nos detalhes da chave, visto que usa uma configuração diferente. Esse processo não estará presente se a solução de automação do Azure não estiver habilitada e o DIY Linux Hybrid Worker não estiver registrado.

Se o Agente do OMS para Linux não estiver em execução, execute o seguinte comando para iniciar o serviço: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="class-does-not-exist"></a>Cenário: a classe especificada não existe

Se você vir o erro: **A classe especificada não existe..** no `/var/opt/microsoft/omsconfig/omsconfig.log`, o Agente do OMS para Linux precisa ser atualizado. Execute o comando a seguir para reinstalar o Agente do OMS:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a> Windows

O Windows Hybrid Runbook Worker depende do Microsoft Monitoring Agent para se comunicar com sua conta de automação para registrar o trabalhador, receber tarefas do runbook e relatar o status. Se o registro do trabalhador falhar, aqui estão algumas das possíveis causas do erro:

### <a name="mma-not-running"></a> Cenário: o Microsoft Monitoring Agent não está em execução

#### <a name="issue"></a>Problema

O serviço `healthservice` não está sendo executado na máquina Hybrid Runbook Worker.

#### <a name="cause"></a>Causa

Se o serviço do Windows Microsoft Monitoring Agent não estiver em execução, impedirá o Hybrid Runbook Worker de se comunicar com a Automação do Azure.

#### <a name="resolution"></a>Resolução

Verifique se o agente está em execução digitando o seguinte comando no PowerShell: `Get-Service healthservice`. Se o serviço for interrompido, digite o seguinte comando no PowerShell para iniciar o serviço: `Start-Service healthservice`.

### <a name="event-4502"></a> Evento 4502 no log do Operations Manager

#### <a name="issue"></a>Problema

No log de eventos **Logs de Aplicativos e Serviços\Operations Manager**, você verá evento 4502 e EventMessage contendo **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** com a seguinte descrição: *O certificado apresentado pelo serviço \<wsid\>.oms.opinsights.azure.com não foi emitido por uma autoridade de certificação usada para serviços da Microsoft. Entre em contato com seu administrador de rede para ver se há um proxy em execução que intercepte a comunicação TLS/SSL. O artigo KB3126513 tem informações adicionais sobre solução de problemas de conectividade.*

#### <a name="cause"></a>Causa

Isso poderá ocorrer se seu proxy ou firewall de rede estiver bloqueando a comunicação com o Microsoft Azure. Verifique se o computador tem acesso de saída para *.azure-automation.net na porta 443.

#### <a name="resolution"></a>Resolução

Os logs são armazenados localmente em cada hybrid worker, em C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Você pode verificar se há algum aviso ou eventos de erro gravados nos logs de eventos **Logs de Aplicativos e Serviços\Microsoft-SMA\Operações** e **Logs de Aplicativos e Serviços\Operations Manager** que indiquem um problema de conectividade ou outro problema que esteja afetando a integração da função de Automação do Azure ou um problema ao executar operações normais.

[A saída e as mensagens do runbook](../automation-runbook-output-and-messages.md) são enviadas à Automação do Azure do Hybrid Workers assim como os trabalhos do runbook são executados na nuvem. Também é possível habilitar os fluxos Verbose e Progress da mesma forma que você faria para outros runbooks.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio de [Fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.
