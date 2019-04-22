---
title: Solucionar problemas de erros do AMQP no barramento de serviço do Azure | Microsoft Docs
description: Fornece uma lista de erros AMQP, você pode receber ao usar o barramento de serviço do Azure e fazer com que esses erros.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2019
ms.author: aschhab
ms.openlocfilehash: 85d24a9e7c753ec5dba80c4f259dd3fb51d9c14b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58910039"
---
# <a name="amqp-errors-in-azure-service-bus"></a>Erros AMQP no barramento de serviço do Azure
Este artigo fornece alguns dos erros que você recebe ao usar AMQP com o barramento de serviço do Azure. Eles são todos os comportamentos padrão do serviço. Você pode evitá-las por meio de chamadas de enviar/receber em conexão/link, que recria automaticamente o link/conexão.

## <a name="link-is-closed"></a>Link é fechado 
Você vê o seguinte erro quando a conexão AMQP e o link estiver ativo, mas nenhuma chamada (por exemplo, enviar ou receber) são feitas usando o link para 10 minutos. Portanto, o link está fechado. A conexão ainda está aberto.

```
amqp:link:detach-forced:The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker due to errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:10:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp:2/16/2018 11:10:40 PM
```

## <a name="connection-is-closed"></a>Conexão é fechada
Você verá o erro a seguir sobre a conexão AMQP quando todos os links de conexão foram fechados porque não houve atividade (ocioso) e um novo link não foi criado em 5 minutos.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}
```

## <a name="link-is-not-created"></a>Link não é criado. 
Você verá esse erro quando uma nova conexão de AMQP é criado, mas não é criado um link dentro de 1 minuto da criação de Conexão do AMQP.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 60000 milliseconds and is closed by container 'LinkTracker'. TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T18:41:51', info=null}
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o AMQP e o Barramento de Serviço, visite os seguintes links:

* [Visão geral do Barramento de Serviço para AMQP]
* [Guia do protocolo AMQP 1.0]
* [AMQP no Barramento de Serviço para Windows Server]

[Visão geral do Barramento de Serviço para AMQP]: service-bus-amqp-overview.md
[Guia do protocolo AMQP 1.0]: service-bus-amqp-protocol-guide.md
[AMQP no Barramento de Serviço para Windows Server]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)
