---
title: "Resource Manager de Cluster do Service Fabric – afinidade | Microsoft Docs"
description: "Visão geral da configuração de afinidade para os Serviços do Service Fabric"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 678073e1-d08d-46c4-a811-826e70aba6c4
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 16863217eddf0a4bbd85c52f8481c03e50dd9108


---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Configurando e usando a afinidade de serviço no Service Fabric
A afinidade é um controle fornecido principalmente para ajudar a facilitar a transição de aplicativos monolíticos maiores para a nuvem e o mundo de microsserviços. Isso posto, ela também pode ser usada em certos casos como uma otimização legítima para melhorar o desempenho de serviços, embora fazer isso possa ter efeitos colaterais.

Vamos supor que você esteja levando um aplicativo maior, ou que simplesmente não tenha sido desenvolvido pensando nos microsserviços, para o Service Fabric. Esse tipo de transição é comum. Comece levantando o aplicativo inteiro no ambiente, empacotando-o e certificando-se de que ele esteja sendo executado perfeitamente. Em seguida, comece a dividi-lo em serviços menores diferentes que se comunicam entre si.

Em seguida, há um "Ops...". O "Ops" geralmente se encaixa em uma destas categorias:

1. Algum componente X no aplicativo monolítico tinha uma dependência não documentada no componente Y, e você acabou de transformar esses componentes em serviços separados. Uma vez que agora esses serviços estão sendo executados em diferentes nós do cluster, eles estão desvinculados.
2. Essas coisas são comunicadas por (pipes nomeados locais | memória compartilhada | arquivos no disco), mas eles realmente precisam poder gravar em um recurso compartilhado por motivos de desempenho neste momento. Essa forte dependência é removida depois.
3. Tudo está bem, mas acontece que esses dois componentes são, na prática, verborrágicos/dependentes do desempenho. Quando eles são movidos para serviços separados, o desempenho do aplicativo geral despenca ou a latência aumenta. Consequentemente, o aplicativo, de modo geral, não está atendendo às expectativas.

Nesses casos, não queremos perder nosso trabalho de refatoração e não queremos voltar ao monólito. No entanto, até que possamos recriar os componentes para trabalhar naturalmente como serviços (ou até que possamos resolver as expectativas de desempenho de algum modo), vamos precisar de algum senso de localidade.

O que fazer? Bem, você poderia tentar ativar a afinidade.

## <a name="how-to-configure-affinity"></a>Como configurar a afinidade
Para configurar a afinidade, você define uma relação de afinidade entre dois serviços diferentes. Você pode pensar na afinidade como se estivesse "apontando" um serviço para outro e dizendo "Este serviço só pode ser executado quando esse serviço estiver em execução". Às vezes, nos referimos à afinidade como relações entre pai e filho (nas quais você aponta o filho para o pai). A afinidade garante que as réplicas ou instâncias de um serviço sejam colocadas nos mesmos nós que as réplicas ou instâncias de outro.

``` csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## <a name="different-affinity-options"></a>Diferentes opções de afinidade
A afinidade é representada por meio de um dos vários esquemas de correlação e tem dois modos diferentes. O modo mais comum de afinidade é o que chamamos de NonAlignedAffinity. No modo NonAlignedAffinity, as réplicas ou instâncias dos diferentes serviços são colocadas nos mesmos nós. Outro modo é o AlignedAffinity. A Afinidade Alinhada é útil apenas com serviços com estado. Configurar dois serviços com estado para afinidade alinhada garante que os primários desses serviços sejam colocados nos mesmos nós um do outro. Ele também faz com que cada par de secundários desses serviços sejam colocados nos mesmos nós. Também é possível (embora menos comum) configurar NonAlignedAffinity para serviços com estado. Para NonAlignedAffinity, as diferentes réplicas dos dois serviços com estado seriam colocados nos mesmos nós, mas não seria feita qualquer tentativa para alinhar seus primários ou secundários.

<center>
![Modos de afinidade e seus efeitos][Image1]
</center>

### <a name="best-effort-desired-state"></a>Estado desejado de melhor esforço
Há algumas diferenças entre arquiteturas monolíticas e de afinidade. Muitas delas se devem ao fato de uma relação de afinidade ser considerada a melhor solução. Os serviços em uma relação de afinidade são basicamente entidades diferentes que podem falhar e serem movidas de modo independente. Também há causas pelas quais uma relação de afinidade pode se romper. Por exemplo, nas limitações de capacidade, em que apenas alguns do objetos de serviço na relação de afinidade podem se ajustar em um determinado nó. Nesses casos, embora haja uma relação de afinidade definida, ela não pode ser imposta devido a outras restrições. Se for possível fazer isso, a violação será corrigida automaticamente mais tarde.

### <a name="chains-vs-stars"></a>Cadeias vs. estrelas
Atualmente, o Gerenciador de Recursos de Cluster não é capaz de modelar cadeias de relações de afinidade. Isso significa que um serviço filho em um relacionamento de afinidade não pode ser pai em outro relacionamento de afinidade. Se você quiser modelar esse tipo de relação, será preciso modelá-la efetivamente como uma estrela, e não como uma cadeia. Para passar de uma cadeia para uma estrela, o filho no nível mais baixo seria como pai para o pai do primeiro filho. Dependendo da organização dos seus serviços, você pode ter que fazer isso várias vezes. Se não houver serviço pai natural, talvez você tenha que criar um que sirva como um espaço reservado. Dependendo dos seus requisitos, também pode ser conveniente examinar os [Grupos de Aplicativos](service-fabric-cluster-resource-manager-application-groups.md).

<center>
![Cadeias vs. estrelas no contexto de relações de afinidade][Image2]
</center>

Outra coisa a ser observada sobre as relações atuais de afinidade é que elas são direcionais. Isso significa que a regra de "afinidade" impõe apenas que o filho esteja onde o pai está. Por exemplo, digamos que o pai, de repente, faça failover para outro nó. Quando isso acontece, o Gerenciador de Recursos de Cluster pensa que tudo está indo bem até perceber que o filho não está localizado com um pai. A relação de afinidade não pode ser perfeita ou imposta instantaneamente, uma vez que este são diferentes serviços com diferentes ciclos de vida.

### <a name="partitioning-support"></a>Suporte ao particionamento
A observação final sobre a afinidade é que as relações de afinidade não têm suporte quando o pai é particionado. Isso é algo ao qual poderemos oferecer suporte eventualmente, mas não é permitido no momento.

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre outras opções disponíveis para a configuração de serviços, confira o tópico sobre outras configurações disponíveis do Gerenciador de Recursos de Cluster em [Saiba mais sobre a configuração de serviços](service-fabric-cluster-resource-manager-configure-services.md)
* Para usos como limitar serviços a um pequeno conjunto de computadores e tentar agregar a carga de um conjunto de serviços, use os [Grupos de Aplicativos](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png



<!--HONumber=Jan17_HO1-->


