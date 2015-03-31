<properties
	pageTitle="Visão geral de mensagens do Barramento de Serviço - Azure"
	description="Mensagens do Barramento de Serviço: Entrega de dados flexível na nuvem"
	services="service-bus"
	documentationCenter=".net"
	authors="djrosanova"
	manager="timlt"
	editor="mattshel"/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="02/26/2015"
	ms.author="sethm"/>


# Mensagens do Barramento de Serviço: Entrega de dados flexível na nuvem

As mensagens do Barramento de Serviço do Microsoft Azure é um serviço de entrega de informações confiáveis. A finalidade desse serviço é facilitar a comunicação. Quando dois ou mais participantes desejam trocar informações, eles precisam de um mecanismo de comunicação. As mensagens do Barramento de Serviço são um mecanismo de comunicação agenciado ou de terceiros. Isso é semelhante a um serviço postal no mundo físico. Os serviços postais tornam muito fácil enviar tipos diferentes de cartas e pacotes com uma variedade de garantias de entrega em qualquer lugar do mundo.

Semelhante ao serviço de entrega de cartas do serviço postal, as mensagens do Barramento de Serviço do Azure é sobre a entrega de informações flexível do remetente e do destinatário. O serviço de mensagens garante que as informações sejam entregues mesmo se as duas partes nunca estiverem online ao mesmo tempo, ou se elas não estão disponíveis no mesmo exato momento. Dessa forma, o sistema de mensagens é semelhante ao envio de uma carta, enquanto que a comunicação não agenciada é semelhante ao fazer uma chamada telefônica (ou como uma chamada telefônica costumava ser - antes da chamada em espera e da ID de chamador, que são muito mais mensagens agenciadas).

O remetente da mensagem também pode exigir uma variedade de características de entrega, incluindo transações, detecção de duplicidades, expiração com base em tempo e envio em lote. Esses padrões também têm analogias postais: repetir a entrega, assinatura exigida, alteração de endereço ou recuperação.

As mensagens do Barramento de Serviço têm dois recursos separados: filas e tópicos. Ambas as entidades de mensagens oferecem suporte a todos os conceitos apresentados acima - e muito mais. A principal diferença é que tópicos oferecem suporte a recursos de publicação/assinatura que podem ser usados para roteamento baseado em conteúdo sofisticado e lógica de entrega, incluindo o envio para vários destinatários.

## Próximas etapas

Para obter mais informações sobre as mensagens do Barramento de Serviço, consulte os tópicos a seguir.

- [Visão geral da arquitetura de Barramento de Serviço do Azure](../fundamentals-service-bus-hybrid-solutions/)

- [Como usar filas do Barramento de Serviço](../service-bus-dotnet-how-to-use-queues)

- [Como usar tópicos do Barramento de Serviço](../service-bus-dotnet-how-to-use-topics-subscriptions)

<!--HONumber=47-->
