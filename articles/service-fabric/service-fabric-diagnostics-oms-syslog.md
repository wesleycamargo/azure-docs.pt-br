---
title: Monitorar os eventos de cluster do Linux do Azure Service Fabric | Microsoft Docs
description: Saiba como monitorar eventos de cluster do Linux do Syslog
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/23/2018
ms.author: srrengar
ms.openlocfilehash: 25452d3d65518511c47087d1cb712d0a512416fc
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2019
ms.locfileid: "57245549"
---
# <a name="service-fabric-linux-cluster-events-in-syslog"></a>Eventos de cluster no Service Fabric do Linux no Syslog

O Service Fabric expõe um conjunto de eventos de plataforma para informá-lo de atividade importante no seu cluster. A lista completa de eventos que são expostos está disponível [aqui](service-fabric-diagnostics-event-generation-operational.md). Há várias maneiras por meio dos quais esses eventos podem ser consumidos. Neste artigo, vamos discutir como configurar o Service Fabric para gravar esses eventos de Syslog.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="introduction"></a>Introdução

Na versão 6.4, foi introduzido o SyslogConsumer para enviar os eventos de plataforma do Service Fabric para Syslog para clusters do Linux. Assim que ativado, os eventos irão fluir automaticamente ao Syslog que pode ser coletado e enviado pelo Agente do Log Analytics.

Cada evento de Syslog tem 4 componentes
* Recurso
* Identidade
* Mensagem
* Severity

O SyslogConsumer grava todos os eventos de plataforma usando o Recurso `Local0`. Você pode atualizar qualquer recurso válido, alterando a configuração. A identidade usada é `ServiceFabric`. O campo de Mensagem contém o evento inteiro serializado em JSON para que possa ser consultado e consumido por uma variedade de ferramentas. 

## <a name="enable-syslogconsumer"></a>Habilitar SyslogConsumer

Para habilitar o SyslogConsumer, você precisa executar uma atualização do cluster. A `fabricSettings` seção precisa ser atualizada com o código a seguir. Observe que esse código inclui apenas as seções relacionadas ao SyslogConsumer

```json
    "fabricSettings": [
        {
            "name": "Diagnostics",
            "parameters": [
            {
                "name": "ConsumerInstances",
                "value": "AzureWinFabCsv, AzureWinFabCrashDump, AzureTableWinFabEtwQueryable, SyslogConsumer"
            }
            ]
        },
        {
            "name": "SyslogConsumer",
            "parameters": [
            {
                "name": "ProducerInstance",
                "value": "WinFabLttProducer"
            },
            {
            "name": "ConsumerType",
            "value": "SyslogConsumer"
            },
            {
                "name": "IsEnabled",
                "value": "true"
            }
            ]
        },
        {
            "name": "Common",
            "parameters": [
            {
                "name": "LinuxStructuredTracesEnabled",
                "value": "true"
            }
            ]
        }
    ],
```

Aqui estão as alterações para ressaltar
1. Na seção Comum, há um novo parâmetro chamado `LinuxStructuredTracesEnabled`. **Isso é necessário ter eventos de Linus estruturados e serializado quando enviado para Syslog.**
2. Na seção de Diagnóstico, um novo ConsumerInstance: SyslogConsumer foi adicionado. Isso informa à plataforma que há outro consumidor de eventos. 
3. A nova seção SyslogConsumer precisa ter `IsEnabled` como `true`. É configurado para usar o recurso de Local0 automaticamente. Você pode substituir isso, adicionando outro parâmetro.

```json
    {
        "name": "New LogFacility",
        "value": "<Valid Syslog Facility>"
    }
```

## <a name="azure-monitor-logs-integration"></a>Integração de logs do Azure Monitor
Você pode ler esses eventos de Syslog em uma ferramenta de monitoramento, como logs do Azure Monitor. Você pode criar um espaço de trabalho do Log Analytics usando o Azure Marketplace usando essas [instruções].(.. / azure-monitor/learn/quick-create-workspace.md) você também precisará adicionar o agente do Log Analytics ao seu cluster para coletar e enviar esses dados para o espaço de trabalho. Isso é o mesmo agente usado para coletar contadores de desempenho. 

1. Navegue até a `Advanced Settings` folha

    ![Configurações do Workspace](media/service-fabric-diagnostics-oms-syslog/workspace-settings.png)

2. Clique em `Data`
3. Clique em `Syslog`
4. Configure Local0 como o recurso para acompanhar. Você pode adicionar outro recurso, se você alterar as configurações em fabricSettings

    ![Configurar Syslog](media/service-fabric-diagnostics-oms-syslog/syslog-configure.png)
5. Vá para o Gerenciador de consultas clicando `Logs` no menu do recurso do espaço de trabalho para começar a consultar

    ![Logs do espaço de trabalho](media/service-fabric-diagnostics-oms-syslog/workspace-logs.png)
6. Você pode consultar a `Syslog` tabela procurando `ServiceFabric` como ProcessName. A consulta a seguir é um exemplo de como analisar o JSON no evento e exibir seu conteúdo

```kusto
    Syslog | where ProcessName == "ServiceFabric" | extend $payload = parse_json(SyslogMessage) | project $payload
```

![Consulta Syslog](media/service-fabric-diagnostics-oms-syslog/syslog-query.png)

O exemplo acima é de um evento NodeDown. Você pode exibir a lista completa de eventos [aqui](service-fabric-diagnostics-event-generation-operational.md).

## <a name="next-steps"></a>Próximas etapas
* [Implantar o Agente do Log Analytics](service-fabric-diagnostics-oms-agent.md) para os nós para coletar os contadores de desempenho e coletar logs e estatísticas do docker para seus contêineres
* Familiarize-se com o [pesquisa e consulta de log](../log-analytics/log-analytics-log-searches.md) recursos oferecidos como parte dos logs do Azure Monitor
* [Use o Designer de exibição para criar exibições personalizadas nos logs do Azure Monitor](../log-analytics/log-analytics-view-designer.md)
* Referência para saber como [do Azure Monitor registra a integração com o Syslog](../log-analytics/log-analytics-data-sources-syslog.md).
