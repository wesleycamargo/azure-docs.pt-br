<properties
   pageTitle="Visão geral do modelo de programação de Reliable Service do Service Fabric | Microsoft Azure"
   description="Saiba mais sobre o modelo de programação dos Serviços Confiáveis da Malha de Serviços e comece a desenvolver seus próprios serviços."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor="vturecek; mani-ramaswamy"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/25/2016"
   ms.author="masnider;vturecek"/>

# Visão geral dos Reliable Services
O Azure Service Fabric simplifica o desenvolvimento e o gerenciamento de Reliable Services com e sem estado. Este documento aborda:

- O modelo de programação do Reliable Services para serviços com e sem estado.
- As decisões que você precisa tomar ao desenvolver um Reliable Service.
- Alguns cenários e exemplos de quando você usaria Reliable Services e como eles são desenvolvidos.

Os Serviços Confiáveis são um dos modelos de programação disponíveis na Malha de Serviços. Para saber mais sobre o modelo de programação de Reliable Actors, confira [Introdução aos Reliable Actors do Service Fabric](service-fabric-reliable-actors-introduction.md).

No Service Fabric, um serviço é formado por configuração, código do aplicativo e, opcionalmente, estado.

O Service Fabric gerencia a vida útil dos serviços, desde o provisionamento e da implantação até a atualização e a exclusão, por meio do [gerenciamento de aplicativos do Service Fabric](service-fabric-deploy-remove-applications.md).

## O que são os Reliable Services?
Os Reliable Services fornecem um modelo de programação simples, potente e de nível superior que ajuda você a demonstrar o que é importante para seu aplicativo. Com o modelo de programação dos Reliable Services, você obtém:

- Para serviços com estado, o modelo de programação dos Reliable Services permite que você armazene de forma consistente e confiável o seu estado diretamente de seu serviço usando o Reliable Collections. O Reliable Collections são um conjunto simples de classes de coleção altamente disponíveis que serão familiares para as pessoas que já utilizaram as coleções C#. Tradicionalmente, os serviços precisavam de sistemas externos para um gerenciamento de estado confiável. Com o Reliable Collections, você pode armazenar o estado próximo ao computador com a mesma alta disponibilidade e confiabilidade que você espera dos armazenamentos externos altamente disponíveis e com os aprimoramentos de latência adicionais fornecidos pela colocalização da computação e do estado.

- Um modelo simples para execução de seu próprio código que parece com os modelos de programação com os quais você está acostumado. Seu código tem um ponto de entrada bem definido e um ciclo de vida fácil de gerenciar.

- Um modelo de comunicação conectável. Use o transporte de sua escolha, como HTTP com [API Web](service-fabric-reliable-services-communication-webapi.md), WebSockets, protocolos TCP personalizados, etc. O Reliable Services oferece excelentes opções de utilização ou permite que você forneça suas próprias.

## O que torna o Reliable Services diferente?
O Reliable Services no Service Fabric é diferente dos serviços que você já desenvolveu anteriormente. A Malha de Serviços fornece confiabilidade, disponibilidade, consistência e escalabilidade.

- **Confiabilidade** - seu serviço permanecerá ativo mesmo em ambientes não confiáveis em que suas máquinas podem falhar ou ter problemas de rede.

- **Disponibilidade** - seu serviço estará acessível e responderá rapidamente. (Isso não significa que você não possa ter serviços que não podem ser encontrados ou acessados de fora.)

- **Escalabilidade** - os serviços são separados do hardware específico e podem ser aumentados ou reduzidos conforme necessário por meio da adição ou da remoção de hardware ou recursos virtuais. Os serviços são particionados com facilidade (especialmente no caso dos serviços com estado) para garantir que partes independentes do serviço possam ser dimensionadas e responder a falhas de forma independente. Finalmente a Malha de Serviços incentiva que os serviços sejam leves, permitindo que milhares de serviços sejam provisionados em um único processo, em vez de exigir ou dedicar instâncias de sistema operacional inteiras para uma única instância de determinada carga de trabalho.

- **Consistência** - quaisquer informações armazenadas nesse serviço podem ser garantidamente consistentes (isso se aplica somente aos serviços com estado. Encontre mais informações posteriormente)

## Ciclo de vida do serviço
Independentemente de seu serviço ser com ou sem estado, o Reliable Services fornece um ciclo de vida simples que permite conectar rapidamente seu código e começar. Há apenas um ou dois métodos que você precisa implementar para colocar o serviço em funcionamento.

- **CreateServiceReplicaListeners/CreateServiceInstanceListeners** - onde o serviço define a pilha de comunicações que deseja usar. A pilha de comunicações, como [API Web](service-fabric-reliable-services-communication-webapi.md), é o que define o ponto de extremidade de escuta ou os pontos de extremidade do serviço (como os clientes o acessarão). Ele também define como as mensagens que aparecem acabam interagindo com o restante do código do serviço.

- **RunAsync** - é aqui que seu serviço executa sua lógica de negócios. O token de cancelamento fornecido é um sinal de quando esse trabalho deve parar. Por exemplo, se você tiver um serviço que precisa constantemente extrair e processar mensagens de uma ReliableQueue, é aí que esse trabalho aconteceria.

### Inicialização do serviço

Os principais eventos no ciclo de vida de um Reliable Service são:

1. O objeto de serviço (o elemento derivado de um serviço sem estado ou serviço com estado) é criado.

2. O método `CreateServiceReplicaListeners`/`CreateServiceInstanceListeners` é chamado, dando ao serviço a oportunidade de retornar um ou mais ouvintes de comunicação de sua escolha.
  - Observe que isso é opcional, embora a maioria dos serviços exponha um ponto de extremidade diretamente.

3. Após a criação dos ouvintes de comunicação, eles são abertos.
  - Os ouvintes de comunicação têm um método denominado `OpenAsync()`, que é chamado neste ponto e retorna o endereço de escuta do serviço. Se seu Reliable Service usar um dos ICommunicationListeners internos, isso será processado para você.

4. Depois que o ouvinte de comunicação for aberto, o método `RunAsync()` será chamado no serviço principal.
  - Observe que `RunAsync()` é opcional. Se o serviço fizer todo seu trabalho diretamente e apenas em resposta a chamadas do usuário, não será necessário implementar `RunAsync()`.

### Desligamento do serviço

Quando o serviço está sendo desligado (para ser excluído, movido ou atualizado) a ordem de chamada é espelhada: primeiro, o token de cancelamento mantido por `RunAsync()` é cancelado. Em seguida, `CloseAsync()` é chamado nos ouvintes de comunicação.

Há algumas coisas importantes a serem observadas sobre o desligamento de serviços com estado:

- O Service Fabric não promoverá outra réplica do serviço para o status Primário até que `CloseAsync` e `RunAsync` sejam retornados. Se você estiver usando um ouvinte de comunicação interno, o método `CloseAsync` será manipulado para você.

- Embora não haja um limite de tempo para o retorno desses métodos, você perde imediatamente a capacidade de gravar em Reliable Collections e, portanto, não pode concluir qualquer trabalho real. Recomendamos que você retorne o mais rápido possível após o recebimento da solicitação de cancelamento.

## Exemplo de serviços
Conhecendo este modelo de programação, vamos dar uma olhada em dois serviços diferentes para ver como essas peças se encaixam.

### Serviços Confiáveis sem estado
Um serviço sem estado é aquele em que não há literalmente nenhum estado mantido dentro do serviço, ou o estado que está presente é totalmente descartável e não requer sincronização, replicação, persistência ou alta disponibilidade.

Por exemplo, considere uma calculadora que não tenha memória e que receba todos os termos e operações para serem executados ao mesmo tempo.

Nesse caso, o RunAsync() do serviço pode estar vazio porque não há processamentos de tarefas em segundo plano que o serviço precisa executar. Quando o serviço da calculadora for criado, ele retornará um ICommunicationListener (por exemplo, [API Web](service-fabric-reliable-services-communication-webapi.md)) que abre um ponto de extremidade de escuta em alguma porta. Esse ponto de extremidade de escuta se conectará aos diferentes métodos (por exemplo: "Add (n1, n2)") que definem a API pública da calculadora.

Quando é feita uma chamada de um cliente, o método apropriado é invocado e o serviço da calculadora executa as operações nos dados fornecidos e retorna o resultado. O estado não é armazenado.

Não armazenar o estado interno torna este exemplo de calculadora muito simples. Mas a maioria dos serviços não é realmente sem estado. Em vez disso, eles externalizam o estado para algum outro repositório. (Por exemplo, qualquer aplicativo Web que depende da manutenção do estado da sessão em um repositório de backup ou do cache não é completamente sem estado.)

Um exemplo comum de como serviços sem estado são usados na Malha de Serviços é um front-end que expõe a API voltada ao público para um aplicativo Web. O serviço front-end se comunica com serviços com estado para concluir a solicitação de um usuário. Nesse caso, chamadas de clientes são direcionadas para uma porta conhecida, por exemplo, a 80, na qual o serviço sem estado está escutando. Esse serviço sem estado recebe a chamada e determina se ela é de uma parte confiável, bem como o serviço ao qual ela é destinada. Em seguida, o serviço sem estado encaminha a chamada para a partição correta do serviço com estado e aguarda uma resposta. Quando o serviço sem estado recebe uma resposta, ele responde ao cliente original.

### Serviços Confiáveis com estado
Um serviço com estado deve ter alguma parte do estado consistente e presente para que o serviço funcione. Considere um serviço que calcula constantemente uma média móvel de algum valor com base em atualizações que recebe. Para fazer isso, ela deve ter o conjunto atual de solicitações de entrada que precisa processar, além da média atual. Qualquer serviço que recupera, processa e armazena informações em um repositório externo (como um repositório de tabelas ou blob do Azure) é um serviço com estado. Ele simplesmente mantém seu estado no repositório de estado externo.

A maioria dos serviços atuais armazena seu estado externamente, pois o repositório externo é o que fornece confiabilidade, disponibilidade, escalabilidade e consistência para esse estado. No Service Fabric, os serviços com estado não precisam armazenar seu estado externamente, pois o Service Fabric cuida desses requisitos para o código e o estado do serviço.

Vamos supor que queiramos desenvolver um serviço que recebe solicitações para uma série de conversões que precisam ser realizadas em uma imagem e a imagem que precisa ser convertida. Para esse serviço, ele retornaria um ouvinte de comunicação (por exemplo, API Web) que abre uma porta de comunicação e permite envios por meio de uma API como `ConvertImage(Image i, IList<Conversion> conversions)`. Nessa API, o serviço pode receber as informações e armazenar a solicitação em um Reliable Queue e depois retornar algum token ao cliente para que ele possa rastrear a solicitação (já que as solicitações podem demorar algum tempo).

Nesse serviço, RunAsync poderia ser mais complexo. O serviço teria um loop dentro de seu RunAsync que extrairia solicitações do IReliableQueue, executaria as conversões listadas e armazenaria os resultados em um IReliableDictionary de modo que, quando o cliente voltasse, obteria suas imagens convertidas. Para garantir que mesmo com alguma falha a imagem não seja perdida, esse Reliable Service retiraria da fila, executaria as conversões e armazenaria o resultado em uma transação. Nesse caso, a mensagem é realmente removida apenas da fila e os resultados são armazenados no dicionário de resultados após a conclusão das conversões. Se algo falhar no meio (como o computador no qual essa instância do código está em execução), a solicitação permanecerá na fila aguardando para ser processada novamente.

Um ponto a ser observado sobre esse serviço é que parece um serviço .NET normal. A única diferença é que as estruturas de dados que estão sendo usadas (IReliableQueue e IReliableDictionary) são fornecidas pelo Service Fabric e, portanto, são altamente confiáveis, disponíveis e consistentes.

## Quando usar APIs de Reliable Services
Se um dos itens a seguir caracterizar as necessidades de serviço do aplicativo, considere as APIs de Reliable Services:

- Você deve fornecer o comportamento do aplicativo em várias unidades de estado (por exemplo, pedidos e itens de linha de pedido).

- O estado do aplicativo pode ser modelado naturalmente como Filas e Dicionários Confiáveis.

- O estado precisa estar altamente disponível com acesso de baixa latência.

- Seu aplicativo precisa controlar a simultaneidade ou a granularidade das operações realizadas em uma ou mais Reliable Collections.

- Você deseja gerenciar as comunicações e controlar o esquema de particionamento do seu serviço.

- Seu código precisa de um ambiente de tempo de execução de thread livre.

- Seu aplicativo precisa criar ou destruir Filas ou Dicionários Confiáveis dinamicamente no tempo de execução.

- Você precisa controlar de forma programática os recursos de backup e restauração fornecidos pelo Service Fabric para o estado do seu serviço*.

- Seu aplicativo precisa manter um histórico de alterações para suas unidades de estado*.

- Você deseja desenvolver ou consumir provedores de estado personalizados desenvolvidos por terceiros*.

> [AZURE.NOTE] *Recursos disponíveis na disponibilidade geral do SDK.


## Próximas etapas
+ [Início Rápido dos Serviços Confiáveis](service-fabric-reliable-services-quick-start.md)
+ [Uso avançado de Reliable Services](service-fabric-reliable-services-advanced-usage.md)
+ [O modelo de programação de Reliable Actors](service-fabric-reliable-actors-introduction.md)

<!---HONumber=AcomDC_0406_2016-->