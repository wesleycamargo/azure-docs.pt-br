<properties
   pageTitle="Comunique-se e conecte-se a serviços no Service Fabric do Azure | Microsoft Azure"
   description="Saiba como se conectar e se comunicar com serviços em aplicativos do Service Fabric."
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
   ms.date="08/21/2015"
   ms.author="kunalds"/>


# Comunicar-se com serviços
No mundo de microsserviços a solução geral é composta de vários serviços diferentes, em que cada serviço executa uma tarefa específica. Esses microsserviços comunicam-se entre si para permitir o fluxo de trabalho de ponta a ponta. Também existem aplicativos cliente que se conectam e se comunicam com os serviços. Este documento discute como o Service Fabric do Azure facilita a instalação da comunicação com os serviços que você escreve com o Service Fabric.

## Principais conceitos
Estes são alguns dos principais conceitos que você precisa saber ao configurar a comunicação com os serviços.
### A comunicação é representada como cliente-servidor
APIs de comunicação do Service Fabric representam tipos de cliente-servidor de interações, mesmo quando a interação é entre dois serviços em execução no mesmo cluster. Um serviço de destino que será conectado de um cliente ou outro serviço atua como um servidor e escuta solicitações de entrada. O cliente, que pode ser apenas outro serviço em cluster, conecta-se ao servidor e faz chamadas para ele.
### Movimentação de serviços
Em um sistema distribuído, as instâncias de serviço que você executa podem passar de um computador para outro ao longo do tempo. Isso pode ocorrer por vários motivos, incluindo o balanceamento de carga quando configurado com métricas de carga para balanceamento de recursos, atualizações, failovers, expansão. O impacto disso é que os endereços de ponto de extremidade de uma instância de serviço podem mudar. Para configurar a comunicação com a instância do serviço, o seguinte loop precisa ser executado. Esses detalhes são omitidos de você se você usar as APIs de comunicação fornecidas pela malha do serviço.

* **Resolver**: todas as instâncias de serviço do Service Fabric têm URIs exclusivos. Por exemplo, "fabric:/ MyApplication/MyService" e seus URIs não são alterados ao longo do tempo. Cada instância de serviço também expõe pontos de extremidade que podem ser alterados quando as instâncias de serviço são movidas. Isso é semelhante aos sites que têm URLs constantes, mas em que o endereço IP pode mudar. E como o DNS na web que resolve as URLs do site para endereços IP, a plataforma do Service Fabric também tem um serviço do sistema que resolve os URIs para os pontos de extremidade. Esta etapa envolve resolver o URI da instância de serviço para um ponto de extremidade.

* **Conectar**: depois que o URI de serviço tiver sido resolvido para os endereços de ponto de extremidade, a próxima etapa será tentar uma conexão com esse serviço. Essa conexão pode falhar se o endereço de ponto de extremidade for alterado devido a um movimento de serviço, que, por exemplo, pode ter sido causado por uma falha do computador ou um balanceamento de carga.

* **Tentar novamente**: se houver uma falha na tentativa, as etapas resolver e conectar precisam ser repetidas, e esse ciclo é repetido até que a conexão seja bem-sucedida.

## Opções de comunicação de API
Como parte do Service Fabric, fornecemos algumas opções diferentes para APIs de comunicação. A decisão sobre qual uma funcionará melhor para você depende da escolha do modelo de programação, da estrutura de comunicação e da linguagem de programação em que os serviços são escritos.
### Comunicação para atores confiáveis
Para serviços escritos usando a API Reliable Actors, todos os detalhes de comunicação são abstraídos. A comunicação acontece como chamadas de método no ActorProxy, portanto, você pode interromper a leitura aqui.

### Opções de comunicação para serviços confiáveis
Há algumas opções diferentes se o serviço tiver sido escrito usando a API do Reliable Services. A escolha do protocolo de comunicação orientará sobre quais APIs de comunicação de Service Fabric você usa.

* **Nenhum protocolo específico:** se você não tiver uma preferência específica de estrutura de comunicação, mas desejar colocar algo em funcionamento rapidamente, a opção ideal para você é a [pilha padrão](service-fabric-reliable-services-communication-remoting.md), que permite um modelo semelhante ao modelo de comunicação de ator. Essa é a maneira mais fácil e rápida de começar a comunicação de serviço. Ele fornece comunicação RPC com rigidez de tipos que abstrai a maior parte dos detalhes de comunicação para que invocar um método em um serviço remoto no seu código seja semelhante a chamar um método em uma instância de objeto local. Essas classes abstraem a resolução, a conexão, a repetição e o tratamento do erro ao mesmo tempo em que configuram o canal de comunicação e permitem um modelo de comunicação baseado em chamada de método. Para isso, use a classe `ServiceCommunicationListener` no lado do servidor e a classe `ServiceProxy` no lado do cliente da comunicação.

* **HTTP**: Para aproveitar a flexibilidade que comunicação HTTP oferece você pode usar as APIs de comunicação do Service Fabric que permitem definir o mecanismo de comunicação enquanto a lógica de resolução, de conexão e de repetição é omitida de você. Por exemplo, você pode usar a API Web para especificar o mecanismo de comunicação e aproveitar as [classes `ICommunicationClient` e `ServicePartitionClient`](service-fabric-reliable-services-communication.md) para definir a comunicação.
* **WCF**: se você tiver um código existente que use WCF como estrutura de comunicação, poderá usar o WcfCommunicationListener para o lado do servidor e as classes WcfCommunicationClient e ServicePartitionClient para o cliente. Para obter mais detalhes, consulte este artigo sobre [implementação baseada no WCF da pilha de comunicação](service-fabric-reliable-services-communication-wcf.md).

* **Outras estruturas de comunicação, inclusive protocolos personalizados**: se você estiver planejando usar qualquer outra estrutura de comunicação incluindo protocolos de comunicação personalizados, existem APIs de comunicação do Service Fabric nas quais você pode conectar a sua pilha de comunicação enquanto todo o trabalho para descobrir e conectar é omitido de você. Consulte este artigo sobre o [modelo de comunicação de serviço confiável](service-fabric-reliable-services-communication.md) para obter mais detalhes.

### Comunicação entre serviços escritos em linguagens de programação diferentes
Todas as APIs de comunicação do Service Fabric atualmente estão disponíveis apenas em c#. Isso significa que, se você tiver um serviço escrito em qualquer outra linguagem de programação, como Java ou Node. js, precisará escrever seu próprio mecanismo de comunicação.

## Próximas etapas
* [Pilha de comunicação padrão fornecida pela estrutura de serviços confiáveis ](service-fabric-reliable-services-communication-remoting.md)
* [Modelo de comunicação de serviços confiáveis](service-fabric-reliable-services-communication.md)
* [Introdução aos serviços de API Web do Service Fabric do Microsoft Azure com auto-hospedagem do OWIN](service-fabric-reliable-services-communication-webapi.md)
* [Pilha de comunicação baseada no WCF para Reliable Services](service-fabric-reliable-services-communication-wcf.md)

<!---HONumber=AcomDC_1223_2015-->