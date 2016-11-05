---
title: Gerenciador de Recursos de Cluster do Service Fabric – Afinidade | Microsoft Docs
description: Visão geral da configuração de afinidade para os Serviços do Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''

ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/19/2016
ms.author: masnider

---
# Configurando e usando a afinidade de serviço no Service Fabric
A afinidade é um controle fornecido principalmente para ajudar a facilitar a transição de aplicativos monolíticos maiores para a nuvem e o mundo de microsserviços. Isso posto, ele também pode ser usado em certos casos como uma otimização legítima para melhorar o desempenho de serviços, embora isso possa ter seus efeitos colaterais.

Vamos supor que você esteja levando um aplicativo maior, ou que simplesmente não tenha sido desenvolvido pensando nos microsserviços, para o Service Fabric. Na verdade, essa transição é comum, e vários clientes (internos e externos) já estiveram nessa situação. Comece levando todo o aplicativo para o ambiente, empacotando-o e colocando-o em execução. Em seguida, comece a dividi-lo em serviços menores diferentes que se comunicam entre si.

Em seguida, há um "Ops...". O "Ops" geralmente se encaixa em uma destas categorias:

1. Algum componente X no aplicativo monolítico tinha uma dependência não documentada no componente Y, e nós acabamos de transformá-los em serviços distintos. Uma vez que agora eles estão sendo executados em diferentes nós no cluster, eles estão desvinculados.
2. Essas coisas se comunicam por meio de (pipes locais nomeados| memória compartilhada | arquivos em disco), mas eu preciso ser capaz de atualizá-las de forma independente para agilizar um pouco as coisas. Vou remover a dependência rígida posteriormente.
3. Tudo está bem, mas acontece que esses dois componentes são muito verborrágicos/dependentes do desempenho. Quando eles são movidos para serviços separados, o desempenho do aplicativo geral despenca ou a latência aumenta. Consequentemente, o aplicativo, de modo geral, não está atendendo às expectativas.

Nesses casos, não queremos perder nosso trabalho de refatoração e não queremos voltar ao monólito, mas precisamos mesmo de algum senso de localidade. Isso se manterá, ou até que possamos recriar os componentes para trabalhar naturalmente como serviços, ou até que consigamos resolver as expectativas de desempenho de alguma outra maneira, se possível.

O que fazer? Bem, você pode tentar ativar a afinidade.

## Como configurar a afinidade
Para configurar a afinidade, você define uma relação de afinidade entre dois serviços diferentes. Você pode pensar na afinidade como se estivesse "apontando" um serviço para outro e dizendo "Este serviço só pode ser executado quando esse serviço estiver em execução". Às vezes, nos referimos à afinidade como relações entre pai e filho (nas quais você aponta o filho para o pai). A afinidade garante que as réplicas ou instâncias de um serviço sejam colocadas nos mesmos nós que as réplicas ou instâncias de outro.

``` csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## Diferentes opções de afinidade
A afinidade é representada por meio de um dos vários esquemas de correlação e tem dois modos diferentes. O modo mais comum de afinidade é o que chamamos de NonAlignedAffinity. No modo NonAlignedAffinity, as réplicas ou instâncias dos diferentes serviços são colocadas nos mesmos nós. Outro modo é o AlignedAffinity. A Afinidade Alinhada é útil apenas com serviços com estado. Configurar dois serviços com estado para afinidade alinhada garante que os primários desses serviços sejam colocados nos mesmos nós um do outro. Ele também faz com que cada par de secundários desses serviços sejam colocados nos mesmos nós. Também é possível (embora menos comum) configurar NonAlignedAffinity para serviços com estado. Para NonAlignedAffinity, as diferentes réplicas dos dois serviços com estado seriam colocados nos mesmos nós, mas não seria feita qualquer tentativa para alinhar seus primários ou secundários.

![Modos de afinidade e seus efeitos][Image1]

### Estado desejado de melhor esforço
Há algumas diferenças entre arquiteturas monolíticas e de afinidade. Muitas delas se devem ao fato de uma relação de afinidade ser considerada a melhor solução. Os serviços em uma relação de afinidade são basicamente entidades diferentes que podem falhar e serem movidas de modo independente. Também há causas pelas quais uma relação de afinidade pode se romper. Por exemplo, nas limitações de capacidade, em que apenas alguns do objetos de serviço na relação de afinidade podem se ajustar em um determinado nó. Nesses casos, embora haja uma relação de afinidade definida, ela não pode ser imposta devido a outras restrições. Se for possível impor todas as outras restrições e afinidades mais tarde, a violação da restrição de afinidade será corrigida automaticamente.

### Cadeias vs. estrelas
Hoje, não somos capazes de modelar cadeias de relações de afinidade. Isso significa que um serviço filho em um relacionamento de afinidade não pode ser pai em outro relacionamento de afinidade. Se você quiser modelar esse tipo de relação, será preciso modelá-la efetivamente como uma estrela, e não como uma cadeia. Para fazer isso, o filho no nível mais baixo teria que ser considerado filho do pai do filho do "meio". Dependendo da organização dos seus serviços, isso pode exigir a criação de um serviço de "espaço reservado" para servir como pai para vários filhos.

![Cadeias versus Estrelas no contexto de relações de afinidade][Image2]

Outra coisa a ser observada sobre as relações atuais de afinidade é que elas são direcionais. Isso significa que a regra de "afinidade" impõe apenas que o filho está onde o pai está. Se, por exemplo, o pai, de repente, fizer failover para outro nó e o Gerenciador de Recursos de Cluster não achar que há algo errado até perceber que o filho não está localizado com um pai; a relação não é imposta imediatamente.

### Suporte ao particionamento
A observação final sobre a afinidade é que as relações de afinidade não têm suporte quando o pai é particionado. Isso é algo ao qual poderemos oferecer suporte eventualmente, mas não é permitido no momento.

## Próximas etapas
* Para saber mais sobre outras opções disponíveis para a configuração de serviços, confira o tópico sobre outras configurações disponíveis do Gerenciador de Recursos de Cluster em [Saiba mais sobre a configuração de serviços](service-fabric-cluster-resource-manager-configure-services.md)
* Muitos motivos pelos quais as pessoas usam a afinidade, como para limitar serviços a um pequeno conjunto de computadores e para tentar agregar a carga de um conjunto de serviços, têm um suporte melhor por meio de Grupos de Aplicativos. Confira [Grupos de Aplicativos](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png

<!---HONumber=AcomDC_0824_2016-->