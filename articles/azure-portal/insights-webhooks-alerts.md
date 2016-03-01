<properties
	pageTitle="Como configurar alertas do Azure para enviar para outros sistemas"
	description="Redirecionar alertas do Azure para outros sistemas que não são do Azure."
	authors="kamathashwin"
	manager=""
	editor=""
	services="azure-portal"
	documentationCenter="na"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/16/2016"
	ms.author="ashwink"/>

# Como configurar webhooks para alertas

Webhooks permitem ao usuário rotear as notificações de Alerta do Azure para outros sistemas para pós-processamento ou notificações personalizadas. Exemplos disso podem ser o roteamento do Alerta para serviços que podem lidar com uma solicitação da Web de entrada para enviar SMS, registrar bugs, notificar a equipe por meio de serviços de chat/mensagens etc.

O URI do webhook deve ser um ponto de extremidade HTTP ou HTTPS válido. O serviço de Alerta do Azure fará uma operação POST no webhook especificado, passando um esquema e conteúdo JSON específico.

## Configurando webhooks através do portal

Na tela Criar/Atualizar Alertas no [Portal do Azure](https://portal.azure.com/), você pode adicionar ou atualizar o URI do webhook.

![Adicionar uma Regra de alerta](./media/insights-webhooks-alerts/Alertwebhook.png)


## Autenticação

A autenticação pode ser de dois tipos:

1. **Autenticação baseada em token** - neste caso, você salvará o URI do webhook com uma ID de token, como **https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue*
2.	**Autenticação básica** - usando uma ID de usuário e senha: neste caso, você salvará o URI do webhook como **https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar*

## Esquema de conteúdo

A operação POST conterá o seguinte esquema e conteúdo JSON para todos os alertas baseados em métricas.

```
{
"status": "Activated",
"context": {
            "timestamp": "2015-08-14T22:26:41.9975398Z",
            "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
            "name": "ruleName1",
            "description": "some description",
            "conditionType": "Metric",
            "condition": {
                        "metricName": "Requests",
                        "metricUnit": "Count",
                        "metricValue": "10",
                        "threshold": "10",
                        "windowSize": "15",
                        "timeAggregation": "Average",
                        "operator": "GreaterThanOrEqual"
                },
            "subscriptionId": "s1",
            "resourceGroupName": "useast",                                
            "resourceName": "mysite1",
            "resourceType": "microsoft.foo/sites",
            "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
            "resourceRegion": "centralus",
            "portalLink": “https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1”                                
},
"properties": {
              "key1": "value1",
              "key2": "value2"
              }
}
```

>[AZURE.NOTE] Em nossa próxima atualização, adicionaremos suporte para os alertas de Eventos ("conditionType": "Event")


| Campo | Obrigatório? | Conjunto fixo de valor(es)? | Observações |
| :-------------| :-------------   | :-------------   | :-------------   |
|status|S|“Activated”, “Resolved”|É como você descobre o tipo de alerta. O Azure envia automaticamente alertas ativados e resolvidos para a condição definida.|
|context| S | | O contexto do alerta|
|timestamp| S | | A hora em que o alerta foi disparado. O alerta é disparado assim que a métrica é lida no armazenamento de diagnóstico.|
|ID | S | | Cada regra de alerta tem uma ID exclusiva.|
|name|S | |
|description |S | |Descrição do alerta.|
|conditionType |S |“Metric”, “Event” |Há suporte para dois tipos de alertas. Um baseado em métrica e outro baseado em evento. No futuro, daremos suporte a alertas para Eventos, então use este valor para verificar se o alerta se baseia em métrica ou evento|
|condition |S | |Terá os campos específicos de verificação com base no conditionType|
|metricName |para alertas de Métrica | |O nome da métrica define o que a regra monitora.|
|metricUnit |para alertas de Métrica |"Bytes", "BytesPerSecond" , "Count" , "CountPerSecond" , "Percent", "Seconds"|	 A unidade permitida na métrica. Valores permitidos: https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx|
|metricValue |para alertas de Métrica | |O valor real da métrica que causou o alerta|
|threshold |para alertas de Métrica | |O valor de limite que ativa o alerta|
|windowSize |para alertas de Métrica | |O período de tempo que é usado para monitorar a atividade de alerta com base no limite. Deve estar entre 5 minutos e 1 dia. Formato de duração ISO 8601.|
|timeAggregation |para alertas de Métrica |"Average", "Last" , "Maximum" , "Minimum" , "None", "Total" |	Como os dados coletados devem ser combinados ao longo do tempo. O valor padrão é Average. Valores permitidos: https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx|
|operator |para alertas de Métrica | |O operador usado para comparar os dados e o limite.|
|subscriptionId |S | |GUID da assinatura do Azure|
|resourceGroupName |S | |resource-group-name do recurso afetado|
|resourceName |S | |nome do recurso afetado|
|resourceType |S | |tipo do recurso afetado|
|resourceId |S | |URI de ID do recurso que identifica exclusivamente o recurso|
|resourceRegion |S | |região/local do recurso que é afetado|
|portalLink |S | |link direto do Portal do Azure para a página de resumo de recursos|
|propriedades |N |Opcional |É um conjunto de pares <Key  Value> (ou seja, Dicionário<String  String>) que inclui detalhes sobre o evento. O campo de propriedades é opcional. Em um fluxo de trabalho personalizado baseado em aplicativo lógico ou UI, os usuários podem inserir chaves/valores que podem ser transmitidos por meio do conteúdo. O modo alternativo para transmitir as propriedades personalizadas para o webhook é por meio do próprio URI do webhook (como parâmetros de consulta)|


>[AZURE.NOTE] Você não pode usar o campo de propriedades por meio do Portal. Em nossa próxima versão do SDK do Insights, você poderá definir as propriedades por meio da API de Alerta.

## Próximas etapas

Para obter informações adicionais, veja mais sobre os Alertas do Azure e webhooks no vídeo [Integrar Alertas do Azure com o PagerDuty](http://go.microsoft.com/fwlink/?LinkId=627080)

Para saber como criar programaticamente um webhook, consulte [Criar um alerta com Webhooks usando o SDK do Azure Insights (C#)](https://code.msdn.microsoft.com/Create-Azure-Alerts-with-b938077a).

Depois de configurar seus webhooks e Alertas, explore essas outras opções para iniciar um script de automação.

[Exemplos de scripts da Automação do Azure (Runbooks)](http://go.microsoft.com/fwlink/?LinkId=627081)

Use os Alertas do Azure para enviar mensagens para outros serviços. Use os modelos de exemplo a seguir para começar.

[Usar Aplicativo Lógico para enviar SMS por meio da API do Twilio](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)

[Usar Aplicativo Lógico para enviar mensagens de Margem de atraso](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)

[Usar Aplicativo Lógico para enviar mensagens para uma Fila do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<!---HONumber=AcomDC_0218_2016-->