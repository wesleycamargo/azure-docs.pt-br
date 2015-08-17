<properties 
   pageTitle="Cenários da Possibilidade de Teste da Malha do Serviço: comunicação do serviço" 
   description="As comunicação entre serviços é um ponto de integração essencial de um aplicativo da Malha do Serviço. Este artigo aborda as considerações de design e as técnicas de teste." 
   services="service-fabric" 
   documentationCenter=".net" 
   authors="vturecek" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA" 
   ms.date="04/17/2015"
   ms.author="vturecek"/>

# Cenários da Possibilidade de Teste da Malha do Serviço: comunicação do serviço

Os estilos de arquitetura orientada a serviços e microsserviços surgem naturalmente na Malha do Serviço. Nesses tipos de arquiteturas distribuídas, os aplicativos de microsserviço divididos em componentes são compostos normalmente por vários serviços que precisam se comunicar. Mesmo no caso mais simples, há geralmente pelo menos um serviço Web sem estado e um serviço de armazenamento de dados com estado que precisam se comunicar.

A comunicação entre os serviços é um ponto de integração essencial de um aplicativo, uma vez que cada serviço expõe uma API remota para outros serviços. Trabalhar com um conjunto de limites de API que envolvem E/S geralmente exige algum cuidado com uma boa quantidade de teste e validação.

Há várias considerações a serem feitas quando esses limites de serviço são conectados em um sistema distribuído:

 - **Protocolo de transporte**. Será HTTP para maior interoperabilidade, ou um protocolo binário personalizado para taxa de transferência máxima?
 - **Manipulação de erros**. Como os erros transitórios e permanentes são tratados, e o que acontece quando um serviço é movido para um nó diferente?
 - **Tempos limite e latência**. Em aplicativos com várias camadas, como cada camada de serviço lida com a latência por meio da pilha e até o usuário?

Se você estiver usando um dos componentes integrados de comunicação de serviço fornecidos pela Malha do Serviço ou criando seu próprio, o teste a interação entre os serviços é uma parte fundamental da garantia de resiliência em seu aplicativo.

## Onde está meu serviço?

As instâncias do serviço podem se movimentar com o tempo, especialmente quando configuradas com métricas de carga para balanceamento personalizado e ideal de recursos. Atualizações, failovers, expansão e outras situações diversas que ocorrem durante a vida útil de um sistema distribuído são o local para onde a Malha do Serviço move suas instâncias de serviço a fim de maximizar a disponibilidade.

À medida que os serviços se movimentam pelo cluster, há dois cenários para os quais seus clientes e outros serviços devem estar preparados para manipular ao conversar com um serviço:

 + A instância do serviço ou réplica da partição foi movida desde a última vez que você conversou com ela. Essa é uma parte normal do ciclo de vida do serviço e deve acontecer durante a vida útil do seu aplicativo.
 + A instância do serviço ou a réplica da partição está se movimentando. Embora o failover de um serviço de um nó para outro ocorra rapidamente na Malha do Serviço, pode haver um atraso na disponibilidade se o componente de comunicação do serviço iniciar de forma lenta.

A manipulação tranquila desses cenários é importante para um sistema em execução adequada. Para fazer isso, precisamos fazer algumas considerações:

+ Todos os serviços aos quais é possível se conectar têm um *endereço* no qual escutam (HTTP, WebSockets etc.). Quando uma instância de serviço ou partição tiver sido movida, o ponto de extremidade de seu endereço será alterado (foi movida para outro nó com um endereço IP diferente). Se você estiver usando os componentes integrados de comunicação, eles manipularão a nova resolução dos endereços de serviço para você. 
+ Pode haver um aumento temporário na latência do serviço à medida que a instância do serviço começa sua escuta novamente, dependendo da rapidez com que serviço abre após a movimentação.
+ Quaisquer conexões existentes precisarão ser fechadas e reabertas novamente quando o serviço for aberto em um novo nó. Um desligamento ou reinicialização de nó proporciona tempo para o desligamento correto das conexões existentes.

### Teste: mover instâncias do serviço

Usando ferramentas de Possibilidade de Teste da Malha do Serviço, podemos criar um cenário de teste para testar essas situações de maneiras diferentes.

1. Mova a réplica primária de um serviço com estado.
 
    A réplica primária de uma partição de serviço com estado pode ser movida por vários motivos. Use isso para direcionar a réplica primária de uma partição específica para ver como os serviços reagem à movimentação de uma maneira muito controlada.

    ```powershell

    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService

    ```

2. Parar um nó.

    Quando um nó é interrompido, a Malha do Serviço move todas as instâncias ou partições de serviço que estavam nesse nó para um dos nós disponíveis no cluster. Use isso para testar uma situação na qual um nó é perdido em seu cluster e todas as instâncias e réplicas do serviço nesse nó precisam ser movidas.

    Um nó pode ser interrompido usando o cmdlet Stop-ServiceFabricNode PowerShell:

    ```powershell

    PS > Restart-ServiceFabricNode -NodeName Node.1

    ```

    
    


### Disponibilidade do serviço

A Malha do Serviço é uma plataforma projetada para fornecer alta disponibilidade para seus serviços. No entanto, os problemas de infraestrutura subjacentes ainda podem causar indisponibilidade em casos extremos. Portanto, também é importante testar esse cenário.

Serviços com estado usam um sistema baseado em quorum para a replicação de estado a fim de obter alta disponibilidade. Isso significa que um quorum de réplicas precisa estar disponível para executar operações de gravação. Em casos raros, como em uma falha generalizada de hardware, talvez um quorum de réplicas não esteja disponível. Nesse caso, você não poderá executar operações de gravação, mas ainda conseguirá executar operações de leitura.

### Teste: indisponibilidade da operação de gravação

As ferramentas de Possibilidade de Teste da Malha do Serviço permitem injetar uma falha que induz a perda de quorum para esse tipo de cenário de teste. Embora seja raro, é importante que os clientes e serviços que dependem do serviço com estado sejam preparados para lidar com situações nas quais não podem fazer solicitações de gravação para o serviço com estado. Ao mesmo tempo, também é importante que o próprio serviço com estado reconheça essa possibilidade e possa comunicá-los adequadamente aos chamadores.

A perda de quorum pode ser induzida usando o cmdlet Invoke-ServiceFabricPartitionQuorumLoss PowerShell:

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode PartialQuorumLoss -QuorumLossDurationInSeconds 20

```

Neste exemplo, definimos `QuorumLossMode` como `PartialQuorumLoss` para indicar que desejamos induzir perda de quorum sem interromper todas as réplicas, para que as operações de leitura ainda sejam possíveis. Para testar um cenário no qual uma partição inteira não está disponível, você pode definir essa opção como `FullQuorumLoss`.

## Próximas etapas

[Saiba mais sobre as Ações da Possibilidade de Teste](service-fabric-testability-actions.md)

[Saiba mais sobre os Cenários da Possibilidade de Teste](service-fabric-testability-scenarios.md)

<!---HONumber=August15_HO6-->