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
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: b2428f93680c12c76000fa8eb1a7138f72a8efe7
ms.openlocfilehash: 9d67f089f4aba03e846a8fe020a91b6b1ac6ea48


---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Integração do Gerenciador de Recursos de Cluster com o gerenciamento de cluster do Service Fabric
O Service Fabric Cluster Resource Manager não é o principal componente do Service Fabric que lida com as operações de gerenciamento (como atualizações de aplicativo), mas está envolvido. A primeira maneira que o Cluster Resource Manager pode ajudar no gerenciamento é rastreando o estado desejado do cluster e dos serviços dentro dele. O Cluster Resource Manager envia relatórios de integridade quando não consegue deixar o cluster na configuração desejada. Um exemplo seria em caso de capacidade insuficiente ou regras conflitantes sobre onde um serviço deve ser colocado. Outra parte de integração tem a ver com a forma como as atualizações funcionam. O Cluster Resource Manager altera ligeiramente seu comportamento durante as atualizações. Falaremos sobre ambos esses pontos de integração abaixo.

## <a name="health-integration"></a>Integração da integridade
O Cluster Resource Manager rastreia constantemente as regras que você definiu para seus serviços e a capacidade disponível em nós e no cluster. Se ele não puder atender a essas regras ou se não houver capacidade insuficiente, erros e avisos de integridade serão emitidos. Por exemplo, se um nó estiver acima da capacidade, o Cluster Resource Manager tentará corrigir a situação movendo serviços. Se não puder corrigir a situação, ele emitirá um aviso de integridade indicando qual nó está acima da capacidade e para quais métricas.

Outro exemplo de avisos de integridade do Resource Manager são violações de restrições de posicionamento. Por exemplo, se você tiver definido uma restrição de posicionamento (como `“NodeColor == Blue”`) e o Resource Manager detectar uma violação desta restrição, ele emitirá um aviso de integridade. Isso é válido tanto para restrições personalizadas e padrão (como restrições dp domínio de falha e de atualização).

Eis um exemplo de tal relatório de integridade. Nesse caso, o relatório de integridade é voltado para uma das partições do serviço do sistema. A mensagem de integridade indica quais réplicas dessa partição estão temporariamente empacotadas em Domínios de Atualização insuficientes.

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
4. Quando o relatório ocorreu (10/08/2015 19:13:02)

Informações sobre como isso gera alertas que são disparados na produção, para que você saiba que algo deu errado. Nesse caso, queremos ver se conseguimos descobrir por que o Resource Manager precisou empacotar as réplicas no Domínio de Atualização. Isso pode ser devido aos nós em outros Domínios de Atualização estarem inativos, por exemplo.

Digamos que você queira criar um serviço ou que o Resource Manager esteja tentando encontrar um lugar para alocar alguns serviços, mas nenhuma solução parece funcionar. Isso pode acontecer por vários motivos, mas normalmente é devido a uma das duas condições abaixo:

1. Alguma condição temporária impossibilitou o posicionamento correto dessa instância de serviço ou dessa réplica
2. Os requisitos do serviço estão configurados incorretamente, de maneira que seus requisitos são considerados insatisfatórios.

Nessas condições, você verá um relatório de integridade do Cluster Resource Manager que fornece informações para ajudá-lo a determinar o que está acontecendo e por que o serviço não pode ser alocado. Chamamos esse processo de "sequência de eliminação de restrição". Durante o processo, o sistema percorre as restrições configuradas que afetam o serviço e os registros que eliminam. Dessa forma, quando os serviços não podem ser posicionados, você pode ver quais nós foram eliminados e por quê.

## <a name="constraint-types"></a>Tipos de restrição
Vamos falar sobre cada uma das diferentes restrições que você pode ver nesses relatórios de integridade. Na maioria das vezes essas restrições não eliminam nós, visto que eles estão no nível flexível ou de otimização por padrão. No entanto, você poderá ver mensagens de integridade relacionadas a essas restrições se elas estiverem configuradas como restrições inflexíveis ou em casos raros nos quais elas causam a eliminação dos nós.

* **ReplicaExclusionStatic** e **ReplicaExclusionDynamic**: essa restrição indica que duas réplicas com estado ou instâncias sem monitoração de estado da mesma partição precisam ser colocadas no mesmo nó (o que não é permitido). ReplicaExclusionStatic e ReplicaExclusionDynamic são regras quase idênticas. A restrição ReplicaExclusionDynamic diz “não foi possível colocar essa réplica aqui porque a única solução proposta já alocou uma réplica para este lugar”. Isso é diferente da restrição ReplicaExclusionStatic, que indica um conflito real e não um proposto. Nesse caso, já existe uma réplica no nó. Isso parece confuso? Sim. Isso importa muito? Não. Se você estiver vendo uma sequência de eliminação de restrição contendo a restrição ReplicaExclusionStatic ou ReplicaExclusionDynamic, o Cluster Resource Manager considerará que não existem nós válidos suficientes. As restrições adicionais normalmente podem indicar por que estamos ficando com nós insuficientes.
* **PlacementConstraint**: se você encontrar essa mensagem, significa que eliminamos alguns nós porque eles não correspondiam a restrições de posicionamento do serviço. Rastreamos as restrições de posicionamento configuradas atualmente como parte dessa mensagem. Isso é normal se você tiver restrições de posicionamento em vigor. No entanto, se houver um bug na restrição de posicionamento que faz com que muitos de nós sejam eliminados, isso será notado aqui.
* **NodeCapacity**: essa restrição significa que o Cluster Resource Manager não conseguiu posicionar as réplicas nos nós indicados porque isso faria com que o nó ficasse acima da capacidade.
* **Affinity**: essa restrição indica que não é possível colocar a réplica nos nós afetados, pois isso causaria uma violação da restrição de afinidade. Veja mais informações sobre afinidade [neste artigo](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* **FaultDomain** e **UpgradeDomain**: essa restrição elimina nós se o posicionamento da réplica nos nós indicados causar empacotamento em um domínio de atualização ou de falha específico. Há vários exemplos que discutem essa restrição no tópico sobre [restrições de domínio de falha e de atualização e o comportamento resultante](service-fabric-cluster-resource-manager-cluster-description.md)
* **PreferredLocation**: normalmente, você não verá essa restrição removendo nós da solução, visto que isto é definido no nível de otimização por padrão. Além disso, a restrição de local preferencial geralmente está presente durante as atualizações. Durante a atualização, ela é usada para mover réplicas para onde estavam quando a atualização foi iniciada.

### <a name="constraint-priorities"></a>Prioridades de restrição
Em todas essas restrições, você pode ter pensado “Ei, acho que restrições de posicionamento são a coisa mais importante no meu sistema. Estou disposto a violar outras restrições, até mesmo coisas como afinidade e capacidade, se isso garantir que as restrições de posicionamento não sejam violadas".

Acontece que também podemos faz isso! As restrições podem ser configuradas com alguns níveis diferentes de imposição, mas elas resumem a "rígida" (0), "flexível" (1), "otimização" (2) e "desativado" (-1). A maioria das restrições foram definidas como inflexíveis por padrão. Por exemplo, a maioria das pessoas normalmente não pensa em capacidade como algo que pode ser mais relaxado, sendo quase sempre flexível ou inflexível.

As diferentes prioridades de restrição também são o porquê de alguns avisos de violação de restrição aparecerem com mais frequência do que outros, pois existem certas restrições que deixamos relaxar (violar) temporariamente. Esses níveis não significam que uma determinada restrição será violada, apenas que há uma ordem na qual elas são impostas preferencialmente. Isso permite que o Cluster Resource Manager faça as ponderações corretas caso seja impossível atender a todas as restrições.

Em situações avançadas, é possível alterar as prioridades de restrição. Por exemplo, digamos que você queira garantir que a afinidade seja violada para solucionar problemas de capacidade do nó. Para fazer isso, você poderia definir a prioridade da restrição de afinidade como “flexível” (1) e deixar a restrição de capacidade definida como “inflexível” (0).

Os valores de prioridade padrão para as diferentes restrições são especificados na configuração:

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

via ClusterConfig.json para implantações Autônomas ou Template.json para clusters hospedados pelo Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PlacementConstraintPriority",
          "value": "0"
      },
      {
          "name": "CapacityConstraintPriority",
          "value": "0"
      },
      {
          "name": "AffinityConstraintPriority",
          "value": "0"
      },
      {
          "name": "FaultDomainConstraintPriority",
          "value": "0"
      },
      {
          "name": "UpgradeDomainConstraintPriority",
          "value": "1"
      },
      {
          "name": "PreferredLocationConstraintPriority",
          "value": "2"
      }
    ]
  }
]
```

## <a name="fault-domain-and-upgrade-domain-constraints"></a>Restrições de domínio de falha e de atualização
O Cluster Resource Manager modela o desejo de manter os serviços distribuídos entre domínios de falha e de atualização como uma restrição dentro do mecanismo do Resource Manager. Para obter mais informações sobre como eles são usados, consulte o artigo na [configuração de cluster](service-fabric-cluster-resource-manager-cluster-description.md).

Houve vezes em que precisados ser rígidos quanto aos domínios de Falha e de Atualização para evitar que algo ruim aconteça. Também houve casos em que precisamos para ignorá-los completamente (embora brevemente!). Em geral, a flexibilidade da infraestrutura de prioridade da restrição funcionou muito bem, mas ela não é necessário com tanta frequência. Na maioria das vezes, tudo fica em suas prioridades padrão. Domínios de atualização permanecem sendo uma restrição flexível. O Cluster Resource Manager pode precisar empacotar algumas réplicas em um domínio de atualização para lidar com uma atualização, falhas ou violações de restrição. Isso normalmente ocorre somente quando há várias falhas ou outra variação no sistema que impede o posicionamento correto. Se o ambiente estiver configurado corretamente, todas as restrições, incluindo as restrições de falha e de atualização, serão totalmente respeitadas, mesmo durante as atualizações. O mais importante é que o Cluster Resource Manager esteja observando suas restrições e relatando imediatamente quando ele detectar violações.

## <a name="the-preferred-location-constraint"></a>A restrição do local preferencial
A restrição de PreferredLocation é um pouco diferente sendo, portanto, a única restrição definida como "Otimização". Usamos essa restrição durante atualizações em trânsito para optar por colocar os serviços novamente onde se encontravam antes da atualização. Há inúmeras razões por que isso pode não funcionar na prática, mas é uma bela otimização.

## <a name="upgrades"></a>Atualizações
O Cluster Resource Manager também ajuda durante atualizações de aplicativos e clusters, quando então ele tem dois trabalhos:

* verificar se as regras e o desempenho do cluster não foram comprometidos
* tentar ajudar a atualização a ocorrer sem problemas

### <a name="keep-enforcing-the-rules"></a>Manter a imposição das regras
O principal a ser observado são se as regras, restrições rígidas sobre coisas como restrições de posicionamento, ainda estão sendo impostas durante as atualizações. Restrições de posicionamento garantem que suas cargas de trabalho serão executadas apenas onde elas são permitidas, mesmo durante atualizações. Se o seu ambiente estiver altamente restrito, as atualizações poderão levar muito tempo. Isso ocorre porque pode haver opções insuficientes para onde um serviço pode ir se ele (ou o nó em que ele está) precisar ser desativado para uma atualização.

### <a name="smart-replacements"></a>Substituições inteligentes
Quando uma atualização é iniciada, o Resource Manager tira um instantâneo da disposição atual do cluster. À medida que cada domínio de atualização é concluído, ele tenta retornar as coisas para a disposição original. Dessa forma, há no máximo duas transições durante a atualização (a movimentação para fora do nó afetado e de volta para ele). Retornar o cluster a como ele estava antes da atualização também garante que a atualização não afete o layout do cluster. Se o cluster estava bem organizado antes da atualização, permanecerá assim depois ou pelo menos não estará pior.

### <a name="reduced-churn"></a>Variação reduzida
Outra coisa que acontece durante as atualizações é que o Resource Manager de Cluster desativa o balanceamento da entidade que está sendo atualizada. Isso significa que se você tiver duas instâncias diferentes de aplicativo diferentes e atualizar uma delas, o balanceamento estará em pausa naquela instância do aplicativo, mas não em outra. Impedir o balanceamento impede reações desnecessárias à atualização em si, como a movimentação dos serviços para nós que foram esvaziados para a atualização. Se a atualização em questão for uma atualização de Cluster, então o cluster inteiro não poderá ser balanceado durante a atualização. Verificações de restrição, que garantem que as regras serão impostas, permanecem ativas e apenas o rebalanceamento é desabilitado.

### <a name="relaxed-rules"></a>Regras reduzidas
Em geral, você deseja que a atualização seja concluída mesmo se o cluster estiver restrito ou cheio de maneira geral. Durante as atualizações, ser capaz de gerenciar a capacidade do cluster é ainda mais importante do que o normal. Isso porque geralmente há perda de capacidade de 5% e 20% no momento em que a atualização passa pelo cluster. Esse trabalho geralmente precisa ir para algum lugar. É aqui que a noção de [capacidades em buffer](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity) realmente entra em cena. Embora a capacidade em buffer seja respeitada durante o funcionamento normal (permitindo certa sobrecarga), o Cluster Resource Manager encherá até a capacidade total (preenchendo o buffer) durante as atualizações.

## <a name="next-steps"></a>Próximas etapas
* Comece do princípio e [veja uma introdução ao Resource Manager de Cluster do Service Fabric](service-fabric-cluster-resource-manager-introduction.md)



<!--HONumber=Feb17_HO2-->


