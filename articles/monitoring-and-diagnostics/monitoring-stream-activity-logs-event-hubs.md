---
title: Transmissão do Log de Atividades do Azure para os Hubs de Eventos | Microsoft Docs
description: Saiba como transmitir o Log de Atividades do Azure para os Hubs de Eventos.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ec4c2d2c-8907-484f-a910-712403a06829
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2018
ms.author: johnkem
ms.openlocfilehash: 4b2d9866839f943f65beb271d44bc691441b0fb3
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
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
Se já existir um perfil de log, primeiro remova esse perfil.

1. Use `Get-AzureRmLogProfile` para identificar se já existe um perfil de log.
2. Se existir, use `Remove-AzureRmLogProfile` para removê-lo.
3. Use `Set-AzureRmLogProfile` para criar um perfil:

   ```powershell

   Add-AzureRmLogProfile -Name my_log_profile -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action

   ```

A ID da regra do Barramento de Serviço é uma cadeia de caracteres com este formato: `{service bus resource ID}/authorizationrules/{key name}`. 

### <a name="via-azure-cli"></a>Via CLI do Azure
Se já existir um perfil de log, primeiro remova esse perfil.

1. Use `azure insights logprofile list` para identificar se já existe um perfil de log.
2. Se existir, use `azure insights logprofile delete` para removê-lo.
3. Use `azure insights logprofile add` para criar um perfil:

   ```azurecli-interactive
   azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
   ```

A ID da regra do Barramento de Serviço é uma cadeia de caracteres com este formato: `{service bus resource ID}/authorizationrules/{key name}`.

## <a name="consume-the-log-data-from-event-hubs"></a>Consumir os dados de log dos Hubs de Eventos
O esquema para o Log de atividades está disponível em [Monitorar a atividade de assinatura com o Log de atividades do Azure](monitoring-overview-activity-logs.md). Cada evento está em uma matriz de blobs JSON chamada *registros*.

## <a name="next-steps"></a>Próximas etapas
* [Arquivar o Log de Atividades em uma conta de armazenamento](monitoring-archive-activity-log.md)
* [Leia a visão geral do Log de Atividades do Azure](monitoring-overview-activity-logs.md)
* [Configurar um alerta com base em um evento do Log de Atividades](insights-auditlog-to-webhook-email.md)

