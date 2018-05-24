---
title: Hybrid Runbook Worker do Linux de Automação do Azure
description: Este artigo fornece informações sobre como instalar um Hybrid Runbook Worker da Automação do Azure que permite executar runbooks em computadores Linux no seu datacenter local ou ambiente de nuvem.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: e95f5d585fa97a62b709e73b6ed6eacafe69a2b3
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34158715"
---
# <a name="how-to-deploy-a-linux-hybrid-runbook-worker"></a>Como implantar o Hybrid Runbook Worker do Linux

O recurso Hybrid Runbook Worker da Automação do Azure permite executar runbooks diretamente no computador que hospeda a função e em recursos no ambiente para gerenciar esses recursos locais. O Hybrid Runbook Worker do Linux executa runbooks como um usuário especial que pode ser elevado para executar comandos que precisam de elevação. Os runbooks são armazenados e gerenciados na Automação do Azure e entregues a um ou mais computadores designados. Este artigo descreve como instalar o Hybrid Runbook Worker em um computador Linux.

## <a name="supported-linux-operating-systems"></a>Sistemas operacionais Linux com suporte

A seguir há uma lista de distribuições do Linux com suporte:

* Amazon Linux 2012.09 --> 2015.09 (x86/x64)
* CentOS Linux 5, 6 e 7 (x86/x64)
* Oracle Linux 5, 6 e 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 e 7 (x86/x64)
* Debian GNU/Linux 6, 7 e 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS (x86/x64)
* SUSE Linux Enteprise Server 11 e 12 (x86/x64)

## <a name="installing-linux-hybrid-runbook-worker"></a>Instalação do Hybrid Runbook Worker do Linux

Para instalar e configurar um Hybrid Runbook Worker no seu computador Linux, você segue um processo simples para instalar e configurar manualmente a função. Requer a habilitação da solução **Hybrid Worker de Automação** em seu espaço de trabalho do Log Analytics e, em seguida, a execução de um conjunto de comandos para registrar o computador como um trabalhador e adicioná-lo a um grupo novo ou existente.

A seguir estão os requisitos mínimos para um Hybrid Runbook Worker do Linux:

* Um mínimo de dois núcleos
* Um mínimo de 4 GB de RAM
* Porta 443 (saída)

### <a name="package-requirements"></a>Requisitos do pacote

| **Pacote necessário** | **Descrição** | **Versão mínima**|
|--------------------- | --------------------- | -------------------|
|Glibc |Biblioteca GNU C| 2.5-12 |
|Openssl| Bibliotecas OpenSSL | 0.9.8e ou 1.0|
|Curl | cliente Web cURL | 7.15.5|
|Python-ctypes | |
|PAM | Módulos de autenticação conectáveis|

Antes de continuar, é necessário anotar o espaço de trabalho do Log Analytics ao qual sua conta de Automação está vinculada e também a chave primária para a conta de Automação. É possível encontrar os dois no portal selecionando sua conta de Automação do Azure, **Espaço de trabalho** para a ID do Espaço de Trabalho e **Chaves** para a chave primária. Para obter informações sobre portas e endereços necessários para o Hybrid Runbook Worker, consulte [Configurar sua rede](automation-hybrid-runbook-worker.md#network-planning).

1. Habilite a solução "Hybrid Worker de Automação" no Azure. Isso pode ser feito das seguintes maneiras:

   1. Adicione a solução **Hybrid Worker de Automação** à sua assinatura usando o procedimento em [Adicionar soluções de gerenciamento do Log Analytics ao espaço de trabalho](../log-analytics/log-analytics-add-solutions.md).
   1. Execute o cmdlet a seguir:

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Instalar o agente do OMS para Linux executando o comando a seguir, substituindo \<WorkspaceID\> e \<WorkspaceKey\> pelos valores apropriados do espaço de trabalho.

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Execute o seguinte comando, alterando os valores dos parâmetros *-w*, *-k*, *-g* e *-e*. Para o parâmetro *-g*, substitua o valor pelo nome do grupo do Hybrid Runbook Worker ao qual o novo Linux Hybrid Runbook Worker deve ingressar. Se o nome ainda não existir em sua conta de Automação do Azure, um novo grupo do Hybrid Runbook Worker é criado com esse nome.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. Depois que o comando for concluído, a página Grupos de Hybrid Worker no portal do Azure mostrará o novo grupo e o número de membros ou, se for um grupo existente, o número de membros será aumentado. Você pode selecionar o grupo da lista na página **Grupos do Hybrid Worker** e no bloco **Hybrid Workers**. Na página **Hybrid Workers**, você verá cada membro do grupo listado.

## <a name="turning-off-signature-validation"></a>Desativar a validação de assinatura

Por padrão, o Linux Hybrid Runbook Workers exige validação de assinatura. Se você executar um runbook sem sinal em relação a um trabalho, um erro contendo "Falha na validação de assinatura" é exibido. Para desligar a validação de assinatura, execute o seguinte comando, substituindo o segundo parâmetro por sua ID de espaço de trabalho do Log Analytics:

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>Tipos de runbook com suporte

O Hybrid Runbook Workers do Linux não dá suporte ao conjunto completo dos tipos de runbook que são localizados na automação do Azure.

Os seguintes tipos de runbooks funcionam em um Hybrid Worker do Linux:

* Python 2
* PowerShell

Os seguintes tipos de runbook não funcionam em um Hybrid Worker do Linux:

* Fluxo de Trabalho do PowerShell
* Gráfico
* Fluxo de Trabalho Gráfico do PowerShell

## <a name="troubleshooting"></a>solução de problemas

O Hybrid Runbook Worker do Linux depende do Agente do OMS para Linux para se comunicar com sua conta de Automação para registrar o worker, receber trabalhos de runbook e relatar status. Se o registro do worker falhar, aqui estão algumas das possíveis causas do erro:

### <a name="the-oms-agent-for-linux-is-not-running"></a>O Agente do OMS para Linux não está em execução

Se o Agente do OMS para Linux não estiver em execução, isso impedirá o Hybrid Runbook Worker do Linux de se comunicar com a Automação do Azure. Verifique se o agente está em execução digitando o seguinte comando: `ps -ef | grep python`. Você deverá ver uma saída semelhante à seguinte, os processos de python com conta de usuário **nxautomation**. Se as soluções de Gerenciamento de Atualizações ou Automação do Azure não estiverem habilitadas, nenhum dos seguintes processos será executado.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

A lista a seguir mostra os processos que são iniciados para um Hybrid Runbook Worker do Linux. Eles estão localizados no diretório `/var/opt/microsoft/omsagent/state/automationworker/`.

* **oms.conf** - Este é o processo do gerenciador de trabalho, que é iniciado diretamente no DSC.

* **worker.conf** -Este processo é o processo do Hybrid Worker Registrado Automaticamente, ele é iniciado pelo gerenciador de trabalho. Esse processo é usado pelo Gerenciamento de Atualizações e é transparente para o usuário. Esse processo não estará presente se a solução Gerenciamento de Atualização não estiver habilitada no computador.

* **diy/worker.conf** - Este processo é o processo do Hybrid Worker DIY. O processo do Hybrid Worker DIY é usado para executar runbooks de usuário no Hybrid Runbook Worker. Ele é diferente apenas do processo do Hybrid Worker Registrado Automaticamente nos detalhes da chave, visto que usa uma configuração diferente. Esse processo não estará presente se a solução Automação do Azure não estiver habilitada e o Hybrid Worker do Linux DIY não estiver registrado.

Se o Agente do OMS para Linux não estiver em execução, execute o seguinte comando para iniciar o serviço: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="the-specified-class-does-not-exist"></a>A classe especificada não existe

Se você vir o erro **A classe especificada não existe..** no `/var/opt/microsoft/omsconfig/omsconfig.log`, o Agente do OMS para Linux precisa ser atualizado. Execute o seguinte comando para reinstalar o Agente do OMS:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

Para obter etapas adicionais sobre como solucionar problemas com o Gerenciamento de Atualizações, consulte [Gerenciamento de Atualizações - solução de problemas](automation-update-management.md#troubleshooting)

## <a name="next-steps"></a>Próximas etapas

* Leia [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para aprender a configurar seus runbooks para automatizar processos em seu datacenter local ou em outro ambiente de nuvem.
* Para obter instruções sobre como remover os Hybrid Runbook Workers, consulte [Remover Hybrid Runbook Workers da Automação do Azure](automation-hybrid-runbook-worker.md#removing-hybrid-runbook-worker)
