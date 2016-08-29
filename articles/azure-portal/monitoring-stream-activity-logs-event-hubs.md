<properties
	pageTitle="Transmissão do Log de Atividades do Azure para os Hubs de Eventos | Microsoft Azure"
	description="Saiba como transmitir o Log de Atividades do Azure para os Hubs de Eventos."
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

# Transmissão do Log de Atividades do Azure para os Hubs de Eventos
[**Os Logs de Atividade do Azure**](./monitoring-overview-activity-logs.md) podem ser transmitidos quase em tempo real a qualquer aplicativo usando a opção interna "Exportar" no Portal, ou habilitando a Id da Regra de Barramento de Serviço em um Perfil de Log por meio de Cmdlets do Azure PowerShell ou da CLI do Azure.

## O que você pode fazer com o Log de Atividades e os Hubs de Eventos
Veja algumas maneiras de usar o recurso de streaming para o Log de Atividade:

- **Transmitir para sistemas de registro em log e telemetria de terceiros** – Ao longo do tempo, a transmissão de Hubs de Eventos se tornará o mecanismo para direcionar seus Logs de Atividade para SIEMs e soluções de análise de log de terceiros.
- **Compilar uma plataforma de registro em log e telemetria personalizada** – Se você já tiver uma plataforma de telemetria personalizada ou estiver pensando em criar uma, a natureza altamente escalonável de publicação-assinatura dos Hubs de Eventos permite a flexibilidade de ingestão de log de atividade. [Consulte o guia de Dan Rosanova sobre como usar os Hubs de Eventos em uma plataforma de telemetria de escala global.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## Habilitar o streaming do Log de Atividades
Você pode habilitar o streaming do Log de Atividades programaticamente ou por meio do portal. De qualquer forma, você escolhe um Namespace de Barramento de Serviço (crie se não houver nenhum), o namespace selecionado é o local onde o Hub de Eventos será criado (se for a primeira vez que você realiza o streaming do Log de Atividades) ou transmitido (se você já tiver realizado o streaming do Log de Atividades para esse namespace), e a política definir as permissões do mecanismo de streaming. Atualmente, o streaming para um Hub de Eventos exige as permissões **Gerenciamento**, **Leitur** e **Envio**. Você pode criar ou modificar políticas de acesso compartilhado do Namespace do Barramento de Serviço no portal clássico, na guia "Configurar" para seu Namespace de Barramento de Serviço. Para atualizar o perfil de registro do Log de Atividades a fim de incluir o streaming, o cliente deve ter a permissão ListKey na Regra de Autorização do Barramento de Serviço.

### Via Portal do Azure 
1. Navegue até a folha **Log de Atividades** usando o menu no lado esquerdo do portal.

    ![Navegue até o Log de Atividades no portal](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Clique no botão **Exportar** na parte superior da folha.

    ![Botão Exportar no portal](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. Na folha que aparece, você pode selecionar as regiões para as quais você deseja realizar o streaming de eventos, e o Namespace do Barramento de Serviço no qual você gostaria de criar um Hub de Eventos para esses eventos de streaming.

    ![Folha Exportar Log de Atividades](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Clique em **Salvar** para salvar as configurações. As configurações serão aplicadas imediatamente à sua assinatura.


### Via Cmdlets do PowerShell
Se já existir um perfil de log, primeiro remova esse perfil.

1. Use `Get-AzureRmLogProfile` para identificar se já existe um perfil de log
2. Se existir, use `Remove-AzureRmLogProfile` para removê-lo.
3. Use `Set-AzureRmLogProfile` para criar um perfil:

```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

A ID da Regra do Barramento de Serviço é uma cadeia de caracteres com este formato: {ID de recurso do barramento de serviço} /authorizationrules/ {nome da chave}, por exemplo

### Via CLI do Azure
Se já existir um perfil de log, primeiro remova esse perfil.

1. Use `azure insights logprofile list` para identificar se já existe um perfil de log
2. Se existir, use `azure insights logprofile delete` para removê-lo.
3. Use `azure insights logprofile add` para criar um perfil:

```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

A ID da Regra do Barramento de Serviço é uma cadeia de caracteres com este formato: `{service bus resource ID}/authorizationrules/{key name}`.
 
## Como eu consumo os dados de log dos Hubs de Eventos?
[O esquema para o Log de Atividades está disponível aqui](./monitoring-overview-activity-logs.md). Cada evento é uma matriz de blobs JSON chamada "registros".

## Próximas etapas
- [Leia a visão geral do Log de Atividades do Azure](./monitoring-overview-activity-logs.md)
- [Configurar um alerta com base em um evento do Log de Atividades](./insights-auditlog-to-webhook-email.md)

<!---HONumber=AcomDC_0817_2016-->