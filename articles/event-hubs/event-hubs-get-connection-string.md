---
title: Obter cadeia de conexão - Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece instruções para obter uma cadeia de conexão que os clientes podem usar para conectar Hubs de Eventos do Azure.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: ee4bd5d2acf1a029486f83ee721b9e1f72347958
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56238140"
---
# <a name="get-an-event-hubs-connection-string"></a>Obter uma cadeia de conexão dos Hubs de Eventos

Para usar os Hubs de Eventos, você precisa criar um namespace dos Hubs de Eventos. Um namespace é um contêiner de escopo que pode hospedar vários tópicos de Hub de Eventos/Kafka. Esse namespace fornece a você um [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) exclusivo. Após a criação de um namespace, você pode obter a cadeia de conexão necessária para se comunicar com os Hubs de Eventos.

A cadeia de conexão para os Hubs de Eventos do Azure tem os seguintes componentes inseridos nela

* FQDN = o FQDN do namespace de Hubs de Eventos que você criou (isso incluirá o nome de namespace EventHubs seguido por servicebus.windows.net)
* SharedAccessKeyName = o nome que você escolheu para as chaves SAS do seu aplicativo
* SharedAccessKey = o valor gerado da chave.

O modelo de cadeia de conexão tem esta aparência
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

Um exemplo de cadeia de conexão pode parecer com `Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

Este artigo orienta você pelas diversas maneiras de obter a cadeia de conexão.

## <a name="get-connection-string-from-the-portal"></a>Obter a cadeia de conexão do portal

Quando você tiver o namespace dos Hubs de Eventos, a seção de visão geral do portal poderá fornecer a você a cadeia de conexão, conforme mostrado abaixo:

![Cadeia de conexão dos Hubs de Eventos](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)

Quando você clica no link da cadeia de conexão na seção de visão geral, ele abre a guia de políticas SAS, conforme mostrado na figura abaixo:

![Políticas de SAS dos Hubs de Eventos](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)

Você pode adicionar uma nova política de SAS e obter a cadeia de conexão ou usar a política padrão que já foi criada para você. Quando a política é aberta, a cadeia de conexão é obtida, conforme mostra a figura abaixo:

![Obter a cadeia de conexão dos Hubs de Eventos](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>Obter a cadeia de conexão com o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Você pode usar Get-AzEventHubNamespaceKey para obter a cadeia de conexão para o nome de política/regra específica, conforme mostrado abaixo:

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

Consulte o [módulo do PowerShell dos Hubs de Eventos do Azure](https://docs.microsoft.com/powershell/module/az.eventhub/get-azeventhubkey) para obter mais detalhes.

## <a name="getting-the-connection-string-with-azure-cli"></a>Obter a cadeia de conexão com a CLI do Azure
Use o seguinte para obter a cadeia de conexão para o namespace:

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

Consulte a [CLI do Azure para Hubs de Eventos](https://docs.microsoft.com/cli/azure/eventhubs) para saber mais.

## <a name="next-steps"></a>Próximas etapas

Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

* [Visão geral de Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Criar um Hub de Eventos](event-hubs-create.md)
