---
title: "Arquivar Logs de Diagnóstico do Azure | Microsoft Docs"
description: "Saiba como arquivar os Logs de Diagnóstico do Azure para retenção de longo prazo em uma conta de armazenamento."
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 3a55c73f-2ef3-45f3-8956-bcf9c0cb7e05
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2016
ms.author: johnkem
translationtype: Human Translation
ms.sourcegitcommit: 3e82377c36da1563931bc0301daa4534e6f82377
ms.openlocfilehash: e3413236445f3f6034a228dc93624d3fdc9b87ad


---
# <a name="archive-azure-diagnostic-logs"></a>Arquivar Logs de Diagnóstico do Azure
Neste artigo, mostraremos como você pode usar o Portal do Azure, os cmdlets do PowerShell, CLI ou API REST para arquivar seu [Log de Diagnósticos do Azure](monitoring-overview-of-diagnostic-logs.md) em uma conta de armazenamento. Essa opção será útil se você quiser manter seu Log de Diagnósticos com uma política de retenção opcional para auditoria, análise estática ou backup. A conta de armazenamento não precisa estar na mesma assinatura que o recurso que emite os logs, contanto que o usuário que define a configuração tenha acesso RBAC apropriado a ambas as assinaturas.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, você precisará [criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) na qual é possível arquivar o seu Logs de Diagnósticos. É altamente recomendável que você não use uma conta de armazenamento existente que tenha outros dados sem monitoramento armazenados para que você possa controlar melhor o acesso aos dados de monitoramento. No entanto, se você estiver arquivando também os Logs de Atividade e as métricas de diagnóstico em uma conta de armazenamento, talvez faça sentido usar essa conta de armazenamento para o Log de Diagnósticos, bem como manter todos os dados de monitoramento em um local central. A conta de armazenamento usada deve ser uma conta de armazenamento de finalidade geral e não uma conta de armazenamento de blobs.

## <a name="diagnostic-settings"></a>Configurações de Diagnóstico
Para arquivar os Logs de Diagnóstico usando qualquer um dos métodos abaixo, defina uma **Configuração de Diagnóstico** para um determinado recurso. Uma configuração de diagnóstico para um recurso define as categorias de logs que são transmitidas ou armazenadas e as saídas—hub de eventos e/ou contas de armazenamento. Ela também define a política de retenção (número de dias para armazenamento) para eventos de cada categoria de log armazenados em uma conta de armazenamento. Se uma política de retenção for definida como zero, os eventos para essa categoria de log serão armazenados indefinidamente (ou seja, para sempre). Uma política de retenção pode ser qualquer quantidade de dias, entre 1 e 2147483647. [Leia mais sobre configurações de diagnóstico aqui](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings). As políticas de retenção são aplicadas por dia, para que, ao final de um dia (UTC), os logs do dia após a política de retenção sejam excluídos. Por exemplo, se você tiver uma política de retenção de um dia, no início do dia de hoje, os logs de anteontem serão excluídos

## <a name="archive-diagnostic-logs-using-the-portal"></a>Arquivar logs de diagnóstico usando o portal
1. No portal, clique na folha de recursos do recurso no qual você gostaria de habilitar o arquivamento de Logs de Diagnóstico.
2. Na seção **Monitoramento** do menu de configurações de recursos, selecione **Diagnósticos**.
   
    ![Seção de monitoramento do menu de recursos](media/monitoring-archive-diagnostic-logs/diag-log-monitoring-sec.png)
3. Marque a caixa **Exportar para Conta de Armazenamento**e, em seguida, selecione uma conta de armazenamento. Como opção, defina um número de dias para manter esses logs usando os controles deslizantes **Retenção (dias)** . Uma retenção de zero dias armazena os logs indefinidamente.
   
    ![Folha Logs de Diagnóstico](media/monitoring-archive-diagnostic-logs/diag-log-monitoring-blade.png)
4. Clique em **Salvar**.

Os Logs de Diagnóstico são arquivados nessa conta de armazenamento, assim que novos dados de evento são gerados.

## <a name="archive-diagnostic-logs-via-the-powershell-cmdlets"></a>Arquivar Logs de Diagnóstico por meio de cmdlets do PowerShell
```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg -StorageAccountId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Categories networksecuritygroupevent,networksecuritygrouprulecounter -Enabled $true -RetentionEnabled $true -RetentionInDays 90
```

| Propriedade | Obrigatório | Descrição |
| --- | --- | --- |
| ResourceId |Sim |ID de Recurso do recurso no qual você deseja definir uma configuração de diagnóstico. |
| StorageAccountId |Não |A ID de Recurso da Conta de Armazenamento na qual os Logs de Diagnóstico devem ser salvos. |
| Categorias |Não |Lista separada por vírgulas de categorias de log para habilitar. |
| Habilitado |Sim |Booliano indicando se os diagnósticos estão habilitados ou desabilitados nesse recurso. |
| RetentionEnabled |Não |Booliano indicando se há uma política de retenção habilitada nesse recurso. |
| RetentionInDays |Não |Número de dias durante os quais os eventos devem ser mantidos, entre 1 e 2147483647. Um valor de zero armazena os logs indefinidamente. |

## <a name="archive-diagnostic-logs-via-the-cross-platform-cli"></a>Arquivar os logs de diagnóstico por meio da CLI de plataforma cruzada
```
azure insights diagnostic set --resourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg --storageId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage –categories networksecuritygroupevent,networksecuritygrouprulecounter --enabled true
```

| Propriedade | Obrigatório | Descrição |
| --- | --- | --- |
| ResourceId |Sim |ID de Recurso do recurso no qual você deseja definir uma configuração de diagnóstico. |
| storageId |Não |A ID de Recurso da Conta de Armazenamento na qual os Logs de Diagnóstico devem ser salvos. |
| Categorias |Não |Lista separada por vírgulas de categorias de log para habilitar. |
| Habilitado |Sim |Booliano indicando se os diagnósticos estão habilitados ou desabilitados nesse recurso. |

## <a name="archive-diagnostic-logs-via-the-rest-api"></a>Arquivar Logs de Diagnóstico por meio da API REST
[Confira este documento](https://msdn.microsoft.com/library/azure/dn931931.aspx) para saber mais sobre como definir uma configuração de diagnóstico usando a API REST do Azure Monitor.

## <a name="schema-of-diagnostic-logs-in-the-storage-account"></a>Esquema de Logs de Diagnóstico na conta de armazenamento
Após a configuração do arquivamento, um contêiner de armazenamento será criado na conta de armazenamento assim que ocorrer um evento em uma das categorias de log habilitadas. Os blobs no contêiner seguem o mesmo formato em todos os Logs de Diagnóstico e no Log de Atividades. A estrutura desses blobs é:

> insights-logs-{nome da categoria de log}/resourceId=/SUBSCRIPTIONS/{ID da assinatura}/RESOURCEGROUPS/{nome do grupo de recursos}/PROVIDERS/{nome do provedor de recursos}/{tipo de recurso}/{nome do recurso}/y={ano com quatro dígitos numéricos}/m={mês com dois dígitos numéricos}/d={dia com dois dígitos numéricos}/h={horário com dois dígitos no formato 24 horas}/m=00/PT1H.json
> 
> 

Ou, mais simplesmente,

> insights-logs-{nome da categoria de log}/resourceId=/{ID do recurso}/y={ano com quatro dígitos numéricos}/m={mês com dois dígitos numéricos}/d={dia com dois dígitos numéricos}/h={horário com dois dígitos no formato 24 horas}/m=00/PT1H.json
> 
> 

Por exemplo, um nome de blob poderia ser:

> insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
> 
> 

Cada blob PT1H.json contém um blob JSON de eventos que ocorreram dentro de uma hora especificada na URL do blob (por exemplo, h=12). Durante a hora presente, os eventos são acrescentados ao arquivo PT1H.json conforme eles ocorrem. O valor de minuto (m=00) é sempre 00, como eventos de Log de Diagnóstico são divididos em blobs individuais por hora.

No arquivo PT1H.json, cada evento é armazenado na matriz de "registros", seguindo este formato:

```
{
    "records": [
        {
            "time": "2016-07-01T00:00:37.2040000Z",
            "systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1",
            "category": "NetworkSecurityGroupRuleCounter",
            "resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG",
            "operationName": "NetworkSecurityGroupCounters",
            "properties": {
                "vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}",
                "subnetPrefix": "10.3.0.0/24",
                "macAddress": "000123456789",
                "ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp",
                "direction": "In",
                "type": "allow",
                "matchedConnections": 1988
            }
        }
    ]
}
```

| Nome do elemento | Descrição |
| --- | --- |
| tempo real |Carimbo de hora quando o evento foi gerado pelo serviço do Azure que está processando a solicitação correspondente ao evento. |
| ResourceId |ID de recurso do recurso afetado. |
| operationName |Nome da operação. |
| categoria |Categoria de log do evento. |
| propriedades |Conjunto de pares de `<Key, Value>` (ou seja, Dicionário) que descreve os detalhes do evento. |

> [!NOTE]
> As propriedades e o uso dessas propriedades podem variar dependendo do recurso.
> 
> 

## <a name="next-steps"></a>Próximas etapas
* [Baixar blobs para análise](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs)
* [Transmitir Logs de Diagnóstico para os Hubs de Eventos](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Saiba mais sobre Logs de Diagnóstico](monitoring-overview-of-diagnostic-logs.md)




<!--HONumber=Dec16_HO2-->


