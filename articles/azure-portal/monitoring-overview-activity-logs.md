<properties
	pageTitle="Visão geral do Log de Atividades do Azure | Microsoft Azure"
	description="Saiba o que é o Log de Atividades do Azure e como usá-lo para compreender os eventos que ocorrem dentro de sua assinatura do Azure."
	authors="johnkemnetz"
	manager="rboucher"
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2016"
	ms.author="johnkem"/>

# Visão geral do Log de Atividades do Azure
O **Log de Atividades do Azure** é um log que fornece informações sobre as operações executadas em recursos em sua assinatura. O Log de Atividades era conhecido como "Logs de Auditoria" ou "Logs Operacionais", pois ele relata eventos de plano de controle de suas assinaturas. Com o Log de Atividades, você pode determinar “o que, quem e quando” sobre qualquer operação de gravação (PUT, POST, DELETE) executadas nos recursos em sua assinatura, além de compreender o status da operação e outras propriedades relevantes. O Log de Atividades não inclui operações de leitura (GET).

O Log de Atividades difere dos [Logs de Diagnóstico](./monitoring-overview-of-diagnostic-logs.md), que são todos os logs emitidos por um recurso. Esses logs fornecem dados sobre a operação desse recurso, em vez de operações nesse recurso.

Você pode recuperar os eventos de seu Log de Atividade usando o Portal do Azure, a CLI, cmdlets do PowerShell e a API REST do Insights.

## O que você pode fazer com o Log de Atividades
Veja algumas coisas que você pode fazer com o Log de Atividades:

- Consultar e exibi-lo no **Portal do Azure**.
- Consultá-lo por meio da API REST, do Cmdlet do PowerShell ou da CLI.
- [Criar um alerta de email ou webhook que dispara um evento do Log de Atividades.](./insights-auditlog-to-webhook-email.md)
- Salvá-lo em uma **Conta de Armazenamento** para inspeção manual ou arquivamento. Você pode especificar o tempo de retenção (em dias) usando os **Perfis de Log**.
- Analisá-lo no Power BI usando o [**Pacote de conteúdo do PowerBI**](https://powerbi.microsoft.com/pt-BR/documentation/powerbi-content-pack-azure-audit-logs/).
- [Transmiti-lo para um **Hub de Eventos**](./monitoring-stream-activity-logs-event-hubs.md) para ingestão por um serviço de terceiro ou solução de análise personalizada, como o Power BI.

## Exportar o Log de Atividades com Perfis de Log
Um **Perfil de Log** controla o modo de exportação de seu Log de Atividades. Com um Perfil de Log, você pode configurar:

- Aonde o Log de Atividades deve ser enviado (Conta de Armazenamento ou Hubs de Eventos)
- Quais categorias de evento (por exemplo, Gravação, Exclusão, Ação) devem ser enviadas
- Quais regiões (locais) devem ser exportados
- Por quanto tempo o Log de Atividades deve ser mantido em uma Conta de Armazenamento – uma retenção de zero dias significa que os logs serão mantidos para sempre. Se as políticas de retenção são definidas, mas o armazenamento dos logs em uma Conta de Armazenamento está desabilitado (por exemplo, se apenas as opções dos Hubs de Eventos ou OMS estão selecionadas), as políticas de retenção não têm nenhum efeito.

Essas configurações podem ser definidas por meio da opção "Exportar" na folha do Log de Atividades no portal, ou [usando programaticamente a API REST](https://msdn.microsoft.com/library/azure/dn931927.aspx), os cmdlets do PowerShell ou a CLI. Uma assinatura pode ter somente um perfil de log.

### Configurar os perfis de log usando o Portal do Azure
Você pode transmitir o Log de Atividades para um Hub de Eventos ou armazená-lo em uma Conta de Armazenamento usando a opção "Exportar" no Portal do Azure.

1. Navegue até a folha **Log de Atividades** usando o menu no lado esquerdo do portal.

    ![Navegue até o Log de Atividades no portal](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Clique no botão **Exportar** na parte superior da folha.

    ![Botão Exportar no portal](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. Na folha que aparece, você pode selecionar as regiões para as quais você deseja exportar eventos, a Conta de Armazenamento na qual você gostaria de salvar eventos (bem como o número de dias para manter esses eventos no armazenamento) e o Namespace do Barramento de Serviço no qual você gostaria de criar um Hub de Eventos para transmissão desses eventos.

    ![Folha Exportar Log de Atividades](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Clique em **Salvar** para salvar as configurações. As configurações serão aplicadas imediatamente à sua assinatura.

### Configurar os perfis de log usando Cmdlets do Azure PowerShell
#### Obter o perfil de log existente
```
Get-AzureRmLogProfile
```

#### Adicionar um perfil de log
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

| Propriedade | Obrigatório | Descrição |
|------------------|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nome | Sim | Nome de seu perfil de log. |
| StorageAccountId | Não | ID de recursos da Conta de Armazenamento na qual o Log de Atividades deve ser salvo. |
| serviceBusRuleId | Não | ID da Regra de Barramento de Serviço para o namespace do Barramento de Serviço no qual você gostaria que os hubs de eventos fossem criados. Será uma cadeia de caracteres com este formato: `{service bus resource ID}/authorizationrules/{key name}`. |
| Locais | Sim | Lista separada por vírgulas de regiões para as quais você gostaria de coletar eventos do Log de Atividades. |
| RetentionInDays | Sim | Número de dias durante os quais os eventos devem ser mantidos. Um valor de zero armazenará os logs indefinidamente. |
| Categorias | Não | Lista separada por vírgulas de categorias de eventos que devem ser coletados. Os valores possíveis são Gravação, Exclusão e Ação. |

#### Remover um perfil de log
```
Remove-AzureRmLogProfile -name my_log_profile
```

### Configurar os perfis de log usando a CLI de plataforma cruzada do Azure
#### Obter o perfil de log existente
```
azure insights logprofile list
```
```
azure insights logprofile get --name my_log_profile
```
A propriedade `name` deve ser o nome de seu perfil de log.

#### Adicionar um perfil de log
``` 
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

| Propriedade | Obrigatório | Descrição |
|------------------|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| name | Sim | Nome de seu perfil de log. |
| storageId | Não | ID de recursos da Conta de Armazenamento na qual o Log de Atividades deve ser salvo. |
| serviceBusRuleId | Não | ID da Regra de Barramento de Serviço para o namespace do Barramento de Serviço no qual você gostaria que os hubs de eventos fossem criados. Será uma cadeia de caracteres com este formato: `{service bus resource ID}/authorizationrules/{key name}`. |
| locais | Sim | Lista separada por vírgulas de regiões para as quais você gostaria de coletar eventos do Log de Atividades. |
| retentionInDays | Sim | Número de dias durante os quais os eventos devem ser mantidos. Um valor de zero armazena os logs indefinidamente. |
| categorias | Não | Lista separada por vírgulas de categorias de eventos que devem ser coletados. Os valores possíveis são Gravação, Exclusão e Ação. |

#### Remover um perfil de log
```
azure insights logprofile delete --name my_log_profile
```

## Esquema do evento
Cada evento no Log de Atividades tem um blob JSON como este:

```
{
  "value": [ {
    "authorization": {
      "action": "microsoft.support/supporttickets/write",
      "role": "Subscription Admin",
      "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841"
    },
    "caller": "admin@contoso.com",
    "channels": "Operation",
    "claims": {
      "aud": "https://management.core.windows.net/",
      "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
      "iat": "1421876371",
      "nbf": "1421876371",
      "exp": "1421880271",
      "ver": "1.0",
      "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
      "puid": "20030000801A118C",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
      "name": "John Smith",
      "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
      "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
      "appidacr": "2",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.microsoft.com/claims/authnclassreference": "1"
    },
    "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
    "description": "",
    "eventDataId": "44ade6b4-3813-45e6-ae27-7420a95fa2f8",
    "eventName": {
      "value": "EndRequest",
      "localizedValue": "End request"
    },
    "eventSource": {
      "value": "Microsoft.Resources",
      "localizedValue": "Microsoft Resources"
    },
    "httpRequest": {
      "clientRequestId": "27003b25-91d3-418f-8eb1-29e537dcb249",
      "clientIpAddress": "192.168.35.115",
      "method": "PUT"
    },
    "id": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
    "level": "Informational",
    "resourceGroupName": "MSSupportGroup",
    "resourceProviderName": {
      "value": "microsoft.support",
      "localizedValue": "microsoft.support"
    },
    "resourceUri": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
    "operationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
    "operationName": {
      "value": "microsoft.support/supporttickets/write",
      "localizedValue": "microsoft.support/supporttickets/write"
    },
    "properties": {
      "statusCode": "Created"
    },
    "status": {
      "value": "Succeeded",
      "localizedValue": "Succeeded"
    },
    "subStatus": {
      "value": "Created",
      "localizedValue": "Created (HTTP Status Code: 201)"
    },
    "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
    "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
    "subscriptionId": "s1"
  } ],
"nextLink": "https://management.azure.com/########-####-####-####-############$skiptoken=######"
}
```

| Nome do elemento | Descrição |
|----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| authorization | Blob de propriedades RBAC do evento. Geralmente, inclui as propriedades "action", "role" e "scope". |
| chamador | Endereço de email do usuário que realizou a operação, declaração UPN ou declaração SPN com base na disponibilidade. |
| canais | Um dos seguintes valores: "Admin", "Operação" |
| correlationId | Geralmente, um GUID no formato de cadeia de caracteres. Os eventos que compartilham um correlationId pertencem à mesma ação superior. |
| description | Descrição de texto estático de um evento. |
| eventDataId | Identificador exclusivo de um evento. |
| eventSource | Nome do serviço ou infraestrutura do Azure que gerou esse evento. |
| httpRequest | Blob que descreve a solicitação Http. Geralmente inclui a “clientRequestId”, o “clientIpAddress” e o “método” (método HTTP, como PUT, por exemplo). |
| level | Nível do evento. Um dos seguintes valores: “Crítico”, “Erro”, “Aviso”, “Informativo” e “Detalhado” |
| resourceGroupName | Nome do grupo de recursos do recurso afetado. |
| resourceProviderName | Nome do provedor de recursos do recurso afetado |
| resourceUri | Id de recurso do recurso afetado. |
| operationId | Um GUID compartilhado entre os eventos que correspondem a uma única operação. |
| operationName | Nome da operação. |
| propriedades | Conjunto de pares de `<Key, Value>` (ou seja, Dicionário) que descreve os detalhes do evento. |
| status | Cadeia de caracteres que descreve o status da operação. Alguns valores comuns são: Iniciado, Em Andamento, Êxito, Falha, Ativo, Resolvido. |
| subStatus | Geralmente, o código de status HTTP da chamada REST correspondente, mas também pode incluir outras cadeias de caracteres que descrevam um substatus, como estes valores comuns: OK (Código de Status HTTP: 200), Criado (Código de Status HTTP: 201), Aceito (Código de Status HTTP: 202), Sem Conteúdo (Código de Status HTTP: 204), Solicitação Incorreta (Código de Status HTTP: 400), Não Encontrado (Código de Status HTTP: 404), Conflito (Código de Status HTTP: 409), Erro de Servidor Interno (Código de Status HTTP: 500), Serviço Indisponível (Código de Status HTTP: 503), Tempo Limite do Gateway (Código de Status HTTP: 504). |
| eventTimestamp | Carimbo de hora quando o evento foi gerado pelo serviço do Azure que está processando a solicitação correspondente ao evento. |
| submissionTimestamp | Carimbo de hora quando o evento tornou-se disponível para consulta. |
| subscriptionId | ID de Assinatura do Azure. |
| nextLink | Token de continuação para buscar o próximo conjunto de resultados quando eles forem divididos em várias respostas. Isso normalmente será o caso, se houver mais de 200 registros. |

## Próximas etapas
- [Saiba mais sobre o Log de Atividades (anteriormente conhecido como Logs de Auditoria)](../resource-group-audit.md)
- [Transmissão do Log de Atividades do Azure para os Hubs de Eventos](./monitoring-stream-activity-logs-event-hubs.md)

<!---HONumber=AcomDC_0817_2016-->