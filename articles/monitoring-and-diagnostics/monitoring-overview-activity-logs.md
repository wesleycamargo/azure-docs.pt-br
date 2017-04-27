---
title: "Visão geral do Log de Atividades do Azure | Microsoft Docs"
description: "Saiba o que é o Log de Atividades do Azure e como usá-lo para compreender os eventos que ocorrem dentro de sua assinatura do Azure."
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: c274782f-039d-4c28-9ddb-f89ce21052c7
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: johnkem
translationtype: Human Translation
ms.sourcegitcommit: f41fbee742daf2107b57caa528e53537018c88c6
ms.openlocfilehash: c123b76b0e4c95cfebcc79063fb1c3a27efc8646
ms.lasthandoff: 03/31/2017


---
# <a name="overview-of-the-azure-activity-log"></a>Visão geral do Log de Atividades do Azure
O **Log de Atividades do Azure** é um log que fornece informações sobre as operações executadas em recursos em sua assinatura. O Log de Atividades era conhecido como "Logs de Auditoria" ou "Logs Operacionais", pois ele relata eventos de plano de controle de suas assinaturas. Usando o Log de Atividades, você pode determinar 'o que, quem e quando' para quaisquer operações de gravação (PUT, POST, DELETE) executadas nos recursos em sua assinatura. Também é possível compreender o status da operação e outras propriedades relevantes. O Log de Atividades não inclui operações de leitura (GET) ou operações para recursos que usam o modelo Clássico/"RDFE".

![Logs de Atividade X outros tipos de logs ](./media/monitoring-overview-activity-logs/Activity_Log_vs_other_logs_v5.png)

Figura 1: Logs de Atividade X outros tipos de logs

O Log de Atividades difere dos [Logs de Diagnóstico](monitoring-overview-of-diagnostic-logs.md). Os Logs de Atividade fornecem dados externos sobre as operações em um recurso. Os Logs de Diagnóstico são emitidos por um recurso e fornecem informações sobre a operação do recurso.

Você pode recuperar os eventos de seu Log de Atividade usando o Portal do Azure, a CLI, cmdlets do PowerShell e a API REST do Azure Monitor.


> [!WARNING]
> O Log de Atividades do Azure é usado principalmente para atividades que ocorrem no Azure Resource Manager. Ele não controla os recursos usando o modelo Clássico/RDFE. Alguns tipos de recursos Clássicos têm um provedor de recursos de proxy no Azure Resource Manager (por exemplo, Microsoft.ClassicCompute). Se você interagir com um tipo de recurso Clássico por meio do Azure Resource Manager usando esses provedores de recursos de proxy, as operações aparecerão no Log de Atividades. Se você interagir com um tipo de recurso clássico no portal Clássico ou fora dos proxies do Azure Resource Manager, suas ações somente serão registradas no Log de Operação. O Log de Operação só pode ser acessado no portal Clássico.
>
>

Exiba o vídeo de introdução do Log de Atividades a seguir.
[! VIDEO https://channel9.msdn.com/Blogs/Seth-Juarez/Logs-John-Kemnetz/player]

## <a name="what-you-can-do-with-the-activity-log"></a>O que você pode fazer com o Log de Atividades
Veja algumas coisas que você pode fazer com o Log de Atividades:

![Log de Atividades do Azure](./media/monitoring-overview-activity-logs/Activity_Log_Overview_v3.png)


* [Crie um alerta que dispara um evento do Log de Atividades.](monitoring-activity-log-alerts.md)
* [Transmiti-lo para um **Hub de Eventos**](monitoring-stream-activity-logs-event-hubs.md) para ingestão por um serviço de terceiro ou solução de análise personalizada, como o PowerBI.
* Analisá-lo no PowerBI usando o [**Pacote de conteúdo do PowerBI**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
* [Salvá-lo em uma **Conta de Armazenamento** para inspeção manual ou arquivamento](monitoring-archive-activity-log.md). Você pode especificar o tempo de retenção (em dias) usando os **Perfis de Log**.
* Consultar e exibi-lo no **Portal do Azure**.
* Consultar por meio de Cmdlet do PowerShell, da CLI ou da API REST.


Você pode usar uma conta de armazenamento ou um namespace de hub de eventos que não esteja na mesma assinatura para emitir logs. O usuário que define a configuração deve ter o devido acesso RBAC para ambas as assinaturas.

## <a name="export-the-activity-log-with-log-profiles"></a>Exportar o Log de Atividades com Perfis de Log
Um **Perfil de Log** controla o modo de exportação de seu Log de Atividades. Com um Perfil de Log, você pode configurar:

* Aonde o Log de Atividades deve ser enviado (Conta de Armazenamento ou Hubs de Eventos)
* Quais categorias de evento (Gravação, Exclusão, Ação) devem ser enviadas. *O significado de "categoria" em Perfis de Log e eventos de Log de Atividades é diferente. No Perfil de Log, "Category" representa o tipo de operação (Gravar, Excluir, Ação). Em um evento do Log de Atividades, a propriedade "category" representa a origem ou o tipo de evento (por exemplo, Administração, Serviço de Integridade, Alerta e muito mais).*
* Quais regiões (locais) devem ser exportados
* Quanto tempo o Log de Atividades deve ser mantido em uma Conta de Armazenamento.
    - Uma retenção de zero dias significa que os registros serão mantidos indefinidamente. O valor pode ser qualquer quantidade de dias, entre 1 e 2147483647.
    - Se as políticas de retenção são definidas, mas o armazenamento dos logs em uma Conta de Armazenamento está desabilitado (por exemplo, se apenas as opções Hubs de Eventos ou OMS estão selecionadas), as políticas de retenção não têm nenhum efeito.
    - As políticas de retenção são aplicadas por dia, para que, ao final de um dia (UTC), os logs do dia após a política de retenção sejam excluídos. Por exemplo, se você tiver uma política de retenção de um dia, no início do dia de hoje, os logs de anteontem serão excluídos.

Essas configurações podem ser definidas por meio da opção "Exportar" na folha do Log de Atividades no portal. Elas também podem ser definidas por meio de programação [usando a API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931927.aspx), de cmdlets do PowerShell ou da CLI. Uma assinatura pode ter somente um perfil de log.

### <a name="configure-log-profiles-using-the-azure-portal"></a>Configurar os perfis de log usando o Portal do Azure
Você pode transmitir o Log de Atividades para um Hub de Eventos ou armazená-lo em uma Conta de Armazenamento usando a opção "Exportar" no Portal do Azure.

1. Navegue até a folha **Log de Atividades** usando o menu no lado esquerdo do portal.

    ![Navegue até o Log de Atividades no portal](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Clique no botão **Exportar** na parte superior da folha.

    ![Botão Exportar no portal](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. Na folha que aparece, você pode selecionar:  
  * regiões para as quais você deseja exportar eventos
  * a conta de armazenamento na qual você deseja salvar os eventos
  * o número de dias para manter esses eventos no armazenamento. Uma configuração de 0 dias retém os logs para sempre.
  * o namespace do Barramento de Serviço no qual você deseja que um Hub de Eventos seja criado para transmitir esses eventos.

     ![Folha Exportar Log de Atividades](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Clique em **Salvar** para salvar as configurações. As configurações serão aplicadas imediatamente à sua assinatura.

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>Configurar os perfis de log usando Cmdlets do Azure PowerShell
#### <a name="get-existing-log-profile"></a>Obter o perfil de log existente
```
Get-AzureRmLogProfile
```

#### <a name="add-a-log-profile"></a>Adicionar um perfil de log
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

| Propriedade | Obrigatório | Descrição |
| --- | --- | --- |
| Nome |Sim |Nome de seu perfil de log. |
| StorageAccountId |Não |ID de recursos da Conta de Armazenamento na qual o Log de Atividades deve ser salvo. |
| serviceBusRuleId |Não |ID da Regra de Barramento de Serviço para o namespace do Barramento de Serviço no qual você gostaria que os hubs de eventos fossem criados. É uma cadeia de caracteres com este formato: `{service bus resource ID}/authorizationrules/{key name}`. |
| Locais |Sim |Lista separada por vírgulas de regiões para as quais você gostaria de coletar eventos do Log de Atividades. |
| RetentionInDays |Sim |Número de dias durante os quais os eventos devem ser mantidos, entre 1 e 2147483647. Um valor de zero armazena os logs indefinidamente (para sempre). |
| Categorias |Não |Lista separada por vírgulas de categorias de eventos que devem ser coletados. Os valores possíveis são Gravação, Exclusão e Ação. |

#### <a name="remove-a-log-profile"></a>Remover um perfil de log
```
Remove-AzureRmLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cross-platform-cli"></a>Configurar os perfis de log usando a CLI de plataforma cruzada do Azure
#### <a name="get-existing-log-profile"></a>Obter o perfil de log existente
```
azure insights logprofile list
```
```
azure insights logprofile get --name my_log_profile
```
A propriedade `name` deve ser o nome de seu perfil de log.

#### <a name="add-a-log-profile"></a>Adicionar um perfil de log
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

| Propriedade | Obrigatório | Descrição |
| --- | --- | --- |
| name |Sim |Nome de seu perfil de log. |
| storageId |Não |ID de recursos da Conta de Armazenamento na qual o Log de Atividades deve ser salvo. |
| serviceBusRuleId |Não |ID da Regra de Barramento de Serviço para o namespace do Barramento de Serviço no qual você gostaria que os hubs de eventos fossem criados. É uma cadeia de caracteres com este formato: `{service bus resource ID}/authorizationrules/{key name}`. |
| locais |Sim |Lista separada por vírgulas de regiões para as quais você gostaria de coletar eventos do Log de Atividades. |
| RetentionInDays |Sim |Número de dias durante os quais os eventos devem ser mantidos, entre 1 e 2147483647. Um valor de zero armazena os logs indefinidamente (para sempre). |
| Categorias |Não |Lista separada por vírgulas de categorias de eventos que devem ser coletados. Os valores possíveis são Gravação, Exclusão e Ação. |

#### <a name="remove-a-log-profile"></a>Remover um perfil de log
```
azure insights logprofile delete --name my_log_profile
```

## <a name="event-schema"></a>Esquema do evento
Cada evento no Log de Atividades tem um blob JSON semelhante a este exemplo:

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
| --- | --- |
| authorization |Blob de propriedades RBAC do evento. Geralmente, inclui as propriedades "action", "role" e "scope". |
| chamador |Endereço de email do usuário que realizou a operação, declaração UPN ou declaração SPN com base na disponibilidade. |
| canais |Um dos seguintes valores: "Admin", "Operação" |
| correlationId |Geralmente, um GUID no formato de cadeia de caracteres. Os eventos que compartilham um correlationId pertencem à mesma ação superior. |
| Descrição |Descrição de texto estático de um evento. |
| eventDataId |Identificador exclusivo de um evento. |
| eventSource |Nome do serviço ou infraestrutura do Azure que gerou esse evento. |
| httpRequest |Blob que descreve a solicitação Http. Geralmente inclui a “clientRequestId”, o “clientIpAddress” e o “método” (método HTTP. Por exemplo, PUT). |
| level |Nível do evento. Um dos seguintes valores: “Crítico”, “Erro”, “Aviso”, “Informativo” e “Detalhado” |
| resourceGroupName |Nome do grupo de recursos do recurso afetado. |
| resourceProviderName |Nome do provedor de recursos do recurso afetado |
| resourceUri |Id de recurso do recurso afetado. |
| operationId |Um GUID compartilhado entre os eventos que correspondem a uma única operação. |
| operationName |Nome da operação. |
| propriedades |Conjunto de pares de `<Key, Value>` (ou seja, um Dicionário) que descreve os detalhes do evento. |
| status |Cadeia de caracteres que descreve o status da operação. Alguns valores comuns são: Iniciado, Em Andamento, Êxito, Falha, Ativo, Resolvido. |
| subStatus |Geralmente, o código de status HTTP da chamada REST correspondente, mas também pode incluir outras cadeias de caracteres que descrevam um substatus, como estes valores comuns: OK (Código de Status HTTP: 200), Criado (Código de Status HTTP: 201), Aceito (Código de Status HTTP: 202), Sem Conteúdo (Código de Status HTTP: 204), Solicitação Incorreta (Código de Status HTTP: 400), Não Encontrado (Código de Status HTTP: 404), Conflito (Código de Status HTTP: 409), Erro de Servidor Interno (Código de Status HTTP: 500), Serviço Indisponível (Código de Status HTTP: 503), Tempo Limite do Gateway (Código de Status HTTP: 504). |
| eventTimestamp |Carimbo de hora quando o evento foi gerado pelo serviço do Azure que está processando a solicitação correspondente ao evento. |
| submissionTimestamp |Carimbo de hora quando o evento tornou-se disponível para consulta. |
| subscriptionId |ID de Assinatura do Azure. |
| nextLink |Token de continuação para buscar o próximo conjunto de resultados quando eles forem divididos em várias respostas. Normalmente necessário quando há mais de 200 registros. |

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre o Log de Atividades (anteriormente conhecido como Logs de Auditoria)](../azure-resource-manager/resource-group-audit.md)
* [Transmissão do Log de Atividades do Azure para os Hubs de Eventos](monitoring-stream-activity-logs-event-hubs.md)

