---
title: Exemplos de início rápido da CLI 2.0 do Azure Monitor. | Microsoft Docs
description: Comandos de exemplo da CLI 2.0 para recursos do Azure Monitor. O Azure Monitor é um serviço do Microsoft Azure que permite enviar notificações de alerta ou chamar URLs da Web baseadas em valores dos dados de telemetria configurados, bem como dimensionar automaticamente Serviços de Nuvem, Máquinas Virtuais e Aplicativos Web.
author: kamathashwin
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1653aa81-0ee6-4622-9c65-d4801ed9006f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2018
ms.author: ashwink
ms.openlocfilehash: e429ba460a97daed4a7bdf71895fe24c1619a645
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="azure-monitor-cli-20-quick-start-samples"></a>Exemplos de início rápido da CLI 2.0 do Azure Monitor
Este artigo mostra um exemplo de CLI (interface de linha de comando) que ajudará você a acessar os recursos do Azure Monitor. O Azure Monitor permite que você dimensione automaticamente Serviços de Nuvem, Máquinas Virtuais e Aplicativos Web e envie notificações de alerta ou chame URLs da Web com base em valores de dados de telemetria configurados.

## <a name="prerequisites"></a>pré-requisitos

Se você ainda não instalou a CLI do Azure, siga as instruções para [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli). Você também pode usar o [Azure Cloud Shell](/azure/cloud-shell) para executar a CLI como uma experiência interativa no navegador. 

## <a name="log-in-to-azure"></a>Fazer logon no Azure
A primeira etapa é fazer logon na conta do Azure.

```azurecli
az login
```

Depois de executar esse comando, será necessário entrar usando as instruções na tela. Todos os comandos funcionam no contexto de sua assinatura padrão.

Para listar os detalhes da sua assinatura atual, use o comando a seguir.

```azurecli
az account show
```

Para alterar o contexto de trabalho para uma assinatura diferente, use o comando a seguir.

```azurecli
az account set -s <Subscription ID or name>
```

Para exibir uma lista de todos os comandos permitidos do Azure Monitor, execute o seguinte.

```azurecli
az monitor -h
```

## <a name="view-activity-log-for-a-subscription"></a>Exibir o log de atividade para uma assinatura

Para exibir uma lista de eventos de log de atividade, execute o seguinte.

```azurecli
az monitor activity-log list
```

Tente o seguinte para exibir todas as opções disponíveis:

```azurecli
az monitor activity-log list -h
```

Veja um exemplo para listar logs por resourceGroup

```azurecli
az monitor activity-log list --resource-group <group name>
```

Exemplo para listar logs por chamador

```azurecli
az monitor activity-log list --caller myname@company.com
```

Exemplo para listar logs por chamador em um tipo de recurso, dentro de um intervalo de datas

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Trabalhar com alertas

Você pode usar as informações na seção para trabalhar com alertas.

### <a name="get-alert-rules-in-a-resource-group"></a>Obter regras de alerta em um grupo de recursos

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-rule"></a>Criar uma regra de alerta de métrica

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-rule"></a>Excluir uma regra de alerta

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="log-profiles"></a>Perfis de log

Use as informações desta seção para trabalhar com perfis de log.

### <a name="get-a-log-profile"></a>Obter um perfil de log

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

### <a name="add-a-log-profile-with-retention"></a>Adicionar um perfil de log com retenção

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true \
    --storage-account-id <storage account ID to store the logs in>
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Adicionar um perfil de log com retenção e Hub de Eventos

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true
    --storage-account-id <storage account ID to store the logs in>
    --service-bus-rule-id <service bus rule ID to stream to>
```

### <a name="remove-a-log-profile"></a>Remover um perfil de log

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="diagnostics"></a>Diagnostics

Use as informações desta seção para trabalhar com configurações de diagnóstico.

### <a name="get-a-diagnostic-setting"></a>Obter uma configuração de diagnóstico

```azurecli
az monitor diagnostic-settings list --resource <target resource ID>
```

### <a name="create-a-diagnostic-log-setting"></a>Criar uma configuração de log de diagnóstico 

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <storage account ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

### <a name="delete-a-diagnostic-setting"></a>Excluir uma configuração de diagnóstico

```azurecli
az monitor diagnostic-settings delete --name <diagnostic name> \
    --resource <target resource ID>
```

## <a name="autoscale"></a>Autoscale

Use as informações desta seção para trabalhar com configurações de dimensionamento automático. Você precisa modificar estes exemplos.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Obter configurações de dimensionamento automático para um grupo de recursos

```azurecli
az monitor autoscale list --resource-group <group name>
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Obter configurações de dimensionamento automático por nome em um grupo de recursos

```azurecli
az monitor autoscale show --name <settings name> --resource-group <group name>
```

### <a name="set-auotoscale-settings"></a>Definir as configurações de dimensionamento automático

```azurecli
az monitor autoscale create --name <settings name> --resource-group <group name> \
    --count <# instances> \
    --resource <target resource ID>
```
