---
title: "Adicionar métricas estendidas para máquinas virtuais do Azure | Microsoft Docs"
description: "Este artigo ajuda você a habilitar e configurar métricas de diagnóstico estendido para suas VMs do Azure."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/30/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: 91797aaab1dca96e78643f57776eb16d336e894b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>Adicionar métricas estendidas para máquinas virtuais do Azure

Gerenciamento de Custos usa dados de métrica do Azure de suas VMs do Azure para mostrar informações detalhadas sobre seus recursos. Dados da métrica, também chamados de contadores de desempenho, são usados pelo Gerenciamento de Custos para gerar relatórios. No entanto, o Gerenciamento de Custos não coleta automaticamente todos os dados de métrica do Azure de VMs convidadas — você deve habilitar a coleta de métricas. Este artigo ajuda você a habilitar e configurar métricas de diagnóstico adicionais para suas VMs do Azure.

Depois que você habilita a coleta de métricas, você pode:

- Saber quando suas VMs são atingir seus limites de CPU, memória e disco.
- Detectar problemas e tendências de uso.
- Controlar os custos de dimensionamento de acordo com o uso.
- Obter recomendações de otimização de dimensionamento custo-efetiva do Gerenciamento de Custos.

Por exemplo, convém monitorar o percentual de CPU e memória das suas VMs do Azure. As métricas de máquina virtual do Azure correspondem às _porcentagem de CPU [Host]_ e _porcentagem de memória [convidado]_.

## <a name="verify-that-metrics-are-enabled-on-vms"></a>Verifique se a métrica está habilitada em máquinas virtuais

1. Faça logon no Portal do Azure em http://portal.azure.com.
2. Em **Máquinas virtuais**, selecione uma máquina virtual e, em seguida, em **Monitoramento**, selecione **Métricas**. É mostrada uma lista das métricas disponíveis.
3. Selecione algumas métricas e é mostrado um gráfico de dados para elas.  
    ![Métrica de exemplo – porcentagem de CPU de host](./media/azure-vm-extended-metrics/metric01.png)

No exemplo anterior, um conjunto limitado de métricas padrão estão disponíveis para seus hosts, mas as métricas de memória não estão. Métricas da memória fazem parte das métricas estendidas. Você deve executar algumas etapas adicionais para habilitar a métrica estendida. As informações a seguir o orientará para habilitá-las.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Habilitar métricas estendidas no portal do Azure

Métricas padrão são as métricas do computador host. A métrica de _porcentagem de CPU [Host]_ é um exemplo. Também há métricas básicas para VMs convidadas e também são chamadas métricas estendidas. Exemplos de métricas estendidas incluem _porcentagem de memória [convidado]_ e _memória disponível [convidado]_.

Habilitar métricas estendidas é simples. Para cada VM, habilite o monitoramento em nível de convidado. Quando você habilita o monitoramento de nível de convidado, o agente de diagnóstico do Azure é instalado na VM. O processo a seguir é o mesmo para VMs clássicas e regulares e o mesmo para VMs do Windows e do Linux.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>Habilitar o monitoramento em nível de convidado em VMs existentes

1. Em **Máquinas Virtuais**, exiba a lista das suas VMs e, em seguida, selecione uma VM.
2. Em **Monitoramento**, selecione **Métricas**.
3. Clique em **Configurações do Diagnóstico**.
4. Na página de Configurações de Diagnóstico, clique em **Habilitar o monitoramento de nível de convidado**. As VMs do Linux requerem usar uma conta de armazenamento existente. Se você não escolher uma conta de armazenamento para uma VM do Windows, será criado uma para você.  
    ![Habilitar o monitoramento de nível de convidado](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
5. Depois de alguns minutos, o agente de diagnóstico do Azure está instalado na VM. Atualize a página e a lista de métricas disponíveis é atualizada com as métricas de convidado.  
    ![Métricas estendidas](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>Habilitar o monitoramento em nível de convidado em VMs novas

Quando você cria VMs novas, certifique-se de selecionar **Diagnóstico do sistema operacional convidado**. As VMs do Linux requerem usar uma conta de armazenamento existente. Se você não escolher uma conta de armazenamento para uma VM do Windows, será criado uma para você.

![Habilitar o diagnóstico do sistema operacional convidado](./media/azure-vm-extended-metrics/new-enable-diag.png)

## <a name="resource-manager-credentials"></a>Credenciais de Gerenciador de Recursos

Depois de habilitar métricas estendidas, certifique-se de que o Gerenciamento de Custos tem acesso às suas [credenciais de Gerenciador de Recursos](activate-subs-accounts.md). Suas credenciais são necessárias para o Gerenciamento de Custos coletar e exibir dados de desempenho para suas VMs. Eles também são usados para criar recomendações de otimização de custo. Gerenciamento de Custos precisa de pelo menos três dias de dados de desempenho de uma instância para determinar se é um candidato para uma recomendação de downsizing.

## <a name="enable-vm-metrics-with-a-script"></a>Habilitar as métricas VM com um script

Você pode habilitar as métricas VM com scripts do Azura PowerShell. Quando você tem muitas VMs que você deseja habilitar métricas, você pode usar um script para automatizar o processo. Scripts de exemplo estão no GitHub em [Habilitar o diagnóstico do Azure](https://github.com/Cloudyn/azure-enable-diagnostics).

## <a name="view-azure-performance-metrics"></a>Exibir métricas de desempenho do Azure

Para exibir as métricas de desempenho em suas instâncias do Azure no portal de Cloudyn, navegue até **Assets** > **Compute** > **Instance Explorer**. Na lista de instâncias de VM, expanda uma instância e um recurso para exibir os detalhes.

![Instance Explorer](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>Próximas etapas

- Se você ainda não tiver habilitado o acesso à API do Azure Resource Manager para suas contas, vá para [Ativar contas e assinaturas do Azure](activate-subs-accounts.md)
