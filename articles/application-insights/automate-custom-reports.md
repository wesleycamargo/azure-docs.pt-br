---
title: Automatizar relatórios personalizados com dados do Azure Application Insights
description: Automatizar relatórios personalizados diariamente/semanalmente/mensamente com dados do Azure Application Insights
services: application-insights
documentationcenter: ''
author: sdash
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: sdash
ms.openlocfilehash: 804e8c7a43d1ab16d11b6075be44599b33b46a3e
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34072653"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Automatizar relatórios personalizados com dados do Azure Application Insights

Relatórios periódicos ajudam a manter uma equipe informada sobre como seus serviços críticos de negócios estão indo. Desenvolvedores, equipes de DevOps/SRE e seus gerentes podem ser produtivos com relatórios automatizados entregando confiáveis insights sem a necessidade de todas as pessoas fazerem logon no portal. Tais relatórios podem também ajudar a identificar aumentos graduais dos níveis de latência, carregamento ou falha que podem não disparar nenhuma regra de alerta.

Cada empresa tem suas próprias necessidades de relatório, tais como: 

* Agregações percentis específicas de métricas, ou métricas personalizadas em um relatório.
* Ter diferentes relatórios diariamente, semanalmente e mensalmente para roll-ups de dados para diferentes públicos.
* Segmentação por atributos personalizados como região, ou ambiente. 
* Agrupar alguns recursos IA juntos em um único relatório, mesmo se eles talvez estejam em diferentes assinaturas ou grupos de recursos etc.
* Separar relatórios contendo métricas confidenciais enviados para públicos confidenciais.
* Relatórios para stakeholders que talvez não tenham acesso aos recursos do portal.

> [!NOTE] 
> O e-mail de resumo semanal do Application Insights não permite qualquer personalização, e será descontinuado em favor das opções de personalização listadas abaixo. O último e-mail de resumo semanal será enviado em 11 de junho de 2018. Por favor configure uma das seguintes opções para obter relatórios personalizados parecidos (use a consulta sugerida abaixo).

## <a name="to-automate-custom-report-emails"></a>Para automatizar e-mails de relatório personalizados

Você pode [programaticamente consultar os dados do Application Insights](https://dev.applicationinsights.io/) para gerar relatórios personalizados de forma agendada. As seguintes opções podem ajudá-lo a começar rapidamente:

* [Automatize relatórios com o Microsoft Flow](app-insights-automate-with-flow.md)
* [Automatize relatórios com Aplicativos Lógicos](automate-with-logic-apps.md)
* Use o modelo da [função do Azure](https://azure.microsoft.com/services/functions/) “resumo de mensagem agendado do Application Insights” no cenário de Monitoramento. Esta função usa SendGrid para enviar o e-mail. 

    ![Modelo de função do Azure](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Exemplo de consulta para um e-mail de resumo semanal
A consulta seguinte mostra a junção de vários conjuntos de dados para um e-mail de resumo semanal como um relatório. Personalize isso conforme necessário e use isso com quaisquer das opções listadas acima para automatizar um relatório semanal.   

```AIQL
let period=7d;
requests
| where timestamp > ago(period)
| summarize Row = 1, TotalRequests = sum(itemCount), FailedRequests = sum(toint(success == 'False')),
    RequestsDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
| join (
dependencies
| where timestamp > ago(period)
| summarize Row = 1, TotalDependencies = sum(itemCount), FailedDependencies = sum(success == 'False'),
    DependenciesDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row | join (
pageViews
| where timestamp > ago(period)
| summarize Row = 1, TotalViews = sum(itemCount)
) on Row | join (
exceptions
| where timestamp > ago(period)
| summarize Row = 1, TotalExceptions = sum(itemCount)
) on Row | join (
availabilityResults
| where timestamp > ago(period)
| summarize Row = 1, OverallAvailability = iff(isnan(avg(toint(success))), '------', tostring(toint(avg(toint(success)) * 10000) / 100.0)),
    AvailabilityDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row
| project TotalRequests, FailedRequests, RequestsDuration, TotalDependencies, FailedDependencies, DependenciesDuration, TotalViews, TotalExceptions, OverallAvailability, AvailabilityDuration
```

  
## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como criar [consultas do Analytics](app-insights-analytics-using.md).
- Saiba mais sobre [dados consultando programaticamente do Application Insights](https://dev.applicationinsights.io/)
- Saiba mais sobre o [Aplicativos Lógicos](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).
- Saiba mais sobre o [Microsoft Flow](https://ms.flow.microsoft.com).


