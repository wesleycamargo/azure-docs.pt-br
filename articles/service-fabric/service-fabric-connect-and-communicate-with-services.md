<properties
   pageTitle="Malha de serviço do Microsoft Azure Como se comunicar com serviços"
   description="Este artigo descreve como você pode conectar-se e comunicar-se com serviços em aplicativos de malha do serviço."
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/29/2015"
   ms.author="kunalds"/>


# Comunicação com os serviços
No mundo de microsserviços a solução geral é composta de vários serviços diferentes, onde cada um executa uma tarefa específica. Esses microsserviços se comunicam uns com os outros para permitir o fluxo de trabalho de ponta a ponta. Também existem aplicativos cliente que se conectam e se comunicam com os serviços. Este documento discute como a malha de serviços facilita a instalação da comunicação com os serviços que você escreve com a malha de serviço.

## Principais conceitos
Esses são alguns dos principais conceitos que você precisa saber ao configurar a comunicação com os serviços.
### A comunicação é representada como cliente-servidor
As APIs de comunicação da malha de serviço representam o tipo interação cliente-serviço, mesmo no caso em que é entre dois serviços em execução no mesmo cluster. Um serviço de destino que será conectado a partir de um cliente ou outro serviço atua como um servidor e escuta solicitações de entrada. O cliente, que pode ser apenas outro serviço em cluster, conecta-se ao servidor e faz chamadas para ele.
### Movimentação de serviços
Em um sistema distribuído as instâncias de serviço que você executa podem passar de um computador para outro ao longo do tempo por vários motivos, como balanceamento de carga quando configurado com métricas de carga para balanceamento de recursos ou devido a atualizações, failovers, expansão e várias outras situações. O impacto disso é que os endereços de ponto de extremidade de uma instância de serviço podem ser alterados e para comunicação com a instância do serviço o loop a seguir deve ser executado. Esses detalhes são omitidos de você se você usar as APIs de comunicação fornecidas pela malha do serviço.

* **Resolver**: Todas as instâncias de serviços na malha de serviço tem URIs exclusivos, por exemplo "fabric:/MyApplication/MyService" e esses URIs não são alterados ao longo do tempo. Cada instância de serviço também expõe pontos de extremidade que podem ser alterados quando instâncias de serviço são movidas. Isso é semelhante aos sites que tenham URLs constantes, mas que podem alterar o endereço IP. E como o DNS na world wide web que resolve as URLs do site para endereços IP a plataforma de malha do serviço também tem um serviço do sistema que resolve os URIs para os pontos de extremidade. Esta etapa envolve resolver o URI da instância de serviço para um ponto de extremidade.

* **Conectar**: Depois que o URI de serviço tiver sido resolvido para os endereços de ponto de extremidade a próxima etapa é tentar uma conexão com esse serviço. Essa conexão pode falhar se o endereço de ponto de extremidade for alterado devido a um movimento de serviço, que, por exemplo, pode ter sido causado por uma falha do computador ou devido ao balanceamento de carga.

* **Tentar novamente**: Se houver uma falha na tentativa as etapas resolver e conectar precisam ser repetidas e esse ciclo é repetido até que a conexão seja bem-sucedida.

## Decisão de usar a API de comunicação
Como parte da malha de serviços, fornecemos algumas opções diferentes para APIs de comunicação e a decisão para a qual uma funcionará melhor para você depende da escolha do modelo de programação, da estrutura de comunicação e da linguagem de programação na qual os serviços são escritos.
### Comunicação para atores confiáveis
Para serviços usando a API de atores confiável todos os detalhes de comunicação são abstraídos e a comunicação ocorre conforme o método chama o ActorProxy e você pode interromper a leitura aqui.

### Opções de comunicação para serviços confiáveis
Há algumas opções diferentes se o serviço foi escrito usando a API de serviços confiáveis. A escolha do protocolo de comunicação orientará sobre quais APIs de comunicação de malha do serviço que você pode usar.

* **Não há um protocolo de comunicação específicos que preciso usar e quero logo algo funcionando**: Se você não tiver uma preferência específica de estrutura de comunicação, a opção ideal para você deve usar o [Pilha padrão](service-fabric-reliable-services-communication-default.md) que permite um modelo semelhante ao modelo de comunicação de Ator. Essa é a maneira mais fácil e rápida de começar a comunicação de serviço. Ele fornece comunicação RPC com rigidez de tipos que abstrai a maior parte dos detalhes de comunicação para que invocar um método em um serviço remoto no seu código seja semelhante a chamar um método em uma instância de objeto local. Essas classes abstraem a resolução, a conexão, a repetição e o tratamento do erro ao mesmo tempo em que configura o canal de comunicação e permite um modelo de comunicação baseado em chamada de método. Para isso a classe `ServiceCommunicationListener` deve ser usada para o lado do servidor e a classe `ServiceProxy` no lado do cliente da comunicação.

* **HTTP**: Para aproveitar a flexibilidade que comunicação HTTP oferece você pode usar as APIs de comunicação da malha de serviço que permitem que o mecanismo de comunicação seja definido enquanto a lógica de resolução, conexão e repetição é omitida de você. Por exemplo, você pode usar a API da Web para especificar o mecanismo de comunicação e aproveitar as [classes `ICommunicationClient` e `ServicePartitionClient`](service-fabric-reliable-services-communication.md) para definir a comunicação.
* **WCF**: Se você tiver um código existente que usa WCF como sua estrutura de comunicação, você pode usar o WcfCommunicationListener para o lado do servidor e as classes WcfCommunicationClient e ServicePartitionClient para o cliente. Para obter mais detalhes, consulte [este artigo](service-fabric-reliable-services-communication-wcf.md).

* **Outras estruturas de comunicação, inclusive protocolos personalizados**: Se você estiver planejando usar qualquer outra estrutura de comunicação incluindo protocolos de comunicação personalizados existem APIs nas quais você pode ligar a sua pilha de comunicação enquanto todo o trabalho para descobrir e conectar é omitido de você. Consulte [este artigo](service-fabric-reliable-services-communication.md) para obter mais detalhes.

### Comunicação entre serviços escritos em linguagens de programação diferentes
Todas as APIs de comunicação do ServiceFabric estão atualmente disponíveis somente em C# então se você tiver um serviço escrito em qualquer outra linguagem de programação, como Java ou Node.JS então você precisa escrever seu próprio mecanismo de comunicação.

## Próximas etapas
* [Pilha de comunicação padrão fornecida pela estrutura de serviços confiáveis ](service-fabric-reliable-services-communication-default.md)
* [Modelo de comunicação de serviços confiáveis](service-fabric-reliable-services-communication.md)
* [Introdução à API da Web da malha de serviço do Microsoft Azure OWIN auto-host](service-fabric-reliable-services-communication-webapi.md)
* [WCF com base em pilha de comunicação para serviços confiáveis](service-fabric-reliable-services-communication-wcf.md)
 

<!---HONumber=August15_HO6-->