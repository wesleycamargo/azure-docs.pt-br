---
title: Comparar a Grade de Eventos, roteamento para Hub IoT | Microsoft Docs
description: "O Hub IoT oferece seu próprio serviço de roteamento de mensagens, mas também integra com a Grade de Eventos para publicação de eventos. Compare os dois recursos."
services: iot-hub
documentationcenter: 
author: kgremban
manager: timlt
editor: 
ms.service: iot-hub
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2018
ms.author: kgremban
ms.openlocfilehash: 5a0a97ccf033b2981ba13be455482146ba212228
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Comparar roteamento de mensagens e Grade de Eventos para Hub IoT

O Hub IoT do Azure fornece a capacidade de transmitir por streaming dados de seus dispositivos conectados e integrar esses dados nos aplicativos de negócios. O Hub IoT oferece dois métodos para integrar eventos IoT em outros serviços do Azure ou aplicativos de negócios. Este artigo discute os dois recursos que fornecem essa capacidade para que você possa escolher qual é a melhor opção para seu cenário.

* **Roteamento de mensagens do Hub IoT**: esse recurso do Hub IoT habilita os usuários a [rotear as mensagens do dispositivo para a nuvem](iot-hub-devguide-messages-read-custom.md) para pontos de extremidade de serviço como contêineres de Armazenamento do Microsoft Azure, Hubs de Eventos, filas de Barramento de Serviço e tópicos de Barramento de Serviço. As regras de roteamento fornecem flexibilidade para realizar rotas baseadas em consulta. Eles também habilitam [alertas críticos](iot-hub-devguide-messages-d2c.md) que disparam ações através de consultas e podem ser baseados nos cabeçalhos e corpo da mensagem. 
* **Integração do Hub IoT com a Grade de Eventos**: a Grade de Eventos do Azure é um serviço de roteamento de eventos totalmente gerenciado que usa um modelo de publicação/assinatura. U Hub IoT e a Grade de Eventos trabalham em conjunto para [integrar os eventos do Hub IoT em serviços Azure e não-Azure](iot-hub-event-grid.md), quase em tempo real. 

## <a name="similarities-and-differences"></a>Semelhanças e diferenças

Embora o roteamento de mensagens e a Grade de Eventos habilitam a configuração de alerta, há algumas diferenças importantes entre os dois. Consulte a tabela a seguir para obter detalhes:

| Recurso | Roteamento de mensagens do Hub IoT | Integração do Hub IoT com a Grade de Eventos |
| ------- | --------------- | ---------- |
| **Mensagens do dispositivo** | Sim, o roteamento de mensagens pode ser usado para dados telemétricos. | Não, a Grade de Eventos somente pode ser usada para eventos do Hub IoT de não telemetria. |
| **Tipo de evento** | Sim, o roteamento de mensagens pode relatar alteração de gêmeos e eventos de ciclo de vida do dispositivo. | Sim, a Grade de Eventos pode relatar quando os dispositivos são registrados em um Hub IoT e quando os dispositivos são excluídos. |
| **Ordenação** | Sim, a ordenação de eventos é mantida.  | Não, a ordem dos eventos não é garantida. | 
| **Tamanho máximo da mensagem** | 256 KB, dispositivo para nuvem | 64 KB |
| **Filtragem** | A filtragem por linguagem SQL sofisticada oferece suporte à filtragem em corpos e cabeçalhos de mensagens. Por exemplo, consulte [Linguagem de consulta do Hub IoT](iot-hub-devguide-query-language.md). | Filtragem baseada em sufixo/prefixo de IDs de dispositivo, que funciona bem para serviços hierárquicos, como armazenamento. |
| **Pontos de extremidade** | <ul><li>Hub de evento</li> <li>Armazenamento de blobs</li> <li>Fila do Barramento de Serviço</li> <li>Tópicos do Barramento de Serviço</li></ul><br>As SKUs do Hub IoT pagas (S1, S2 e S3) estão limitadas a 10 pontos de extremidades personalizados. 100 rotas podem ser criadas por Hub IoT. | <ul><li>Funções do Azure</li> <li>Automação do Azure</li> <li>Hub de evento</li> <li>Aplicativos Lógicos</li> <li>Microsoft Flow</li> <li>Serviços de terceiros através de WebHooks</li></ul><br>Para obter a lista mais atualizada de pontos de extremidades, consulte [Manipuladores de eventos da Grade de Eventos](../event-grid/overview.md#event-handlers). |
| **Custo** | Não há encargos separados para roteamento de mensagens. Somente o ingresso de telemetria no Hub IoT é cobrado. Por exemplo, se você tiver uma mensagem roteada para três pontos de extremidades diferentes, você será cobrado por apenas uma mensagem. | Não há nenhum custo do Hub IoT. A Grade de Eventos oferece as primeiras 100.000 operações por mês e gratuitamente, depois, $0,60 por milhão de operações após isso. |

O roteamento de mensagens do Hub IoT e a Grade de Eventos também têm semelhanças, algumas das quais detalhadas na tabela a seguir:

| Recurso | Roteamento de mensagens do Hub IoT | Integração do Hub IoT com a Grade de Eventos |
| ------- | --------------- | ---------- |
| **Confiabilidade** | Alta: entrega cada mensagem ao ponto de extremidade pelo menos uma vez por cada rota. Expira todas as mensagens que não são entregues dentro de uma hora. | Alta: entrega cada mensagem ao webhook pelo menos uma vez por cada assinatura. Expira todos os eventos que não são entregues dentro de 24 horas. | 
| **Escalabilidade** | Alta: otimizado para suportar milhões de dispositivos conectados simultaneamente enviando bilhões de mensagens. | Alto: capaz de rotear 10.000.000 eventos por segundo por região. |
| **Latência** | Baixa: quase em tempo real. | Baixa: quase em tempo real. |
| **Enviar para vários pontos de extremidade** | Sim, envie uma única mensagem para vários pontos de extremidade. | Sim, envie uma única mensagem para vários pontos de extremidade.  | 
| **Segurança** | O HUB Iot fornece identidade por dispositivo e controle de acesso revogável. Para obter mais informações, consulte o [Controle de acesso do Hub IoT](iot-hub-devguide-security.md). | A Grade de Eventos fornece validação em três pontos: assinaturas de eventos, publicação de eventos e entrega de eventos do webhook. Para saber mais, confira [Event Grid security and authentication](../event-grid/security-authentication.md) (Segurança e autenticação da Grade de Eventos). |

## <a name="how-to-choose"></a>Como escolher

O roteamento de mensagens do Hub IoT e a integração do Hub IoT com a Grade de Eventos realizam ações diferentes para obter resultados semelhantes. Ambos tomam informações da solução do Hub IoT e informam para que outros serviços possam reagir. Então, como você decide qual deles usar? Além dos dados da seção anterior, use as seguintes perguntas para ajudar a orientar sua decisão: 

* **Que tipo de dados você está enviando para os pontos de extremidade?**

   Use o roteamento de mensagens do Hub IoT quando for necessário enviar dados telemétricos para outros serviços. O roteamento de mensagens também permite consultar cabeçalhos de mensagens e corpos de mensagens. 

   A integração do Hub IoT com a Grade de Eventos funciona com os eventos que ocorrem no serviço do Hub IoT. Esses eventos do Hub IoT incluem criação e exclusão de dispositivos. 

* **Quais pontos de extremidade precisam receber essas informações?**

   O roteamento de mensagens do Hub IoT oferece suporte para pontos de extremidade limitados, mas você pode compilar conectores para redirecionar dados e eventos para pontos de extremidade adicionais. Para obter uma lista completa dos pontos de extremidade com suporte, consulte a tabela na seção anterior. 

   A integração do Hub IoT com a Grade de Eventos oferece suporte a mais pontos de extremidade. Adicionalmente, trabalha com Webhooks para estender o roteamento fora do ecossistema do serviço do Azure e em aplicativos de negócios de terceiros. 

* **É importante os dados chegarem em ordem?**

   O roteamento de mensagens do Hub IoT mantém a ordem em que as mensagens são enviadas para que elas cheguem da mesma maneira.

   A Grade de Eventos não garante que os pontos de extremidade receberão eventos na mesma ordem em que ocorreram. No entanto, o esquema de eventos inclui um carimbo data/hora que pode ser usado para identificar a ordem após os eventos chegarem ao ponto de extremidade. 

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [roteamento de mensagens do Hub IoT](iot-hub-devguide-messages-d2c.md) e os [Pontos de extremidade do Hub IoT ](iot-hub-devguide-endpoints.md).

* Saiba mais sobre a [Grade de Eventos do Azure](../event-grid/overview.md)

* Experimente a integração da Grade de Eventos por [Enviar notificações por email sobre os eventos do Hub IoT do Azure usando Aplicativos Lógicos](../event-grid/publish-iot-hub-events-to-logic-apps.md)