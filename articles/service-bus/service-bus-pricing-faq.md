<properties 
   pageTitle="Perguntas frequentes sobre preços de Barramento de Serviço | Microsoft Azure"
   description="Responde a algumas perguntas frequentes sobre a estrutura de preços do Barramento de Serviço."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2015"
   ms.author="sethm" />

# Perguntas Frequentes sobre Preços do Barramento de Serviço

Esta seção responde a perguntas frequentes sobre a estrutura de preços do Barramento de Serviço. Você também pode visitar as [Perguntas frequentes de suporte do Azure](http://go.microsoft.com/fwlink/?LinkID=185083) para informações gerais sobre preços do Microsoft Azure. Para saber mais sobre o preço do Barramento de Serviço, consulte [Detalhes de preço do Barramento de Serviço](http://azure.microsoft.com/pricing/details/service-bus/).

>[AZURE.NOTE]A estrutura de preços de Hubs de Eventos está descrita em [Perguntas frequentes sobre disponibilidade e suporte dos Hubs de Eventos](event-hubs-availability-and-support-faq.md), com mais informações no tópico [Preços de Hubs de Eventos](http://azure.microsoft.com/pricing/details/event-hubs/).

- [Como é cobrado o Barramento de Serviço?](#how-do-you-charge-for-service-bus)
- [Quais usos do Barramento de Serviço estão sujeitos à transferência de dados? O que não está?](#what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not)
- [O que é exatamente uma "retransmissão" do Barramento de Serviço?](#what-exactly-is-a-service-bus-quotrelayquot)
- [Como é calculado o medidor de horas de retransmissão?](#how-is-the-relay-hours-meter-calculated)
- [E se eu tiver mais de um ouvinte conectado a uma determinada retransmissão?](#what-if-i-have-more-than-one-listener-connected-to-a-given-relay)
- [Como é calculado o medidor mensagens para retransmissões?](#how-is-the-messages-meter-calculated-for-relays)
- [O Barramento de Serviço cobra pelo armazenamento?](#does-service-bus-charge-for-storage)
- [O Barramento de Serviço tem cotas de uso?](#does-service-bus-have-any-usage-quotas)

## Como é cobrado o Barramento de Serviço?

Para obter informações completas sobre os preços de Barramento de Serviço, consulte [Preços de Barramento de Serviço e cobrança](https://msdn.microsoft.com/library/dn831889.aspx) e [Detalhes de preço de Barramento de Serviço](http://azure.microsoft.com/pricing/details/service-bus/). Além dos preços mencionados, você é cobrado por transferências de dados associadas para saída fora do data center em que seu aplicativo está provisionado. Encontre mais detalhes em [Quais usos do Barramento de Serviço estão sujeitos à transferência de dados? na seção O que não está?](#what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not) abaixo.

## Quais usos do barramento de serviço estão sujeitos à transferência de dados? O que é não está?

Todas as transferências de dados dentro de uma determinada região do Azure são feitas gratuitamente. Todas as transferências de dados fora de uma região estão sujeitas a encargos de saída a uma taxa de US$ 0,15 por GB a partir das regiões América do Norte e Europa e US$ 0,20 por GB da região do Pacífico Asiático. Todas as transferências de dados de entrada são feitas gratuitamente.

## O que é exatamente uma "retransmissão" do Barramento de Serviço?

Uma retransmissão é uma entidade do Barramento de Serviço que retransmite mensagens entre clientes e serviços Web. A retransmissão fornece o serviço com um endereço de Barramento de Serviço persistente e detectável, conectividade confiável com recursos de passagem NAT/firewall e recursos adicionais como balanceamento de carga automático. Uma retransmissão é implicitamente instanciada e aberta em um determinado endereço de Barramento de Serviço (URL do namespace) sempre que um serviço WCF habilitado para retransmissão, ou "ouvinte de retransmissão", se conectar pela primeira vez a esse endereço. Aplicativos criam ouvintes de retransmissão usando a API gerenciada pelo.NET do Barramento de Serviço, que fornece versões especiais habilitadas para retransmissão das associações WCF padrão.

## Como é calculado o medidor de horas de retransmissão?

As horas de retransmissão são cobradas pela quantidade cumulativa de tempo durante o qual cada Retransmissão do Barramento de Serviço de retransmissão fica “aberto” em um determinado período de cobrança. Uma retransmissão é implicitamente instanciada e aberta em um determinado endereço do Barramento de Serviço (URL do namespace de serviço) quando um serviço WCF habilitado para retransmissão, ou "ouvinte de retransmissão", se conecta pela primeira vez a esse endereço. A retransmissão só é fechada quando o último ouvinte se desconecta de seu endereço. Portanto, para fins de cobrança, uma retransmissão é considerada "aberta" desde o momento em que o primeiro ouvinte de retransmissão se conecta até a hora em que do último ouvinte de retransmissão se desconecta do endereço do Barramento de Serviço dessa retransmissão. Em outras palavras, uma retransmissão é considerada "aberta" sempre que um ou mais ouvintes de retransmissão estiverem conectados ao seu endereço de Barramento de Serviço.

## E se eu tiver mais de um ouvinte conectado a uma determinada retransmissão?

Em alguns casos, uma única retransmissão no Barramento de Serviço pode ter vários ouvintes conectados. Isso pode ocorrer com serviços de balanceamento de carga que usam as associações WCF **netTCPRelay** ou **HttpRelay**, ou com ouvintes de eventos de transmissão que usam o **netEventRelay** associação WCF. Uma retransmissão no Barramento de Serviço é considerada "aberta" quando pelo menos um ouvinte de retransmissão estiver conectado a ele. A adição de outros ouvintes para uma retransmissão aberta não altera o status dessa retransmissão para fins de cobrança. O número de remetentes de retransmissão (clientes que chamam ou enviam mensagens para retransmissões) conectados a uma retransmissão também não tem efeito sobre o cálculo de horas de retransmissão.

## Como é calculado o medidor mensagens para retransmissões?

Em geral, as mensagens cobráveis são calculadas para retransmissões usando o mesmo método descrito acima para entidades agenciadas (filas, tópicos e assinaturas). No entanto, há algumas diferenças perceptíveis:

1. O envio de uma mensagem para uma retransmissão do Barramento de Serviço é tratado como um envio “full through” para o ouvinte de retransmissão que recebe a mensagem, em vez de um envio para a retransmissão de Barramento de Serviço seguido por uma entrega para o ouvinte de retransmissão. Portanto, uma invocação de serviço do tipo solicitação-resposta (de até 64 KB) em relação a um ouvinte de retransmissão resultará em duas mensagens cobráveis: uma mensagem cobrável para a solicitação e uma mensagem cobrável para a resposta (supondo que a resposta também seja < = 64 KB). Isso é diferente de usar uma fila para atuar como mediador entre um cliente e um serviço. Neste último caso, o mesmo padrão de solicitação-resposta exigiria um envio de solicitação para a fila, seguido por uma remoção da fila/entrega da fila para o serviço, seguido por um envio de resposta para outra fila e uma remoção da fila/entrega dessa fila para o cliente. Usando as mesmas suposições de tamanho (< = 64 KB) para tudo, o padrão de fila intermediado resultaria em quatro mensagens cobráveis, duas vezes o número cobrado para implementar o mesmo padrão usando a retransmissão. É claro que há vantagens em usar filas para atingir esse padrão, como durabilidade e nivelamento de carga. Essas vantagens podem justificar a despesa adicional.

2. As retransmissões abertas usando a associação WCF **netTCPRelay** tratam as mensagens não como mensagens individuais, mas como um fluxo de dados através do sistema. Em outras palavras, somente o remetente e o ouvinte têm visibilidade do enquadramento das mensagens individuais enviadas/recebidas usando essa associação. Assim, para retransmissões usando a associação **netTCPRelay**, todos os dados são tratados como um fluxo para fins de cálculo das mensagens cobráveis. Nesse caso, o Barramento de Serviço calculará a quantidade total de dados enviados ou recebidos por meio de cada retransmissão individual a cada 5 minutos e dividirá esse total por 64 KB para determinar o número de mensagens cobráveis pela retransmissão em questão durante aquele período.

## O Barramento de Serviço cobra pelo armazenamento?

Não, o Barramento de Serviço não cobra pelo armazenamento. No entanto, há uma cota que limita a quantidade máxima de dados que podem persistir por fila/tópico. Consulte as Perguntas Frequentes a seguir.

## O Barramento de Serviço tem cotas de uso?

Por padrão, a Microsoft define uma cota de uso mensal agregado calculada entre todas as assinaturas do cliente para qualquer serviço de nuvem. Como compreendemos que talvez seja necessário usar mais do que esses limites, contate o atendimento ao cliente a qualquer momento para que possamos entender as suas necessidades e ajustar esses limites adequadamente. Para o Barramento de Serviço, as cotas totais de uso são:

- 5 bilhões de mensagens

- 2 milhões de horas de retransmissão

Embora nos reservemos o direito de desabilitar uma conta de cliente que tenha excedido suas cotas de uso em um determinado mês, enviamos aviso por email e fazemos várias tentativas de contatar o cliente antes de realizar qualquer ação. Os clientes que excederem essas cotas ainda serão responsáveis pelas cobranças que excederem as cotas.

Como ocorre com outros serviços no Azure, o Barramento de Serviço aplica um conjunto de cotas específicas para garantir que haja um uso inteligente dos recursos. A seguir estão as cotas de uso impostas pelo serviço:

- **Tamanho da fila/tópico**: especifique o tamanho máximo de fila ou tópico no momento da sua criação. Essa cota pode ter um valor de 1, 2, 3, 4 ou 5 GB. Se tamanho máximo for atingido, as mensagens de entrada adicionais serão rejeitadas e uma exceção será recebida pelo código de chamada.

- **Número de conexões simultâneas**
	- **Fila/Tópico/Assinatura**: o número de conexões TCP simultâneas em uma fila/tópico/assinatura é limitado a 100. Se essa cota for atingida, as solicitações subsequentes de conexões adicionais serão rejeitadas e uma exceção será recebida pelo código de chamada. Para cada fábrica de mensagens, o Barramento de Serviço mantém uma conexão TCP se algum dos clientes criados por essa fábrica de mensagens tiver uma operação ativa pendente ou tiver concluído uma operação há menos de 60 segundos. Operações REST não são consideradas conexões TCP simultâneas.


- **Número de ouvintes simultâneos em uma retransmissão**: o número de ouvintes simultâneos **netTcpRelay** e **netHttpRelay** em uma retransmissão é limitado a 25 (1 para uma retransmissão **NetOneway**).

- **Número de ouvintes de retransmissão simultâneos por namespace**: o Barramento de Serviço impõe um limite de 2000 ouvintes de retransmissão simultâneos por namespace de serviço. Se essa cota for atingida, as solicitações subsequentes de ouvintes de retransmissão adicionais serão rejeitadas e uma exceção será recebida pelo código de chamada.

- **Número de tópicos/filas por namespace de serviço**: o número máximo de tópicos/filas (entidades de armazenamento duráveis) em um namespace de serviço é limitado a 10 mil. Se essa cota for atingida, as solicitações subsequentes para a criação de uma novo tópico/fila no namespace de serviço serão rejeitadas. Nesse caso, o [portal clássico do Azure][] exibirá uma mensagem de erro ou o código de cliente de chamada receberá uma exceção, dependendo se a tentativa foi feita por meio do portal ou no código do cliente.

- **Cotas de tamanho de mensagem**
	- **Fila/Tópico/Assinatura**
		- **Tamanho da mensagem** – cada mensagem está limitada a um tamanho total de 256KB, incluindo os cabeçalhos da mensagem.
		- **Tamanho do cabeçalho da mensagem** – cada cabeçalho de mensagem é limitado a 64KB.

	- **NetOneway e NetEvent** – cada mensagem é limitada a um tamanho total de 64KB, incluindo os cabeçalhos da mensagem.
	- **Retransmissões http e NetTcp** – o Barramento de Serviço não impõe um limite superior ao tamanho dessas mensagens.

	As mensagens que excederem essas cotas de tamanho serão rejeitadas e uma exceção será recebida pelo código de chamada.

- **Número de assinaturas por tópico** – o número máximo de assinaturas por tópico é limitado a 2 mil. Se essa cota for atingida, as solicitações subsequentes para a criação de assinaturas adicionais para o tópico serão rejeitadas. Nesse caso, o [portal clássico do Azure][] exibirá uma mensagem de erro ou o código de cliente de chamada receberá uma exceção, dependendo se a tentativa foi feita por meio do portal ou no código do cliente.

- **Número de filtros SQL por tópico** – o número máximo de filtros SQL por tópico é limitado a 2 mil. Se essa cota for atingida, as solicitações subsequentes de criação de filtros adicionais para o tópico serão rejeitadas e uma exceção será recebida pelo código de chamada.

- **Filtros de número de correlação por tópico** – o número máximo de filtros de correlação por tópico é limitado a 100 mil. Se essa cota for atingida, as solicitações subsequentes de criação de filtros adicionais para o tópico serão rejeitadas e uma exceção será recebida pelo código de chamada.

Para saber mais sobre cotas, consulte [Cotas do Barramento de Serviço](service-bus-quotas.md).

## Próximas etapas

Para saber mais sobre as mensagens do Barramento de Serviço, confira os tópicos a seguir.

- [Introdução ao sistema de mensagens Premium do Barramento de Serviço do Azure (postagem de blog)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Introdução ao sistema de mensagens Premium do Barramento de Serviço do Azure (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
- [Visão geral de mensagens do Barramento de Serviço](service-bus-messaging-overview.md)
- [Visão geral da arquitetura de Barramento de Serviço do Azure](fundamentals-service-bus-hybrid-solutions.md)
- [Como usar filas do Barramento de Serviço](service-bus-dotnet-how-to-use-queues.md)

[portal clássico do Azure]: http://manage.windowsazure.com

<!---HONumber=AcomDC_1203_2015-->