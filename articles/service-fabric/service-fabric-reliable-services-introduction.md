<properties
   pageTitle="Visão geral do modelo de programação de Serviços Confiáveis da Malha de Serviços"
   description="Saiba mais sobre o modelo de programação dos Serviços Confiáveis da Malha de Serviços e comece a desenvolver seus próprios serviços."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor="jessebenson; mani-ramaswamy"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/17/2015"
   ms.author="masnider;jesseb"/>

# Visão geral dos Serviços Confiáveis
A Malha de Serviços simplifica o desenvolvimento e o gerenciamento de serviços confiáveis com e sem estado. Este documento aborda:

1. O modelo de programação de Serviços Confiáveis para serviços com e sem estado.
2. As diferentes decisões que você precisa tomar ao desenvolver um serviço confiável.
3. Alguns dos diferentes cenários e exemplos de quando você usaria Serviços Confiáveis e como eles são desenvolvidos.

Os Serviços Confiáveis são um dos modelos de programação disponíveis na Malha de Serviços. Para obter mais informações sobre o modelo de programação de Atores Confiáveis, confira [a introdução](../service-fabric/service-fabric-reliable-actors-introduction.md).

Na Malha de Serviços, um serviço consiste na configuração, no código do aplicativo e, opcionalmente, no estado.

A Malha de Serviços gerencia a vida útil dos serviços desde o provisionamento e a implantação até a atualização e a exclusão através do [gerenciamento de aplicativos da Malha de Serviço](../service-fabric/service-fabric-deploy-remove-applications.md).

## O que são Serviços Confiáveis?
Os Serviços Confiáveis fornecem um modelo de programação simples, potente e de nível superior que o ajuda a expressar o que é importante para seu aplicativo. Com o modelo de programação de Serviços Confiáveis, você obtém:

1. Para serviços com estado, o modelo de programação de Serviços Confiáveis permite armazenar o estado diretamente dentro do serviço de forma consistente e confiável usando Coleções Confiáveis: um conjunto simples de classes de coleção altamente disponíveis que serão conhecidas por qualquer pessoa que já usou coleções C#. Tradicionalmente, os serviços precisavam de sistemas externos para um gerenciamento de estado confiável. Com Coleções Confiáveis, você pode armazenar o estado próximo ao computador com as mesmas alta disponibilidade e confiabilidade que você espera de armazenamentos externos altamente disponíveis e com os aprimoramentos de latência adicional que a co-localização da computação e o estado fornecem.

2. Um modelo simples para executar seu próprio código que se parece com os modelos de programação com os quais você já está acostumado: o código tem um ponto de entrada bem definido e um ciclo de vida fácil de gerenciar.

3. Um modelo de comunicação conectável - use o transporte de sua escolha, como HTTP com [Web API](../service-fabric/service-fabric-reliable-services-communication-webapi.md), WebSockets, protocolos TCP personalizados, etc. Os Serviços Confiáveis oferece excelentes opções prontas predefinidas ou permite que você forneça suas próprias.

## Por que os Serviços Confiáveis são diferentes?
Os Serviços Confiáveis na Malha de Serviços são diferentes dos serviços que você já desenvolveu anteriormente. A Malha de Serviços fornece confiabilidade, disponibilidade, consistência e escalabilidade.

+ <u>Confiabilidade</u> - seu serviço permanecerá ativo mesmo em ambientes não confiáveis em que suas máquinas podem falhar ou ter problemas de rede.

+ <u>Disponibilidade</u> - seu serviço ficará acessível e responsivo (isso não significa que você não pode ter serviços que não podem ser encontrados ou acessados de fora).

+ <u>Escalabilidade</u> – Os serviços são separados do hardware específico e podem ser aumentados ou reduzidos conforme necessário por através da adição ou da remoção de hardware ou recursos virtuais. Os serviços são particionados com facilidade (especialmente no caso dos serviços com estado) para garantir que partes independentes do serviço possam ser dimensionadas e responder a falhas de forma independente. Finalmente a Malha de Serviços incentiva que os serviços sejam leves, permitindo que milhares de serviços sejam provisionados em um único processo, em vez de exigir ou dedicar instâncias de sistema operacional inteiras para uma única instância de determinada carga de trabalho.

+ <u>Consistência</u> - Isso significa que é possível garantir que as informações armazenadas nesse serviço são consistentes (isso se aplica somente aos serviços com estado - mais informações serão fornecidas posteriormente)

## Ciclo de vida do serviço
Independentemente de seu serviço ser com ou sem estado, os Serviços Confiáveis fornecem um ciclo de vida simples que permite rapidamente conectar seu código e começar. Há apenas um ou dois métodos que você precisa implementar para colocar o serviço em funcionamento.

+ CreateCommunicationListener - Onde o serviço define a pilha de comunicações que deseja usar. A pilha de comunicação, como [Web API](../service-fabric/service-fabric-reliable-services-communication-webapi.md), é o que define os pontos de extremidade de escuta para do serviço (como os clientes o acessarão) e como essas mensagens que aparecem acabam interagindo com o restante do código do serviço.

+ RunAsync - é aqui que o seu serviço executa sua lógica de negócios. O token de cancelamento fornecido é um sinal de quando esse trabalho deve parar. Por exemplo, se você tiver um serviço que precisa constantemente extrair mensagens de um ReliableQueue e processá-las, é aí que esse trabalho aconteceria.

Os principais eventos no ciclo de vida de um Serviço Confiável são os seguintes:

1. O Objeto de Serviço (o elemento derivado de StatelessService ou StatefulService) é criado.

2. O método CreateCommunicationListener é chamado, dando ao serviço a oportunidade de retornar um ouvinte de comunicação de sua escolha.
  + Observe que isso é opcional, embora a maioria dos serviços exponha um ponto de extremidade diretamente.

3. Depois que o CommunicationListener é criado, ele é aberto
  + O CommunicationListeners tem um método chamado Open(), que é chamado neste ponto e que retorna o endereço de escuta do serviço. Se seu Serviço Confiável usar um dos ICommunicationListeners internos, isso será processado para você.

4. Depois que o ouvinte de comunicação se torna Open(), a chamada RunAsync() no serviço principal é chamada.
  + Observe que RunAsync é opcional; se o serviço fizer todo o seu trabalho somente em resposta direta a chamadas do usuário, não é necessário que ele implemente RunAsync().

Quando o serviço está sendo desligado (ou quando for excluído ou simplesmente estiver sendo movido de um local específico), a ordem de chamada é a mesma, primeiro Close() é chamado no CommunicationListener, em seguida, o token de cancelamento que foi passado para RunAsync() é cancelado.

## Serviços de exemplo
Conhecendo este modelo de programação, vamos dar uma olhada em dois serviços diferentes para ver como essas peças se encaixam.

### Serviços Confiáveis sem estado
Um serviço sem estado é aquele em que não há literalmente nenhum estado mantido dentro do serviço ou o estado que está presente é totalmente descartável e não requer sincronização, replicação, persistência ou alta disponibilidade.

Por exemplo, considere uma calculadora que não tem memória e que recebe todos os termos e as operações para serem executados ao mesmo tempo.

Nesse caso, o RunAsync() do serviço pode ser vazio porque não há nenhum processamento de tarefa em segundo plano que o serviço precisa executar. Quando o serviço da Calculadora for criado, ele retornará um CommunicationListener (por exemplo, [Web API](../service-fabric/service-fabric-reliable-services-communication-webapi.md)) que abre um ponto de extremidade de escuta em alguma porta. Esse ponto de extremidade de escuta se conectará aos diferentes métodos (por ex.: "Add (n1, n2)") que definem a API pública da Calculadora.

Quando é feita uma chamada de um cliente, o método apropriado é invocado e o serviço da Calculadora executa as operações nos dados fornecidos e retorna o resultado. O estado não é armazenado.

Não armazenar o estado interno torna este exemplo de Calculadora muito simples. No entanto, a maioria dos serviços não é realmente sem estado - em vez disso, eles externalizam seu estado em algum outro repositório (por exemplo, qualquer aplicativo Web que mantém o estado em um repositório de suporte ou cache não é completamente sem estado).

Um exemplo comum de como serviços sem estado são usados na Malha de Serviços é um front-end que expõe a API voltada ao público para um aplicativo Web. Em seguida, o serviço front-end se comunica com serviços com estado para concluir a solicitação de um usuário. Nesse caso, chamadas de clientes são direcionadas para uma porta como 80 em que o serviço sem estado está escutando. Esse serviço sem estado recebe a chamada e determina se ela é de uma parte confiável, bem como o serviço ao qual ela é destinada. Em seguida, o serviço sem estado encaminha a chamada para a partição correta do serviço com estado e aguarda uma resposta. Quando ele recebe uma resposta, responde ao cliente original.

### Serviços Confiáveis com estado
Um serviço com estado deve ter alguma parte do estado consistente e presente para que o serviço funcione. Considere um serviço que constantemente calcula uma média móvel de algum valor com base em atualizações que está recebendo. Para fazer isso, ele deve ter o conjunto atual de solicitações de entrada que precisa processar, bem como a média atual. Qualquer serviço que recupera, processa e armazena informações em um repositório externo (como repositório de Tabela ou Blob do Azure hoje) é um serviço com estado - apenas mantém seu estado no repositório de estado externo.

A maioria dos serviços hoje armazena seu estado externamente, pois o repositório externo é o que fornece confiabilidade, disponibilidade, escalabilidade e consistência para esse estado. Na Malha de Serviços, serviços com estado não precisam armazenar seu estado externamente, porque a Malha de Serviços cuida desses requisitos para o código e o estado do serviço.

Vamos supor que queiramos desenvolver um serviço que recebeu solicitações para uma série de conversões que precisavam ser realizadas em uma imagem e a imagem que precisava ser convertida. Para esse serviço, ele retornaria um CommunicationListener (por exemplo, WebAPI) que abre uma porta de comunicação e permite envios por meio de uma API como `ConvertImage(Image i, IList<Conversion> conversions)`. Nessa API, o serviço pode receber as informações e armazenar a solicitação em um ReliableQueue e depois retornar algum token ao cliente para que ele possa rastrear a solicitação (já que as solicitações podem demorar algum tempo).

Neste serviço, o RunAsync poderia ser mais complexo: o serviço teria um loop dentro de seu RunAsync que extrai solicitações do ReliableQueue, executa as conversões listadas e armazena os resultados em um ReliableDictionary para que, quando o cliente volta, pode obter suas imagens convertidas. Para garantir que, mesmo que algo falhe, a imagem não seja perdida, esse serviço confiável extrairia da fila, executaria as conversões e armazenaria o resultado em uma transação para que a mensagem só seja de fato removida da fila e os resultados sejam armazenados no dicionário de resultados quando as conversões estiverem concluídas. Se algo falhar no meio (como o computador em que essa instância do código está em execução), a solicitação permanecerá na fila aguardando para ser processada novamente.

Algo a ser observado sobre esse serviço é que parece um serviço .NET normal - a única diferença é que as estruturas de dados que estão sendo usadas (ReliableQueue e ReliableDictionary) são fornecidas pela Malha de Serviços e, portanto, se tornam altamente confiáveis, disponíveis e consistentes.

## Quando usar APIs de Serviços Confiáveis
Se um dos seguintes itens caracterizar as necessidades de serviço do aplicativo, as APIs de Serviços Confiáveis devem ser consideradas:

- Você deve fornecer o comportamento do aplicativo em várias unidades de estado (por exemplo, pedidos e itens de linha de pedido)

- O estado do aplicativo pode ser modelado naturalmente como filas e dicionários confiáveis

- O estado precisa ser altamente disponível com acesso de baixa latência

- Seu aplicativo precisa controlar a simultaneidade ou a granularidade das operações realizadas em uma ou mais coleções confiáveis

- Você deseja gerenciar as comunicações e controlar o esquema de particionamento do seu serviço

- Seu código precisa de um ambiente de tempo de execução de thread livre

- Seu aplicativo precisa criar ou destruir filas ou dicionários confiáveis dinamicamente no tempo de execução

- Você precisa controlar de forma programática os recursos de backup e restauração fornecidos pela Malha de Serviços para o estado do seu serviço*

- Seu aplicativo precisa manter um histórico de alterações para suas unidades de estado*

- Você deseja desenvolver ou consumir provedores de estado personalizados desenvolvidos por terceiros*

> [AZURE.NOTE]*Recursos disponíveis na disponibilidade geral do SDK


## Próximas etapas
+ [Início Rápido dos Serviços Confiáveis](../service-fabric/service-fabric-reliable-services-quick-start.md)
+ [Confira o uso avançado dos Serviços Confiáveis](service-fabric-reliable-services-advanced-usage.md)
+ [Leia o modelo de programação de Atores Confiáveis](../service-fabric/service-fabric-reliable-actors-introduction.md)
 

<!---HONumber=July15_HO4-->