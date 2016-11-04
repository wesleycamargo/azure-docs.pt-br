<properties 
    pageTitle="Perguntas frequentes sobre o Barramento de Serviço | Microsoft Azure"
    description="Responde a algumas perguntas frequentes sobre o Barramento de Serviço do Azure."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/04/2016"
    ms.author="sethm;juconway" />


# <a name="service-bus-faq"></a>Perguntas frequentes sobre o Barramento de Serviço

Este artigo responde a algumas perguntas frequentes sobre o Barramento de Serviço do Microsoft Azure. Você também pode visitar as [Perguntas frequentes de suporte do Azure](http://go.microsoft.com/fwlink/?LinkID=185083) para informações gerais sobre preços e suporte do Azure. Os tópicos a seguir estão incluídos:

- [Perguntas gerais sobre Mensagens do Barramento de Serviço do Azure](#general-questions-about-azure-service-bus-messaging)
- [Práticas recomendadas do Barramento de Serviço](#service-bus-best-practices)
- [Preços do Barramento de Serviço](#service-bus-pricing)
- [Cotas do Barramento de Serviço](#service-bus-quotas)
- [Gerenciamento de assinaturas e de namespaces](#subscription-and-namespace-management)
- [Solução de problemas](#service-bus-troubleshooting)

## <a name="general-questions-about-azure-service-bus-messaging"></a>Perguntas gerais sobre Mensagens do Barramento de Serviço do Azure

### <a name="what-is-azure-service-bus-messaging"></a>O que é o sistema de Mensagens do Barramento de Serviço do Azure?

[Mensagens do Barramento de Serviço do Azure](service-bus-messaging-overview.md) é uma plataforma de nuvem de mensagens assíncronas que permite enviar dados entre sistemas separados. A Microsoft oferece esse recurso como um serviço, o que significa que você não precisa hospedar seu próprio hardware para usá-lo.

### <a name="what-is-a-service-bus-namespace"></a>O que é um namespace do Barramento de Serviço?

Um [namespace](service-bus-create-namespace-portal.md) fornece um contêiner de escopo para endereçar recursos do barramento de serviço dentro de seu aplicativo. A criação de um hardware é necessária para a utilização do Barramento de Serviço e será uma das primeiras etapas da introdução.

### <a name="what-is-an-azure-service-bus-queue"></a>O que é uma fila do Barramento de Serviço do Azure?

A [Fila do Barramento de Serviço](service-bus-queues-topics-subscriptions.md) é uma entidade na qual as mensagens são armazenadas. As filas são particularmente úteis quando você tem vários aplicativos ou várias partes de um aplicativo distribuído que precisam se comunicar umas com as outras. A fila é semelhante a um centro de distribuição em que vários produtos (mensagens) são recebidos e então enviados desse local.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>O que são os tópicos e as assinaturas do Barramento de Serviço do Azure?

Um tópico pode ser visualizado como uma fila e ao usar várias assinaturas, ele se torna um modelo mais abrangente de mensagens; essencialmente, uma ferramenta de comunicação de um-para-muitos. Esse modelo de publicação/assinatura (ou *pub/sub*) habilita um aplicativo que envia uma mensagem para um tópico com várias assinaturas para fazer com que essa mensagem seja recebida por vários aplicativos.

### <a name="what-is-the-azure-service-bus-relay-service"></a>O que é o serviço Retransmissão do Barramento de Serviço do Azure?

O [serviço de retransmissão](../service-bus-relay/service-bus-relay-overview.md) fornece a capacidade de hospedar de modo transparente e acessar serviços WCF de qualquer lugar. Em outras palavras, isso habilita aplicativos híbridos executados em um datacenter do Azure e em um ambiente corporativo local.

### <a name="what-is-a-partitioned-entity"></a>O que é uma entidade particionada?

Uma fila ou um tópico convencional é manipulado por um único agente de mensagem e armazenado em um repositório de mensagens. Uma [fila ou um tópico particionado](service-bus-partitioning.md) é manipulada por vários agentes de mensagens e armazenada em vários repositórios de mensagens. Isso significa que a produtividade geral de uma fila ou um tópico particionado não é mais limitada pelo desempenho de um único agente ou repositório de mensagens. Além disso, uma interrupção temporária de um repositório de mensagens não torna uma fila ou um tópico particionado indisponível.

Observe que a ordenação não é garantida ao usar o particionamento de entidades. Se uma partição não estiver disponível, você poderá enviar e receber mensagens de outras partições.

## <a name="service-bus-best-practices"></a>Práticas recomendadas do Barramento de Serviço

### <a name="what-are-some-azure-service-bus-best-practices"></a>Quais são algumas das práticas recomendadas do Barramento de Serviço do Azure?

-   [Práticas recomendadas para melhorias de desempenho usando o sistema de mensagens agenciado do Barramento de Serviço][] – este artigo descreve como otimizar o desempenho na troca de mensagens orientadas.

-   [Práticas recomendadas para isolar aplicativos contra interrupções de serviço e desastres do Barramento de Serviço][] – este artigo discute a melhor maneira de proteger pontos de extremidade de retransmissão, filas e tópicos e replicação ativa e passiva contra falhas do datacenter.

### <a name="what-should-i-know-before-creating-messaging-entities"></a>O que devo saber antes de criar entidades de mensagens?   

As propriedades de uma fila e tópico a seguir são imutáveis. Leve isso em conta ao provisionar suas entidades, já que isso não pode ser modificado sem criar uma nova entidade de substituição.

-   Tamanho

-   Particionamento

-   Sessões

-   Detecção de duplicidade

-   Entidade expressa

## <a name="service-bus-pricing"></a>Preços do Barramento de Serviço

Esta seção responde a perguntas frequentes sobre a estrutura de preços do Barramento de Serviço. Você também pode visitar as [Perguntas frequentes de suporte do Azure](http://go.microsoft.com/fwlink/?LinkID=185083) para informações gerais sobre preços do Microsoft Azure. Para saber mais sobre o preço do Barramento de Serviço, consulte [Detalhes de preço do Barramento de Serviço](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="how-do-you-charge-for-service-bus"></a>Como é cobrado o Barramento de Serviço?

Para saber mais sobre o preço do Barramento de Serviço, veja [Detalhes de preço do Barramento de Serviço][Visão geral sobre preços]. Além dos preços mencionados, você é cobrado por transferências de dados associadas para saída fora do data center em que seu aplicativo está provisionado.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>Quais usos do barramento de serviço estão sujeitos à transferência de dados? O que é não está?

Todas as transferências de dados dentro de uma determinada região do Azure são feitas gratuitamente. Todas as transferências de dados fora de uma região estão sujeitas a encargos de saída a uma taxa de US$ 0,15 por GB a partir das regiões América do Norte e Europa e US$ 0,20 por GB da região do Pacífico Asiático. Todas as transferências de dados de entrada são feitas gratuitamente.

### <a name="how-is-the-relay-hours-meter-calculated"></a>Como é calculado o medidor de horas de retransmissão?

As horas de retransmissão são cobradas pela quantidade cumulativa de tempo durante o qual cada retransmissão do Barramento de Serviço fica “aberta” durante determinado período de cobrança. Uma retransmissão é implicitamente instanciada e aberta em determinado endereço do Barramento de Serviço (URL do namespace do serviço) quando um serviço WCF habilitado para retransmissão, ou “Ouvinte de retransmissão”, se conectar pela primeira vez a esse endereço. A retransmissão só é fechada quando o último ouvinte se desconecta de seu endereço. Portanto, para fins de cobrança, uma retransmissão é considerada “aberta” a partir do momento em que o primeiro ouvinte de retransmissão se conecta até o momento em que o último ouvinte de retransmissão se desconecta do endereço do Barramento de Serviço dessa retransmissão. Em outras palavras, uma retransmissão é considerada aberta sempre que um ou mais ouvintes de retransmissão estiverem conectados ao seu endereço do Barramento de Serviço.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-given-relay"></a>E se eu tiver mais de um ouvinte conectado a uma determinada retransmissão?

Em alguns casos, uma única retransmissão no Barramento de Serviço pode ter vários ouvintes conectados. Isso pode ocorrer com serviços de balanceamento de carga que usam as associações WCF netTCPRelay ou HttpRelay, ou com ouvintes de eventos de transmissão que usam o netEventRelay associação WCF. Uma retransmissão no Barramento de Serviço é considerada "aberta" quando pelo menos um ouvinte de retransmissão estiver conectado a ele. A adição de outros ouvintes para uma retransmissão aberta não altera o status dessa retransmissão para fins de cobrança. O número de remetentes de retransmissão (clientes que chamam ou enviam mensagens para retransmissões) conectados a uma retransmissão também não tem efeito sobre o cálculo de horas de retransmissão.

### <a name="how-is-the-messages-meter-calculated-for-relays"></a>Como é calculado o medidor mensagens para retransmissões?

Em geral, as mensagens cobráveis são calculadas para retransmissões usando o mesmo método descrito acima para entidades agenciadas (filas, tópicos e assinaturas). No entanto, há algumas diferenças perceptíveis:

O envio de uma mensagem para uma retransmissão do Barramento de Serviço é tratado como um envio “full through” para o ouvinte de retransmissão que recebe a mensagem, em vez de um envio para a retransmissão do Barramento de Serviço seguido por uma entrega para o ouvinte de retransmissão. Portanto, uma invocação de serviço do tipo solicitação-resposta (de até 64 KB) em relação a um ouvinte de retransmissão resultará em duas mensagens cobráveis: uma mensagem cobrável para a solicitação e uma mensagem cobrável para a resposta (supondo que a resposta também seja \<= 64 KB). Isso é diferente de usar uma fila para atuar como mediador entre um cliente e um serviço. Neste último caso, o mesmo padrão de solicitação-resposta exigiria um envio de solicitação para a fila, seguido por uma remoção da fila/entrega da fila para o serviço, seguido por um envio de resposta para outra fila e uma remoção da fila/entrega dessa fila para o cliente. Usando as mesmas suposições de tamanho (\<= 64 KB) para tudo, o padrão de fila intermediado resultaria em quatro mensagens cobráveis, duas vezes o número cobrado para implementar o mesmo padrão usando a retransmissão. É claro que há vantagens em usar filas para atingir esse padrão, como durabilidade e nivelamento de carga. Essas vantagens podem justificar a despesa adicional.

As retransmissões abertas usando a associação WCF netTCPRelay tratam as mensagens não como mensagens individuais, mas como um fluxo de dados através do sistema. Em outras palavras, somente o remetente e o ouvinte têm visibilidade do enquadramento das mensagens individuais enviadas/recebidas usando essa associação. Assim, para retransmissões usando a associação netTCPRelay, todos os dados são tratados como uma transmissão para fins de cálculo das mensagens cobráveis. Nesse caso, o Barramento de Serviço calculará a quantidade total de dados enviados ou recebidos por meio de cada retransmissão individual a cada 5 minutos e dividirá esse total por 64 KB para determinar o número de mensagens cobráveis pela retransmissão em questão durante aquele período.

### <a name="does-service-bus-charge-for-storage"></a>O Barramento de Serviço cobra pelo armazenamento?

Não, o Barramento de Serviço não cobra pelo armazenamento. No entanto, há uma cota que limita a quantidade máxima de dados que podem persistir por fila/tópico. Consulte as Perguntas Frequentes a seguir.

## <a name="service-bus-quotas"></a>Cotas do Barramento de Serviço

Para obter uma lista de cotas e limites do Barramento de Serviço, confira [Visão geral sobre cotas][].

### <a name="does-service-bus-have-any-usage-quotas"></a>O Barramento de Serviço tem cotas de uso?

Por padrão, para qualquer serviço de nuvem, a Microsoft define uma cota de uso mensal agregado calculada entre todas as assinaturas de um cliente. Como compreendemos que talvez seja necessário usar mais do que esses limites, contate o atendimento ao cliente a qualquer momento para que possamos entender as suas necessidades e ajustar esses limites adequadamente. Para o Barramento de Serviço, as cotas totais de uso são:

- 5 bilhões de mensagens
- 2 milhões de horas de retransmissão

Embora reservemos o direito de desabilitar uma conta de cliente que tenha excedido suas cotas de uso em determinado mês, forneceremos uma notificação por email e faremos várias tentativas de contatar o cliente antes de realizar qualquer ação. Os clientes que excederem essas cotas ainda serão responsáveis pelas cobranças que excederem as cotas.

Como ocorre com outros serviços no Azure, o Barramento de Serviço aplica um conjunto de cotas específicas para garantir que haja um uso inteligente dos recursos. A seguir estão as cotas de uso impostas pelo serviço:

#### <a name="queuetopic-size"></a>Tamanho do tópico/fila

Especifique o tamanho máximo da fila ou do tópico no momento da sua criação. Essa cota pode ter um valor de 1, 2, 3, 4 ou 5 GB. Se tamanho máximo for atingido, as mensagens de entrada adicionais serão rejeitadas e uma exceção será recebida pelo código de chamada.

#### <a name="naming-restrictions"></a>Restrições de nomenclatura

Um nome de namespace do Barramento de Serviço só pode ter entre 6 e 50 caracteres. O limite da contagem de caracteres para cada fila, tópico ou assinatura está entre 1 e 50 caracteres.

#### <a name="number-of-concurrent-connections"></a>Número de conexões simultâneas

Fila/Tópico/Assinatura – o número de conexões TCP simultâneas em uma fila/tópico/assinatura é limitado a 100. Se essa cota for atingida, as solicitações subsequentes de conexões adicionais serão rejeitadas e uma exceção será recebida pelo código de chamada. Para cada fábrica de mensagens, o Barramento de Serviço mantém uma conexão TCP se algum dos clientes criados por essa fábrica de mensagens tiver uma operação ativa pendente ou tiver concluído uma operação há menos de 60 segundos. Operações REST não são consideradas conexões TCP simultâneas.

#### <a name="number-of-concurrent-listeners-on-a-relay"></a>Número de ouvintes simultâneos em uma retransmissão

O número de ouvintes simultâneos **netTcpRelay** e **netHttpRelay** em uma retransmissão é limitado a 25 (1 para uma retransmissão **NetOneway**).

#### <a name="number-of-concurrent-relay-listeners-per-namespace"></a>Número de ouvintes de retransmissão simultâneos por namespace

O Barramento de Serviço impõe um limite de 2000 ouvintes de retransmissão simultâneos por namespace de serviço. Se essa cota for atingida, as solicitações subsequentes de ouvintes de retransmissão adicionais serão rejeitadas e uma exceção será recebida pelo código de chamada.

#### <a name="number-of-topicsqueues-per-service-namespace"></a>Número de tópicos/filas por namespace de serviço

O número máximo de tópicos/filas (entidades de armazenamento duráveis) em um namespace de serviço é limitado a 10.000. Se essa cota for atingida, as solicitações subsequentes para a criação de uma novo tópico/fila no namespace de serviço serão rejeitadas. Nesse caso, o portal clássico do Azure exibirá uma mensagem de erro ou o código de cliente de chamada receberá uma exceção, dependendo se a tentativa foi feita por meio do portal ou no código do cliente.

### <a name="message-size-quotas"></a>Cotas de tamanho de mensagem

#### <a name="queuetopicsubscription"></a>Fila/Tópico/Assinatura

**Tamanho da mensagem** : cada mensagem está limitada a um tamanho total de 256KB, incluindo os cabeçalhos da mensagem.

**Tamanho do cabeçalho da mensagem** – cada cabeçalho de mensagem é limitado a 64KB.

**NetOneway e NetEvent** – cada mensagem é limitada a um tamanho total de 64KB, incluindo os cabeçalhos da mensagem.

**Retransmissões http e NetTcp** : o Barramento de Serviço não impõe um limite superior ao tamanho dessas mensagens.

As mensagens que excederem essas cotas de tamanho serão rejeitadas e uma exceção será recebida pelo código de chamada.

**Número de assinaturas por tópico** – o número máximo de assinaturas por tópico é limitado a 2 mil. Se essa cota for atingida, as solicitações subsequentes para a criação de assinaturas adicionais para o tópico serão rejeitadas. Nesse caso, o portal clássico do Azure exibirá uma mensagem de erro ou o código de cliente de chamada receberá uma exceção, dependendo se a tentativa foi feita por meio do portal ou no código do cliente.

**Número de filtros SQL por tópico** – o número máximo de filtros SQL por tópico é limitado a 2 mil. Se essa cota for atingida, as solicitações subsequentes de criação de filtros adicionais para o tópico serão rejeitadas e uma exceção será recebida pelo código de chamada.

**Filtros de número de correlação por tópico** – o número máximo de filtros de correlação por tópico é limitado a 100 mil. Se essa cota for atingida, as solicitações subsequentes de criação de filtros adicionais para o tópico serão rejeitadas e uma exceção será recebida pelo código de chamada.

## <a name="subscription-and-namespace-management"></a>Gerenciamento de assinaturas e de namespaces

### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Como posso migrar um namespace para outra assinatura do Azure?

Você pode usar comandos do PowerShell (encontrados no artigo [aqui][]) para mover um namespace de uma assinatura do Azure para outra. Para executar a operação, o namespace já deve estar ativo. Além disso, o usuário que executa os comandos deve ser administrador nas assinaturas de origem e de destino.

## <a name="service-bus-troubleshooting"></a>Solução de problemas do Barramento de Serviço

[Visão geral sobre exceções][]

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-messaging-apis-and-their-suggested-actions"></a>Quais são algumas das exceções geradas pelas APIs de mensagens do Barramento de Serviço do Azure e suas ações sugeridas?

As exceções que as APIs de mensagens podem gerar se enquadram nestas categorias:

-   Erro de codificação do usuário

-   Erro de instalação/configuração

-   Exceções temporárias

-   Outras exceções

O artigo [Exceções de mensagens do Barramento de Serviço][Visão geral sobre exceções] descreve algumas exceções com as ações sugeridas.

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>O que é uma Assinatura de Acesso Compartilhado e quais idiomas oferecem suporte para a geração de uma assinatura?

As Assinaturas de Acesso Compartilhado são um mecanismo de autenticação com base em hashes seguros SHA-256 ou URIs. Para saber mais sobre como gerar suas próprias assinaturas no Node, PHP, Java e C\#, confira o artigo [Assinaturas de Acesso Compartilhado][].

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as mensagens do Barramento de Serviço, confira os tópicos a seguir.

- [Introdução ao sistema de mensagens Premium do Barramento de Serviço do Azure (postagem de blog)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Introdução ao sistema de mensagens Premium do Barramento de Serviço do Azure (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
- [Visão geral de mensagens do Barramento de Serviço](service-bus-messaging-overview.md)
- [Visão geral da arquitetura de Barramento de Serviço do Azure](service-bus-fundamentals-hybrid-solutions.md)
- [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)

[Práticas recomendadas para melhorias de desempenho usando o sistema de mensagens agenciado do Barramento de Serviço]: service-bus-performance-improvements.md
[Práticas recomendadas para isolar aplicativos contra interrupções e desastres do Barramento de Serviço]: service-bus-outages-disasters.md
[Visão geral sobre preços]: https://azure.microsoft.com/pricing/details/service-bus/
[Visão geral sobre cotas]: service-bus-quotas.md
[aqui]: service-bus-powershell-how-to-provision.md#migrate-a-namespace-to-another-azure-subscription
[Visão geral sobre exceções]: service-bus-messaging-exceptions.md
[As Assinaturas de Acesso Compartilhado]: service-bus-sas-overview.md



<!--HONumber=Oct16_HO2-->


