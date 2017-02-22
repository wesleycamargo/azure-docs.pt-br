---
title: "Configurações de porta de Retransmissão do WCF no Barramento de Serviço do Azure | Microsoft Docs"
description: "Detalhes sobre os valores de porta de Retransmissão do WCF no Barramento de Serviço."
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/23/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: f222caa272b86ff42873df09561ca4f6c6e6aa26
ms.openlocfilehash: ba499a98960b0cf2f865e2d349003cd8cb65b526


---

# <a name="azure-relay-port-settings"></a>Configurações de porta de Retransmissão do Azure

A tabela a seguir descreve a configuração necessária dos valores de porta para uma associação de Retransmissão do WCF no Barramento de Serviço.

## <a name="ports"></a>Portas
  
|Associação|Segurança de transporte|Porta|  
|-------------|------------------------|----------|  
|[Classe BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (cliente)|Sim|HTTPS| 
| |" |Não|HTTP|  
|[Classe BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (serviço)|Você pode usar o|9351/HTTP|  
|[Classe NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (cliente)|Sim|9351/HTTPS|  
||" |Não|9350/HTTP|  
|[Classe NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (serviço)|Você pode usar o|9351/HTTP|  
|[Classe NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (cliente/serviço)|Você pode usar o|5671/9352/HTTP (9352/9353 se híbrido for usado)|  
|[Classe NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (cliente)|Sim|9351/HTTPS|  
||" |Não|9350/HTTP|  
|[Classe NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (serviço)|Você pode usar o|9351/HTTP|  
|[Classe WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (cliente)|Sim|HTTPS|  
||" |Não|HTTP|  
|[Classe WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (serviço)|Você pode usar o|9351/HTTP|  
|[Classe WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (cliente)|Sim|HTTPS|  
||" |Não|HTTP|  
|[Classe WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (serviço)|Você pode usar o|9351/HTTP|

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as mensagens do Barramento de Serviço, confira os tópicos a seguir.

* [Conceitos fundamentais do barramento de serviço](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
* [Filas, tópicos e assinaturas do Barramento de Serviço](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)
* [Como usar filas do Barramento de Serviço](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)
* [Como usar tópicos e assinaturas do Barramento de Serviço](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md)



<!--HONumber=Nov16_HO4-->


