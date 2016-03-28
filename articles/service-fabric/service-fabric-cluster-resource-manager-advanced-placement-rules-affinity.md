<properties
   pageTitle="Gerenciador de Recursos de Cluster do Service Fabric – Afinidade | Microsoft Azure"
   description="Visão geral das políticas de posicionamento adicionais e das regras para os Serviços do Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/10/2016"
   ms.author="masnider"/>

# Configurando e usando a afinidade de serviço no Service Fabric

A afinidade é uma das coisas que, pelo menos a princípio, não faz muito sentido para um ambiente de microsserviço. E isso acontece porque realmente não faz muito sentido em um ambiente de microsserviço. A afinidade é um controle fornecido principalmente para ajudar a facilitar a transição de aplicativos maiores e anteriormente monolíticos para a nuvem e para o mundo de microsserviços.

Vamos supor que você esteja levando um aplicativo maior, ou que simplesmente não tenha sido desenvolvido pensando nos microsserviços, para o Service Fabric. Na verdade, isso é bastante comum, e vários clientes (internos e externos) já estiveram nessa situação. Comece levando todo o aplicativo para o ambiente, empacotando-o e colocando-o em execução. Em seguida, comece a dividi-lo em serviços menores diferentes que se comunicam entre si.

Em seguida, há um "Ops". O "Ops" geralmente se encaixa em uma destas categorias:

1. Verificou-se que o componente X no aplicativo monolítico tinha uma dependência não documentada do componente Y que acabamos de transformar em um serviço e movimentar pelo cluster. Agora ele está dividido.
2.	Essas coisas se comunicam por meio de (pipes locais nomeados| memória compartilhada | arquivos em disco), mas eu preciso apenas ser capaz de atualizá-las de forma independente para agilizar um pouco as coisas.
3.	Tudo está bem, mas acontece que esses dois componentes são muito verborrágicos/dependentes do desempenho e, portanto, quando os coloquei em serviços separados, meu desempenho estagnou.

Em todos esses casos, não queremos perder nosso trabalho de refatoração e não queremos voltar ao monolítico, mas queremos tentar agrupar as coisas novamente para que exista a "sensação" de localidade até que possamos corrigir tudo.

O que fazer? Bem, você pode tentar ativar a afinidade.

## Como funciona a afinidade
Para configurar a afinidade, você define uma relação de afinidade entre dois serviços diferentes. Normalmente, você pode pensar nisso como se estivesse "apontando" um serviço para outro e dizendo "este serviço só pode ser executado quando esse serviço estiver em execução". Às vezes, nos referimos a eles como relações de pai e filho (nas quais você aponta o filho para o pai). O que isso faz é assegurar que as réplicas ou instâncias de um serviço sejam colocadas nos mesmos nós que as réplicas ou instâncias do serviço com o qual ele tem uma afinidade.

``` csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## Diferentes opções de afinidade
A afinidade é representada por meio de um dos vários esquemas de correlação e tem dois modos diferentes. O modo mais comum de afinidade é o que chamamos de NonAlignedAffinity. No modo NonAlignedAffinity, as réplicas ou instâncias dos diferentes serviços são colocadas nos mesmos nós. Outro modo é o AlignedAffinity. O modo AlignedAffinity é usado com serviços com estado. A configuração de um serviço com estado como tendo afinidade alinhada com outro serviço com estado garante que os principais desses serviços sejam colocadas nos mesmos nós, e que cada par de secundários também sejam colocados nos mesmos nós. Também é possível (embora menos comum) configurar NonAlignedAffinity para serviços com estado. Nesse caso, você obteria praticamente o mesmo comportamento que os serviços sem estado: as réplicas diferentes dos dois serviços com estado seriam colocadas nos mesmos nós, mas nenhuma tentativa seria feita para garantir que os principais e secundários estivessem nos mesmos nós.

![Modos de afinidade e seus efeitos][Image1]

### Estado desejado de melhor esforço
Há algumas diferenças entre arquiteturas monolíticas e de afinidade. Quase todas elas se resumem ao fato de que uma relação de afinidade é a melhor solução; já que são serviços fundamentalmente diferentes, eles podem falhar de forma independente, por exemplo. Outras coisas podem causar a separação de réplicas diferentes do serviço, como as limitações de capacidade.


### Cadeias vs. estrelas
Hoje, não somos capazes de modelar cadeias de relações de afinidade. Isso significa que um serviço filho em um relacionamento de afinidade não pode ser pai em outro relacionamento de afinidade. Isso também significa que se você quiser modelar esse tipo de relação, será necessário modelá-la efetivamente como uma estrela, em vez de uma cadeia, colocando o filho mais baixo como pai do pai do filho do "meio".

![Cadeias versus Estrelas no contexto de relações de afinidade][Image2]

Outra coisa a ser observada sobre as relações atuais de afinidade é que elas são direcionais. Isso é sutil, mas significa, efetivamente, que a regra de "afinidade" apenas impõe que o filho esteja onde o pai estiver. Caso o pai, de repente, realize um failover para outro nó (ou qualquer outra ação limitada que force a movimentação apenas do pai), o Gerenciador de Recursos não considerará que há algo errado até que perceba que o filho não está localizado com um pai; a relação não é aplicada imediatamente.

### Suporte ao particionamento
A observação final sobre a afinidade é que as relações de afinidade não têm suporte quando o pai é particionado. Isso é algo ao qual poderemos oferecer suporte eventualmente, mas não é permitido no momento.

## Próximas etapas
- Para saber mais sobre outras opções disponíveis para a configuração de serviços, confira o tópico sobre outras configurações disponíveis do Gerenciador de Recursos de Cluster em [Saiba mais sobre a configuração de Serviços](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png

<!---HONumber=AcomDC_0316_2016-->