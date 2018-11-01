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
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: fc9ed37ef24e776336124bf097def2f6a00ee83e
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50419785"
---
# <a name="common-questions-for-transition-from-oms-portal-to-azure-portal-for-log-analytics-users"></a>Perguntas comuns sobre a transição do portal do OMS para o Portal do Azure para usuários do Log Analytics
Inicialmente, o Log Analytics usava seu próprio portal, o portal do OMS, para gerenciar as configurações e analisar os dados coletados.  Todas as funcionalidades desse portal foram transferidas para o Portal do Azure, onde continuarão a ser desenvolvidas.

Este artigo responde às perguntas comuns dos usuários que estão fazendo essa transição.  Se você usou o Log Analytics no portal do OMS, encontrará aqui as respostas sobre como executar as mesmas tarefas no Portal do Azure.

## <a name="do-i-need-to-migrate-anything"></a>É necessário migrar alguma coisa?
Não. Não há alterações feitas no próprio Log Analytics, portanto, não há nada que precise ser migrado. A única coisa que está alterando é a interface que você usa para acessá-lo. Na verdade, agora é possível usar o portal do Azure para acessar os mesmos workspaces, soluções, exibições e pesquisas de logs que você atualmente usa no portal do OMS.

## <a name="where-do-i-find-log-analytics-in-azure"></a>Onde está o Log Analytics no Azure?
Faça logon no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).  Clique em **Todos os serviços** e, na lista de recursos, digite **Log Analytics**. Selecione **Log Analytics** e selecione seu workspace. A página de resumo do workspace será exibida.

![Workspace do Log Analytics](media/log-analytics-oms-portal-faq/log-analytics.png)

## <a name="how-do-i-manage-permissions"></a>Como fazer para gerenciar permissões?
Se você não tiver acesso ao seu workspace do Log Analytics no Portal do Azure, precisará configurar as permissões usando o [acesso baseado em função do Azure](../active-directory/role-based-access-control-configure.md). Para obter detalhes sobre como gerenciar permissões do workspace, confira [Gerenciar workspaces](../log-analytics/log-analytics-manage-access.md#manage-accounts-and-users). Para saber mais sobre como gerenciar permissões para alertas, confira [Introdução às funções, permissões e segurança com o Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md).

## <a name="how-do-i-create-a-new-workspace"></a>Como fazer para criar um novo workspace? 
Na lista de workspaces no Portal do Azure, clique em **Adicionar** na lista de workspaces.  Para obter detalhes completos, confira [Criar um workspace do Log Analytics no Portal do Azure](../log-analytics/log-analytics-quick-create-workspace.md).

![Página de visão geral](media/log-analytics-oms-portal-faq/new-workspace.png)

## <a name="where-is-my-overview-page"></a>Onde está minha página de visão geral?
A tela principal no portal do OMS exibe os blocos de todas as soluções de gerenciamento instaladas em seu workspace, além de qualquer exibição personalizada que você criou. A mesma exibição está disponível no portal do Azure. No seu workspace, selecione **Resumo do Workspace**.

![Página de visão geral](media/log-analytics-oms-portal-faq/overview.png)

## <a name="how-do-i-open-log-search-and-view-designer"></a>Como fazer para abrir a Pesquisa de Log e o Designer de Exibição?
A **Pesquisa de Log** e o **Designer de Exibição** estão disponíveis na página principal e no menu à esquerda do seu workspace no Portal do Azure, logo ao lado de **Visão geral**.

## <a name="where-do-i-find-settings"></a>Onde encontro as configurações?
Muitas configurações da seção **Configurações** do portal do OMS estão disponíveis no menu **Configurações avançadas** no Portal do Azure para o workspace.

![Configurações avançadas](media/log-analytics-oms-portal-faq/advanced-settings.png)

As seções a seguir fornecem uma lista completa de como você pode acessar as configurações disponíveis anteriormente na seção **Configurações** do portal do OMS.

### <a name="accounts"></a>Contas 
As configurações das contas são gerenciadas em locais diferentes no Portal do Azure, conforme descrito na tabela a seguir.

| Configuração no portal do OMS | Equivalente no Portal do Azure |
|:---|:---|
| Conta de Automação | Menu **Conta de Automação** para o workspace. |
| Assinatura do Azure e Plano de Dados | Menu **Tipo de preço** do workspace. |
| Gerenciar usuários | Use o acesso baseado em função do Azure para [gerenciar permissões de seu workspace](#how-do-i-manage-permissions). |
| Informações do workspace | As informações disponíveis no menu **Workspace do OMS** para o workspace. |

### <a name="alerts"></a>Alertas
Agora, as regras de alerta com base em consultas do Log Analytics são gerenciadas na [experiência de alerta unificado](#how-do-i-create-and-manage-alerts). 

### <a name="computer-groups"></a>Grupos de computadores
Gerencie Grupos de computadores no menu **Configurações avançadas** do workspace. 

### <a name="connected-sources"></a>Fontes conectadas
Gerencie a maioria das configurações de Fonte conectada no menu **Configurações avançadas** do workspace. A tabela a seguir fornece detalhes de cada seção do menu.

| Configuração no portal do OMS | Equivalente no Portal do Azure |
|:---|:---|
| Servidores Windows   | Menu **Configurações avançadas** do workspace. |
| Servidores Linux   | Menu **Configurações avançadas** do workspace. |
| Armazenamento do Azure     | Menu **Configurações avançadas** do workspace. |
| System Center     | Menu **Configurações avançadas** do workspace. |
| Office 365        | Confira ao [documentação da solução de gerenciamento do Office 365](../operations-management-suite/oms-solution-office-365.md) para ver detalhes da configuração. |
| Telemetria do Windows | Menu de configurações da solução. Confira [Windows Analytics no portal do Azure](/windows/deployment/update/windows-analytics-azure-portal) para obter detalhes de configuração. |
| Conector ITSM    | Veja [Conectar produtos/serviços de ITSM com o Conector de Gerenciamento de Serviços de TI](../log-analytics/log-analytics-itsmc-connections.md) para obter instruções sobre como conectar seu serviço de ITSM com o Log Analytics. |

### <a name="data"></a>Dados
Gerencie a maioria das configurações de dados no menu **Configurações avançadas** do workspace. A tabela a seguir fornece detalhes de cada seção do menu.

| Configuração no portal do OMS | Equivalente no Portal do Azure |
|:---|:---|
| Logs de Eventos do Windows           | Menu **Configurações avançadas** do workspace. |
| Contadores de desempenho do Windows | Menu **Configurações avançadas** do workspace. |
| Contadores de desempenho do Linux   | Menu **Configurações avançadas** do workspace. |
| Logs IIS                     | Menu **Configurações avançadas** do workspace. |
| Campos personalizados                | Menu **Configurações avançadas** do workspace. |
| Logs personalizados                  | Menu **Configurações avançadas** do workspace. |
| syslog                       | Menu **Configurações avançadas** do workspace. |
| Application Insights         | Esta solução foi preterida agora que o Log Analytics e o Application Insights compartilham o mesmo mecanismo de dados.  |
| Rastreamento de arquivos do Windows        | Menu **Controle de Alterações** na Automação do Azure. Confira [Controlar alterações no ambiente com a solução Controle de Alterações](../automation/automation-change-tracking.md) para obter detalhes. |
| Rastreamento de Registro do Windows        | Menu **Controle de Alterações** na Automação do Azure. Confira [Controlar alterações no ambiente com a solução Controle de Alterações](../automation/automation-change-tracking.md) para obter detalhes. |
| Rastreamento de Arquivos do Linux          | Menu **Controle de Alterações** na Automação do Azure. Confira [Controlar alterações no ambiente com a solução Controle de Alterações](../automation/automation-change-tracking.md) para obter detalhes. |

### <a name="solutions"></a>Soluções
Gerencie soluções no menu **Soluções** do workspace. 

## <a name="how-do-i-install-and-remove-management-solutions"></a>Como fazer para instalar e remover soluções de gerenciamento?
No portal do OMS, instale as soluções de gerenciamento da Galeria de Soluções e as remove de **Configurações**. No Portal do Azure, [instale as soluções de gerenciamento](../monitoring/monitoring-solutions.md#install-a-management-solution) do Azure Marketplace. [Remova soluções](../monitoring/monitoring-solutions.md#remove-a-management-solution) da lista de soluções instaladas.

## <a name="how-do-i-create-and-manage-alerts"></a>Como fazer para criar e gerenciar alertas?
Agora, as regras de alerta com base em consultas do Log Analytics são gerenciadas na [experiência de alerta unificado](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). Veja [Como estender alertas do Log Analytics para os Alertas do Azure](../monitoring-and-diagnostics/monitoring-alerts-extend-tool.md) e obtenha detalhes sobre como configurar e usar alertas no Portal do Azure.

## <a name="how-do-i-access-my-dashboards"></a>Como fazer para acessar meus painéis?
O recurso [Meu Painel](../log-analytics/log-analytics-dashboards.md) do Log Analytics foi preterido. Este recurso permitiu que você tenha uma coleção particular de partes do Designer de Exibição e é substituído pela funcionalidade interna do painel do Azure. Você pode continuar a visualizar dados no Log Analytics usando o [Designer de Exibição](../log-analytics/log-analytics-view-designer.md) para exibições compartilhadas. Você também pode fixar visualizações desses modos de exibição, ou para [consultas individuais](../log-analytics/log-analytics-tutorial-dashboards.md) para Painéis do Azure.

## <a name="how-do-i-check-my-usage"></a>Como fazer para verificar meu uso?
Veja e gerencie facilmente seu uso e o custo do Log Analytics selecionando **Uso e custos estimados** no workspace.

![Uso e custos estimados](media/log-analytics-oms-portal-faq/usage.png)


## <a name="can-i-still-use-the-classic-portal"></a>Ainda posso usar o portal clássico?
Por um tempo limitado, você ainda pode acessar o portal usando esta URL com seu próprio nome de workspace: https://\<seu nome de workspace\>.portal.mms.microsoft.com. No entanto, recomendamos o uso do Portal do Azure e o envio de comentários em LAUpgradeFeedback@microsoft.com sobre qualquer problema impeditivo.

## <a name="next-steps"></a>Próximas etapas

- [Localize e instale as soluções de gerenciamento](../monitoring/monitoring-solutions.md) usando o Portal do Azure.
- Saiba mais sobre [Pesquisa de Logs no Portal do Azure](log-analytics-log-search-portals.md).
