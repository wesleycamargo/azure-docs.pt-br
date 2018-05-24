---
title: Fontes de eventos da Grade de Eventos do Azure
description: Descreve as fontes de eventos com suporte para a Grade de Eventos do Azure
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: tomfitz
ms.openlocfilehash: f9c3bcb6b92b43fe5b5bad72c99e6ce199c17448
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/18/2018
---
# <a name="event-sources-in-azure-event-grid"></a>Fontes de eventos na Grade de Eventos do Azure

A origem de um evento é onde o evento acontece. Vários serviços do Azure são automaticamente configurados para enviar eventos. Você também pode criar aplicativos personalizados que enviam eventos. Os aplicativos personalizados não precisam ser hospedados no Azure para usar a Grade de Eventos para a distribuição de eventos.

Este artigo fornece links para conteúdo de cada fonte de eventos.

## <a name="azure-subscriptions"></a>Assinaturas do Azure

Assine eventos de Assinaturas do Azure para responder a alterações em recursos em uma assinatura do Azure.

|Title |DESCRIÇÃO  |
|---------|---------|
| [Integrar a Automação do Azure à Grade de Eventos e ao Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Crie uma máquina virtual, que envia um evento. O evento dispara um runbook de Automação que marca a máquina virtual e dispara uma mensagem que é enviada para um canal do Microsoft Teams. |
| [Esquema do evento](event-schema-subscriptions.md) | Mostra campos nos eventos de assinatura do Azure. |

## <a name="custom-topics"></a>Tópicos personalizados

Assine os tópicos personalizados para responder a eventos do aplicativo.

|Title  |DESCRIÇÃO  |
|---------|---------|
| [Criar e encaminhar eventos personalizados com a CLI do Azure](custom-event-quickstart.md) | Mostra como usar a CLI do Azure para enviar eventos personalizados. |
| [Criar e encaminhar eventos personalizados com o Azure PowerShell](custom-event-quickstart-powershell.md) | Mostra como usar o Azure PowerShell para enviar eventos personalizados. |
| [Criar e rotear eventos personalizados com o portal do Azure](custom-event-quickstart-portal.md) | Mostra como usar o portal para enviar eventos personalizados. |
| [Publicar para tópico personalizado](post-to-custom-topic.md) | Mostra como publicar um evento para um tópico personalizado. |
| [Encaminhar eventos personalizados para o Armazenamento de Filas do Azure](custom-event-to-queue-storage.md) | Descreve como enviar eventos personalizados para um Armazenamento de filas. |
| [Esquema do evento](event-schema.md) | Mostra campos em eventos personalizados. |

## <a name="event-hubs"></a>Hubs de Eventos

Assine eventos de Hubs de Eventos para responder a eventos de arquivo e Captura.

|Title  |DESCRIÇÃO  |
|---------|---------|
| [Transmitir Big Data para um data warehouse](event-grid-event-hubs-integration.md) | Quando os Hubs de Eventos criam um arquivo de Captura, a Grade de Eventos envia um evento para um aplicativo de função. O aplicativo recupera o arquivo de Captura e migra dados para um data warehouse. |
| [Esquema do evento](event-schema-event-hubs.md) | Mostra campos nos eventos de Hubs de Eventos. |

## <a name="iot-hub"></a>Hub IoT

Assine os eventos de Hub IoT para responder aos eventos criados e excluídos do dispositivo.

|Title  |DESCRIÇÃO  |
|---------|---------|
| [Enviar notificações por email sobre os eventos do Hub IoT usando Aplicativos Lógicos](publish-iot-hub-events-to-logic-apps.md) | Um aplicativo lógico envia um email de notificação sempre que um dispositivo é adicionado ao seu hub de IoT. |
| [Reagir aos eventos do Hub IoT usando a Grade de Eventos para acionar ações](../iot-hub/iot-hub-event-grid.md) | Visão geral da integração de Hubs de IoT com a Grade de Eventos. |
| [Esquema do evento](event-schema-iot-hub.md) | Mostra campos nos eventos de Hub IoT. |

## <a name="media-services"></a>Serviços de mídia

Assine eventos de Serviços de Mídia para responder a eventos de estado de trabalho.

|Title  |DESCRIÇÃO  |
|---------|---------|
| [Reagindo a eventos de Serviços de Mídia](../media-services/latest/reacting-to-media-services-events.md) | Visão geral da integração de Serviços de Mídia com Grade de Eventos. |
| [Encaminhar eventos de Serviços de Mídia do Azure para um ponto de extremidade da Web personalizado usando CLI](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como enviar eventos de Serviços de Mídia. |
| [Esquema do evento](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra campos nos eventos de Serviços de Mídia. |

## <a name="resource-groups"></a>Grupos de recursos

Assine eventos de grupo de recursos para responder a alterações em recursos em um grupo de recursos.

|Title  |DESCRIÇÃO  |
|---------|---------|
| [Monitorar alterações de máquina virtual com a Grade de Eventos do Azure e os Aplicativos Lógicos](monitor-virtual-machine-changes-event-grid-logic-app.md) | Um aplicativo lógico monitora as alterações feitas em uma máquina virtual e envia emails sobre essas alterações. |
| [Esquema do evento](event-schema-resource-groups.md) | Mostra campos em eventos de grupo de recursos. |

## <a name="service-bus"></a>Barramento de Serviço

Assine eventos de Barramento de Serviço para responder às mensagens sem um ouvinte de ativo.

|Title  |DESCRIÇÃO  |
|---------|---------|
| [Exemplos do Barramento de Serviço do Azure para a integração da Grade de Eventos do Azure](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | A Grade de Eventos envia mensagens do tópico do Barramento de Serviço para o aplicativo de função e o aplicativo lógico. |
| [Visão geral da integração do Barramento de Serviço do Azure com a Grade de Eventos](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Visão geral da integração do Barramento de Serviço com a Grade de Eventos. |
| [Esquema do evento](event-schema-service-bus.md) | Mostra os campos nos eventos de Barramento de Serviço. |

## <a name="storage"></a>Armazenamento

Assine os eventos de Armazenamento de Blobs para responder aos eventos criados e excluídos de blob.

|Title  |DESCRIÇÃO  |
|---------|---------|
| [Encaminhar eventos de armazenamento de Blobs para um ponto de extremidade da Web personalizado com CLI do Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar a CLI do Azure para enviar eventos de armazenamento de blob. |
| [Encaminhar eventos de armazenamento de Blobs para um ponto de extremidade da Web personalizado com PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar o Azure PowerShell para enviar eventos de armazenamento de blobs. |
| [Reagindo a eventos de Armazenamento de Blobs](../storage/blobs/storage-blob-event-overview.md) | Visão geral da integração do Armazenamento de Blobs com a Grade de Eventos. |
| [Esquema do evento](event-schema-blob-storage.md) | Mostra campos nos eventos de Armazenamento de Blobs. |

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos, confira [About Event Grid](overview.md) (Sobre a Grade de Eventos).
* Para começar a usar rapidamente a Grade de Eventos, confira [Create and route custom events with Azure Event Grid](custom-event-quickstart.md) (Criar e rotear eventos personalizados com a Grade de Eventos do Azure).
