---
title: Comparar a Grade de Eventos, roteamento para Hub IoT | Microsoft Docs
description: O Hub IoT oferece seu próprio serviço de roteamento de mensagens, mas também integra com a Grade de Eventos para publicação de eventos. Compare os dois recursos.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: e7d5ab3ebdf29fbf699220a3b214176ec4862739
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60398716"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Comparar roteamento de mensagens e Grade de Eventos para Hub IoT

O IoT Hub do Azure fornece a capacidade de transmitir dados de seus dispositivos conectados e integrar esses dados em seus aplicativos de negócios. O Hub IoT oferece dois métodos para integrar eventos IoT em outros serviços do Azure ou aplicativos de negócios. Este artigo discute os dois recursos que fornecem essa capacidade para que você possa escolher qual é a melhor opção para seu cenário.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

**[Roteamento de mensagens do IoT Hub](iot-hub-devguide-messages-d2c.md)**: Esse recurso de IoT Hub permite aos usuários rotear mensagens de dispositivo para nuvem para pontos de extremidade de serviço como contêineres de armazenamento do Azure, Hubs de eventos, filas de barramento de serviço e tópicos do barramento de serviço. O roteamento também fornece uma funcionalidade de consulta para filtrar os dados antes de encaminhá-los aos pontos de extremidade. Além dos dados de telemetria do dispositivo, você também pode enviar [eventos de não-telemetria](iot-hub-devguide-messages-d2c.md#non-telemetry-events) que podem ser usados para acionar ações. 

**Integração do IoT Hub com a grade de eventos**: A Grade de Eventos do Azure é um serviço de roteamento de eventos totalmente gerenciado que usa um modelo de publicação/assinatura. U Hub IoT e a Grade de Eventos trabalham em conjunto para [integrar os eventos do Hub IoT em serviços Azure e não-Azure](iot-hub-event-grid.md), quase em tempo real. 

## <a name="differences"></a>Diferenças

Embora o roteamento de mensagens e a Grade de Eventos habilitam a configuração de alerta, há algumas diferenças importantes entre os dois. Consulte a tabela a seguir para obter detalhes:

| Recurso | Roteamento de mensagens do Hub IoT | Integração do Hub IoT com a Grade de Eventos |
| ------- | --------------- | ---------- |
| **Mensagens do dispositivo** | Sim, o roteamento de mensagens pode ser usado para dados telemétricos. | Não, a Grade de Eventos somente pode ser usada para eventos do Hub IoT de não telemetria. |
| **Eventos de dispositivo** | Sim, o roteamento de mensagens pode relatar alteração de gêmeos e eventos de ciclo de vida do dispositivo. | Sim, a Grade de Eventos pode relatar quando os dispositivos são criados, excluídos, conectados e desconectados do Hub IoT |
| **Ordenação** | Sim, a ordenação de eventos é mantida.  | Não, a ordem dos eventos não é garantida. | 
| **Tamanho máximo da mensagem** | 256 KB, dispositivo para nuvem | 64 KB |
| **Filtragem** | Filtragem avançada em propriedades do aplicativo de mensagens, propriedades do sistema de mensagens, corpo da mensagem, tags de gêmeos de dispositivo e propriedades de gêmeos de dispositivo. Para exemplos, consulte [Sintaxe de Consulta de Roteamento de Mensagens](iot-hub-devguide-routing-query-syntax.md). | Filtragem baseada em sufixo/prefixo de IDs de dispositivo, que funciona bem para serviços hierárquicos, como armazenamento. |
| **Pontos de extremidade** | <ul><li>Hubs de Eventos</li> <li>Armazenamento do Blobs do Azure</li> <li>Fila do Barramento de Serviço</li> <li>Tópicos do Barramento de Serviço</li></ul><br>As SKUs do Hub IoT pagas (S1, S2 e S3) estão limitadas a 10 pontos de extremidades personalizados. 100 rotas podem ser criadas por Hub IoT. | <ul><li>Funções do Azure</li> <li>Automação do Azure</li> <li>Hubs de Eventos</li> <li>Aplicativos Lógicos</li> <li>Blob de Armazenamento</li> <li>Tópicos personalizados</li> <li>Serviços de terceiros através de WebHooks</li></ul><br>Para obter a lista mais atualizada de pontos de extremidades, consulte [Manipuladores de eventos da Grade de Eventos](../event-grid/overview.md#event-handlers). |
| **Custo** | Não há encargos separados para roteamento de mensagens. Somente o ingresso de telemetria no Hub IoT é cobrado. Por exemplo, se você tiver uma mensagem roteada para três pontos de extremidades diferentes, você será cobrado por apenas uma mensagem. | Não há nenhum custo do Hub IoT. Grade de eventos oferece as primeiras 100.000 operações por mês gratuitamente e depois, $0,60 por milhão de operações posteriormente. |

## <a name="similarities"></a>Semelhanças

O roteamento de mensagens do Hub IoT e a Grade de Eventos também têm semelhanças, algumas das quais detalhadas na tabela a seguir:

| Recurso | Roteamento de mensagens do Hub IoT | Integração do Hub IoT com a Grade de Eventos |
| ------- | --------------- | ---------- |
| **Confiabilidade** | Alta: Entrega cada mensagem ao ponto de extremidade pelo menos uma vez para cada rota. Expira todas as mensagens que não são entregues dentro de uma hora. | Alta: Entrega cada mensagem ao webhook pelo menos uma vez para cada assinatura. Expira todos os eventos que não são entregues dentro de 24 horas. | 
| **Escalabilidade** | Alta: Otimizado para dar suporte a milhões de dispositivos conectados simultaneamente enviando bilhões de mensagens. | Alta: Capaz de rotear 10.000.000 eventos por segundo por região. |
| **Latência** | Baixa: Quase em tempo real. | Baixa: Quase em tempo real. |
| **Enviar para vários pontos de extremidade** | Sim, envie uma única mensagem para vários pontos de extremidade. | Sim, envie uma única mensagem para vários pontos de extremidade.  | 
| **Segurança** | O HUB Iot fornece identidade por dispositivo e controle de acesso revogável. Para obter mais informações, consulte o [Controle de acesso do Hub IoT](iot-hub-devguide-security.md). | A Grade de Eventos fornece validação em três pontos: assinaturas de eventos, publicação de eventos e entrega de eventos do webhook. Para saber mais, confira [Event Grid security and authentication](../event-grid/security-authentication.md) (Segurança e autenticação da Grade de Eventos). |

## <a name="how-to-choose"></a>Como escolher

O roteamento de mensagens do Hub IoT e a integração do Hub IoT com a Grade de Eventos realizam ações diferentes para obter resultados semelhantes. Ambos tomam informações da solução do Hub IoT e informam para que outros serviços possam reagir. Então, como você decide qual deles usar? Considere as seguintes perguntas para ajudar a orientar sua decisão: 

* **Que tipo de dados você está enviando para os pontos de extremidade?**

   Use o roteamento de mensagens do Hub IoT quando for necessário enviar dados telemétricos para outros serviços. O roteamento de mensagens também permite consultar o aplicativo de mensagens e as propriedades do sistema, o corpo da mensagem, as tags gêmeas do dispositivo e as propriedades gêmeas do dispositivo.

   A integração do Hub IoT com a Grade de Eventos funciona com os eventos que ocorrem no serviço do Hub IoT. Esses eventos do Hub IoT incluem a criação, exclusão, conexão e desconexão de dispositivos. 

* **Quais pontos de extremidade precisam receber essas informações?**

   O roteamento de mensagens do Hub IoT oferece suporte para pontos de extremidade limitados, mas você pode compilar conectores para redirecionar dados e eventos para pontos de extremidade adicionais. Para obter uma lista completa dos pontos de extremidade com suporte, consulte a tabela na seção anterior. 

   A integração do Hub IoT com a Grade de Eventos oferece suporte a mais pontos de extremidade. Adicionalmente, trabalha com Webhooks para estender o roteamento fora do ecossistema do serviço do Azure e em aplicativos de negócios de terceiros. 

* **É importante os dados chegarem em ordem?**

   O roteamento de mensagens do Hub IoT mantém a ordem em que as mensagens são enviadas para que elas cheguem da mesma maneira.

   A Grade de Eventos não garante que os pontos de extremidade receberão eventos na mesma ordem em que ocorreram. No entanto, o esquema de eventos inclui um carimbo data/hora que pode ser usado para identificar a ordem após os eventos chegarem ao ponto de extremidade. 

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre [Roteamento de Mensagens do Hub IoT ](iot-hub-devguide-messages-d2c.md) e os [endpoints do Hub IoT ](iot-hub-devguide-endpoints.md).
* Saiba mais sobre a [Grade de Eventos do Azure](../event-grid/overview.md).
* Para aprender como criar Rotas de Mensagens, confira o tutorial [Processar mensagens de dispositivo para nuvem do Hub IoT usando rotas](../iot-hub/tutorial-routing.md).
* Experimente a integração da grade de eventos por [enviar notificações de email sobre eventos do IoT Hub do Azure usando aplicativos lógicos](../event-grid/publish-iot-hub-events-to-logic-apps.md).