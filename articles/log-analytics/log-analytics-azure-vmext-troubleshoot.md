---
title: "Solucionar problemas de extensão da VM do Azure Log Analytics | Microsoft Docs"
description: "Descreva os sintomas, as causas e a resolução dos problemas mais comuns com a extensão de VM do Log Analytics para VMs do Azure para Windows e Linux."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/08/2018
ms.author: magoedte
ms.openlocfilehash: 4e43c7a7cea903a2e94e60a519f6ead1e6f932e3
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2018
---
# <a name="troubleshooting-the-log-analytics-vm-extension"></a>Solução de problemas da extensão de VM de Log Analytics
Este artigo fornece ajuda para solucionar erros que você pode encontrar com a extensão de VM do Log Analytics para máquinas virtuais Windows e Linux em execução no Microsoft Azure e sugere soluções possíveis para resolvê-los.

Para verificar o status da extensão, execute as seguintes etapas no portal do Azure.

1. Faça logon no [Portal do Azure](http://portal.azure.com).
2. No portal do Azure, clique em **Mais serviços** encontrado no canto inferior esquerdo. Na lista de recursos, digite **máquinas virtuais**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Máquinas virtuais**.
3. Na lista de máquinas virtuais, localize e selecione-a.
3. Na máquina virtual, clique em **Extensões**.
4. Na lista, verifique se a extensão de Log Analytics está habilitada ou não.  Para o Linux, o agente é listado como **OMSAgentforLinux** e, para o Windows, o agente é listado como **MicrosoftMonitoringAgent**.

   ![Exibição da Extensão de VM](./media/log-analytics-azure-vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Clique na extensão para exibir detalhes. 

   ![Detalhes da Extensão da VM](./media/log-analytics-azure-vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Solucionando problemas da extensão da VM do Windows no Azure

Se a extensão da VM *Microsoft Monitoring Agent* não está instalando ou reportando, você pode executar as etapas a seguir para solucionar o problema.

1. Verifique se o agente de VM do Azure está instalado e funcionando corretamente usando as etapas em [KB 2965986](https://support.microsoft.com/kb/2965986#mt1).
   * Você também pode examinar o arquivo de log do agente de VM `C:\WindowsAzure\logs\WaAppAgent.log`
   * Se o log não existir, isso significará que o agente de VM não está instalado.
   * [Instalar o Agente de VM do Azure](log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Confirme que a tarefa de pulsação da extensão do Microsoft Monitoring Agent está em execução usando as seguintes etapas:
   * Fazer logon na máquina virtual
   * Abrir o agendador de tarefas e localizar a tarefa `update_azureoperationalinsight_agent_heartbeat`
   * Confirmar que a tarefa está habilitada e está executando a cada minuto
   * Verificar o arquivo de log de pulsação em `C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\heartbeat.log`
3. Examinar os arquivos de log de extensão de VM do Microsoft Monitoring Agent em `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
4. Certificar-se de que a máquina virtual pode executar scripts do PowerShell
5. Certificar-se de que as permissões em C:\Windows\temp não foram alteradas
6. Exibir o status do Microsoft Monitoring Agent, digitando o seguinte em uma janela do PowerShell com privilégios elevados na máquina virtual `  (New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
7. Examinar os arquivos de log de instalação do Microsoft Monitoring Agent em `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Para obter mais informações, consulte [Solucionando problemas em extensões do Windows](../virtual-machines/windows/extensions-oms.md).

## <a name="troubleshooting-linux-vm-extension"></a>Solucionando problemas da extensão da VM do Linux
Se a extensão da VM *Agente do OMS para Linux* não estiver instalando ou reportando, você pode executar as etapas a seguir para solucionar o problema.

1. Se o status da extensão for *Desconhecido*, verifique se o agente de VM do Azure está instalado e funcionando corretamente, examinando o arquivo de log do agente de VM `/var/log/waagent.log`
   * Se o log não existir, isso significará que o agente de VM não está instalado.
   * [Instalar o Agente de VM do Azure em VMs Linux](log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Para outros status não íntegros, examine o Agente do OMS para arquivos de log de extensão de VMs do Linux em `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` e `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Se o status da extensão estiver íntegro mas os dados não estiverem sendo carregados, examine o Agente do OMS para arquivos de log do Linux em `/var/opt/microsoft/omsagent/log/omsagent.log`

Para obter mais informações, consulte [Solucionando problemas em extensões do Linux](../virtual-machines/linux/extensions-oms.md).

## <a name="next-steps"></a>Próximas etapas

Para mais orientações sobre solução de problemas relacionados ao Agente do Operations Management Suite para Linux hospedado em computadores fora do Azure, consulte [Solucionar problemas do Agente do Linux do Azure Log Analytics](log-analytics-agent-linux-support.md).  
