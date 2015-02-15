#Barramento de Serviço do Azure

Se o software for executado na nuvem ou no local, ele geralmente precisa interagir com outro software. Para fornecer uma maneira amplamente útil para fazer isso, o Azure oferece o barramento de serviço. Este artigo lança um olhar sobre esta tecnologia, descrevendo o que ela é e por que você talvez queira usá-la.

##Sumário      
- [Conceitos fundamentais do barramento de serviço](#fundamentals)
- [Filas](#queues)
- [Tópicos](#topics)
- [Retransmissões](#relays)


## <a name="fundamentals"></a>Conceitos fundamentais do barramento de serviço
Situações diferentes pedem estilos diferentes de comunicação. Às vezes, permitindo que aplicativos de enviar e receber mensagens por meio de uma fila simple é a melhor solução. Em outras situações, uma fila comum não é suficiente; uma fila com um mecanismo de publicação e assinatura é melhor. E em alguns casos, tudo que realmente é preciso é uma conexão entre filas de aplicativos não são necessárias. O Barramento de Serviço fornece todas as três opções, permitindo que os aplicativos interajam de várias maneiras diferentes.

O Barramento de Serviço é um serviço de nuvem de diversos clientes, o que significa que o serviço é compartilhado por vários usuários. Cada usuário, como um desenvolvedor de aplicativos, cria um *namespace*, em seguida, define os mecanismos de comunicação necessárias dentro desse namespace. [Figura 1](#Fig1) mostra isso.

<a name="Fig1"></a>![Diagram of Azure Service Bus][svc-bus]
 
**Figura 1: O Barramento de Serviço fornece um serviço de multilocação para conexão de aplicativos por meio da nuvem.**

Dentro de um namespace, você pode usar uma ou mais instâncias de três mecanismos de comunicação diferentes, cada um deles conecta aplicativos de uma maneira diferente. As opções são:

- *Queues*, que permitem a comunicação bidirecional. Cada fila atua como um intermediário (às vezes, chamado de um *broker*) que armazena mensagens enviadas até que elas sejam recebidas.
- *Topics*, que fornecem comunicação unidirecional usando *subscriptions*. Como uma fila, um tópico atua como um intermediário, mas permite que cada assinatura veja apenas as mensagens que correspondem a critérios específicos.
- *Relays*, que fornece comunicação bidirecional. Ao contrário das filas e dos tópicos, uma retransmissão não armazena em trânsito mensagens: trata-se não um agente. Em vez disso, ela só as passa para o aplicativo de destino.

Ao criar uma fila, um tópico ou uma retransmissão, você atribui um nome a ele/ela. Esse nome combinado com qualquer é chamado seu espaço para nome, cria um identificador exclusivo para o objeto. Aplicativos podem fornecer esse nome ao Barramento de Serviço e usar essa fila, o tópico ou a retransmissão para se comunicar uns com os outros. 

Para usar qualquer um desses objetos, os aplicativos do Windows podem usar o Windows Communication Foundation (WCF). Para filas e tópicos, aplicativos do Windows também podem usar uma API de mensagens definidas pelo Barramento de Serviço. Filas e tópicos também podem ser acessados na HTTP e para torná-los mais fáceis de usar que aplicativos não Windows, a Microsoft fornece SDKs para Java, Node. js e outras linguagens.

É importante compreender que, embora o Barramento de Serviço em si seja executado na nuvem (ou seja, nos datacenters do Azure da Microsoft), os aplicativos que o utilizam podem ser executados em qualquer lugar. Você pode usar o Barramento de Serviço para conectar aplicativos em execução no Azure, por exemplo, ou aplicativos executados dentro de seu próprio datacenter. Também pode usá-lo para se conectar a um aplicativo em execução no Azure ou outra plataforma de nuvem com um aplicativo local ou tablets e telefones. Também é possível conectar os aparelhos domésticos, sensores e outros dispositivos para um aplicativo central ou a qualquer outra. O Barramento de Serviço é um mecanismo de comunicação genérico na nuvem, que é acessível a partir de praticamente qualquer lugar. Como você o usa depende do que os seus aplicativos precisam fazer.


## <a name="queues"></a>Filas

Suponha que você queira conectar dois aplicativos usando uma fila do Barramento de Serviço. [Figura 2](#Fig2) ilustra essa situação.

<a name="Fig2"></a>![Diagram of Service Bus Queues][filas]
 
**Figura 2: As filas do barramento de serviço fornecem o enfileiramento unidirecional de mensagens assíncronas.**

O processo é simples: Um remetente envia uma mensagem para uma fila do Barramento de Serviço e um receptor pega essa mensagem em um momento posterior. Uma fila pode ter apenas um único destinatário como a [Figura 2](#Fig2) mostra, ou vários aplicativos podem ler por meio da mesma fila. Na última situação, cada mensagem normalmente é lida por um receptor de filas que não fornece um serviço de vários casts.

Cada mensagem tem duas partes: um conjunto de propriedades, cada um sendo um par chave/valor e um corpo de mensagem binária. Como eles são usados depende do que um aplicativo está tentando fazer. Por exemplo, um aplicativo que envia uma mensagem sobre uma venda recente pode incluir as propriedades *Seller="Ava"* e *Amount=10000*. O corpo da mensagem pode conter uma imagem digitalizada do contrato assinado da venda ou, se não houver um, pode permanecer vazio.

Um receptor pode ler uma mensagem por meio de uma fila do Barramento de Serviço de duas maneiras diferentes. A primeira opção, chamada ReceiveAndDelete, remove uma mensagem da fila e exclui-lo imediatamente. Isso é simples, mas se o receptor falhar antes de terminar de processar a mensagem, a mensagem será perdida. Porque ele é removido da fila, nenhum outro receptor pode acessá-lo. 

A segunda opção, PeekLock, é destinada a ajudar com esse problema. Como ReceiveAndDelete, uma leitura PeekLock remove uma mensagem da fila. Ela não exclui a mensagem, no entanto. Em vez disso, ela bloqueia a mensagem, tornando-a invisível para outros destinatários, e espera por um dos três eventos:

- Se o receptor processa a mensagem com êxito, ele a chama de Concluída e a fila exclui a mensagem. 
- Se o receptor decidir que não é possível processar a mensagem com êxito, ele chamará Abandon. A fila removerá, então, o bloqueio da mensagem e a tornará disponível para outros destinatários.
- Se o receptor não chamar nenhuma delas dentro de um período de tempo configurável (por padrão, 60 segundos), a fila supõe que o receptor falhou. Nesse caso, ele se comporta como se o receptor tivesse chamado o Abandon, disponibilizando a mensagem a outros destinatários.

Observe o que pode acontecer aqui: A mesma mensagem pode ser entregue duas vezes, talvez para dois destinatários diferentes. Aplicativos usando filas do Barramento de Serviço devem estar preparados para isso. Para facilitar a detecção de duplicidades, que cada mensagem tem uma propriedade MessageID exclusiva que, por padrão, permanece o mesmo, não importa como muitas vezes a mensagem é lida de uma fila. 

As filas são úteis em algumas situações. Eles permitem que os aplicativos se comuniquem mesmo quando ambos não são executados ao mesmo tempo, algo que é especialmente útil com lotes e aplicativos móveis. Uma fila com vários receptores também fornece balanceamento de carga automático, desde que as mensagens enviadas sejam distribuídas entre esses receptores.


## <a name="topics"></a>Tópicos

Úteis como são, as filas nem sempre são a solução certa. Às vezes, os tópicos do Barramento de Serviço são melhores. [Figura 3](#Fig3) ilustra essa ideia.

<a name="Fig3"></a>![Diagram of Service Bus Topics and Subscriptions][topics-subs]
 
**Figura 3: Com base no filtro que especifica um aplicativo de assinatura, ele pode receber algumas ou todas as mensagens enviadas para um tópico do Barramento de Serviço.**

Um tópico é semelhante em muitas formas a uma fila. Mensagens de envio de remetentes a um tópico da mesma maneira que eles enviam mensagens para uma fila e essas mensagens parecem como com filas. A grande diferença é que tópicos permitem que cada aplicativo receptor crie sua própria assinatura definindo um *filter*. Um assinante verá apenas as mensagens que correspondem a esse filtro. Por exemplo, a [Figura 3](#Fig3) mostra um remetente e um tópico com três assinantes, cada um com seu próprio filtro:

- O Assinante 1 recebe apenas as mensagens que contêm a propriedade *Seller="Ava"*.
- SO Assinante 2 recebe mensagens que contêm a propriedade *Seller="Ruby"* e/ou que contenham uma propriedade *Amount* cujo valor é superior a 100.000. Talvez Ruby seja a gerente de vendas e, assim, ela quer ver as suas próprias vendas e todas as grandes vendas, independentemente de quem as fez.
- O Assinante 3 definiu seu filtro como *True*, que significa que ele recebe todas as mensagens. Por exemplo, esse aplicativo pode ser responsável por manter uma trilha de auditoria, e por isso ele precisa ver tudo.

Assim como acontece com filas, os assinantes de um tópico podem ler mensagens usando ReceiveAndDelete ou PeekLock. Ao contrário das filas, no entanto, uma única mensagem enviada a um tópico pode ser recebida por vários assinantes. Essa abordagem, normalmente conhecida como *publish and subscribe*, é útil sempre que vários aplicativos podem interessar nas mesmas mensagens. Definindo o filtro à direita, cada assinante pode tocar na parte de fluxo de mensagens que precisa ver.


## <a name="relays"></a>Retransmissões

Filas e tópicos fornecem comunicação unidirecional assíncrona por meio de um agente. O tráfego flui em apenas uma direção, e não há nenhuma conexão direta entre remetentes e receptores. Mas e se você não quiser isso? Suponha que ambos os seus aplicativos precisam enviar e receber, ou talvez você queira que um link direto entre eles; você não precisa de um local para armazenar as mensagens entre ambos. Para solucionar problemas como este, o Barramento de Serviço fornece retransmissões, como a [Figura 4](#Fig4) mostra.

<a name="Fig4"></a>![Diagram of Service Bus Relay][retransmissão]
 
**Figura 4: A retransmissão do Barramento de Serviço fornece comunicação bidirecional síncrona entre os aplicativos.**

A questão óbvia para perguntar sobre retransmissões é: Por que usar uma? Mesmo que eu não precise filas, por que tornar os aplicativos se comuniquem através de um serviço de nuvem, e não apenas interagem diretamente? A resposta é que falar diretamente pode ser mais difícil do que você imagina.

Suponha que você deseja conectar dois aplicativos locais, ambos em execução dentro de datacenters corporativos. Cada um desses aplicativos fica atrás de um firewall, e cada centro de dados provavelmente usará a conversão de endereços de rede (NAT). O firewall bloqueia os dados de entrada em tudo menos algumas portas e NAT significa que a máquina que cada aplicativo seja executado em provavelmente não tem um endereço IP fixo. Sem alguma ajuda extra, conectar esses aplicativos por meio da Internet pública é algo problemático.

Uma retransmissão do Barramento de Serviço fornece essa Ajuda. Para a comunicação bidirecional por meio de uma retransmissão, cada aplicativo estabelece uma conexão TCP de saída com o Barramento de Serviço e mantém aberta. Toda a comunicação entre os dois aplicativos percorrida por essas conexões. Porque cada conexão foi estabelecida de dentro do data center, o firewall permitirá que o tráfego de entrada para cada aplicativo "dados enviados por meio de retransmissão" sem abrir novas portas. Essa abordagem também contorna o problema NAT, porque cada aplicativo tem um ponto de extremidade consistente em toda a comunicação. Trocando dados por meio de retransmissão, os aplicativos podem evitar os problemas que seriam caso contrário dificultam a comunicação. 

Para usar as retransmissões do Barramento de Serviço, os aplicativos se baseiam no Windows Communication Foundation (WCF). O Barramento de Serviço fornece ligações do WCF que tornam simples para aplicativos do Windows interagir por meio de retransmissões. Aplicativos que já usam o WCF podem normalmente apenas especificar uma dessas vinculações e conversar entre si através de uma retransmissão. Ao contrário das filas e dos tópicos, no entanto, usar as retransmissões de aplicativos não Windows, enquanto for possível, requer algum esforço de programação. Nenhuma biblioteca padrão é fornecida.

Ao contrário das filas e dos tópicos, os aplicativos não criam retransmissões explicitamente. Em vez disso, quando um aplicativo que deseja receber mensagens estabelece uma conexão TCP com o Barramento de Serviço, uma retransmissão é criada automaticamente. Quando a conexão for interrompida, a retransmissão é excluída. Para permitir que um aplicativo encontre a retransmissão criada por um ouvinte específico, o Barramento de Serviço oferece um Registro que permite localizar uma retransmissão específica por nome.

Retransmissões são a solução ideal quando você precisa direcionar a comunicação. Pense em um sistema de reservas do companhia aérea em execução em um datacenter local, por exemplo, deve ser acessado de outros computadores, dispositivos móveis e quiosques de seleção. Os aplicativos em execução em todos esses sistemas poderiam confiar nas retransmissões do Barramento de Serviço na nuvem para se comunicar, onde quer que eles estejam em execução.

Conectar aplicativos sempre fez parte da criação de soluções completas e será difícil ver esse problema deixar de existir. Fornecendo tecnologias baseadas em nuvem para fazer isto por meio de filas, tópicos e retransmissões, o Barramento de Serviço tem como objetivo fazer essa função essencial ser mais fácil e estar mais amplamente disponível.

[svc-bus]: ./media/hybrid-solutions/SvcBus_01_architecture.png
[filas]: ./media/hybrid-solutions/SvcBus_02_queues.png
[topics-subs]: ./media/hybrid-solutions/SvcBus_03_topicsandsubscriptions.png
[retransmissão]: ./media/hybrid-solutions/SvcBus_04_relay.png

<!--HONumber=42-->
