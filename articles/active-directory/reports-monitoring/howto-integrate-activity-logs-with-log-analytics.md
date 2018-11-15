---
title: Como integrar logs do Azure Active Directory ao Log Analytics usando o Azure Monitor (versão prévia) | Microsoft Docs
description: Aprenda a integrar os logs do Azure Active Directory ao Log Analytics usando o Azure Monitor (visualização)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 7509081bbf43aeaf39570f84afef81b6dd5a39fe
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51621659"
---
# <a name="integrate-azure-ad-logs-with-log-analytics-using-azure-monitor-preview"></a>Integrar logs do Azure Active Directory ao Log Analytics usando o Azure Monitor (visualização)

O Log Analytics permite consultar os dados para localizar eventos em particular, analisar tendências e executar a correlação entre várias fontes de dados. Com a integração do logs de atividade do Microsoft Azure Active Directory no Log Analytics, agora você pode executar tarefas como:

 * Comparar os logs de entrada do dos logs de assinatura do Azure Active Directory mediante os logs de segurança pela Central de Segurança do Azure

 * Solucionar problemas de gargalos de desempenho na página de entrada do seu aplicativo, correlacionando os dados de desempenho de aplicativo do Aplicativo Azure Insights.  

O vídeo de uma sessão do Ignite a seguir demonstra os benefícios do uso de logs do Log Analytics para o Azure AD em cenários práticos de usuários.

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

Neste artigo, você aprenderá como integrar logs do Azure Active Directory (Azure AD) ao Log Analytics usando o Azure Monitor.

## <a name="supported-reports"></a>Relatórios com suporte

Você pode rotear os logs de atividade de auditoria e logs de atividade de entrada para o Log Analytics para análise posterior. 

* **Trilhas de auditoria**: o [relatório de atividade das trilhas de auditoria](concept-audit-logs.md) dá acesso ao histórico de todas as tarefas executadas em seu locatário.
* **Logs de entrada**: com o [relatório de atividades de entrada](concept-sign-ins.md), você pode determinar quem realizou as tarefas indicadas pelo relatório das trilhas de auditoria.

> [!NOTE]
> Não há suporte para logs de atividades de auditoria e entradas relacionados ao B2C no momento.
>

## <a name="prerequisites"></a>Pré-requisitos 

Para usar esse recurso, você precisa de:

* Uma assinatura do Azure. Se não tiver uma assinatura do Azure, você poderá [inscrever-se em uma avaliação gratuita](https://azure.microsoft.com/free/).
* Um locatário do Azure AD.
* Um usuário que seja *administrador global* ou *administrador de segurança* do locatário do Azure AD.
* Um espaço de trabalho do Log Analytics em sua assinatura do Azure. Saiba como [criar um espaço de trabalho do Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).

## <a name="send-logs-to-log-analytics"></a>Enviar logs ao Log Analytics

1. Entre no [Portal do Azure](https://portal.azure.com). 

2. Selecione as configurações do **Azure Active Directory** > **Diagnostic** -> **Adicionar configuração de diagnóstico**. Você também pode selecionar a página**Exportar Configurações** dos **Logs de Auditoria** ou **Assinaturas** para obter a página de configuração de configurações de diagnóstico.  
    
3. No menu **Configurações de Diagnóstico**, selecione **Enviar para a caixa de seleção do Log Analytics** e, em seguida, selecione **Configurar**.

4. Selecione o espaço de trabalho do Log Analytics para enviar os logs ou criar um novo espaço de trabalho na caixa de diálogo fornecida.  

5. Siga um destes procedimentos, ou ambos:
    * Para enviar os logs de auditoria para o espaço de trabalho do Log Analytics, selecione a caixa de seleção **AuditLogs**. 
    * Para enviar logs de entrada para o espaço de trabalho do Log Analytics, selecione a caixa de seleção **SignInLogs**.

6. Clique em **Salvar** para salvar a configuração.

    ![Configurações de diagnóstico](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. Após cerca de 15 minutos, verifique se que os eventos são transmitidos para seu espaço de trabalho do Log Analytics.

## <a name="next-steps"></a>Próximas etapas

* [Analisar os logs de atividade do Azure Active Directory no Log Analytics](howto-analyze-activity-logs-log-analytics.md)
* [Instalar e usar os modos de exibição do Log Analytics do Azure Active Directory](howto-install-use-log-analytics-views.md)
