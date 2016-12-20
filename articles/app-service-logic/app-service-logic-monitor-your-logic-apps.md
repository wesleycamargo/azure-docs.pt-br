---
title: "Monitorar seus Aplicativos lógicos no Serviço de Aplicativo do Azure | Microsoft Docs"
description: "Como ver o que os Aplicativos lógicos fizeram"
author: jeffhollan
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 5c1b1e15-3b6c-49dc-98a6-bdbe7cb75339
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: cc73caf6ffdc47199369112a5310a3f4dd0cc9bf


---
# <a name="monitor-your-logic-apps"></a>Monitorar seus Aplicativos lógicos
Depois de [criar um Aplicativo lógico](app-service-logic-create-a-logic-app.md), é possível ver o histórico completo de sua execução no portal do Azure.  Você também pode configurar serviços como o Diagnóstico do Azure e os Alertas do Azure para monitorar eventos em tempo real, e alertas para eventos como "quando mais do que cinco execuções falharem em uma hora."

## <a name="monitor-in-the-azure-portal"></a>Monitorar no Portal do Azure
Para exibir o histórico, selecione **Procurar** e **Aplicativos Lógicos**. É exibida uma lista com todos os aplicativos lógicos em sua assinatura.  Selecione o aplicativo lógico que você deseja monitorar.  Você verá uma lista de todas as ações e disparadores que ocorreram para esse aplicativo lógico.

![Visão geral](./media/app-service-logic-monitor-your-logic-apps/overview.png)

Há algumas seções nessa folha que são úteis:

* **Resumo** lista **Todas as execuções** e o **Histórico de Gatilho**
  * **Todas as execuções** executa o aplicativo lógico mais recente da lista.  Você pode clique em qualquer linha para obter detalhes sobre a execução ou clicar no bloco para listar mais execuções.
  * **Histórico de Gatilho** lista todas as atividades de gatilho para esse aplicativo lógico.  A atividade de gatilho pode ser uma verificação "Ignorada" para novos dados (por exemplo, verificando se um novo arquivo foi adicionado ao FTP), "Êxito", que significa que os dados foram retornados para disparar um aplicativo lógico ou "Falha" corresponde a um erro na configuração.
* **Diagnóstico** permite que você exiba os detalhes de tempo de execução e eventos, e assine [Alertas do Azure](#adding-azure-alerts)

> [!NOTE]
> Todos os eventos e os detalhes de tempo de execução são criptografados em repouso no serviço Aplicativo Lógico. Eles só serão descriptografados após uma solicitação de exibição de um usuário. O acesso a esses eventos também pode ser controlado pelo RBAC (Controle de Acesso Baseado em Função do Azure).
> 
> 

### <a name="view-the-run-details"></a>Exibir os detalhes de execução
Esta lista de execuções mostra o **Status**, a **Hora de Início** e a **Duração** da execução específica. Clique em qualquer linha para ver os detalhes da execução.

A exibição de monitoramento mostra cada etapa da execução, as entradas e saídas e qualquer mensagem de erro que possa ter ocorrido.

![Execução e ações](./media/app-service-logic-monitor-your-logic-apps/monitor-view.png)

Se você precisar de outros detalhes como a **ID de Correlação** da execução (que pode ser usada para a API REST), clique no botão **Detalhes da Execução**.  Isso inclui todas as etapas, status e entradas/saídas da execução.

## <a name="azure-diagnostics-and-alerts"></a>Diagnósticos e alertas do Azure
Além dos detalhes fornecidos pelo Portal do Azure e pela API REST acima, você pode configurar seu aplicativo lógico para usar o Diagnóstico do Azure para obter detalhes mais avançados e depuração.

1. Clique na seção **Diagnóstico** da folha do aplicativo lógico
2. Clique para definir as **Configurações de Diagnóstico**
3. Configurar um Hub de Eventos ou a Conta de Armazenamento à qual emitir dados
   
    ![Configurações de Diagnóstico do Azure](./media/app-service-logic-monitor-your-logic-apps/diagnostics.png)

### <a name="adding-azure-alerts"></a>Adicionar Alertas do Azure
Após a configuração do diagnóstico, você pode adicionar Alertas do Azure a serem disparados quando determinados limites forem atingidos.  Na folha **Diagnóstico**, selecione o bloco **Alertas** e **Adicionar alerta**.  Isso explicará como configurar um alerta com base em um número de limites e métricas.

![Métricas de Alerta do Azure](./media/app-service-logic-monitor-your-logic-apps/alerts.png)

Você pode configurar a **Condição**, **Limite** e **Período** conforme desejado.  Por fim, você pode configurar um endereço de email para enviar uma notificação ou configurar um webhook.  Você pode usar o [gatilho de solicitação](../connectors/connectors-native-reqres.md) em um aplicativo lógico para executar em um alerta também (fazer coisas como [publicar no Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app), [enviar um texto](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app) ou [adicionar uma mensagem a uma fila](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)).

### <a name="azure-diagnostics-settings"></a>Configurações de Diagnóstico do Azure
Cada um desses eventos contém detalhes sobre o aplicativo lógico e status como evento.  Veja um exemplo de um evento *ActionCompleted* :

```javascript
{
            "time": "2016-07-09T17:09:54.4773148Z",
            "workflowId": "/SUBSCRIPTIONS/80D4FE69-ABCD-EFGH-A938-9250F1C8AB03/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP",
            "resourceId": "/SUBSCRIPTIONS/80D4FE69-ABCD-EFGH-A938-9250F1C8AB03/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP/RUNS/08587361146922712057/ACTIONS/HTTP",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-06-01",
                "startTime": "2016-07-09T17:09:53.4336305Z",
                "endTime": "2016-07-09T17:09:53.5430281Z",
                "status": "Succeeded",
                "code": "OK",
                "resource": {
                    "subscriptionId": "80d4fe69-ABCD-EFGH-a938-9250f1c8ab03",
                    "resourceGroupName": "MyResourceGroup",
                    "workflowId": "cff00d5458f944d5a766f2f9ad142553",
                    "workflowName": "MyLogicApp",
                    "runId": "08587361146922712057",
                    "location": "eastus",
                    "actionName": "Http"
                },
                "correlation": {
                    "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
                    "clientTrackingId": "my-custom-tracking-id"
                },
                "trackedProperties": {
                    "myProperty": "<value>"
                }
            }
        }
```

As duas propriedades especialmente úteis para controlar e monitorar são *clientTrackingId* e *trackedProperties*.  

#### <a name="client-tracking-id"></a>ID de rastreamento do cliente
A ID de rastreamento do cliente é um valor que correlacionará eventos em uma execução de aplicativo lógico, incluindo quaisquer fluxos de trabalho aninhados chamados como parte de um aplicativo lógico.  Essa ID será gerada automaticamente se não for fornecida, mas você pode especificar manualmente a ID de rastreamento do cliente de um gatilho passando um cabeçalho `x-ms-client-tracking-id` com o valor da ID na solicitação do gatilho (gatilho de solicitação, gatilho HTTP ou gatilho de webhook).

#### <a name="tracked-properties"></a>Propriedades rastreadas
É possível adicionar propriedades rastreadas às ações na definição do fluxo de trabalho a fim de rastrear entradas ou saídas em dados de diagnóstico.  Isso pode ser útil se você quiser controlar dados como uma "ID de pedido" em sua telemetria.  Para adicionar uma propriedade rastreada, inclua a propriedade `trackedProperties` em uma ação.  As propriedades rastreadas só podem rastrear entradas e saídas de uma única ação, mas você pode usar as propriedades `correlation` dos eventos para correlacionar entre as ações em uma execução.

```javascript
{
    "myAction": {
        "type": "http",
        "inputs": {
            "uri": "http://uri",
            "headers": {
                "Content-Type": "application/json"
            },
            "body": "@triggerBody()"
        },
        "trackedProperties":{
            "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
            "myActionHTTPValue": "@action()['outputs']['body']['foo']",
            "transactionId": "@action()['inputs']['body']['bar']"
        }
    }
}
```

### <a name="extending-your-solutions"></a>Estendendo as soluções
Você pode aproveitar essa telemetria do Hub de Eventos ou Armazenamento em outros serviços, como o [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite), o [Stream Analytics do Azure](https://azure.microsoft.com/services/stream-analytics/) e o [Power BI](https://powerbi.com), para monitorar em tempo real seus fluxos de trabalho de integração.

## <a name="next-steps"></a>Próximas etapas
* [Exemplos comuns e cenários de aplicativos lógicos](app-service-logic-examples-and-scenarios.md)
* [Criando um Modelo de Implantação do Aplicativo Lógico](app-service-logic-create-deploy-template.md)
* [Recursos de integração corporativa](app-service-logic-enterprise-integration-overview.md)




<!--HONumber=Nov16_HO3-->


