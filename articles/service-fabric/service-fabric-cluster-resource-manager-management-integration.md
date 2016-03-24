<properties
   pageTitle="Gerenciador de Recursos de Cluster do Service Fabric - integração de gerenciamento"
   description="Uma visão geral dos pontos de integração entre o Gerenciador de Recursos de Cluster e o Gerenciamento do Service Fabric."
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
   ms.date="03/03/2016"
   ms.author="masnider"/>


# Integração de gerenciamento
O Gerenciador de Recursos não é o principal componente do Service Fabric que lida com as operações de gerenciamento (como atualizações de aplicativo), mas está envolvido. A primeira forma que o Gerenciador de Recursos tem para ajudar com o gerenciamento é rastreando o estado desejado do cluster e os serviços nele de uma perspectiva de balanceamento e obtenção de recursos, alterando com o subsistema de integridade do Service Fabric quando houver erros. Outra forma de integração tem a ver com o funcionamento das atualizações. Especificamente durante as atualizações, algumas coisas relativas ao funcionamento do Gerenciador de Recursos mudam e alguns comportamentos específicos são invocados. Falaremos sobre ambos mais abaixo.

## Integração de integridade
O Gerenciador de Recursos rastreia constantemente as regras definidas para os serviços e emite avisos de erro de integridade se eles não podem atender a essas regras. Por exemplo, se um nó está acima da capacidade e o Gerenciador de Recursos não pode corrigir a situação, ele emite um aviso de integridade indicando qual nó está acima da capacidade e para quais métricas.

Outro exemplo de quando o Gerenciador de Recursos emitirá avisos de integridade é se você definiu uma restrição de posicionamento (como "NodeColor==Blue") e o Gerenciador de Recursos detecta uma violação de restrição. Fazemos isso tanto para restrições personalizadas como para restrições padrão (como distribuição de domínio de atualização e de falha) que o Gerenciador de Recursos impõe a você. Eis um exemplo de tal relatório de integridade. Nesse caso, o relatório de integridade é para uma das partições do serviço do sistema porque as réplicas dessa partição foram incluídas temporariamente em poucos domínios de falha, o que pode acontecer devido a uma sucessão de falhas:

```posh
PS C:\Users\User > Get-WindowsFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


PartitionId           : 00000000-0000-0000-0000-000000000001
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB', Property='ReplicaConstraintViolation_FaultDomain', HealthState='Warning', ConsiderWarningAsError=false.

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
                        Property              : ReplicaConstraintViolation_FaultDomain
                        HealthState           : Warning
                        SequenceNumber        : 130837100116930204
                        SentAt                : 8/10/2015 7:53:31 PM
                        ReceivedAt            : 8/10/2015 7:53:33 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer has detected a Constraint Violation for this Replica: fabric:/System/FailoverManagerService Secondary Partition 00000000-0000-0000-0000-000000000001 is
                        violating the Constraint: FaultDomain Details: Node -- 3d1a4a68b2592f55125328cd0f8ed477  Policy -- Packing
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM
```

Veja o que a mensagem de integridade está nos dizendo:

1.	Todas as réplicas estão íntegras (essa é a prioridade do Service Fabric)
2.	No momento, a restrição de distribuição do domínio de falha está sendo violada (o que significa que um domínio de falha específico tem mais réplicas para essa partição do que deveria)
3.	O nó que contém a réplica que está causando a violação (o nó com a ID: 3d1a4a68b2592f55125328cd0f8ed477)
4.	Quando isso tudo aconteceu (10/8/2015 19:13:02). Esses são dados excelentes para um alerta na produção que indique que houve um problema e que é melhor você averiguar. Nesse caso, por exemplo, queremos ver se conseguimos descobrir por que o Gerenciador de Recursos não viu escolha além de empacotar as réplicas no domínio de falha. Poderia ser porque todos os nós nos outros domínios de falha estavam inativos e não havia domínios de reserva suficientes ou, se havia domínios, outro motivo que tenha invalidado os nós nesses outros domínios de falha (por exemplo, uma política InvalidDomain no serviço).

No entanto, digamos que você queira criar um serviço, ou que o Gerenciador de Recursos esteja tentando localizar um lugar para posicionar alguns serviços, mas não parece haver soluções que funcionem. Isso pode acontecer por vários motivos, mas normalmente é devido a uma das duas condições abaixo:

1.	Alguma condição temporária impossibilitou o posicionamento correto dessa instância de serviço ou dessa réplica
2.	Os requisitos do serviço estão configurados incorretamente, de maneira que seus requisitos são considerados insatisfatórios.

Em cada uma dessas condições, você verá um relatório de integridade do Gerenciador de Recursos que fornece informações para ajudá-lo a determinar o que está acontecendo e por que o serviço não pode ser posicionado. Chamamos esse processo de "sequência de eliminação de restrição". Durante o processo, percorremos as restrições configuradas que afetam o serviço e vemos o que elas eliminam. Portanto, quando as coisas não podem ser posicionadas, você pode ver quais nós foram eliminados e por quê. Vamos falar sobre cada uma das restrições diferentes que você pode ver nesses relatórios de integridade e o que está sendo verificado. Observe que, na maioria das vezes, você não verá essas restrições eliminando nós, já que as restrições estão no nível de atenuação ou de otimização por padrão (como indicamos anteriormente). Você poderá vê-las se estiverem invertidas ou se forem tratadas como restrições graves, como mostramos a seguir:
-	ReplicaExclusionStatic e ReplicaExclusionDynamic – essa é uma restrição interna que indica que durante a pesquisa determinamos que duas réplicas precisam ser colocadas no nó (o que não é permitido). ReplicaExclusionStatic e ReplicaExclusionDynamic são quase exatamente a mesma regra. A restrição ReplicaExclusionDynamic diz "não foi possível colocar essa réplica aqui porque a única solução proposta já posicionou uma réplica neste lugar". Isso é diferente da exclusão ReplicaExclusionStatic, que indica um conflito real, e não um proposto. Já existe uma réplica no nó. Isso parece confuso? Sim. Isso importa muito? Não. Basta saber que se você estiver vendo uma sequência de eliminação de restrição que contenha a restrição ReplicaExclusionStatic ou ReplicaExclusionDynamic, o Gerenciador de Recursos considerará que não existem nós suficientes para posicionar todas as réplicas. As restrições adicionais normalmente podem indicar, acima de tudo, por que estamos ficando com poucos nós
-	PlacementConstraint: se você vir essa mensagem, significa que eliminamos alguns nós porque eles não correspondiam a restrições de posicionamento do serviço. Rastreamos as restrições de posicionamento configuradas atualmente como parte dessa mensagem.
-	NodeCapacity: se você vir essa restrição, isso significa que não posicionamos as réplicas nos nós indicados porque isso faria com que o nó ficasse acima da capacidade.
-	Affinity: essa restrição indica que não posicionamos a réplica em nós afetados, pois isso causaria uma violação da restrição de afinidade.
-	FaultDomain e UpgradeDomain: essa restrição elimina nós se o posicionamento da réplica nos nós indicados causar empacotamento em um domínio de atualização ou de falha específico.
-	PreferredLocation: normalmente, você não verá essa restrição removendo nós da solução, já que é uma otimização somente por padrão. Além disso, a restrição de local preferida geralmente só estará presente durante atualizações (quando ela é usada para mover réplicas de volta a onde estavam quando a atualização foi iniciado). No entanto, é possível.

## Atualizações
O Gerenciador de Recursos também ajuda durante as atualizações de aplicativos e de cluster para garantir que a atualização seja perfeita e que as regras e o desempenho do cluster não fiquem comprometidos.

### Manter a imposição das regras
O principal a se notar é as regras: os controles rígidos de coisas como restrições de posicionamento ainda são impostos durante as atualizações. Isso parece ser óbvio, mas preferimos ser explícitos em relação a isso. O lado positivo disso é que você pode garantir que, caso não queira que determinadas cargas de trabalho sejam executadas em determinados nós, isso não acontecerá mesmo durante a atualização, e não haverá interação entre as operações. Se seu ambiente for altamente restrito, isso poderá fazer com que as atualizações demorem muito tempo, pois há poucas opções para um serviço se ele tiver que ser desativado para a aplicação de patch.

### Substituições inteligentes
Quando uma atualização é iniciada, o Gerenciador de Recursos tira um instantâneo da disposição do cluster atual e tenta retornar as coisas a esse estado quando a atualização é concluída. O raciocínio por trás disso é simples: primeiro, ele garante que haja apenas duas transições para cada serviço como parte da atualização (a movimentação para fora do nó afetado e a movimentação de volta). Em segundo lugar, ele garante que a atualização em si não tenha muito impacto no layout do cluster. Se o cluster foi organizado bem antes da atualização, ele será organizado bem depois dela.

### Variação reduzida
Outra coisa que acontece durante as atualizações é que o Gerenciador de Recursos desativa o balanceamento da entidade que está sendo atualizada. Então, se você tiver duas instâncias diferentes do aplicativo e iniciar uma atualização em uma delas, o balanceamento é pausado naquela instância do aplicativo, mas não em outra. Impedir o balanceamento reativo impede que o Gerenciador de Recursos reaja à atualização ("Essa não! Um nó vazio! Melhor preenchê-lo com vários tipos de coisas!") e, consequentemente, gere muitos movimentos extras para serviços no cluster que terão que ser desfeitos quando os serviços forem movidos de volta para os nós após a conclusão da atualização. Se a atualização em questão for uma atualização de cluster, todo o cluster terá o balanceamento pausado durante a atualização (as verificações de restrição, que garantem que as regras são impostas, ficam ativas).

### Regras relaxadas
Uma das questões que aparecem durante as atualizações é querer que a atualização seja concluída mesmo quando o cluster está, no geral, restrito ou cheio. Na verdade, já falamos sobre como podemos fazer isso, mas durante as atualizações é ainda mais importante, pois você normalmente tem entre 5 e 20% do seu cluster inativo sempre que faz a atualização usando o cluster, e a carga de trabalho tem que ir para algum lugar. É aí que entra a noção de capacidades de buffer que mencionamos antes. Embora a capacidade de buffer seja respeitada durante o funcionamento normal, o Gerenciador de Recursos encherá até a capacidade total durante as atualizações.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas
- [Aprenda mais sobre o Gerenciador de Recursos de Cluster do Service Fabric](service-fabric-cluster-resource-manager-introduction.md)

<!---HONumber=AcomDC_0309_2016-->