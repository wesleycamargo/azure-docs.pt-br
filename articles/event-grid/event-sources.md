---
title: Fontes de eventos da Grade de Eventos do Azure
description: Descreve as fontes de eventos com suporte para a Grade de Eventos do Azure
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: tomfitz
ms.openlocfilehash: efc71e02cc02574973f0b6b428b5b670cd9cfd5b
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/22/2018
ms.locfileid: "42617345"
---
# <a name="event-sources-in-azure-event-grid"></a>Fontes de eventos na Grade de Eventos do Azure

A origem de um evento é onde o evento acontece. Vários serviços do Azure são automaticamente configurados para enviar eventos. Você também pode criar aplicativos personalizados que enviam eventos. Os aplicativos personalizados não precisam ser hospedados no Azure para usar a Grade de Eventos para a distribuição de eventos.

Este artigo fornece links para conteúdo de cada fonte de eventos.

## <a name="azure-subscriptions"></a>Assinaturas do Azure

Assine eventos de Assinaturas do Azure para responder a alterações em recursos em uma assinatura do Azure.

|Title |DESCRIÇÃO  |
|---------|---------|
| [Tutorial: Automação do Azure com Grade de Eventos e Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Crie uma máquina virtual, que envia um evento. O evento dispara um runbook de Automação que marca a máquina virtual e dispara uma mensagem que é enviada para um canal do Microsoft Teams. |
| [Como: assinar eventos por meio do portal](subscribe-through-portal.md) | Use o portal para assinar eventos de uma assinatura do Azure. |
| [CLI do Azure: assinar eventos de uma assinatura do Azure](./scripts/event-grid-cli-azure-subscription.md) |Script de exemplo que cria uma assinatura de Grade de Eventos para uma assinatura do Azure e envia eventos para um WebHook. |
| [PowerShell: assinar eventos de uma assinatura do Azure](./scripts/event-grid-powershell-azure-subscription.md)| Script de exemplo que cria uma assinatura de Grade de Eventos para uma assinatura do Azure e envia eventos para um WebHook. |
| [Esquema do evento](event-schema-subscriptions.md) | Mostra campos nos eventos de assinatura do Azure. |

## <a name="container-registry"></a>Registro de Contêiner

Assine eventos de Registro de Contêiner para responder a alterações nas imagens.

|Title |DESCRIÇÃO  |
|---------|---------|
| [Início Rápido: enviar eventos de registro de contêiner](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar a CLI do Azure para enviar eventos de Registro de Contêiner. |
| [Esquema do evento](event-schema-container-registry.md) | Mostra campos nos eventos do Registro de Contêiner. |

## <a name="custom-topics"></a>Tópicos personalizados

Assine os tópicos personalizados para responder a eventos do aplicativo.

|Title  |DESCRIÇÃO  |
|---------|---------|
| [Início Rápido: como criar e encaminhar eventos personalizados com a CLI do Azure](custom-event-quickstart.md) | Mostra como usar a CLI do Azure para enviar eventos personalizados. |
| [Início Rápido: como criar e encaminhar eventos personalizados com o Azure PowerShell](custom-event-quickstart-powershell.md) | Mostra como usar o Azure PowerShell para enviar eventos personalizados. |
| [Início Rápido: como criar e encaminhar eventos personalizados com o portal do Azure](custom-event-quickstart-portal.md) | Mostra como usar o portal para enviar eventos personalizados. |
| [Início Rápido: como encaminhar eventos personalizados para o Armazenamento de Filas do Azure](custom-event-to-queue-storage.md) | Descreve como enviar eventos personalizados para um Armazenamento de filas. |
| [Como publicar para tópico personalizado](post-to-custom-topic.md) | Mostra como publicar um evento para um tópico personalizado. |
| [CLI do Azure: criar um tópico personalizado da Grade de Eventos](./scripts/event-grid-cli-create-custom-topic.md)|Script de exemplo que cria um tópico personalizado. O script recupera o ponto de extremidade e uma chave.|
| [CLI do Azure: assinar eventos para um tópico personalizado](./scripts/event-grid-cli-subscribe-custom-topic.md)|Script de exemplo que cria uma assinatura para um tópico personalizado. Envia eventos para um WebHook.|
| [PowerShell: criar um tópico de Grade de Eventos personalizado](./scripts/event-grid-powershell-create-custom-topic.md)|Script de exemplo que cria um tópico personalizado. O script recupera o ponto de extremidade e uma chave.|
| [PowerShell: assinar eventos para um tópico personalizado](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Script de exemplo que cria uma assinatura para um tópico personalizado. Envia eventos para um WebHook.|
| [Modelo do Gerenciador de Recursos: tópico personalizado e ponto de extremidade de WebHook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Um modelo do Gerenciador de Recursos que cria um tópico personalizado e uma assinatura para esse tópico personalizado. Envia eventos para um WebHook. |
|
| [Modelo do Gerenciador de Recursos: tópico personalizado e ponto de extremidade de Hubs de Eventos](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/subscribeCustomTopicToEventHub.json)| Um modelo do Gerenciador de Recursos que cria uma assinatura para um tópico personalizado. Envia eventos para os Hubs de Eventos do Azure. |
| [Esquema do evento](event-schema.md) | Mostra campos em eventos personalizados. |

## <a name="event-hubs"></a>Hubs de Eventos

Assine eventos de Hubs de Eventos para responder a eventos de arquivo e Captura. Os Hubs de Eventos podem agir como uma fonte de evento ou um manipulador de eventos. Os artigos a seguir mostram como usar os Hubs de Eventos como uma fonte.

|Title  |DESCRIÇÃO  |
|---------|---------|
| [Tutorial: transmitir Big Data para um data warehouse](event-grid-event-hubs-integration.md) | Quando os Hubs de Eventos criam um arquivo de Captura, a Grade de Eventos envia um evento para um aplicativo de função. O aplicativo recupera o arquivo de Captura e migra dados para um data warehouse. |
| [Esquema do evento](event-schema-event-hubs.md) | Mostra campos nos eventos de Hubs de Eventos. |

Para obter exemplos de Hubs de Eventos como um manipulador, consulte [manipulador de Hubs de Eventos](event-handlers.md#event-hubs).

## <a name="iot-hub"></a>Hub IoT

Assine os eventos de Hub IoT para responder aos eventos criados e excluídos do dispositivo.

|Title  |DESCRIÇÃO  |
|---------|---------|
| [Tutorial: enviar notificações por email sobre os eventos do Hub IoT usando Aplicativos Lógicos](publish-iot-hub-events-to-logic-apps.md) | Um aplicativo lógico envia um email de notificação sempre que um dispositivo é adicionado ao seu hub de IoT. |
| [Visão geral: reagir aos eventos do Hub IoT usando a Grade de Eventos para acionar ações](../iot-hub/iot-hub-event-grid.md) | Visão geral da integração de Hubs de IoT com a Grade de Eventos. |
| [Esquema do evento](event-schema-iot-hub.md) | Mostra campos nos eventos de Hub IoT. |

## <a name="media-services"></a>Serviços de mídia

Assine eventos de Serviços de Mídia para responder a eventos de estado de trabalho.

|Title  |DESCRIÇÃO  |
|---------|---------|
| [Visão geral: reagir aos eventos dos Serviços de Mídia](../media-services/latest/reacting-to-media-services-events.md) | Visão geral da integração de Serviços de Mídia com Grade de Eventos. |
| [Tutorial: rotear eventos de Serviços de Mídia do Azure para um ponto de extremidade da Web personalizado usando a CLI](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como enviar eventos de Serviços de Mídia. |
| [Esquema do evento](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra campos nos eventos de Serviços de Mídia. |

## <a name="resource-groups"></a>Grupos de recursos

Assine eventos de grupo de recursos para responder a alterações em recursos em um grupo de recursos.

|Title  |DESCRIÇÃO  |
|---------|---------|
| [Tutorial: como monitorar alterações de máquina virtual com a Grade de Eventos do Azure e os aplicativos lógicos](monitor-virtual-machine-changes-event-grid-logic-app.md) | Um aplicativo lógico monitora as alterações feitas em uma máquina virtual e envia emails sobre essas alterações. |
| [CLI do Azure: assinar eventos para um grupo de recursos com a CLI do Azure](./scripts/event-grid-cli-resource-group.md)| Script de exemplo que assina eventos para um grupo de recursos. Envia eventos para um WebHook. |
| [CLI do Azure: assinar eventos de um grupo de recursos e filtrar por um recurso](./scripts/event-grid-cli-resource-group-filter.md) | Script de exemplo que assina eventos de um grupo de recursos e filtra eventos por um recurso. |
| [PowerShell: assinar eventos para um grupo de recursos](./scripts/event-grid-powershell-resource-group.md) | Script de exemplo que assina eventos para um grupo de recursos. Envia eventos para um WebHook. |
| [PowerShell: assinar eventos de um grupo de recursos e filtrar por um recurso](./scripts/event-grid-powershell-resource-group-filter.md) | Script de exemplo que assina eventos de um grupo de recursos e filtra eventos por um recurso. |
| [Modelo do Gerenciador de Recursos: assinatura de grupo de recursos](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/subscribeResourceGroupToWebHook.json) | Assina eventos para um grupo de recursos. Envia eventos para um WebHook. |
| [Esquema do evento](event-schema-resource-groups.md) | Mostra campos em eventos de grupo de recursos. |

## <a name="service-bus"></a>Barramento de Serviço

Assine eventos de Barramento de Serviço para responder às mensagens sem um ouvinte de ativo.

|Title  |DESCRIÇÃO  |
|---------|---------|
| [Tutorial: exemplos do Barramento de Serviço do Azure para a integração da Grade de Eventos do Azure](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | A Grade de Eventos envia mensagens do tópico do Barramento de Serviço para o aplicativo de função e o aplicativo lógico. |
| [Visão geral: integração do Barramento de Serviço do Azure com a Grade de Eventos](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Visão geral da integração do Barramento de Serviço com a Grade de Eventos. |
| [Esquema do evento](event-schema-service-bus.md) | Mostra os campos nos eventos de Barramento de Serviço. |

## <a name="storage"></a>Armazenamento

Assine os eventos de Armazenamento de Blobs para responder aos eventos criados e excluídos de blob.

|Title  |DESCRIÇÃO  |
|---------|---------|
| [Início Rápido: encaminhar eventos de armazenamento de Blob para um ponto de extremidade da Web personalizado com a CLI do Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar a CLI do Azure para enviar eventos de armazenamento de blob para um WebHook. |
| [Início Rápido: encaminhar eventos de armazenamento de Blobs para um ponto de extremidade da Web personalizado com PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar o Azure PowerShell para enviar eventos de armazenamento de blobs para um WebHook. |
| [Início Rápido: criar e rotear eventos personalizados de armazenamento de Blobs com o portal do Azure](blob-event-quickstart-portal.md) | Mostra como usar o portal para enviar eventos de armazenamento de blob para um WebHook. |
| [CLI do Azure: assinar eventos para uma conta de armazenamento de Blob](./scripts/event-grid-cli-blob.md) | Script de exemplo que assina evento para uma conta de armazenamento de Blob. Ele envia o evento para um WebHook. |
| [PowerShell: assinar eventos para uma conta de armazenamento de Blob](./scripts/event-grid-powershell-blob.md) | Script de exemplo que assina evento para uma conta de armazenamento de Blob. Ele envia o evento para um WebHook. |
| [Modelo do Gerenciador de Recursos: criar o armazenamento de Blob e assinatura](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/createBlobAndSubscribe.json) | Implanta uma conta de armazenamento de blobs do Azure e assina eventos para essa conta de armazenamento. Envia eventos para um WebHook. |
| [Visão geral: reagir aos eventos de armazenamento de Blobs](../storage/blobs/storage-blob-event-overview.md) | Visão geral da integração do Armazenamento de Blobs com a Grade de Eventos. |
| [Esquema do evento](event-schema-blob-storage.md) | Mostra campos nos eventos de Armazenamento de Blobs. |

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos, confira [About Event Grid](overview.md) (Sobre a Grade de Eventos).
* Para começar a usar rapidamente a Grade de Eventos, confira [Create and route custom events with Azure Event Grid](custom-event-quickstart.md) (Criar e rotear eventos personalizados com a Grade de Eventos do Azure).
