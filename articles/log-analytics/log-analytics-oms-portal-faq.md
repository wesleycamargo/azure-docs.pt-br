---
title: Perguntas comuns sobre a transição do portal do OMS para o Portal do Azure para usuários do Log Analytics | Microsoft Docs
description: Responde as perguntas comuns de usuários do Log Analytics que estão fazendo a transição do portal do OMS para o Portal do Azure.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: bwren
ms.openlocfilehash: 1e0fd56b6e420103b4f786985f71a84737db642d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "36333625"
---
# <a name="common-questions-for-transition-from-oms-portal-to-azure-portal-for-log-analytics-users"></a>Perguntas comuns sobre a transição do portal do OMS para o Portal do Azure para usuários do Log Analytics
Inicialmente, o Log Analytics usava seu próprio portal, o portal do OMS, para gerenciar as configurações e analisar os dados coletados.  Todas as funcionalidades desse portal foram transferidas para o Portal do Azure, onde continuarão a ser desenvolvidas.

Este artigo responde às perguntas comuns dos usuários que estão fazendo essa transição.  Se você usou o Log Analytics no portal do OMS, encontrará aqui as respostas sobre como executar as mesmas tarefas no Portal do Azure.

## <a name="where-do-i-find-log-analytics-in-azure"></a>Onde está o Log Analytics no Azure?
Faça logon no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).  Clique em **Todos os serviços** e, na lista de recursos, digite **Log Analytics**. Selecione **Log Analytics** e selecione seu espaço de trabalho. A página de resumo do espaço de trabalho será exibida.

![Espaço de trabalho do Log Analytics](media/log-analytics-new-portal/log-analytics.png)

## <a name="how-do-i-manage-permissions"></a>Como fazer para gerenciar permissões?
Se você não tiver acesso ao seu espaço de trabalho do Log Analytics no Portal do Azure, precisará configurar as permissões usando o [acesso baseado em função do Azure](../active-directory/role-based-access-control-configure.md). Para obter detalhes sobre como gerenciar permissões do espaço de trabalho, confira [Gerenciar espaços de trabalho](../log-analytics/log-analytics-manage-access.md#manage-accounts-and-users). Para saber mais sobre como gerenciar permissões para alertas, confira [Introdução às funções, permissões e segurança com o Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md).

## <a name="how-do-i-create-a-new-workspace"></a>Como fazer para criar um novo espaço de trabalho? 
Na lista de espaços de trabalho no Portal do Azure, clique em **Adicionar** na lista de espaços de trabalho.  Para obter detalhes completos, confira [Criar um espaço de trabalho do Log Analytics no Portal do Azure](../log-analytics/log-analytics-quick-create-workspace.md).

![Página de visão geral](media/log-analytics-new-portal/new-workspace.png)

## <a name="where-is-my-overview-page"></a>Onde está minha página de visão geral?
A tela principal no portal do OMS exibe os blocos de todas as soluções de gerenciamento instaladas em seu espaço de trabalho, além de qualquer exibição personalizada que você criou. A mesma exibição está disponível no portal do Azure. No seu espaço de trabalho, selecione **Resumo do Espaço de Trabalho**.

![Página de visão geral](media/log-analytics-new-portal/overview.png)

## <a name="how-do-i-open-log-search-and-view-designer"></a>Como fazer para abrir a Pesquisa de Log e o Designer de Exibição?
A **Pesquisa de Log** e o **Designer de Exibição** estão disponíveis na página principal e no menu à esquerda do seu espaço de trabalho no Portal do Azure, logo ao lado de **Visão geral**.

## <a name="where-do-i-find-settings"></a>Onde encontro as configurações?
Muitas configurações da seção **Configurações** do portal do OMS estão disponíveis no menu **Configurações avançadas** no Portal do Azure para o espaço de trabalho.

![Configurações avançadas](media/log-analytics-new-portal/advanced-settings.png)

As seções a seguir fornecem uma lista completa de como você pode acessar as configurações disponíveis anteriormente na seção **Configurações** do portal do OMS.

### <a name="accounts"></a>Contas 
As configurações das contas são gerenciadas em locais diferentes no Portal do Azure, conforme descrito na tabela a seguir.

| Configuração no portal do OMS | Equivalente no Portal do Azure |
|:---|:---|
| Conta de Automação | Menu **Conta de Automação** para o espaço de trabalho. |
| Assinatura do Azure e Plano de Dados | Menu **Tipo de preço** do espaço de trabalho. |
| Gerenciar usuários | Use o acesso baseado em função do Azure para [gerenciar permissões de seu espaço de trabalho](#how-do-i-manage-permissions). |
| Informações do espaço de trabalho | As informações disponíveis no menu **Espaço de Trabalho do OMS** para o espaço de trabalho. |

### <a name="alerts"></a>Alertas
Agora, as regras de alerta com base em consultas do Log Analytics são gerenciadas na [experiência de alerta unificado](#how-do-i-create-and-manage-alerts). 

### <a name="computer-groups"></a>Grupos de computadores
Gerencie Grupos de computadores no menu **Configurações avançadas** do espaço de trabalho. 

### <a name="connected-sources"></a>Fontes conectadas
Gerencie a maioria das configurações de Fonte conectada no menu **Configurações avançadas** do espaço de trabalho. A tabela a seguir fornece detalhes de cada seção do menu.

| Configuração no portal do OMS | Equivalente no Portal do Azure |
|:---|:---|
| Servidores Windows   | Menu **Configurações avançadas** do espaço de trabalho. |
| Servidores Linux   | Menu **Configurações avançadas** do espaço de trabalho. |
| Armazenamento do Azure     | Menu **Configurações avançadas** do espaço de trabalho. |
| System Center     | Menu **Configurações avançadas** do espaço de trabalho. |
| Office 365        | Confira ao [documentação da solução de gerenciamento do Office 365](../operations-management-suite/oms-solution-office-365.md) para ver detalhes da configuração. |
| Telemetria do Windows | Não disponível ainda no Portal do Azure. |
| Conector ITSM    | Veja [Conectar produtos/serviços de ITSM com o Conector de Gerenciamento de Serviços de TI](../log-analytics/log-analytics-itsmc-connections.md) para obter instruções sobre como conectar seu serviço de ITSM com o Log Analytics. |

### <a name="data"></a>Dados
Gerencie a maioria das configurações de dados no menu **Configurações avançadas** do espaço de trabalho. A tabela a seguir fornece detalhes de cada seção do menu.

| Configuração no portal do OMS | Equivalente no Portal do Azure |
|:---|:---|
| Logs de Eventos do Windows           | Menu **Configurações avançadas** do espaço de trabalho. |
| Contadores de desempenho do Windows | Menu **Configurações avançadas** do espaço de trabalho. |
| Contadores de desempenho do Linux   | Menu **Configurações avançadas** do espaço de trabalho. |
| Logs IIS                     | Menu **Configurações avançadas** do espaço de trabalho. |
| Campos personalizados                | Menu **Configurações avançadas** do espaço de trabalho. |
| Logs personalizados                  | Menu **Configurações avançadas** do espaço de trabalho. |
| syslog                       | Menu **Configurações avançadas** do espaço de trabalho. |
| Application Insights         | Esta solução foi preterida agora que o Log Analytics e o Application Insights compartilham o mesmo mecanismo de dados.  |
| Rastreamento de arquivos do Windows        | Menu **Controle de Alterações** na Automação do Azure. Confira [Controlar alterações no ambiente com a solução Controle de Alterações](../automation/automation-change-tracking.md) para obter detalhes. |
| Rastreamento de Registro do Windows        | Menu **Controle de Alterações** na Automação do Azure. Confira [Controlar alterações no ambiente com a solução Controle de Alterações](../automation/automation-change-tracking.md) para obter detalhes. |
| Rastreamento de Arquivos do Linux          | Menu **Controle de Alterações** na Automação do Azure. Confira [Controlar alterações no ambiente com a solução Controle de Alterações](../automation/automation-change-tracking.md) para obter detalhes. |

### <a name="solutions"></a>Soluções
Gerencie soluções no menu **Soluções** do espaço de trabalho. 

## <a name="how-do-i-install-and-remove-management-solutions"></a>Como fazer para instalar e remover soluções de gerenciamento?
No portal do OMS, instale as soluções de gerenciamento da Galeria de Soluções e as remove de **Configurações**. No Portal do Azure, [instale as soluções de gerenciamento](../monitoring/monitoring-solutions.md#install-a-management-solution) do Azure Marketplace. [Remova soluções](../monitoring/monitoring-solutions.md#remove-a-management-solution) da lista de soluções instaladas.

## <a name="how-do-i-create-and-manage-alerts"></a>Como fazer para criar e gerenciar alertas?
Agora, as regras de alerta com base em consultas do Log Analytics são gerenciadas na [experiência de alerta unificado](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). Veja [Como estender alertas do Log Analytics para os Alertas do Azure](../monitoring-and-diagnostics/monitoring-alerts-extend-tool.md) e obtenha detalhes sobre como configurar e usar alertas no Portal do Azure.

## <a name="how-do-i-access-my-dashboards"></a>Como fazer para acessar meus painéis?
Os [Painéis](../log-analytics/log-analytics-dashboards.md) no Log Analytics foram preteridos.  Visualize os dados no Log Analytics usando o [Designer de Exibição](../log-analytics/log-analytics-view-designer.md), que conta com uma funcionalidade adicional e fixa consulta e exibições nos painéis do Azure.

## <a name="how-do-i-check-my-usage"></a>Como fazer para verificar meu uso?
Veja e gerencie facilmente seu uso e o custo do Log Analytics selecionando **Uso e custos estimados** no espaço de trabalho.

![Uso e custos estimados](media/log-analytics-new-portal/usage.png)


## <a name="can-i-still-use-the-classic-portal"></a>Ainda posso usar o portal clássico?
Por um tempo limitado, você ainda pode acessar o portal usando esta URL com seu próprio nome de espaço de trabalho: https://\<seu nome de espaço de trabalho\>.portal.mms.microsoft.com. No entanto, recomendamos o uso do Portal do Azure e o envio de comentários em LAUpgradeFeedback@microsoft.com sobre qualquer problema impeditivo.

## <a name="next-steps"></a>Próximas etapas

- [Localize e instale as soluções de gerenciamento](../monitoring/monitoring-solutions.md) usando o Portal do Azure.
- Saiba mais sobre [Pesquisa de Logs no Portal do Azure](log-analytics-log-search-portals.md).