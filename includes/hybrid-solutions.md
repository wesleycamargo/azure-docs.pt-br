#Service Bus do Azure

Se o software for executado na nuvem ou no local, ele geralmente precisa interagir com outro software. Para fornecer uma maneira amplamente úteis para fazer isso, o Azure oferece o Service Bus. Este artigo dá uma olhada essa tecnologia, que descreve o que é e por que você desejaria usá-lo.

##Sumário      
- [Noções básicas do Service Bus](#fundamentals)
- [Filas](#queues)
- [Tópicos](#topics)
- [Retransmissão](#relays)


## <a name="fundamentals"></a>Conceitos básicos do serviço barramento
Diferentes situações pedem diferentes estilos de comunicação. Às vezes, permitindo que aplicativos de enviar e receber mensagens por meio de uma fila simple é a melhor solução. Em outras situações, uma fila comum não é suficiente; uma fila com um mecanismo de publicação e assinatura é melhor. E em alguns casos, tudo que realmente é preciso é uma conexão entre filas de aplicativos não são necessárias. O Service Bus fornece todas as três opções, permitindo que os aplicativos interagir de várias maneiras diferentes.

O Service Bus é um serviço de nuvem de diversos clientes, o que significa que o serviço é compartilhado por vários usuários. Cada usuário, como um desenvolvedor de aplicativos, cria um *namespace*, em seguida, define os mecanismos de comunicação necessárias dentro desse namespace. [Figura 1](#Fig1) mostra isso.

<a name="Fig1"></a>![Diagrama do Service Bus do Azure][svc-bus]
 
**Figura 1: O Service Bus fornece um serviço de multilocação para conexão de aplicativos por meio da nuvem.**

Dentro de um namespace, você pode usar uma ou mais instâncias de três mecanismos de comunicação diferentes, cada um deles conecta aplicativos de uma maneira diferente. As opções são:

- *Filas de*, que permitem a comunicação de um-direcional. Cada fila atua como um intermediário (às vezes chamado de um *agente*) que armazena mensagens enviadas até que eles são recebidos.
- *Tópicos*, que fornece comunicação bidirecional um usando *assinaturas* Como uma fila, um tópico atua como um intermediário, mas permite que cada assinatura ver apenas as mensagens que correspondam a critérios específicos.
- *Retransmissões*, que fornece comunicação bidirecional. Ao contrário das filas e dos tópicos, uma retransmissão não armazena em trânsito mensagens: trata-se não um agente. Em vez disso, ele só as passa para o aplicativo de destino.

Quando você criar uma fila, o tópico ou a retransmissão, você atribuir um nome. Esse nome combinado com qualquer é chamado seu espaço para nome, cria um identificador exclusivo para o objeto. Aplicativos podem fornecer esse nome ao Service Bus e usar essa fila, o tópico ou a retransmissão para se comunicar uns com os outros. 

Para usar qualquer um desses objetos, aplicativos do Windows podem usar o Windows Communication Foundation (WCF). Para filas e tópicos, aplicativos do Windows também podem usar uma API de mensagens definidas pelo Service Bus. Filas e tópicos acessados na HTTP também e para torná-las mais fáceis de usar de aplicativos não Windows, a Microsoft fornece SDKs para outras linguagens, Java e Node. js.

É importante compreender que, embora o serviço de barramento em si é executado na nuvem (ou seja, nos data centers de Azure da Microsoft), aplicativos que o utilizam podem ser executados em qualquer lugar. Você pode usar o Service Bus para conectar aplicativos em execução no Azure, por exemplo, ou aplicativos executados dentro de seu próprio datacenter. Também pode usá-lo para se conectar a um aplicativo em execução no Azure ou outra plataforma de nuvem com um aplicativo local ou tablets e telefones. Também é possível conectar os aparelhos domésticos, sensores e outros dispositivos para um aplicativo central ou a qualquer outra. O Service Bus é um mecanismo de comunicação genérico na nuvem, que é acessível a partir de praticamente qualquer lugar. Como você usar depende de que seus aplicativos precisam fazer.


## <a name="queues"></a>Filas

Suponha que você queira conectar dois aplicativos usando uma fila do Service Bus. [Figura 2](#Fig2) ilustra essa opção.

<a name="Fig2"></a>![Diagrama do Service Bus (API, REST)][queues]
 
**Figura 2: Filas do Service Bus fornecem o enfileiramento de mensagens assíncronas unidirecional.**

O processo é simples: um remetente enviar uma mensagem para uma fila do Service Bus e um receptor pega essa mensagem em um momento posterior. Uma fila pode ter apenas um único receptor, como [Figura 2](#Fig2) mostra ou vários aplicativos podem ler a partir da mesma fila. Na última situação, cada mensagem normalmente é lido por um receptor de filas não fornecem um serviço vários cast.

Cada mensagem tem duas partes: um conjunto de propriedades, cada um par chave/valor e um corpo de mensagem binária. Como eles são usados depende de que um aplicativo está tentando fazer. Por exemplo, um aplicativo de envio de uma mensagem sobre uma venda recente pode incluir as propriedades *vendedor = "&"* e *valor = 10000* O corpo da mensagem pode conter uma imagem digitalizada do contrato assinado da venda ou, se não houver um, apenas permanecerão vazio.

Um receptor pode ler uma mensagem de uma fila do Service Bus de duas maneiras diferentes. A primeira opção, chamada ReceiveAndDelete, remove uma mensagem da fila e exclui-lo imediatamente. Isso é simples, mas se o receptor falhar antes de terminar de processar a mensagem, a mensagem será perdida. Porque ele é removido da fila, nenhum outro receptor pode acessá-lo. 

A segunda opção, PeekLock, é destinada a ajudar com esse problema. Como ReceiveAndDelete, uma leitura PeekLock remove uma mensagem da fila. Ela não exclui a mensagem, no entanto. Em vez disso, ele bloqueia a mensagem, tornando-a invisível para outros destinatários, e espera que um dos três eventos:

-Se o receptor processa a mensagem com êxito, ele chama concluída e a fila exclui a mensagem. 
-Se o receptor decide se não for possível processar a mensagem com êxito, ele chama Abandon. A fila remove o bloqueio da mensagem e o torna disponível para outros destinatários.
-Se o receptor chama nenhuma delas dentro de um período de tempo configurável (por padrão, 60 segundos), a fila supõe que o receptor falhou. Nesse caso, ele se comporta como se o receptor tivesse chamado abandonar, disponibilizando a mensagem a outros destinatários.

Observe o que pode acontecer aqui: A mesma mensagem pode ser entregue duas vezes, talvez para dois destinatários diferentes. Aplicativos usando filas do Service Bus devem estar preparados para isso. Para facilitar a detecção de duplicidades, que cada mensagem tem uma propriedade MessageID exclusiva que, por padrão, permanece o mesmo, não importa como muitas vezes a mensagem é lida de uma fila. 

Filas são úteis em algumas situações. Eles permitem que os aplicativos se comuniquem mesmo quando ambos não são executados ao mesmo tempo, algo que é especialmente útil com lotes e aplicativos móveis. Uma fila com vários receptores também fornece balanceamento de carga automático, desde que as mensagens enviadas são distribuídas entre esses receptores.


## <a name="topics"></a>Tópicos

Útil como estão, filas nem sempre são a solução certa. Às vezes, os tópicos do Service Bus são melhores. [Figura 3](#Fig3) ilustra a ideia.

<a name="Fig3"></a>![Diagrama dos tópicos e das assinaturas do Service Bus][topics-subs]
 
**Figura 3: Com base no filtro que especifica um aplicativo de assinatura, ele pode receber algumas ou todas as mensagens enviadas para um tópico do Service Bus.**

Um tópico é semelhante em muitas formas a uma fila. Mensagens de envio de remetentes a um tópico da mesma maneira que eles enviam mensagens para uma fila e essas mensagens parecem como com filas. A grande diferença é que tópicos permitem que cada aplicativo receptor crie sua própria assinatura definindo um *filtro* Um assinante verá apenas as mensagens que correspondem ao filtro. Por exemplo, [Figura 3](#Fig3) mostra um remetente e um tópico com três assinantes, cada um com seu próprio filtro:

-Assinante 1 recebe apenas as mensagens que contêm a propriedade *vendedor = "&"*
-Assinante 2 recebe mensagens que contêm a propriedade *vendedor = "Ruby"* e/ou conter um *valor* propriedade cujo valor é maior que 100.000. Talvez o Ruby é o gerente de vendas e portanto ela deseja ver próprias vendas e todas as vendas grandes independentemente de quem toma-los.
-Assinante 3 definiu seu filtro como *True*, que significa que ele recebe todas as mensagens. Por exemplo, esse aplicativo pode ser responsável por manter uma trilha de auditoria, e por isso ele precisa ver tudo.

Assim como acontece com filas, os assinantes de um tópico podem ler mensagens usando ReceiveAndDelete ou PeekLock. Ao contrário das filas, no entanto, uma única mensagem enviada a um tópico pode ser recebida por vários assinantes. Essa abordagem, conhecida como *publicar e assinar*, é útil sempre que vários aplicativos podem interessar as mesmas mensagens. Definindo o filtro à direita, cada assinante pode tocar na parte de fluxo de mensagens que precisa ver.


## <a name="relays"></a>Retransmissão

Filas e tópicos fornecem comunicação unidirecional assíncrona por meio de um agente. O tráfego flui em apenas uma direção, e não há nenhuma conexão direta entre remetentes e receptores. Mas e se você não quiser isso? Suponha que seus aplicativos precisam enviar e receber, ou talvez você queira que um link direto entre eles "você não precisa de um local para armazenar as mensagens entre. Para solucionar problemas como este, o Service Bus fornece retransmissões, como [Figura 4](#Fig4) mostra.

<a name="Fig4"></a>![Retransmissão do Diagrama do Service Bus][relay]
 
**Figura 4: Retransmissão do Service Bus fornece comunicação bidirecional síncrona entre os aplicativos.**

Esta é a questão óbvia para perguntar sobre retransmissões: por que usar um? Mesmo que eu não precise filas, por que tornar os aplicativos se comuniquem através de um serviço de nuvem, e não apenas interagem diretamente? A resposta é que falando diretamente pode ser mais difícil do que você imagina.

Suponha que você deseja conectar dois aplicativos locais, ambos em execução dentro de data centers corporativos. Cada um desses aplicativos fica atrás de um firewall, e cada centro de dados provavelmente usará a conversão de endereços de rede (NAT). O firewall bloqueia os dados de entrada em tudo menos algumas portas e NAT significa que a máquina que cada aplicativo seja executado em provavelmente não tem um endereço IP fixo. Sem alguma ajuda extra, conectar esses aplicativos através da Internet pública é problemática.

Uma retransmissão do Service Bus fornece essa Ajuda. Para a comunicação bidirecional por meio de uma retransmissão, cada aplicativo estabelece uma conexão TCP de saída com o Service Bus e mantém aberta. Toda a comunicação entre os dois aplicativos percorrida por essas conexões. Porque cada conexão foi estabelecida de dentro do data center, o firewall permitirá que o tráfego de entrada para cada aplicativo "dados enviados por meio de retransmissão" sem abrir novas portas. Essa abordagem também contorna o problema NAT, porque cada aplicativo tem um ponto de extremidade consistente em toda a comunicação. Trocando dados por meio de retransmissão, os aplicativos podem evitar os problemas que seriam caso contrário dificultam a comunicação. 

Para usar as retransmissões do Service Bus, aplicativos, se baseiam no Windows Communication Foundation (WCF). O Service Bus fornece ligações do WCF que tornam simples para aplicativos do Windows interagir por meio de retransmissões. Aplicativos que já usam o WCF podem normalmente apenas especificar uma dessas vinculações e conversar entre si através de uma retransmissão. Ao contrário das filas e dos tópicos, no entanto, usar as retransmissões de aplicativos não Windows, ao mesmo tempo possível, requer o algum esforço de programação; Não há bibliotecas padrão são fornecidas.

Ao contrário das filas e dos tópicos, aplicativos não criam explicitamente retransmissões. Em vez disso, quando um aplicativo que deseja receber mensagens estabelece uma conexão TCP com o Service Bus, uma retransmissão é criada automaticamente. Quando a conexão for interrompida, a retransmissão é excluída. Para permitir que um aplicativo encontrar a retransmissão criada por um ouvinte específico, o Service Bus oferece um registro que permite localizar uma retransmissão específica por nome.

Retransmissões são a solução ideal quando você precisa direcionar comunicação. Pense em um sistema de reservas do companhia aérea em execução em um datacenter local, por exemplo, deve ser acessado de outros computadores, dispositivos móveis e quiosques de seleção. Aplicativos em execução em todos esses sistemas poderiam confiar nas retransmissões do Service Bus na nuvem para se comunicar, onde talvez esteja em execução.

Conectando aplicativos sempre fez parte da criação de soluções completas e é difícil ver esse problema nunca deixará de existir. Fornecendo tecnologias baseadas em nuvem para fazer essa "filas, tópicos e retransmissões", o Service Bus tem como objetivo fazer com essa função essencial mais fácil e mais amplamente disponíveis.

[svc-bus]: ./media/hybrid-solutions/SvcBus_01_architecture.png
[queues]: ./media/hybrid-solutions/SvcBus_02_queues.png
[topics-subs]: ./media/hybrid-solutions/SvcBus_03_topicsandsubscriptions.png
[relay]: ./media/hybrid-solutions/SvcBus_04_relay.png

