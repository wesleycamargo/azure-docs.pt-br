<properties
	pageTitle="Azure Insights: use logs de auditoria para enviar notificações de alerta por email e webhook no Azure Insights. | Microsoft Azure"
	description="Veja como usar entradas de log de auditoria de serviço para chamar URLs da Web ou enviar notificações por email no Azure Insights."
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
	ms.date="03/30/2016"
	ms.author="ashwink"/>

# Use logs de auditoria para enviar notificações de alerta por email e webhook no Azure Insights

Este artigo mostra o esquema de carga quando um evento de log de auditoria dispara um webhook e descreve como você pode enviar emails usando o mesmo evento.

>[AZURE.NOTE] Esse recurso está atualmente em Visualização. Nos próximos meses, a infraestrutura e o desempenho do Alerta em Eventos serão melhorados. Nesta visualização, este recurso só está acessível usando o Azure PowerShell e CLI. O acesso ao mesmo recurso usando o Portal do Azure estará disponível posteriormente.

## Webhooks
Os webhooks permitem rotear as notificações de alerta do Azure para outros sistemas para pós-processamento ou notificações personalizadas. Por exemplo, rotear o alerta para serviços que possam lidar com uma solicitação da Web de entrada para enviar SMS, registrar bugs ou notificar alguém por meio de serviços de chat/mensagens. O URI do webhook deve ser um ponto de extremidade HTTP ou HTTPS válido.

## Email
Emails podem ser enviados para qualquer endereço de email válido. Os administradores e coadministradores da assinatura em que a regra está em execução também serão notificados.

### Regra de email de exemplo
Você deve configurar uma regra de email, uma regra de webhook e, em seguida, dizer às regras para iniciar quando ocorrer o evento de log de auditoria. Você pode ver um exemplo usando o PowerShell em [Exemplos de início rápido do PowerShell do Azure Insights](insights-powershell-samples.md#alert-on-audit-log-event).


## Autenticação
Há duas formas de URI de autenticação:

1. Autenticação baseada em token, salvando o URI do webhook com uma ID de token como um parâmetro de consulta. Por exemplo, https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue
2. Autenticação básica usando uma ID de usuário e senha. Por exemplo, https://userid:password@mysamplealert/webcallback?someparamater=somevalue&parameter=value

## Esquema de carga do webhook de notificação do evento de log de auditoria
Quando um novo evento estiver disponível, o alerta sobre eventos de log de auditoria executará o webhook configurado com metadados de evento na carga do webhook. O exemplo a seguir mostra o esquema de carga do webhook:

```
{
        "status": "Activated",
        "context": {
                "resourceProviderName": "Microsoft.Web",
                "event": {
                        "$type": "Microsoft.WindowsAzure.Management.Monitoring.Automation.Notifications.GenericNotifications.Datacontracts.InstanceEventContext, Microsoft.WindowsAzure.Management.Mon.Automation",
                        "authorization": {
                                "action": "Microsoft.Web/sites/start/action",
                                "scope": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest"
                        },
                        "eventDataId": "327caaca-08d7-41b1-86d8-27d0a7adb92d",
                        "category": "Administrative",
                        "caller": "myname@mycompany.com",
                        "httpRequest": {
                                "clientRequestId": "f58cead8-c9ed-43af-8710-55e64def208d",
                                "clientIpAddress": "104.43.166.155",
                                "method": "POST"
                        },
                        "status": "Succeeded",
                        "subStatus": "OK",
                        "level": "Informational",
                        "correlationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "eventDescription": "",
                        "operationName": "Microsoft.Web/sites/start/action",
                        "operationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "properties": {
                                "$type": "Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage",
                                "statusCode": "OK",
                                "serviceRequestId": "f7716681-496a-4f5c-8d14-d564bcf54714"
                        }
                },
                "timestamp": "Friday, March 11, 2016 9:13:23 PM",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/alertonevent2",
                "name": "alertonevent2",
                "description": "test alert on event start",
                "conditionType": "Event",
                "subscriptionId": "s1",
                "resourceId": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest",
                "resourceGroupName": "rg1"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```

|Nome do elemento|	Descrição|
|---|---|
|status |Sempre definido como "ativado"|
|context|Contexto do evento|
|resourceProviderName|O provedor de recursos do recurso afetado|
|conditionType |"Evento"|
|name |Nome da regra de alerta|
|ID |ID do recurso do alerta|
|description|	Descrição definida no alerta pelo criador do alerta|
|subscriptionId |GUID da assinatura do Azure|
|timestamp|	Carimbo de data/hora quando o evento foi gerado pelo serviço do Azure que processou a solicitação correspondente ao evento|
|resourceId |URI da ID do recurso que identifica o recurso de forma exclusiva|
|resourceGroupName|Resource-group-name do recurso afetado|
|propriedades |Conjunto de pares <Key  Value> (ou seja, Dicionário<String  String>) que inclui detalhes sobre o evento|
|evento|Elemento que contém metadados sobre o evento|
|autorização|Captura as propriedades RBAC do evento. Isso geralmente inclui a “ação”, a “função” e o “escopo”.|
|categoria | Categoria do evento. Os valores com suporte são: Administrativo, Alerta, Segurança, ServiceHealth, Recomendação|
|chamador|Endereço de email do usuário que realizou a operação, a declaração de UPN ou a declaração de SPN com base na disponibilidade. Pode ser nulo para determinadas chamadas do sistema.|
|correlationId|	Geralmente um GUID no formato de cadeia de caracteres. Eventos com correlationId pertencem à mesma ação maior e geralmente compartilham a mesma correlationId.|
|eventDescription |Um texto estático que descreve um evento|
|eventDataId|Identificador exclusivo de um evento|
|eventSource |Nome do serviço ou infraestrutura do Azure que gerou o evento|
|httpRequest|	Geralmente inclui a “clientRequestId”, o “clientIpAddress” e o “método” (método HTTP, como PUT, por exemplo).|
|level|Um dos seguintes valores: “Crítico”, “Erro”, “Aviso”, “Informativo” e “Detalhado”|
|operationId|Geralmente um GUID compartilhado entre os eventos correspondentes a uma única operação|
|operationName|O nome da operação|
|propriedades |O elemento dentro do elemento de evento que contém as propriedades do evento.|
|status|Cadeia de caracteres que descreve o status da operação. Os valores comuns incluem: Iniciado, Em Andamento, Êxito, Falha, Ativo, Resolvido|
|subStatus|	Geralmente inclui o código de status HTTP da chamada REST correspondente. Também pode incluir outras cadeias de caracteres que descrevam um substatus. Os valores de substatus comuns incluem: OK (Código de Status HTTP: 200), Criado (Código de Status HTTP: 201), Aceito (Código de Status HTTP: 202), Sem Conteúdo (Código de Status HTTP: 204), Solicitação Incorreta (Código de Status HTTP: 400), Não Encontrado (Código de Status HTTP: 404), Conflito (Código de Status HTTP: 409), Erro Interno do Servidor (Código de Status HTTP: 500), Serviço Indisponível (Código de Status HTTP: 503), Tempo Limite do Gateway (Código de Status HTTP: 504)|

<!---HONumber=AcomDC_0330_2016-->