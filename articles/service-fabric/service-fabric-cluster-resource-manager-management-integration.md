---
title: "Resource Manager de Cluster do Service Fabric – integração de gerenciamento | Microsoft Docs"
description: "Uma visão geral dos pontos de integração entre o Gerenciador de Recursos de Cluster e o Gerenciamento do Service Fabric."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 956cd0b8-b6e3-4436-a224-8766320e8cd7
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/19/2016
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f13e38b4c01bc718f6f25f92461e332e1aa30136


---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Integração do Gerenciador de Recursos de Cluster com o gerenciamento de cluster do Service Fabric
O Service Fabric Cluster Resource Manager não é o principal componente do Service Fabric que lida com as operações de gerenciamento (como atualizações de aplicativo), mas está envolvido. A primeira maneira que o Cluster Resource Manager ajuda no gerenciamento está rastreando o estado desejado do cluster e os serviços dentro de uma perspectiva de obtenção de recursos e equilíbrio e enviar relatórios de integridade quando não é possível colocar o cluster na configuração desejada (e um exemplo disso é se há capacidade suficiente por exemplo, ou um conflito regras sobre em que um serviço deve ser colocado). Outra parte de integração tem a ver com como as atualizações funcionam: durante as atualizações do Resource Manager de Cluster altera seu comportamento. Falaremos sobre ambos mais abaixo.

## <a name="health-integration"></a>Integração da integridade
O Cluster Resource Manager constantemente rastreará as regras definidas para os serviços, bem como a capacidade disponível em nós e no cluster e emitirá erros e avisos de integridade se ele não puder atender essas regras ou se não houver capacidade suficiente. Por exemplo, se um nó está acima da capacidade e o Cluster Resource Manager não pode corrigir a situação, ele emite um aviso de integridade indicando qual nó está acima da capacidade e para quais métricas.

Outro exemplo de quando o Gerenciador de Recursos emitirá avisos de integridade é se você definiu uma restrição de posicionamento (como "NodeColor==Blue") e o Gerenciador de Recursos detecta uma violação de restrição. Fazemos isso tanto para restrições personalizadas como para restrições padrão (como restrições de domínio de atualização e de falha) impostas automaticamente.

Eis um exemplo de tal relatório de integridade. Nesse caso, o relatório de integridade é para uma das partições do serviço do sistema porque as réplicas dessa partição foram incluídas temporariamente em poucos Domínios de Atualização, o que pode acontecer em caso de uma sucessão de falhas:

```posh
PS C:\Users\User > Get-WindowsFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


PartitionId           : 00000000-0000-0000-0000-000000000001
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB', Property='ReplicaConstraintViolation_UpgradeDomain', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130766528804733380
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577821
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528854889931
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577822
                        AggregatedHealthState : Ok

                        ReplicaId             : 130837073190680024
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.PLB
                        Property              : ReplicaConstraintViolation_UpgradeDomain
                        HealthState           : Warning
                        SequenceNumber        : 130837100116930204
                        SentAt                : 8/10/2015 7:53:31 PM
                        ReceivedAt            : 8/10/2015 7:53:33 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer has detected a Constraint Violation for this Replica: fabric:/System/FailoverManagerService Secondary Partition 00000000-0000-0000-0000-000000000001 is
                        violating the Constraint: UpgradeDomain Details: Node -- 3d1a4a68b2592f55125328cd0f8ed477  Policy -- Packing
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM
```

Veja o que a mensagem de integridade está nos dizendo:

1. Todas as réplicas estão íntegras (essa é a prioridade do Service Fabric)
2. No momento, a restrição de distribuição do Domínio de Atualização está sendo violada (o que significa que um Domínio de Atualização específico tem mais réplicas para essa partição do que deveria)
3. O nó que contém a réplica que está causando a violação (o nó com a ID: 3d1a4a68b2592f55125328cd0f8ed477)
4. Quando tudo aconteceu (10/8/2015 19:13:02)

Esses são dados excelentes para um alerta na produção que indique que houve um problema e que é melhor você averiguar. Nesse caso, por exemplo, queremos ver se conseguimos descobrir por que o Resource Manager não viu escolha além de empacotar as réplicas no Domínio de Atualização. Poderia ser porque todos os nós nos outros Domínios de Atualização estavam inativos e não havia domínios de reserva suficientes ou, se havia domínios, outro motivo que tenha invalidado os nós nesses outros Domínios de Atualização (por exemplo, uma política de colocação no serviço ou capacidade insuficiente).

No entanto, digamos que você queira criar um serviço, ou que o Gerenciador de Recursos esteja tentando localizar um lugar para posicionar alguns serviços, mas não parece haver soluções que funcionem. Isso pode acontecer por vários motivos, mas normalmente é devido a uma das duas condições abaixo:

1. Alguma condição temporária impossibilitou o posicionamento correto dessa instância de serviço ou dessa réplica
2. Os requisitos do serviço estão configurados incorretamente, de maneira que seus requisitos são considerados insatisfatórios.

Em cada uma dessas condições, você verá um relatório de integridade do Cluster Resource Manager que fornece informações para ajudá-lo a determinar o que está acontecendo e por que o serviço não pode ser posicionado. Chamamos esse processo de "sequência de eliminação de restrição". Durante o processo, o sistema percorre as restrições configuradas que afetam o serviço e os registros que eliminam. Dessa forma, quando os serviços não podem ser posicionados, você pode ver quais nós foram eliminados e por quê.

## <a name="constraint-types"></a>Tipos de restrição
Vamos falar sobre cada uma das restrições diferentes que você pode ver nesses relatórios de integridade e o que está sendo verificado. Observe que, na maioria das vezes, você não verá algumas dessas restrições eliminarem nós, já que as restrições estão, por padrão, no nível de atenuação ou de otimização (saiba mais sobre as prioridades de restrição mais adiante neste artigo). No entanto, você pode ver mensagens de integridade relacionadas a essas restrições se elas estiverem configuradas como restrições inflexíveis, ou em casos raros nos quais elas causam a eliminação dos nós, e, portanto, apresentamos aqui para integridade:

* ReplicaExclusionStatic e ReplicaExclusionDynamic – Essa é uma restrição interna que indica que durante a pesquisa encontramos uma situação na qual duas réplicas com estado ou instâncias sem estado da mesma partição precisam ser colocadas no mesmo nó (o que não é permitido). ReplicaExclusionStatic e ReplicaExclusionDynamic são quase exatamente a mesma regra. A restrição ReplicaExclusionDynamic diz "não foi possível colocar essa réplica aqui porque a única solução proposta já posicionou uma réplica neste lugar". Isso é diferente da exclusão ReplicaExclusionStatic, que indica um conflito real, e não um proposto. Já existe uma réplica no nó. Isso parece confuso? Sim. Isso importa muito? Não. Basta saber que se você estiver vendo uma sequência de eliminação de restrição que contenha a restrição ReplicaExclusionStatic ou ReplicaExclusionDynamic, o Resource Manager de Cluster considerará que não existem nós suficientes para posicionar todas as réplicas. As restrições adicionais normalmente podem indicar, acima de tudo, por que estamos ficando com poucos nós
* PlacementConstraint: se você vir essa mensagem, significa que eliminamos alguns nós porque eles não correspondiam a restrições de posicionamento do serviço. Rastreamos as restrições de posicionamento configuradas atualmente como parte dessa mensagem. Normalmente, isso é comum se houver qualquer restrição de posicionamento. No entanto, se houver um bug na restrição de posicionamento que esteja causando a eliminação de muitos nós, é nesse local que você verá esse resultado.
* NodeCapacity: se você vir essa restrição, isso significa que não posicionamos as réplicas nos nós indicados porque isso faria com que o nó ficasse acima da capacidade.
* Affinity: essa restrição indica que não posicionamos a réplica em nós afetados, pois isso causaria uma violação da restrição de afinidade.
* FaultDomain e UpgradeDomain: essa restrição elimina nós se o posicionamento da réplica nos nós indicados causar empacotamento em um domínio de atualização ou de falha específico. Há vários exemplos que discutem essa restrição no tópico sobre [restrições de domínio de falha e de atualização e o comportamento resultante](service-fabric-cluster-resource-manager-cluster-description.md)
* PreferredLocation: normalmente, você não verá essa restrição removendo nós da solução, já que é uma otimização somente por padrão. Além disso, a restrição de local preferida geralmente só estará presente durante atualizações (quando ela é usada para mover réplicas de volta a onde estavam quando a atualização foi iniciado). No entanto, é possível.

### <a name="constraint-priorities"></a>Prioridades de restrição
Em todas essas restrições, você pode ter pensado "Ei, acho que restrições de posicionamento são a coisa mais importante no meu sistema. Estou disposto a violar outras restrições, até mesmo coisas como afinidade e capacidade, se isso garantir que as restrições de posicionamento não sejam violadas".

Acontece que também podemos faz isso! As restrições podem ser configuradas com alguns níveis diferentes de imposição, mas elas resumem a "rígida" (0), "flexível" (1), "otimização" (2) e "desativado" (-1). A maioria das restrições foram definidas como rígida por padrão (como, por exemplo, a maioria das pessoas normalmente não pensam sobre a capacidade como algo que eles estão dispostos a relaxar), e quase todos estão rígidos ou flexíveis. No entanto, em situações avançadas, isso podem ser alterado. Por exemplo, se você quisesse garantir que afinidade sempre seria violada para solucionar problemas de capacidade do nó, poderia priorizar a restrição de afinidade como "Flexível" (1) e deixar a restrição de capacidade definida como "Rígida" (0). As diferentes prioridades de restrição também são o porquê de vermos alguns avisos de violação de restrição com mais frequência do que outros, pois existem certas restrições que deixamos relaxar (violar) temporariamente. Observe que esses níveis realmente não significam que uma determinada restrição será ou não sempre violada, só que haverá uma ordem na qual ela são impostas preferencialmente de modo que podemos fazer o balanceamento correto se for impossível atender a todas eles.

A configuração e os valores de prioridade padrão para diferentes restrições estão listados abaixo:

ClusterManifest.xml

```xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PlacementConstraintPriority" Value="0" />
            <Parameter Name="CapacityConstraintPriority" Value="0" />
            <Parameter Name="AffinityConstraintPriority" Value="0" />
            <Parameter Name="FaultDomainConstraintPriority" Value="0" />
            <Parameter Name="UpgradeDomainConstraintPriority" Value="1" />
            <Parameter Name="PreferredLocationConstraintPriority" Value="2" />
        </Section>
```

Você notará aqui que há restrições definidas para domínios de Atualização e Falha e também que, a restrição de Domínio de Atualização é Flexível. Também há essa restrição "PreferredLocation" estranha com uma prioridade. O que é tudo isso?

Primeiro, modelamos o desejo de manter os serviços distribuídos entre domínios de falha e atualização como uma restrição dentro do mecanismo do Resource Manager. Historicamente temos observado vezes em que precisávamos ser realmente rígidos sobre o posicionamento em relação a domínios de falha e de atualização, e também alguns casos em que houve algum problema que precisamos apenas ignorar completamente (embora brevemente!), por isso em geral estamos felizes com essa escolha de design e a flexibilidade da infraestrutura de restrição. Na maioria das vezes, a restrição de Domínio de atualização funciona como uma restrição flexível, ou seja, se o Resource Manager temporariamente precisar conter duas réplicas em um domínio de falha ou de atualização para lidar com uma atualização em andamento, uma série de falhas simultâneas ou outras violações de restrição (das restrições de disco rígidas), estará ok. Normalmente isso não acontece, a menos que existam muitas falhas ou outras variações no sistema impedindo a colocação correta e, se o ambiente estiver configurado corretamente, o estado estável sempre terá o domínio de atualização totalmente respeitado.

A restrição de PreferredLocation é um pouco diferente sendo, portanto, a única restrição definida como "Otimização". Usamos essa restrição durante atualizações em trânsito para tentar preferir colocar novamente os serviços que encontramos antes da atualização. Há inúmeras razões por que isso pode não funcionar na prática, mas é uma bela otimização, então aqui está. Falaremos mais sobre isso quando falarmos sobre como o Resource Manager de Cluster ajuda nas atualizações.

## <a name="upgrades"></a>Atualizações
O Cluster Resource Manager também ajuda durante as atualizações de aplicativos e de cluster para garantir que a atualização seja perfeita e que as regras e o desempenho do cluster não fiquem comprometidos.

### <a name="keep-enforcing-the-rules"></a>Manter a imposição das regras
O principal a ser observado são se as regras, restrições rígidas sobre coisas como restrições de posicionamento, ainda estão sendo impostas durante as atualizações. Isso parece ser óbvio, mas preferimos ser explícitos em relação a isso. O lado positivo disso é que você pode garantir que, caso não queira que determinadas cargas de trabalho sejam executadas em determinados nós, cujas regras serão impostas mesmo durante a atualização automaticamente. Se seu ambiente for altamente restrito, isso poderá fazer com que as atualizações demorem muito tempo, pois há poucas opções para um serviço (ou o nó no qual ele se encontra) se ele tiver que ser desativado para uma atualização.

### <a name="smart-replacements"></a>Substituições inteligentes
Quando uma atualização é iniciada, o Gerenciador de Recursos tira um instantâneo da disposição do cluster atual e tenta retornar as coisas a esse estado quando a atualização é concluída. O raciocínio por trás disso é simples: primeiro, ele garante que haja apenas duas transições para cada réplica ou instância de serviço como parte da atualização (a movimentação para fora do nó afetado e a movimentação de volta). Em segundo lugar, ele garante que a atualização em si não tenha muito impacto no layout do cluster. Se o cluster foi organizado bem antes da atualização, ele será organizado bem depois dela, ou pelo menos não inferior.

### <a name="reduced-churn"></a>Variação reduzida
Outra coisa que acontece durante as atualizações é que o Resource Manager de Cluster desativa o balanceamento da entidade que está sendo atualizada. Então, se você tiver duas instâncias diferentes do aplicativo e iniciar uma atualização em uma delas, o balanceamento é pausado naquela instância do aplicativo, mas não em outra. Impedir balanceamento reativo impede reações desnecessárias para a atualização em si ("Ah não! Um nó vazio! Melhor preenchê-lo com vários tipos de coisas!") e, consequentemente, impede muitos movimentos extras para serviços no cluster que terão que ser desfeitos quando os serviços forem movidos de volta para os nós após a conclusão da atualização. Se a atualização em questão for uma atualização de Cluster, todo o cluster terá o balanceamento pausado durante a atualização (as verificações de restrição, que garantem que as regras são impostas, ficam ativas, apenas o novo balanceamento proativo é desativado).

### <a name="relaxed-rules"></a>Regras reduzidas
Uma das questões que aparecem durante as atualizações é querer que a atualização seja concluída mesmo quando o cluster está, no geral, restrito ou cheio. Durante as atualizações é ainda mais importante poder gerenciar a capacidade do cluster, pois há geralmente uma redução entre cinco e 20% da capacidade à medida que a atualização percorre o cluster, e essa carga de trabalho geralmente precisa ir para algum lugar. É aí que entra a noção de [capacidades de buffer](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity) , pois embora a capacidade de buffer seja respeitada durante o funcionamento normal (deixando certa sobrecarga), o Cluster Resource Manager encherá até a capacidade total (preenchendo o buffer) durante as atualizações.

## <a name="next-steps"></a>Próximas etapas
* Comece do princípio e [veja uma introdução ao Resource Manager de Cluster do Service Fabric](service-fabric-cluster-resource-manager-introduction.md)




<!--HONumber=Nov16_HO3-->


