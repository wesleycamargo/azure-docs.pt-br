---
title: Início Rápido do Azure – Criar um hub de eventos usando o PowerShell | Microsoft Docs
description: Este início rápido descreve como criar um hub de eventos usando o Azure PowerShell e, em seguida, enviar e receber eventos usando o SDK do .NET Standard.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: 4605ef3876d7a21f99e775b49d7da30ff947fa8e
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52877884"
---
# <a name="quickstart-create-an-event-hub-using-azure-powershell"></a>Início Rápido: criar um hub de eventos usando o Azure PowerShell

Os Hubs de Eventos do Azure são uma plataforma de streaming de Big Data e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos pelos dispositivos e software distribuídos. Os dados enviados para um Hub de Eventos podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Para obter uma visão detalhada dos Hubs de Eventos, confira [Visão geral de Hubs de Eventos](event-hubs-about.md) e [Recursos de Hubs de Eventos](event-hubs-features.md).

Neste início rápido, você cria um hub de eventos usando o Azure PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, verifique se você tem:

- Assinatura do Azure. Se você não tiver [uma conta gratuita][], crie uma antes de começar.
- [Atualização 3 do Visual Studio 2017 (versão 15.3, 26730.01)](https://www.visualstudio.com/vs) ou posterior.
- [SDK do .NET Standard](https://www.microsoft.com/net/download/windows), versão 2.0 ou posterior.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você estiver usando o PowerShell localmente, você deve executar a versão mais recente do PowerShell para concluir este início rápido. Se precisar instalar ou atualizar, confira [Instalar e configurar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é uma coleção lógica de recursos do Azure e você precisa de um grupo de recursos para criar um hub de eventos. 

O exemplo a seguir cria um grupo de recursos na região Leste dos EUA. Substitua `myResourceGroup` pelo nome do grupo de recursos e local que você deseja usar:

```azurepowershell-interactive
New-AzureRmResourceGroup –Name myResourceGroup –Location eastus
```

## <a name="create-an-event-hubs-namespace"></a>Criar um namespace de Hubs de Eventos

Após o seu grupo de recursos ser criado, crie um namespace de Hubs de Eventos dentro deste mesmo grupo de recursos. Um namespace de Hubs de Eventos fornece um nome de domínio exclusivo totalmente qualificado no qual você pode criar seu hub de eventos. Substitua `namespace_name` por um nome exclusivo para o seu namespace:

```azurepowershell-interactive
New-AzureRmEventHubNamespace -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Location eastus
```

## <a name="create-an-event-hub"></a>Criar um Hub de Evento

Agora que você possui um namespace de Hubs de Eventos, crie um hub de eventos dentro desse namespace:  
O período permitido para `MessageRetentionInDays` é entre 1 e 7 dias.

```azurepowershell-interactive
New-AzureRmEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name -MessageRetentionInDays 3
```

Parabéns! Você usou o Azure PowerShell para criar um namespace de Hubs de Eventos e um hub de eventos dentro desse namespace. 

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou um namespace de Hubs de Eventos e aplicativo de exemplo usados para enviar e receber eventos do seu hub de eventos. Para obter instruções passo a passo sobre como enviar eventos (ou) receber eventos de um hub de eventos, confira os tutoriais a seguir: 

- **Enviar eventos para um hub de eventos**: [.NET Standard](event-hubs-dotnet-standard-getstarted-send.md), [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [Node.js](event-hubs-node-get-started-send.md), [Go](event-hubs-go-get-started-send.md), [C](event-hubs-c-getstarted-send.md)
- **Receber eventos para um hub de eventos**: [.NET Standard](event-hubs-dotnet-standard-getstarted-receive-eph.md), [.NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md), [Java](event-hubs-java-get-started-receive-eph.md), [Python](event-hubs-python-get-started-receive.md), [Node.js](event-hubs-node-get-started-receive.md), [Go](event-hubs-go-get-started-receive-eph.md), [Apache Storm](event-hubs-storm-getstarted-receive.md)

[uma conta gratuita]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[New-AzureRmResourceGroup]: https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
