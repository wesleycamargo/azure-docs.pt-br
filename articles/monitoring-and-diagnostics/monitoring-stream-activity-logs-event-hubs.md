---
title: Transmissão do Log de Atividades do Azure para os Hubs de Eventos
description: Saiba como transmitir o Log de Atividades do Azure para os Hubs de Eventos.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/02/2018
ms.author: johnkem
ms.component: activitylog
ms.openlocfilehash: 1f1a131d4e0cf900d04acc9730b04e1375f396a6
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264292"
---
# <a name="stream-the-azure-activity-log-to-event-hubs"></a>Transmissão do Log de Atividades do Azure para os Hubs de Eventos
Você pode transmitir o [Log de Atividades do Azure](monitoring-overview-activity-logs.md) quase em tempo real para qualquer aplicativo das seguintes formas:

* Usando a opção embutida **Exportar** no portal
* Habilitando a ID da regra de barramento de serviço do Azure em um perfil de registro por meio de cmdlets do Azure PowerShell ou da CLI do Azure

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>O que você pode fazer com o Log de Atividades e os Hubs de Eventos
Veja duas maneiras de usar o recurso de streaming para o Log de Atividade:

* **Transmitir para sistemas de registro em log e telemetria de terceiros**: ao longo do tempo, a transmissão de Hubs de Eventos do Azure se tornará o mecanismo para direcionar seus Logs de Atividade para SIEMs e soluções de análise de log de terceiros.
* **Compilar uma plataforma de registro em log e telemetria personalizada**: se você já tiver uma plataforma de telemetria personalizada ou estiver pensando em criar uma, a natureza altamente escalonável de publicação-assinatura dos Hubs de Eventos permite a flexibilidade de ingestão de log de atividade. Consulte mais informações no [vídeo de Dan Rosanova sobre o uso de Hubs de Eventos em uma plataforma de telemetria de escala global](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-the-activity-log"></a>Habilitar o streaming do Log de Atividades
Você pode habilitar o streaming do Log de Atividades programaticamente ou por meio do portal. De qualquer forma, você escolhe um namespace de Hubs de Eventos e uma política de acesso compartilhado para esse namespace. Um hub de eventos com nome insights-logs-operationallogs é criado no namespace quando ocorre o primeiro evento novo de Log de atividades. 

Se você não tiver um namespace de Hubs de Eventos, precisará criar um. Se você distribuiu anteriormente eventos de Log de Atividades para esse namespace de Hubs de Eventos, o hub de evento criado anteriormente será reutilizado. 

A política de acesso compartilhado define as permissões que o mecanismo de streaming tem. Atualmente, o streaming para Hubs de Eventos exige as permissões **Gerenciar**, **Enviar** e **Escutar**. Você pode criar ou modificar políticas de acesso compartilhado para o namespace de Hubs de Eventos no Portal do Azure, na guia **Configurar**, para seu namespace de Hubs de Eventos. 

Para atualizar o perfil de registro do Log de Atividades a fim de incluir o streaming, o usuário que está fazendo a alteração deve ter a permissão ListKey nessa regra de autorização dos Hubs de Eventos. O namespace dos Hubs de Eventos não precisa estar na mesma assinatura que emite os logs, desde que o usuário que define a configuração tenha acesso RBAC adequado a ambas as assinaturas.

### <a name="via-the-azure-portal"></a>Por meio do Portal do Azure
1. Navegue até a seção **Log de Atividades** usando a pesquisa **Todos os serviços** no lado esquerdo do portal.
   
   ![Seleção do Log de atividades da lista de serviços no portal](./media/monitoring-stream-activity-logs-event-hubs/activity.png)
2. Selecione o botão **Exportar** na parte superior do log.
   
   ![Botão Exportar no portal](./media/monitoring-stream-activity-logs-event-hubs/export.png)

   As configurações de filtro aplicadas ao exibir o Log de atividades na exibição anterior não têm impacto sobre as configurações de exportação. Elas servem tão-somente para filtrar o que você vê ao procurar no seu Log de atividades no portal.
3. Na seção que aparece, selecione **Todas as regiões**. Não selecione regiões específicas.
   
   ![Seção de exportação](./media/monitoring-stream-activity-logs-event-hubs/export-audit.png)

   > [!WARNING]  
   > Se você selecionar qualquer coisa diferente de **Todas as regiões**, perderá eventos importantes que espera receber. O Log de atividades é um registro global (não regional), por isso a maioria dos eventos não tem uma região associado a eles. 
   >

4. Selecione **Salvar** para salvar as configurações. As configurações são aplicadas imediatamente à sua assinatura.
5. Se você tiver várias assinaturas, repita essa ação e envie todos os dados para o mesmo hub de eventos.

### <a name="via-powershell-cmdlets"></a>Via cmdlets do PowerShell
Se um perfil de log já existir, primeiro será necessário remover o perfil de log existente e criar um novo.

1. Use `Get-AzureRmLogProfile` para identificar se já existe um perfil de log.  Se um perfil de log existir, localize a propriedade *nome*.
2. Use `Remove-AzureRmLogProfile` para remover o perfil de log usando o valor da propriedade *nome*.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzureRmLogProfile -Name "default"
    ```
3. Use `Add-AzureRmLogProfile` para criar um novo perfil de log:

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzureRmLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespaceName/authorizationrules/RootManageSharedAccessKey"

   Add-AzureRmLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```

### <a name="via-azure-cli"></a>Via CLI do Azure
Se um perfil de log já existir, primeiro será necessário remover o perfil de log existente e criar um novo.

1. Use `az monitor log-profiles list` para identificar se já existe um perfil de log.
2. Use `az monitor log-profiles delete --name "<log profile name>` para remover o perfil de log usando o valor da propriedade *nome*.
3. Use `az monitor log-profiles create` para criar um novo perfil de log:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

## <a name="consume-the-log-data-from-event-hubs"></a>Consumir os dados de log dos Hubs de Eventos
O esquema para o Log de atividades está disponível em [Monitorar a atividade de assinatura com o Log de atividades do Azure](monitoring-overview-activity-logs.md). Cada evento está em uma matriz de blobs JSON chamada *registros*.

## <a name="next-steps"></a>Próximas etapas
* [Arquivar o Log de Atividades em uma conta de armazenamento](monitoring-archive-activity-log.md)
* [Leia a visão geral do Log de Atividades do Azure](monitoring-overview-activity-logs.md)
* [Configurar um alerta com base em um evento do Log de Atividades](insights-auditlog-to-webhook-email.md)

