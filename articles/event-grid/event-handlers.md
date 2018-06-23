---
title: Manipuladores de eventos da Grade de Eventos do Azure
description: Descreve os manipuladores de eventos com suporte para a Grade de Eventos do Azure
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: tomfitz
ms.openlocfilehash: 7c012bdf025a352788aec2d2d70bab33d7914577
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34849535"
---
# <a name="event-handlers-in-azure-event-grid"></a>Manipuladores de eventos na Grade de Eventos do Azure

Um manipulador de eventos é o local para o qual o evento é enviado. O manipulador usa alguma ação adicional para processar o evento. Vários serviços do Azure são automaticamente configurados para manipular eventos. Você também pode usar qualquer webhook para manipular eventos. O webhook não precisa ser hospedado no Azure para manipular eventos. A Grade de Eventos dá suporte apenas a pontos de extremidade do webhook HTTPS.

Este artigo fornece links para conteúdo para cada manipulador de eventos.

## <a name="azure-automation"></a>Automação do Azure

Use a Automação do Azure para processar eventos com runbooks automatizados.

|Title  |DESCRIÇÃO  |
|---------|---------|
|[Integrar a Automação do Azure à Grade de Eventos e ao Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Crie uma máquina virtual, que envia um evento. O evento dispara um runbook de Automação que marca a máquina virtual e dispara uma mensagem que é enviada para um canal do Microsoft Teams. |

## <a name="azure-functions"></a>Funções do Azure

Use o Azure Functions para respostas sem servidor aos eventos.

Ao usar o Azure Functions como o manipulador, use o gatilho Grade de Eventos em vez de gatilhos HTTP genéricos. A Grade de Eventos valida automaticamente os gatilhos de Função da Grade de Eventos. Com os gatilhos HTTP genéricos, você deve implementar a [resposta de validação](security-authentication.md#webhook-event-delivery).

|Title  |DESCRIÇÃO  |
|---------|---------|
| [Gatilho de Grade de Eventos para o Azure Functions](../azure-functions/functions-bindings-event-grid.md) | Visão geral do uso do gatilho da Grade de Eventos no Functions. |
| [Automatizar o redimensionamento de imagens carregadas usando a Grade de Eventos](resize-images-on-storage-blob-upload-event.md) | Os usuários fazem o upload de imagens por meio do aplicativo Web para a conta de armazenamento. Quando um blob de armazenamento é criado, a Grade de Eventos envia um evento para o aplicativo de função, que redimensiona a imagem carregada. |
| [Transmitir Big Data para um data warehouse](event-grid-event-hubs-integration.md) | Quando os Hubs de Eventos criam um arquivo de Captura, a Grade de Eventos envia um evento para um aplicativo de função. O aplicativo recupera o arquivo de Captura e migra dados para um data warehouse. |
| [Exemplos do Barramento de Serviço do Azure para a integração da Grade de Eventos do Azure](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | A Grade de Eventos envia mensagens do tópico do Barramento de Serviço para o aplicativo de função e o aplicativo lógico. |

## <a name="event-hubs"></a>Hubs de Eventos

Use o Hubs de Eventos quando sua solução receber eventos mais rápido do que é capaz de processá-los. Seu aplicativo processa os eventos dos Hubs de Eventos de acordo com sua própria agenda. Você pode dimensionar o processamento dos eventos para manipular os eventos de entrada.

|Title  |DESCRIÇÃO  |
|---------|---------|
| [Encaminhar eventos personalizados para os Hubs de Eventos do Azure com a CLI do Azure e a Grade de Eventos](custom-event-to-eventhub.md) | Envia um evento personalizado para um hub de eventos para processamento por um aplicativo. |

## <a name="hybrid-connections"></a>Conexões Híbridas

Use as Conexões Híbridas de Retransmissão do Azure para enviar eventos para aplicativos que estão em uma rede corporativa e não tem um ponto de extremidade publicamente acessível.

|Title  |DESCRIÇÃO  |
|---------|---------|
| [Selecionar conexão híbrida de eventos](custom-event-to-hybrid-connection.md) | Envia um evento personalizado para uma conexão híbrida existente para processamento por um aplicativo de escuta. |

## <a name="logic-apps"></a>Aplicativos Lógicos

Use aplicativos lógicos para automatizar processos de negócios para responder a eventos.

|Title  |DESCRIÇÃO  |
|---------|---------|
| [Monitorar alterações de máquina virtual com a Grade de Eventos do Azure e os Aplicativos Lógicos](monitor-virtual-machine-changes-event-grid-logic-app.md) | Um aplicativo lógico monitora as alterações feitas em uma máquina virtual e envia emails sobre essas alterações. |
| [Enviar notificações por email sobre os eventos do Hub IoT usando Aplicativos Lógicos](publish-iot-hub-events-to-logic-apps.md) | Um aplicativo lógico envia um email de notificação sempre que um dispositivo é adicionado ao seu hub de IoT. |
| [Exemplos do Barramento de Serviço do Azure para a integração da Grade de Eventos do Azure](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | A Grade de Eventos envia mensagens do tópico do Barramento de Serviço para o aplicativo de função e o aplicativo lógico. |

## <a name="queue-storage"></a>Armazenamento de Filas

Use Armazenamento de filas para receber eventos que precisam ser extraídos. Você pode usar o Armazenamento de Filas quando tem um processo de execução longa que demora muito para responder. Ao enviar eventos para o Armazenamento de Filas, o aplicativo pode receber e processar os eventos de acordo com a própria agenda.

|Title  |DESCRIÇÃO  |
|---------|---------|
| [Encaminhar eventos personalizados para o Armazenamento de Filas do Azure com a CLI do Azure e a Grade de Eventos](custom-event-to-queue-storage.md) | Descreve como enviar eventos personalizados para um Armazenamento de filas. |

## <a name="webhooks"></a>WebHooks

Use webhooks para pontos de extremidade personalizáveis que respondem a eventos.

|Title  |DESCRIÇÃO  |
|---------|---------|
| [Receber eventos em um ponto de extremidade HTTP](receive-events.md) | Descreve como validar um ponto de extremidade HTTP para receber eventos de uma Assinatura de Evento e depois receber e desserializar os eventos. |

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos, confira [About Event Grid](overview.md) (Sobre a Grade de Eventos).
* Para começar a usar rapidamente a Grade de Eventos, confira [Create and route custom events with Azure Event Grid](custom-event-quickstart.md) (Criar e rotear eventos personalizados com a Grade de Eventos do Azure).
