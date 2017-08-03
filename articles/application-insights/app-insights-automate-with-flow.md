---
title: Automatizar os processos do Azure Application Insights com o Microsoft Flow
description: "Saiba como você pode usar o Microsoft Flow para automatizar rapidamente os processos repetidos usando o conector do Application Insights para o Flow."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/25/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 7e17fde55097a8e852be33bac66940a05fdd9df6
ms.contentlocale: pt-br
ms.lasthandoff: 07/01/2017

---

# <a name="automate-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Automatizar os processos do Application Insights com o conector para o Microsoft Flow

Você se encontra executando repetidamente as mesmas consultas nos dados telemétricos para verificar se o serviço está funcionando corretamente? Deseja automatizar essas consultas para descobrir tendências e anomalias e criar seus próprios fluxos de trabalho para elas? Bem, o Conector do Application Insights (versão prévia) para o Microsoft Flow é exatamente o que você precisa!
Com essa integração, diversos processos agora podem ser automatizados sem a necessidade de escrever nenhuma linha de código. Depois de criar um fluxo usando uma ação do Application Insights, ele executa automaticamente a consulta do Application Insights Analytics. Adicione também outras ações. O Flow disponibiliza centenas de ações. Por exemplo, você pode usar o Flow para enviar uma notificação por email automaticamente ou criar um bug no Visual Studio Team Services. Use também um dos vários [modelos](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights) disponíveis para o Conector para o Microsoft Flow. Esses modelos aceleram o processo de criação de um fluxo. 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="tutorial-for-creating-a-flow-for-application-insights"></a>Tutorial para criação de um fluxo do Application Insights

Neste tutorial, você aprenderá a criar um fluxo que usa o algoritmo de cluster automático do Analytics para agrupar atributos nos dados de um aplicativo Web. O fluxo envia os resultados por email automaticamente. Esse é apenas um exemplo de como você pode usar o Microsoft Flow e o Application Insights Analytics juntos. 

### <a name="step-1-create-a-flow"></a>Etapa 1: Criar um fluxo
1. Entre em http://flow.microsoft.com e selecione **Meus Fluxos**.
2. Clique em **Criar um fluxo em branco**.

### <a name="step-2-create-a-trigger-for-your-flow"></a>Etapa 2: Criar um gatilho para o fluxo
1. Escolha **Agendamento** e, depois, **Agendamento – Recorrência**.
2. Defina a **Frequência** como Dia com um **Intervalo** igual a 1.

![Caixa de diálogo do gatilho do fluxo](./media/app-insights-automate-with-flow/flow1.png)


### <a name="step-3-add-an-azure-application-insights-action"></a>Etapa 3: Adicionar uma ação do Azure Application Insights
1. Clique em **Nova etapa** e, depois, em **Adicionar uma ação**.
2. Pesquise o Azure Application Insights.
3. Clique em Versão Prévia do Azure Application Insights – Visualizar consulta do Analytics.

![Tela Executar consulta do Analytics](./media/app-insights-automate-with-flow/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Etapa 4: Conectar-se a um recurso do Application Insights

**Pré-requisito**

Você precisa de uma ID do Aplicativo e uma Chave de API do recurso para concluir esta etapa. Recupere-as no Portal do Azure, conforme mostrado no seguinte diagrama:

![ID do Aplicativo no portal do Azure](./media/app-insights-automate-with-flow/appid.png) 

- Forneça um nome para a conexão, junto com a ID do Aplicativo e a Chave de API.

![Tela de conexão do Flow](./media/app-insights-automate-with-flow/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Etapa 5: Especificar o tipo de consulta do Analytics e o tipo de gráfico
Este exemplo seleciona as solicitações com falha no último dia e correlaciona-as com as exceções que ocorreram como parte da operação. O Analytics correlaciona com base no identificador operation_Id. Em seguida, a consulta segmenta os resultados usando o algoritmo de cluster automático. Ao criar suas próprias consultas, verifique se elas estão funcionando corretamente no Analytics antes de adicioná-las ao fluxo.

- Adicione a consulta do Analytics a seguir e selecione o tipo de gráfico de tabela HTML. 

```
requests
| where timestamp > ago(1d)
| where success == "False"
| project name, operation_Id
| join ( exceptions
    | project problemId, outerMessage, operation_Id
) on operation_Id
| evaluate autocluster()
```
![Tela de configuração de consulta do Analytics](./media/app-insights-automate-with-flow/flow4.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Etapa 6: Configurar o fluxo para enviar email

1. Clique em **Nova etapa** e, depois, selecione **Adicionar uma ação**.
2. Pesquise o Office 365 Outlook.
3. Clique em Office 365 Outlook – Enviar um email.
![Seleção do Office 365 Outlook](./media/app-insights-automate-with-flow/flow2b.png)

4. Na ação de email, adicione os seguintes dados:
 - Especifique o endereço de email do destinatário
 - Indique o assunto do email
 - Coloque o cursor no campo **Corpo** e, no menu de conteúdo dinâmico aberto à direita, selecione **Corpo**.
 - Clique em **Mostrar opções avançadas**.

 ![Configuração do Office 365 Outlook](./media/app-insights-automate-with-flow/flow5.png)

5. No menu de conteúdo dinâmico, faça o seguinte:
- Selecione **Nome do Anexo**
- Selecione **Conteúdo do Anexo**
- Selecione **Sim** no campo **É HTML** 

![Tela de configuração de email do Office 365](./media/app-insights-automate-with-flow/flow7.png)
### <a name="step-7-save-and-test-your-flow"></a>Etapa 7: Salvar e testar o fluxo
- Adicione um nome ao fluxo e clique em **Criar fluxo**.

![Tela de criação do Microsoft Flow](./media/app-insights-automate-with-flow/flow8.png)

Você pode aguardar até que o gatilho execute essa ação ou executar o fluxo imediatamente [executando o gatilho sob demanda](https://flow.microsoft.com/blog/run-now-and-six-more-services/).

Quando o fluxo é executado, os destinatários que você especificou na lista de emails recebem um email que é semelhante ao seguinte:

![Email de exemplo](./media/app-insights-automate-with-flow/flow9.png)


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como criar [consultas do Analytics](app-insights-analytics-using.md).
- Saiba mais sobre o [Microsoft Flow](https://ms.flow.microsoft.com).



<!--Link references-->






