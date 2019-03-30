---
title: Entender como voluntário ferramenta de migração de alertas no Azure Monitor funciona
description: Entender como funciona a ferramenta de migração de alerta e solução de problemas se você enfrentar problemas.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: a45a0cff606bc854924d5da0841b26e1cb9031bb
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631979"
---
# <a name="understand-how-the-migration-tool-works"></a>Entender como funciona a ferramenta de migração

Como [anunciado anteriormente](monitoring-classic-retirement.md), alertas clássicos no Azure Monitor estão sendo desativadas em julho de 2019. A ferramenta de migração para disparar voluntariamente a migração está disponível no portal do Azure e está distribuindo para os clientes que usam regras de alerta clássicas.

Este artigo explica como funciona a ferramenta de migração voluntária. Ele também descreve a correção de alguns problemas comuns.

## <a name="which-classic-alert-rules-can-be-migrated"></a>Quais regras de alerta clássicas podem ser migradas?

Embora quase todas as regras de alerta clássicas podem ser migradas usando a ferramenta, há algumas exceções. As seguintes regras de alerta não serão migradas usando a ferramenta (ou durante a migração automática em julho de 2019)

- Regras de alerta clássicas nas métricas de convidado da máquina virtual (Windows e Linux). [Consulte as diretrizes sobre como recriar essas regras de alerta em novos alertas de métrica](#guest-metrics-on-virtual-machines)
- Regras de alerta clássicas nas métricas de armazenamento clássico. [Consulte as diretrizes sobre como monitorar suas contas de armazenamento clássico](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/)
- Regras de alerta clássicas em algumas métricas da conta de armazenamento. [Detalhes abaixo](#storage-account-metrics)

Se sua assinatura tiver tais regras clássicas, o restante das regras será migrado, mas estas regras precisam ser migrados manualmente. Como não podemos fornecer uma migração automática, qualquer tais existentes alertas de métrica clássicos continuará trabalhando em junho de 2020 para fornecer-lhe tempo para mover para novos alertas. No entanto, nenhum novo alerta clássica pode ser criada após junho de 2019.

### <a name="guest-metrics-on-virtual-machines"></a>Métricas de convidado em máquinas virtuais

Para poder criar alertas de métrica novo nas métricas de convidado, as métricas de convidado precisam ser enviada para o armazenamento do Azure Monitor métricas personalizadas. Siga as instruções abaixo para habilitar o coletor do Azure Monitor em configurações de diagnóstico.

- [Habilitando métricas de convidado para VMs do Windows](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Habilitando métricas de convidado para VMs do Linux](https://docs.microsoft.com/azure/azure-monitor/platform/collect-custom-metrics-linux-telegraf)

Depois de concluir as etapas acima, os novos alertas de métrica podem ser criadas nas métricas de convidado. Depois de você ter recriado novos alertas de métrica, alertas clássicos podem ser excluídas.

### <a name="storage-account-metrics"></a>Métricas da conta de armazenamento

Todos os alertas clássicos em contas de armazenamento podem ser migrados, exceto os alertas nas seguintes métricas:

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentClientOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError

Regras de alerta clássicas nas métricas de porcentagem serão precisam ser migrados com base na [o mapeamento entre as métricas de armazenamento novas e antigas](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics). Os limites precisará ser adequadamente modificadas como a nova métrica disponível é absoluto.

As regras de alerta clássicas AnonymousThrottlingError e SASThrottlingError precisará ser dividido em dois novos alertas quanto houver não é nenhuma métrica combinada que fornece a mesma funcionalidade. Os limites precisará ser adaptado adequadamente.

## <a name="roll-out-phases"></a>Fases de distribuição

A ferramenta de migração está distribuindo em fases para clientes que usam regras de alerta clássicas. **Os proprietários da assinatura** receberá um email quando a assinatura está pronta para ser migrada usando a ferramenta.

> [!NOTE]
> Como a ferramenta está sendo distribuída em fases, as fases iniciais, você poderá ver que a maioria das suas assinaturas ainda não está pronta para ser migrada.

Atualmente, uma **subconjunto** de assinaturas, qual **somente** tiver regras de alerta clássicas no recurso a seguir são os tipos marcados como prontos para a migração. Suporte para mais tipos de recursos será adicionado em futuras fases.

- Microsoft.apimanagement/service
- Microsoft.batch/batchaccounts
- Microsoft.cache/redis
- Microsoft.datafactory/datafactories
- Microsoft.dbformysql/servers
- Microsoft.dbforpostgresql/servers
- Microsoft.eventhub/namespaces
- Microsoft.logic/workflows
- Microsoft.network/applicationgateways
- Microsoft.network/dnszones
- Microsoft.network/expressroutecircuits
- Microsoft.network/loadbalancers
- Microsoft.network/networkwatchers/connectionmonitors
- Microsoft.network/publicipaddresses
- Microsoft.network/trafficmanagerprofiles
- Microsoft.network/virtualnetworkgateways
- Microsoft.search/searchservices
- Microsoft.servicebus/namespaces
- Microsoft.streamanalytics/streamingjobs
- Microsoft.timeseriesinsights/environments
- Microsoft.web/hostingenvironments/workerpools
- Microsoft.web/serverfarms
- Microsoft.web/sites

## <a name="who-can-trigger-the-migration"></a>Quem pode disparar a migração?

Qualquer usuário que tenha a função interna de **Colaborador de monitoramento** na assinatura do nível poderão disparar a migração. Os usuários com uma função personalizada com as seguintes permissões também podem disparar a migração:

- */leitura
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

## <a name="common-issues-and-remediations"></a>Problemas comuns e correções

Depois que você [disparar a migração](alerts-using-migration-tool.md), usaremos os endereços de email fornecido para notificá-lo da conclusão da migração, ou se houver uma ação é necessária. A seção a seguir descreve alguns problemas comuns e como corrigi-los

### <a name="validation-failed"></a>Falha na validação

Devido a algumas alterações recentes a regras de alerta clássicas em sua assinatura, a assinatura não pode ser migrada. Esse é um problema temporário. Você pode reiniciar a migração, depois que o status de migração move de volta **prontos para a migração** em alguns dias.

### <a name="policyscope-lock-preventing-us-from-migrating-your-rules"></a>Impedindo a migrar suas regras de bloqueio de política/escopo

Como parte da migração, serão criados novos alertas de métrica e novos grupos de ação e as regras de alerta clássicas serão excluídas (após a criação de novas regras). No entanto, há uma política ou o escopo de bloqueio impedindo a criação de recursos. Dependendo do escopo ou a política de bloqueio, algumas ou todas as regras não pôde ser migradas. Você pode resolver esse problema removendo a bloqueio de escopo/política temporariamente e disparar a migração novamente.

## <a name="next-steps"></a>Próximas etapas

- [Como usar a ferramenta de migração](alerts-using-migration-tool.md)
- [Preparar para a migração](alerts-prepare-migration.md)
