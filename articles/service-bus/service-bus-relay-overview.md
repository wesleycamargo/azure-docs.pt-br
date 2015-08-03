<properties
	pageTitle="Sistema de mensagens de retransmissão do Barramento de Serviço"
	description="Visão geral da retransmissão do Barramento de Serviço"
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
	ms.date="06/04/2015"
	ms.author="sethm"/>


# Sistema de mensagens de retransmissão do Barramento de Serviço

O componente central do Barramento de Serviço é um serviço de retransmissão centralizado (mas com alto balanceamento de carga) que permite a compilação de aplicativos híbridos que executem tanto no datacenter do Azure quanto em seu próprio ambiente corporativo local. O serviço de retransmissão oferece suporte a vários protocolos de transporte e padrões de serviços Web. Isso inclui SOAP, WS-* e até REST. A retransmissão do Barramento de Serviço facilita seus aplicativos híbridos permitindo que você exiba com segurança os serviços da Windows Communication Foundation (WCF) que residem em uma rede corporativa para a nuvem pública, sem precisar abrir uma conexão de firewall ou exigir mudanças intrusivas em uma infraestrutura de rede corporativa.

![Conceitos de retransmissão](./media/service-bus-relay-overview/sb-relay-01.png)

O serviço de retransmissão oferece suporte às mensagens unidirecionais tradicionais, mensagens de solicitação/resposta e mensagens ponto a ponto. Ele também oferece suporte a distribuição de eventos no escopo da Internet para habilitar cenários de publicação/assinatura, e a comunicação de soquete bidirecional para maior eficiência ponto a ponto.

No padrão do sistema de mensagens de retransmissão, um serviço local conecta-se ao serviço de retransmissão por meio de uma porta de saída e cria um soquete bidirecional para comunicação ligado a um endereço específico de reunião. Em seguida, o cliente pode se comunicar com o serviço local enviando mensagens para o serviço de retransmissão direcionando o endereço de reunião. O serviço de retransmissão "retransmitirá" as mensagens ao serviço local por meio do soquete bidirecional já instalado. O cliente não precisa de uma conexão direta com o serviço local, não precisa saber onde reside o serviço, e o serviço local não precisa de qualquer porta de entrada aberta no firewall.

Você inicia a conexão entre o serviço local e o serviço de retransmissão usando um conjunto de associações de "retransmissão" WCF. Nos bastidores, as associações de retransmissão são mapeadas para novos elementos de ligação de transporte projetados para criar componentes de canal WCF que são integrados ao Barramento de Serviço na nuvem.

## Próximas etapas

Para saber mais sobre a retransmissão do Barramento de Serviço, confira os tópicos a seguir.

- [Visão geral da arquitetura de Barramento de Serviço do Azure](fundamentals-service-bus-hybrid-solutions.md)

- [Como usar o serviço de Retransmissão do Barramento de Serviço](service-bus-dotnet-how-to-use-relay.md)

 

<!---HONumber=July15_HO4-->