---
title: Descrição de cluster do Balanceador do Recursos | Microsoft Docs
description: Descrever um cluster do Service Fabric especificando domínios de falha, domínios de atualização, propriedades de nó e capacidades de nó para o Gerenciador de Recursos de Cluster.
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
# Descrevendo um cluster do Service Fabric
O Gerenciador de Recursos de Cluster do Service Fabric fornece vários mecanismos para descrever um cluster. Durante o tempo de execução, o Gerenciador de Recursos de Cluster usa essas informações para garantir a alta disponibilidade dos serviços executados no cluster, além de assegurar que os recursos do cluster sejam usados apropriadamente.

## Principais conceitos
Os recursos do Gerenciador de Recursos de Cluster oferecem suporte a vários recursos que descrevem um cluster:

* Domínios de falha
* Domínios de atualização
* Propriedades de nó
* Capacidades de nó

## Domínios de falha
Um domínio de falha é qualquer área da falha coordenada. Um único computador é um domínio de falha (já que ele pode falhar sozinho por vários motivos diferentes, desde falhas de fornecimento de energia até falhas devido a firmware NIC inválido). Um grupo de computadores conectados ao mesmo comutador Ethernet estão no mesmo domínio de falha, como estariam aqueles conectados a uma única fonte de alimentação. Uma vez que é natural que eles se sobreponham, os domínios de falha são hierárquicos por natureza e são representados como URIs no Service Fabric.

Se estiver configurando seu próprio cluster, você precisará refletir sobre todas essas diferentes áreas de falha e verificar se os domínios de falha foram configurados corretamente para que o Service Fabric saiba onde é seguro colocar os serviços. Por "seguro", queremos dizer inteligente: não convém posicionar serviços de forma que a perda de um domínio de falha (a falha de qualquer um dos componentes listados abaixo, por exemplo) torne o serviço inoperante. No ambiente do Azure, aproveitamos as informações de domínio de falha fornecidas pelo ambiente para configurar corretamente os nós do cluster em seu nome. Na imagem abaixo (Figura 7), colorimos todas as entidades que resultam razoavelmente em um domínio de falha como um exemplo simples e listamos todos os domínios de falha diferentes resultantes. Neste exemplo, temos datacenters (DC), racks (R) e folhas (B). Em termos conceituais, se cada folha contém mais de uma máquina virtual, pode haver outra camada na hierarquia de domínios de falha.

![Nós organizados por meio de domínios de falha][Image1]

 Durante o tempo de execução, o Gerenciador de Recursos de Cluster do Service Fabric considera os domínios de falha no cluster e tenta distribuir as réplicas de determinado serviço para que fiquem em domínios de falha separados. Esse processo ajuda a garantir que, em caso de falha de qualquer domínio de falha (em qualquer nível da hierarquia), a disponibilidade do serviço não seja comprometida.

 O Gerenciador de Recursos de Cluster do Service Fabric realmente não se importa com a quantidade de camadas que existem na hierarquia. No entanto, como ele tenta garantir que a perda de qualquer parte da hierarquia não afeta o cluster nem os serviços em execução nela, geralmente, será melhor se houver o mesmo número de computadores em cada nível de profundidade no domínio de falha. Em última análise, isso impede que uma parte da hierarquia tenha que conter mais serviços do que outras.

 Se o cluster for configurado de forma que a "árvore" de domínios de falha fique desequilibrada, será bastante difícil que o Gerenciador de Recursos de Cluster descubra qual é a melhor alocação de réplicas, particularmente porque isso significa que a perda de determinado domínio pode afetar muito a disponibilidade do cluster. O Gerenciador de Recursos de Cluster precisa decidir entre usar os computadores no domínio "pesado" com eficiência colocando serviços no mesmo e posicionar os serviços de modo que a perda do domínio não cause problemas.

 No diagrama a seguir, são mostrados dois exemplos de layout de cluster, um em que os nós estão bem distribuídos entre os domínios de falha e outro em que um domínio de falha acaba tendo muito mais nós. Observe que, no Azure, as opções nas quais nós terminam em quais domínios de falha e de atualização são manipuladas para você. Assim, você nunca encontrará esses tipos de desequilíbrios. No entanto, se você vier a criar seu próprio cluster local ou em outro ambiente, é algo em que deverá pensar.

 ![Dois layouts de cluster diferentes][Image2]

## Domínios de atualização
Os Domínios de Atualização são um outro recurso que ajuda o Gerenciador de Recursos do Service Fabric a entender o layout do cluster para que possa planejar antecipadamente como lidar com falhas. Os Domínios de Atualização definem áreas (conjuntos de nós, na realidade) que ficarão inativas ao mesmo tempo durante uma atualização.

Os Domínios de Atualização são muito semelhantes aos Domínios de Falha, mas com algumas diferenças importantes. Primeiro, os Domínios de Atualização normalmente são definidos por uma política, enquanto que os Domínios de Falha são rigorosamente definidos por áreas de falhas coordenadas (e, portanto, geralmente pelo layout de hardware do ambiente). No entanto, no caso dos Domínios de Atualização, você pode decidir quantos deseja ter. Outra diferença é que (atualmente, pelo menos) os Domínios de Atualização não são hierárquicos: eles são mais semelhantes a uma marca simples do que uma hierarquia.

A figura a seguir mostra uma configuração fictícia em que há três domínios de atualização distribuídos em três domínios de falha. Ela também mostra um possível posicionamento para três réplicas diferentes de um serviço com estado. Observe que elas estão todas em diferentes domínios de falha e de atualização. Isso significa que podemos perder um domínio de falha durante uma atualização de serviço e ainda haverá uma cópia em execução do código e dos dados no cluster. Dependendo de suas necessidades, isso pode ser suficiente, porém, você pode notar que essa cópia é antiga (pois o Service Fabric usa a replicação baseado em quórum). Para realmente sobreviver a duas falhas, você precisaria de mais réplicas (cinco, no mínimo).

![Posicionamento com domínios de falha e atualização][Image3]

Há prós e contras em relação a ter um grande número de domínios de atualização. A vantagem é que cada etapa da atualização é mais granular e, assim, afeta um número menor de nós ou serviços. Isso faz com que menos serviços precisem ser movidos por vez, criando menos variação no sistema e melhorando a confiabilidade geral (já que uma parte menor do serviço será afetada por qualquer problema). A desvantagem de ter vários domínios de atualização é que o Service Fabric verifica a integridade de cada Domínio de Atualização à medida que ele é atualizado e garante que o Domínio de Atualização esteja íntegro antes de passar para o próximo Domínio de Atualização. O objetivo dessa verificação é garantir que os serviços tenham a oportunidade de se estabilizar e que sua integridade seja validada antes que a atualização prossiga para que os problemas sejam detectados. Essa compensação é aceitável, pois isso evita que alterações incorretas afetem grande parte do serviço de uma só vez.

Ter um número reduzido de domínios de atualização tem seus próprios efeitos colaterais: enquanto cada domínio de atualização individual está inoperante e é atualizado, uma grande parte de sua capacidade geral fica indisponível. Por exemplo, se tiver apenas três domínios de atualização, você desativará cerca de um terço de sua capacidade geral de serviço ou de cluster de uma vez. Isso não é desejável, pois você precisa ter capacidade suficiente no restante do cluster para cobrir a carga de trabalho, o que significa que, no caso normal, esses nós são menos sobrecarregados do que seriam em caso contrário, aumentando o COGS.

Não há um limite real para o número total de domínios de falha ou de atualização em um ambiente nem restrições sobre como eles se sobrepõem. As estruturas comuns que vimos são 1:1 (em que cada domínio de falha exclusivo também é mapeado para seu próprio domínio de atualização), um Domínio de Atualização por Nó (instância de sistema operacional física ou virtual) e um modelo "distribuído" ou de "matriz" em que os Domínios de Falha e os Domínios de Atualização formam uma matriz em que os computadores geralmente são executados diagonalmente.

![Layouts de Domínios de Falha e de Atualização][Image4]

Não existe uma solução ideal sobre qual layout deve ser escolhido. Cada um deles tem prós e contras. Por exemplo, o modelo 1FD:1UD tem configuração bastante simples, enquanto o modelo 1 UD por Nó é mais parecido com o que as pessoas costumavam usar ao gerenciar pequenos conjuntos de computadores no passado, em que cada um era desativado independentemente.

O modelo mais comum (e aquele que usamos para os clusters do Azure Service Fabric hospedados) é a matriz de FD/UD, em que e FDs e UDs formam uma tabela e nós são posicionados começando ao longo da diagonal. O resultado final, esparso ou compactado, depende do número total de nós em comparação com o número de FDs e UDs (em outras palavras, para clusters suficientemente grandes, quase tudo acaba ficando parecido com o padrão de matriz densa, mostrado na opção inferior direita da Figura 10).

## Restrições de domínio de falha e de atualização e o comportamento resultante
O Gerenciador de recursos de Cluster trata o desejo de manter um serviço balanceado entre domínios de falha e de atualização como uma restrição. Você pode encontrar mais informações sobre restrições [neste artigo](service-fabric-cluster-resource-manager-management-integration.md). As restrições de domínio de falha e de atualização são definidas como a seguir: "para uma partição de serviço específica nunca deverá haver uma diferença *maior que um* no número de réplicas entre dois domínios." Isso significa na prática que para um determinado serviço certos movimentos ou certas disposições podem não ser válidas no cluster, porque isso poderia violar a restrição do domínio de falha ou de atualização.

Vamos dar uma olhada em um exemplo. Digamos que temos um cluster com seis nós, configurados com cinco domínios de falha e cinco domínios de atualização.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| UD0 |N1 | | | | |
| UD1 |N6 |N2 | | | |
| UD2 | | |N3 | | |
| UD3 | | | |N4 | |
| UD4 | | | | |N5 |

Agora vamos supor que criamos um serviço com um TargetReplicaSetSize de cinco. As réplicas recaem sobre N1 a N5. Na verdade, o N6 nunca será usado. Mas por quê? Bem, vamos examinar a diferença entre o layout atual e o que aconteceria se tivéssemos escolhido N6 e veja como isso se relaciona à nossa definição da restrição de domínio de falha e domínio de atualização.

Aqui está o nosso layout e o número total de réplicas por domínio de falha e de atualização.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| UD0 |R1 | | | | |1 |
| UD1 | |R2 | | | |1 |
| UD2 | | |R3 | | |1 |
| UD3 | | | |R4 | |1 |
| UD4 | | | | |R5 |1 |
| FDTotal |1 |1 |1 |1 |1 |- |

Observe que esse layout é balanceado em termos de nós por domínio de falha e domínio de atualização e ele também é balanceado em termos do número de réplicas por domínio de falha e de atualização. Cada domínio possui o mesmo número de nós e o mesmo número de réplicas.

Agora, vamos ver o que aconteceria se tivéssemos usado N6 em vez de N2. Como as réplicas seriam distribuídas? O resultado deve ser algo como:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| UD0 |R1 | | | | |1 |
| UD1 |R5 | | | | |1 |
| UD2 | | |R2 | | |1 |
| UD3 | | | |R3 | |1 |
| UD4 | | | | |R4 |1 |
| FDTotal |2 |0 |1 |1 |1 |- |

Isso viola a nossa definição para a restrição de domínio de falha, já que FD0 tem 2 réplicas, enquanto FD1 tem 0, tornando a diferença total 2 e, portanto, o Gerenciador de Recursos de Cluster não permitirá essa disposição. Da mesma forma se tivéssemos escolhido N2 a N6 obteríamos:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| UD0 | | | | | |0 |
| UD1 |R5 |R1 | | | |2 |
| UD2 | | |R2 | | |1 |
| UD3 | | | |R3 | |1 |
| UD4 | | | | |R4 |1 |
| FDTotal |1 |1 |1 |1 |1 |- |

Que apesar de equilibrada em termos de domínios de falha viola a restrição de domínio de atualização (já que UD0 tem 0 réplicas enquanto UD1 tem 2) e, portanto, também será inválida.

## Configurando domínios de falha e atualização
A definição de Domínios de Falha e Domínios de Atualização é feita automaticamente em implantações hospedadas do Azure Service Fabric. O Service Fabric simplesmente seleciona as informações de ambiente do Azure. No Azure, as informações de domínio de falha e de atualização parecem ser de "nível único", mas na realidade são informações encapsuladas das camadas inferiores do Azure Stack e apenas apresentam a falha lógica e os domínios de atualização a partir da perspectiva do usuário.

Se você estiver criando seu próprio cluster (ou se apenas quiser tentar executar uma topologia específica no computador de desenvolvimento), precisará fornecer o domínio de falha e informações de domínio de atualização por conta própria. Neste exemplo, definimos um cluster de desenvolvimento local com nove nós que abrange três "datacenters" (cada um com três racks) e três domínios de atualização distribuídos entre esses três datacenters. No modelo do manifesto do cluster, ele é semelhante a:

ClusterManifest.xml

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one-box /one single server -->
    <WindowsServer IsScaleMin="true">
      <NodeList>
        <Node NodeName="Node01" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType01" FaultDomain="fd:/DC01/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node02" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType02" FaultDomain="fd:/DC01/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node03" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType03" FaultDomain="fd:/DC01/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node04" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType04" FaultDomain="fd:/DC02/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node05" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType05" FaultDomain="fd:/DC02/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node06" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType06" FaultDomain="fd:/DC02/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node07" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType07" FaultDomain="fd:/DC03/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node08" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType08" FaultDomain="fd:/DC03/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node09" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType09" FaultDomain="fd:/DC03/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
      </NodeList>
    </WindowsServer>
  </Infrastructure>
```
> [!NOTE]
> Em implantações do Azure, domínios de falha e domínios de atualização são atribuídos pelo Azure. Portanto, a definição dos seus nós e funções na opção infraestrutura do Azure não inclui informações sobre o domínio de falha ou o domínio de atualização.
> 
> 

## Restrições de posicionamento e propriedades do nó
Às vezes (na verdade, na maioria das vezes), convém assegurar que determinadas cargas de trabalho sejam executadas apenas em alguns nós ou em certos conjuntos de nós no cluster. Por exemplo, algumas cargas de trabalho podem exigir GPUs ou SSDs, enquanto outras, não. Um bom exemplo disso é praticamente qualquer arquitetura de n camadas, em que certos computadores atuam como front-end/interface de atendimento do aplicativo (estando, portanto, provavelmente expostos à Internet), enquanto outro conjunto (frequentemente com recursos de hardware diferentes) lida com o trabalho das camadas de computação ou armazenamento (normalmente não expostos à Internet). O Service Fabric espera que, mesmo em um mundo de microsserviços, haja casos em que cargas de trabalho específicas precisem ser executadas em configurações de hardware específicas, por exemplo:

* um aplicativo de n camadas existente foi "transferido e posicionado" em um ambiente do Service Fabric
* uma carga de trabalho deseja ser executada em um hardware específico por motivos de desempenho, escala ou isolamento de segurança
* Uma carga de trabalho precisa ser isolada de outras cargas de trabalho por motivos de política ou consumo de recursos

Para dar suporte a esses tipos de configurações, o Service Fabric tem uma noção avançada do que chamamos de restrições de posicionamento. As restrições de posicionamento podem ser usadas para indicar onde determinados serviços devem ser executados. O conjunto de restrições é extensível aos usuários, o que significa que as pessoas podem marcar nós com propriedades personalizadas e, depois, selecioná-los também.

![Diferentes Cargas de Trabalho de Layout do Cluster][Image5]

As diferentes marcas de chave/valor em nós são conhecidas como *propriedades* de posicionamento do nó (ou apenas propriedades de nó), enquanto a instrução no serviço é chamada de *restrição* de posicionamento. O valor especificado na propriedade do nó pode ser uma cadeia de caracteres, bool ou signed long. A restrição pode ser qualquer instrução booliana que opera sobre as diferentes propriedades de nó no cluster. Os seletores válidos nessas instruções boolianas (que são cadeias de caracteres) são:

* verificações condicionais para a criação de instruções
  * "igual a" ==
  * "maior que" >
  * "menor que" <
  * "não é igual a" !=
  * "maior ou igual a" >=
  * "menor ou igual a" <=
* instruções boolianas para agrupamento e negação
  * "e" &&
  * "ou" ||
  * "não" !
* parênteses para agrupar operações
  
  * ()
  
  Aqui estão alguns exemplos de instruções de restrição básicas que usam alguns dos símbolos acima. Observe que as propriedades de nó podem ser cadeias de caracteres, bools ou valores numéricos.
  
  * "Foo >= 5"
  * "NodeColor != green"
  * "((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"

Somente nós em que a instrução geral é avaliada como "True" podem ter o serviço colocado nos mesmos. Nós sem uma propriedade definida não correspondem a nenhuma restrição de posicionamento que contenha essa propriedade.

O Service Fabric também define algumas propriedades padrão que podem ser usadas automaticamente sem que o usuário precise defini-las. Neste artigo, as propriedades padrão definidas em cada nó são NodeType e NodeName. Por exemplo, você poderia escrever uma restrição de posicionamento como "(NodeType == NodeType03)". Geralmente, verificamos que NodeType é uma das propriedades mais comumente usadas, pois geralmente faz a correspondência entre 1:1 e o tipo de um computador, que, por sua vez, corresponde a um tipo de carga de trabalho em uma arquitetura de aplicativos de n camadas tradicional.

![Restrições do Posicionamento e Propriedades do Nó][Image6]

Vamos supor que as seguintes propriedades de nó foram definidas para um tipo de nó específico: ClusterManifest.xml

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

Você pode criar restrições de posicionamento de serviço para um serviço da seguinte forma:

C#

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// add other required servicedescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

Se tiver certeza de que todos os nós de NodeType01 são válidos, você também poderá selecionar esse tipo de nó, usando restrições de posicionamento como as mostradas nas imagens acima.

Uma das vantagens das restrições de posicionamento de um serviço é que elas podem ser atualizadas dinamicamente durante o tempo de execução. Então, se necessário, você poderá mover um serviço pelo cluster, adicionar e remover requisitos, etc. O Service Fabric se encarrega de garantir que o serviço fique ativo e disponível mesmo quando esses tipos de alterações estiverem em andamento.

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

Powershell:

```posh
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

Restrições do posicionamento (juntamente com várias outros controles de orchestrator sobre os quais vamos falar) são especificadas para cada instância de serviço diferente. As atualizações sempre assumem o lugar (substituem) do que foi especificado anteriormente.

Também vale a pena observar que agora as propriedades em um nó são definidas por meio da definição de cluster e, portanto, não podem ser atualizadas sem uma atualização para o cluster e exigirão que cada nó fique inativo e depois ativo novamente para atualização das suas respectivas propriedades.

## Capacidade
Um dos trabalhos mais importantes de qualquer orquestrador é ajudar a gerenciar o consumo de recursos no cluster. A última coisa que você deseja, se estiver tentando executar serviços com eficiência, é ter vários nós ativos (levando à contenção de recursos e a um desempenho ruim) enquanto outros estão inativos (desperdício de recursos). Porém, vamos pensar em termos ainda mais básicos do que o equilíbrio (sobre o qual falaremos em alguns instantes): que tal apenas garantir que não fiquemos sem recursos?

O Service Fabric representa recursos como "Métricas". As métricas são qualquer recurso lógico ou físico que você queira descrever para o Service Fabric. Exemplos de métricas são itens como "WorkQueueDepth" ou "MemoryInMb". As métricas são diferentes das restrições de posicionamento e propriedades de nó porque as propriedades de nó geralmente são descritores estáticos dos próprios nós, enquanto as métricas se referem a recursos que os nós possuem e que os serviços consomem quando são executados em um nó. Portanto, uma propriedade seria algo como HasSSD e pode ser definida como true ou false, mas a quantidade de espaço disponível no SSD (e consumida por serviços) seria uma métrica como "DriveSpaceInMb". A capacidade do nó definiria "DriveSpaceInMb" como a quantidade total de espaço não reservado na unidade e os serviços informariam quanto da métrica foi utilizada durante o tempo de execução.

Se você desativasse todo o *balanceamento* de recursos, o Gerenciador de Recursos de Cluster do Service Fabric ainda seria capaz de garantir que nenhum nó ficasse acima de sua capacidade (a menos que o cluster como um todo estivesse muito cheio). A capacidade é outra *restrição* que usa o Gerenciador de Recursos de Cluster para entender o quanto um nó possui de um recurso. A capacidade e o consumo no nível de serviço são expressos em termos de métricas. Por exemplo, a métrica pode ser "MemoryInMb", um determinado nó pode ter uma capacidade de MemoryInMb de 2048, enquanto um determinado serviço pode dizer a que está consumindo atualmente 64 de MemoryInMb.

Durante o tempo de execução, o Gerenciador de Recursos de Cluster controla a quantidade de cada recurso que está presente em cada nó (definido por sua capacidade) e a quantidade restante (subtraindo qualquer uso declarado de cada serviço). Com essas informações, o Gerenciador de Recursos do Service Fabric pode descobrir onde inserir ou mover réplicas para que os nós não ultrapassem a capacidade.

C#:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "MemoryInMb";
metric.PrimaryDefaultLoad = 64;
metric.SecondaryDefaultLoad = 64;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,64,64)
```

![Capacidade e nós de cluster][Image7]

Você pode ver isso no manifesto do cluster:

ClusterManifest.xml

```xml
    <NodeType Name="NodeType02">
      <Capacities>
        <Capacity Name="MemoryInMb" Value="2048"/>
        <Capacity Name="Disk" Value="10000"/>
      </Capacities>
    </NodeType>
```

Também é possível que a carga de um serviço seja alterada dinamicamente. Digamos que a carga de uma réplica é alterada de 64 para 1024, mas o nó que estava executando no momento tinha somente 512 (da métrica "MemoryInMb") restantes. Por causa disso, o local em que uma réplica ou instância está posicionada no momento se torne inválido, pois o uso combinado de todas as réplicas e instâncias no nó excede a capacidade do nó. Mais tarde, falaremos mais sobre esse cenário em que a carga pode ser alterada dinamicamente, mas em termos de capacidade, ela é tratada da mesma forma: o Gerenciador de Recursos de Cluster é acionado automaticamente e coloca o nó de volta abaixo da capacidade movendo uma ou mais réplicas ou instâncias do nó para nós diferentes. Ao fazer isso, o Gerenciador de Recursos de Cluster tenta minimizar o custo de todas as movimentações (voltaremos à noção de Custo mais tarde).

## Capacidade do cluster
Então, como impedir que o cluster geral fique muito cheio? Bem, com a carga dinâmica, na verdade, não há muito que possamos fazer (pois os serviços podem ter seu pico de carga independente das ações executadas pelo Gerenciador de Recursos de Cluster. O cluster com espaço de sobra hoje pode não ter espaço suficiente quando você ficar famoso amanhã), mas há alguns controles embutidos para evitar problemas básicos. A primeira coisa a fazer é evitar a criação de novas cargas de trabalho que fariam com que o cluster ficasse cheio.

Digamos que você crie um serviço simples sem estado e haja alguma carga associada a ele (falaremos mais sobre relatórios de carga padrão e dinâmica posteriormente). Digamos que esse serviço se preocupe com algum recurso (por exemplo, o espaço em disco) e que, por padrão, ele consumirá cinco unidades de Espaço em Disco para cada instância do serviço. Você deseja criar três instâncias do serviço. Ótimo! Isso significa que é preciso que 15 unidades do Espaço em Disco estejam presentes no cluster para podermos criar essas instâncias de serviço. O Service Fabric calcula continuamente a capacidade e o consumo totais de cada métrica, assim, poderemos facilmente fazer a determinação e rejeitar a chamada de criação de serviço se houver espaço suficiente.

Observe que, como o requisito é apenas que haja 15 unidades disponíveis, esse espaço poderia ser alocado de muitas maneiras diferentes: poderia ser uma unidade restante da capacidade em 15 nós diferentes, por exemplo, ou três unidades restantes da capacidade em cinco nós diferentes, etc. Se não houver capacidade suficiente em três nós diferentes, o Service Fabric reorganizará os serviços já presentes no cluster para liberar espaço nos três nós necessários. Essa reorganização quase sempre é possível, a menos que o cluster, como um todo, esteja quase totalmente cheio.

## Capacidade de buffer
Outra coisa que ajuda as pessoas a gerenciar a capacidade total do cluster é a noção de buffer reservado à capacidade especificada em cada nó. Essa configuração é opcional, mas permite reservar uma parte da capacidade total do nó para que ele só seja usado para posicionar serviços durante atualizações e falhas, casos em que a capacidade do cluster é reduzida. Atualmente, o buffer é especificado globalmente por métrica para todos os nós por meio do ClusterManifest. O valor que você escolher para a capacidade reservada será uma função dos recursos para os quais seus serviços têm mais limitações, bem como o número de domínios de falha e atualização presentes no cluster. Geralmente, um número maior de domínios de falha e atualização significa que você pode escolher um número menor para a capacidade de buffer, pois espera que uma quantidade menor do cluster fique indisponível durante atualizações e falhas. Observe que a especificação do percentual do buffer só fará sentido se você também tiver especificado a capacidade do nó para uma métrica.

Aqui está um exemplo de como especificar a capacidade do buffer:

ClusterManifest.xml

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="DiskSpace" Value="0.10" />
            <Parameter Name="Memory" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

A criação de novos serviços irá falhar quando o cluster está sem capacidade em buffer, garantindo que o cluster retenha sobrecarga sobressalente suficiente, de modo que as atualizações e falhas não resultem em nós realmente acima da capacidade. O Gerenciador de Recursos de Cluster expõe muitas dessas informações por meio do PowerShell e das APIs de Consulta, permitindo que você veja as configurações de capacidade em buffer, a capacidade total e o consumo atual para cada métrica em uso no cluster. Aqui, vemos um exemplo dessa saída:

```posh
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2015 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2015 12:54:59 AM
LoadMetricInformation     :
                            LoadMetricName        : Metric1
                            IsBalancedBefore      : False
                            IsBalancedAfter       : False
                            DeviationBefore       : 0.192450089729875
                            DeviationAfter        : 0.192450089729875
                            BalancingThreshold    : 1
                            Action                : NoActionNeeded
                            ActivityThreshold     : 0
                            ClusterCapacity       : 189
                            ClusterLoad           : 45
                            ClusterRemainingCapacity : 144
                            NodeBufferPercentage  : 10
                            ClusterBufferedCapacity : 170
                            ClusterRemainingBufferedCapacity : 125
                            ClusterCapacityViolation : False
                            MinNodeLoadValue      : 0
                            MinNodeLoadNodeId     : 3ea71e8e01f4b0999b121abcbf27d74d
                            MaxNodeLoadValue      : 15
                            MaxNodeLoadNodeId     : 2cc648b6770be1bc9824fa995d5b68b1
```

## Próximas etapas
* Para obter informações sobre arquitetura e fluxo de informações no Resource Manager de Cluster, confira [este artigo](service-fabric-cluster-resource-manager-architecture.md)
* Definir as Métricas de Desfragmentação é uma forma de consolidar a carga em nós, em vez de distribuí-la. Para saber como configurar a desfragmentação, leia [este artigo](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* Comece do princípio e [veja uma introdução ao Resource Manager de Cluster do Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
* Para descobrir como o Gerenciador de Recursos de Cluster gerencia e balanceia carga no cluster, confira o artigo sobre [como balancear carga](service-fabric-cluster-resource-manager-balancing.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png

<!---HONumber=AcomDC_0824_2016-->