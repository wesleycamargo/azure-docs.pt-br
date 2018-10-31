---
title: Como instalar e usar as exibições do Log Analytics para o Azure Active Directory (versão prévia) | Microsoft Docs
description: Saiba como instalar e usar os modos de exibição do Log Analytics do Azure Active Directory (versão prévia)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 2290de3c-2858-4da0-b4ca-a00107702e26
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 10/01/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: b1333eda0fa0bffdf3852f24414511f55acc090e
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49395446"
---
# <a name="install-and-use-the-log-analytics-views-for-azure-active-directory"></a>Instalar e usar os modos de exibição do Log Analytics do Azure Active Directory

As exibições do Log Analytics do Azure Active Directory ajuda você a analisar e pesquisar os logs de atividades do Azure AD no locatário do Azure AD. Logs de atividade do Azure AD incluem:

* Logs de auditoria: o [relatório de atividade de logs de auditoria](concept-audit-logs.md) dá acesso ao histórico de todas as tarefas executadas em seu locatário.
* Logs de entrada: com o [relatório de atividades de entrada](concept-sign-ins.md), você pode determinar quem realizou as tarefas indicadas pelo relatório das trilhas de auditoria.

## <a name="prerequisites"></a>Pré-requisitos

Para usar as exibições do Log Analytics, você precisa:

* Um espaço de trabalho do Log Analytics em sua assinatura do Azure. Saiba como [criar um espaço de trabalho do Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Em primeiro lugar, conclua as etapas para [rotear os logs de atividades do Azure AD para seu workspace do Log Analytics](howto-integrate-activity-logs-with-log-analytics.md).
* Baixar as exibições do [Repositório do GitHub](https://aka.ms/AADLogAnalyticsviews) para seu computador local.

## <a name="install-the-log-analytics-views"></a>Instalar as exibições do Log Analytics

1. Navegue para seu workspace do Log Analytics. Para fazer isso, primeiro navegue até o [portal do Azure](https://portal.azure.com) e selecione **Todos os serviços**. Digite **Log Analytics** na caixa de texto e selecione **Log Analytics**. Selecione o workspace para o qual você roteou os logs de atividade como parte dos pré-requisitos.
2. Selecione **Designer de Exibição**, **Importe** e, em seguida, **Escolher Arquivo** para importar as exibições do seu computador local.
3. Selecione as exibições que você baixou dos pré-requisitos e selecione **Salvar** para salvar a importação. Faça isso para a exibição **Eventos de Provisionamento de Conta do Azure AD**, para a exibição **Eventos de Entradas** e para a exibição **Usuários Dando Consentimento**.

## <a name="use-the-views"></a>Usar as exibições

1. Navegue para seu workspace do Log Analytics. Para fazer isso, primeiro navegue até o [portal do Azure](https://portal.azure.com) e selecione **Todos os serviços**. Digite **Log Analytics** na caixa de texto e selecione **Log Analytics**. Selecione o workspace para o qual você roteou os logs de atividade como parte dos pré-requisitos.

2. Quando você estiver usando o workspace, selecione **Resumo do Workspace**. Você deve ver as três exibições a seguir:

    * **Eventos de Provisionamento de Conta do Azure AD**: esta exibição mostra os relatórios relacionados à auditoria da atividade de provisionamento, como o número de novos usuários provisionados e falhas de provisionamento, número de usuários atualizados e falhas de atualização e número de usuários desprovisionados e falhas correspondentes.    
    * **Eventos de Entradas**: esta exibição mostra os relatórios mais relevantes relacionados à atividade de entrada de monitoramento, como entradas por aplicativo, usuário, dispositivo, bem como exibição resumida acompanhando o número de entradas ao longo do tempo.
    * **Consentimento dos Usuários para Execução**: esta exibição mostra os relatórios relacionados ao consentimento do usuário, como o consentimento dados por usuário, entradas por usuários que recebeu consentimento, bem como entradas por aplicativo para todos os aplicativos baseados em consentimento. 

3. Selecione qualquer uma dessas exibições para ir para os relatórios individuais. Você também pode definir alertas para qualquer um dos parâmetros do relatório. Por exemplo, vamos definir um alerta sempre que houver um erro de entrada. Para fazer isso, primeiro selecione a exibição **Eventos de Entradas**, selecione o relatório **Erros de entrada ao longo do tempo** e, em seguida, selecione **Analytics** para abrir a página de detalhes com a consulta real subjacente ao relatório. 

    ![Detalhes](./media/howto-install-use-log-analytics-views/details.png)


4. Selecione **Definir Alerta** e, em seguida, **Sempre que a pesquisa de logs personalizada for &lt;lógica não definida&gt;** na seção **Critérios de alerta**. Como queremos alertar sempre que houver um erro de conexão, defina o **Limite** da lógica de alerta padrão como **1** e, em seguida, selecione **Concluído**. 

    ![Configurar sinal lógico](./media/howto-install-use-log-analytics-views/configure-signal-logic.png)

5. Insira um nome e uma descrição para o alerta e defina a gravidade como **Aviso**.

    ![Criar regra](./media/howto-install-use-log-analytics-views/create-rule.png)

6. Selecione o grupo de ações para alerta. Em geral, pode ser uma equipe que você deseja notificar por email ou mensagem de texto, ou pode ser uma tarefa automatizada usando webhooks, runbooks, funções, aplicativos lógicos ou soluções ITSM externas. Este artigo mostra como [criar e gerenciar grupos de ações no portal do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).

7. Selecione **Criar regra de alerta** para criar o alerta. Agora você será alertado sempre que houver um erro de conexão.

## <a name="next-steps"></a>Próximas etapas

* [Como analisar logs de atividades no Log Analytics](howto-analyze-activity-logs-log-analytics.md)
* [Introdução ao uso do Log Analytics no portal do Azure](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-analytics-portal)