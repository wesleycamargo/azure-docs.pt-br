<properties
	pageTitle="Visão geral de mensagens do Barramento de Serviço | Microsoft Azure"
	description="Mensagens do Barramento de Serviço: entrega de dados flexível na nuvem"
	services="service-bus"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="09/04/2015"
	ms.author="sethm"/>


# Mensagens do Barramento de Serviço: entrega de dados flexível na nuvem

Mensagens do Barramento de Serviço é um serviço confiável de entrega de informações. A finalidade desse serviço é facilitar a comunicação. Quando dois ou mais participantes desejam trocar informações, eles precisam de um mecanismo de comunicação. As mensagens do Barramento de Serviço são um mecanismo de comunicação agenciado ou de terceiros. Isso é semelhante a um serviço postal no mundo físico. Os serviços postais tornam muito fácil enviar tipos diferentes de cartas e pacotes com uma variedade de garantias de entrega em qualquer lugar do mundo.

Semelhante ao serviço de entrega de cartas do serviço postal, as mensagens do Barramento de Serviço do Azure é sobre a entrega de informações flexível do remetente e do destinatário. O serviço de mensagens garante que as informações sejam entregues mesmo se as duas partes nunca estiverem online ao mesmo tempo, ou se elas não estão disponíveis no mesmo exato momento. Dessa forma, o sistema de mensagens é semelhante ao envio de uma carta, enquanto que a comunicação não agenciada é semelhante ao fazer uma chamada telefônica (ou como uma chamada telefônica costumava ser - antes da chamada em espera e da ID de chamador, que são muito mais mensagens agenciadas).

O remetente da mensagem também pode exigir uma variedade de características de entrega, incluindo transações, detecção de duplicidades, expiração com base em tempo e envio em lote. Esses padrões também têm analogias postais: repetir a entrega, assinatura exigida, alteração de endereço ou recuperação.

O Barramento de Serviço oferece suporte a dois padrões distintos de sistema de mensagens: *retransmissão* e *agenciado*.

## Sistema de mensagens de retransmissão

O componente de retransmissão do Barramento de Serviço é um serviço centralizado (mas altamente balanceado por carga) que oferece suporte a uma variedade de diferentes protocolos de transporte e padrões de serviços Web. Isso inclui SOAP, WS-* e até REST. O serviço de retransmissão fornece uma variedade de diferentes opções de conectividade de retransmissão e pode ajudar a negociar conexões diretas ponto a ponto quando é possível. O Barramento de Serviço foi otimizado para desenvolvedores de .NET que usam o WCF (Windows Communication Foundation), tanto em relação ao desempenho quanto à usabilidade, além de fornecer acesso completo ao respectivo serviço de retransmissão por meio de interfaces REST e SOAP. Isso possibilita que qualquer ambiente de programação SOAP ou REST integre-se ao Barramento de Serviço.

O serviço de retransmissão oferece suporte às mensagens unidirecionais tradicionais, mensagens de solicitação/resposta e mensagens ponto a ponto. Ele também oferece suporte à distribuição de eventos no escopo da Internet para habilitar cenários de publicação/assinatura e a comunicação de soquete bidirecional para maior eficiência ponto a ponto. No padrão do sistema de mensagens de retransmissão, um serviço local conecta-se ao serviço de retransmissão por meio de uma porta de saída e cria um soquete bidirecional para comunicação ligado a um endereço específico de reunião. Em seguida, o cliente pode se comunicar com o serviço local enviando mensagens para o serviço de retransmissão direcionando o endereço de reunião. O serviço de retransmissão "retransmitirá" as mensagens ao serviço local por meio do soquete bidirecional já instalado. O cliente não precisa de uma conexão direta com o serviço local, nem precisa saber onde reside o serviço, e o serviço local não precisa de alguma porta de entrada aberta no firewall.

Você deve iniciar a conexão entre o serviço local e o serviço de retransmissão usando um conjunto de associações de "retransmissão" do WCF. Nos bastidores, as associações de retransmissão são mapeadas para elementos de associação de transporte projetados para criar componentes de canal WCF que são integrados ao Barramento de Serviço na nuvem.

O sistema de mensagens de retransmissão oferece muitos benefícios, mas exige que servidor e cliente estejam online ao mesmo tempo para envio e recebimento de mensagens. Isso não é ideal para comunicação no estilo HTTP, em que as solicitações normalmente não têm vida longa, nem para clientes que se conectam apenas ocasionalmente, como navegadores, aplicativos móveis, etc. O sistema de mensagens agenciado oferece suporte à comunicação separada e tem suas próprias vantagens; clientes e servidores podem se conectar quando necessário e executar suas operações de maneira assíncrona.

## Sistema de mensagens agenciado

Ao contrário do esquema do sistema de mensagens de retransmissão, o sistema de mensagens agenciado pode ser pensado como assíncrono, ou "temporariamente separado". Os produtores (remetentes) e consumidores (destinatários) não precisam estar online ao mesmo tempo. A infraestrutura do sistema de mensagens armazena mensagens de maneira confiável em um "agente" (como uma fila) até que a parte consumidora esteja pronta para recebê-las. Isso permite que os componentes do aplicativo distribuído sejam desconectados, ou voluntariamente, para manutenção, por exemplo, ou devido a uma falha de componente, sem afetar o sistema inteiro. Além disso, o aplicativo de recebimento talvez tenha que ficar online apenas durante determinados períodos do dia, como um sistema de gerenciamento de inventário, que precisa ser executado apenas no fim do dia.

Os principais componentes da infraestrutura do sistema de mensagens agenciado do Barramento de Serviço são filas, tópicos e assinaturas. A principal diferença é que tópicos oferecem suporte a recursos de publicação/assinatura que podem ser usados para roteamento baseado em conteúdo sofisticado e lógica de entrega, incluindo o envio para vários destinatários. Esses componentes permitem novos cenários de sistema de mensagens assíncrono, como separação temporal, publicação/assinatura e balanceamento de carga. Para saber mais sobre essas entidades de sistema de mensagens, consulte [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md).

Assim como na infraestrutura do sistema de mensagens de retransmissão, o recurso do sistema de mensagens agenciado é fornecido para programadores do .NET Framework e WCF, e também via REST.

## Próximas etapas

Para saber mais sobre as mensagens do Barramento de Serviço, confira os tópicos a seguir.

- [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
- [Visão geral da arquitetura de Barramento de Serviço do Azure](service-bus-fundamentals-hybrid-solutions.md)
- [Como usar filas do Barramento de Serviço](service-bus-dotnet-how-to-use-queues.md)
- [Como usar tópicos do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)
 

<!---HONumber=Sept15_HO2-->