---
title: Transmissão do Log de Atividades do Azure para os Hubs de Eventos
description: Saiba como transmitir o Log de Atividades do Azure para os Hubs de Eventos.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 3d4c1029315e754410f31b13042d1d6acb105da1
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2019
ms.locfileid: "57309682"
---
# <a name="stream-the-azure-activity-log-to-event-hubs"></a>Transmissão do Log de Atividades do Azure para os Hubs de Eventos
Você pode transmitir o [Log de Atividades do Azure](../../azure-monitor/platform/activity-logs-overview.md) quase em tempo real para qualquer aplicativo das seguintes formas:

* Usando a opção embutida **Exportar** no portal
* Habilitando a ID da regra de barramento de serviço do Azure em um perfil de registro por meio de cmdlets do Azure PowerShell ou da CLI do Azure

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>O que você pode fazer com o Log de Atividades e os Hubs de Eventos
Veja duas maneiras de usar o recurso de streaming para o Log de Atividade:

* **Transmitir para sistemas de telemetria e registro em log de terceiros**: Ao longo do tempo, a transmissão de Hubs de Eventos do Azure se tornará o mecanismo para direcionar seu Log de Atividades para SIEMs e soluções de análise de logs de terceiros.
* **Criar uma plataforma de registro em log e telemetria personalizada**: se você já tem uma plataforma de telemetria personalizada ou está pensando em criar uma, a natureza altamente escalonável de publicação-assinatura dos Hubs de Eventos permite a ingestão flexível de log de atividades. Consulte mais informações no [vídeo de Dan Rosanova sobre o uso de Hubs de Eventos em uma plataforma de telemetria de escala global](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-the-activity-log"></a>Habilitar o streaming do Log de Atividades
Você pode habilitar o streaming do Log de Atividades programaticamente ou por meio do portal. De qualquer forma, você escolhe um namespace de Hubs de Eventos e uma política de acesso compartilhado para esse namespace. Um hub de eventos com nome insights-logs-operationallogs é criado no namespace quando ocorre o primeiro evento novo de Log de atividades. 

Se você não tiver um namespace de Hubs de Eventos, precisará criar um. Se você distribuiu anteriormente eventos de Log de Atividades para esse namespace de Hubs de Eventos, o hub de evento criado anteriormente será reutilizado. 

A política de acesso compartilhado define as permissões que o mecanismo de streaming tem. Atualmente, o streaming para Hubs de Eventos exige as permissões **Gerenciar**, **Enviar** e **Escutar**. Você pode criar ou modificar políticas de acesso compartilhado para o namespace de Hubs de Eventos no Portal do Azure, na guia **Configurar**, para seu namespace de Hubs de Eventos. 

Para atualizar o perfil de registro do Log de Atividades a fim de incluir o streaming, o usuário que está fazendo a alteração deve ter a permissão ListKey nessa regra de autorização dos Hubs de Eventos. O namespace dos Hubs de Eventos não precisa estar na mesma assinatura que emite os logs, desde que o usuário que define a configuração tenha acesso RBAC adequado a ambas as assinaturas e as duas assinaturas estejam no mesmo locatário do ADD.

### <a name="via-the-azure-portal"></a>Por meio do Portal do Azure
1. Navegue até a seção **Log de Atividades** usando a pesquisa **Todos os serviços** no lado esquerdo do portal.
   
   ![Seleção do Log de atividades da lista de serviços no portal](./media/activity-logs-stream-event-hubs/activity-logs-portal-navigate-v2.png)
2. Selecione o botão **Exportar para o Hub de Eventos** na parte superior do log.
   
   ![Botão Exportar no portal](./media/activity-logs-stream-event-hubs/activity-logs-portal-export-v2.png)

   As configurações de filtro aplicadas ao exibir o Log de atividades na exibição anterior não têm impacto sobre as configurações de exportação. Elas servem tão-somente para filtrar o que você vê ao procurar no seu Log de atividades no portal.
3. Na seção que aparece, selecione **Todas as regiões**. Não selecione regiões específicas.
   
   ![Seção de exportação](./media/activity-logs-stream-event-hubs/export-audit.png)

   > [!WARNING]  
   > Se você selecionar qualquer coisa diferente de **Todas as regiões**, perderá eventos importantes que espera receber. O Log de atividades é um registro global (não regional), por isso a maioria dos eventos não tem uma região associado a eles. 
   >

4. Clique na opção **Hubs de Eventos do Azure** e selecione um namespace de hubs de eventos para o qual os logs devem ser enviados, então clique em **OK**.
5. Selecione **Salvar** para salvar as configurações. As configurações são aplicadas imediatamente à sua assinatura.
6. Se você tiver várias assinaturas, repita essa ação e envie todos os dados para o mesmo hub de eventos.

### <a name="via-powershell-cmdlets"></a>Via cmdlets do PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Se um perfil de log já existir, primeiro será necessário remover o perfil de log existente e criar um novo.

1. Use `Get-AzLogProfile` para identificar se já existe um perfil de log.  Se um perfil de log existir, localize a propriedade *nome*.
2. Use `Remove-AzLogProfile` para remover o perfil de log usando o valor da propriedade *nome*.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```
3. Use `Add-AzLogProfile` para criar um novo perfil de log:

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   Add-AzLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
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
O esquema para o Log de atividades está disponível em [Monitorar a atividade de assinatura com o Log de atividades do Azure](../../azure-monitor/platform/activity-logs-overview.md). Cada evento está em uma matriz de blobs JSON chamada *registros*.

## <a name="next-steps"></a>Próximas etapas
* [Arquivar o Log de Atividades em uma conta de armazenamento](../../azure-monitor/platform/archive-activity-log.md)
* [Leia a visão geral do Log de Atividades do Azure](../../azure-monitor/platform/activity-logs-overview.md)
* [Configurar um alerta com base em um evento do Log de Atividades](../../azure-monitor/platform/alerts-log-webhook.md)


