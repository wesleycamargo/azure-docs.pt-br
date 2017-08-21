---
title: "Descrição de cluster do Balanceador de Recursos | Microsoft Docs"
description: "Descrever um cluster do Service Fabric especificando domínios de falha, domínios de atualização, propriedades de nó e capacidades de nó para o Cluster Resource Manager."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 55f8ab37-9399-4c9a-9e6c-d2d859de6766
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: cfc38cecfaf0a0bdaae043fc35dcfed743459823
ms.contentlocale: pt-br
ms.lasthandoff: 07/11/2017

---

# <a name="describing-a-service-fabric-cluster"></a>Descrevendo um cluster do Service Fabric
O Gerenciador de Recursos de Cluster do Service Fabric fornece vários mecanismos para descrever um cluster. Durante o tempo de execução, o Cluster Resource Manager usa essas informações para garantir a alta disponibilidade dos serviços executados no cluster. Ao aplicar essas regras importantes, ele também tenta otimizar o consumo de recursos do cluster.

## <a name="key-concepts"></a>Principais conceitos
Os recursos do Gerenciador de Recursos de Cluster oferecem suporte a vários recursos que descrevem um cluster:

* Domínios de falha
* Domínios de atualização
* Propriedades de nó
* Capacidades de nó

## <a name="fault-domains"></a>Domínios de falha
Um domínio de falha é qualquer área da falha coordenada. Um único computador é um domínio de falha (já que ele pode falhar sozinho por vários motivos diferentes, desde falhas de fornecimento de energia até falhas de unidade devido a firmware NIC inválido). Computadores conectados ao mesmo comutador Ethernet estão no mesmo domínio de falha, assim como os computadores que compartilham uma única fonte de alimentação. Uma vez que é natural que essas falhas de hardware se sobreponham, os domínios de falha são hierárquicos por natureza e são representados como URIs no Service Fabric.

Ao configurar seu próprio cluster, você precisa considerar essas diferentes áreas de falha. É importante que os domínios de falha sejam definidos corretamente uma vez que o Service Fabric usa essas informações para posicionar os serviços com segurança. O Service Fabric não quer posicionar os serviços de modo que a perda de um domínio de falha (causado por uma falha de algum componente) faça com que os serviços fiquem inativos. No ambiente do Azure, o Service Fabric usa as informações do domínio de falha fornecidas pelo ambiente para configurar corretamente os nós no cluster em seu nome.

Na imagem abaixo, colorimos todas as entidades que contribuem para os domínios de falha e listamos todos os diferentes domínios de falha resultantes. Neste exemplo, temos datacenters (“DC”), racks (“R”) e folhas (“B”). Em termos conceituais, se cada folha contém mais de uma máquina virtual, pode haver outra camada na hierarquia de domínios de falha.

<center>
![Nós organizados por meio de domínios de falha][Image1]
</center>

Durante o tempo de execução, o Cluster Resource Manager do Service Fabric considera os domínios de falha no cluster e planeja o layout.  Ele tenta distribuir as réplicas com estado ou instâncias sem monitoração de estado para um determinado serviço para que elas fiquem em domínios de falha separados. Esse processo ajuda a garantir que se houver uma falha de qualquer domínio de falha (em qualquer nível da hierarquia), a disponibilidade do serviço não seja comprometida.

O Cluster Resource Manager do Service Fabric não se importa com quantas camadas existem na hierarquia de domínio de falha. No entanto, ele tenta assegurar que a perda de qualquer parte da hierarquia não afete os serviços em execução nela. Por isso, é melhor se houver o mesmo número de nós em cada nível de profundidade na hierarquia de domínio de falha. Manter os níveis balanceados impede que uma parte da hierarquia contenha mais serviços que outras. Fazer de outra maneira contribuiria para desequilíbrios na carga de nós individuais e tornaria a falha de certos domínios mais crítica do que de outros.

Se a "árvore" de domínios de falha estiver desequilibrada no cluster, será mais difícil para o Cluster Resource Manager calcular a melhor alocação dos serviços. Layouts de domínios de falha desequilibrados significam que a perda de um determinado domínio pode afetar a disponibilidade do cluster mais do que outros. Como resultado, o Cluster Resource Manager fica dividido entre seus dois objetivos: ele deve usar os computadores nesse domínio “pesado” colocando serviços neles e ele deseja posicionar os serviços de forma que a perda de um domínio não cause problemas. Como é a aparência disso?

<center>
![Dois layouts de cluster diferentes][Image2]
</center>

No diagrama acima, mostramos dois exemplos diferentes de layouts de cluster. No primeiro exemplo os nós são distribuídos uniformemente entre os domínios de falha. No outro um domínio de falha acaba com muitos mais nós. Se você vier a criar seu próprio cluster local ou em outro ambiente, é algo em que deverá pensar.

No Azure, a escolha de qual domínio de falha contém um nó é gerenciada por você. No entanto, dependendo do número de nós provisionados, você ainda pode ficar com domínios de falha com mais nós do que outros. Por exemplo, digamos que você tem cinco domínios de falha, mas provisiona sete nós para um determinado NodeType. Nesse caso, os dois primeiros domínios de falha ficam com mais nós. Se você continuar a implantar mais NodeTypes com apenas algumas instâncias, o problema piorará.

## <a name="upgrade-domains"></a>Domínios de atualização
Os Domínios de Atualização são um outro recurso que ajuda o Cluster Resource Manager do Service Fabric a entender o layout do cluster para que possa planejar antecipadamente como lidar com falhas. Os domínios de atualização definem conjuntos de nós que são atualizados ao mesmo tempo.

Os Domínios de Atualização são muito semelhantes aos Domínios de Falha, mas com algumas diferenças importantes. Primeiro, enquanto os domínios de falha são rigorosamente definidos pelas áreas de falhas de hardware coordenadas, os domínios de atualização são definidos pela política. Com os domínios de atualização, você precisa decidir quantos deseja em vez de ser determinado pelo ambiente. Outra diferença é que (atualmente, pelo menos) os domínios de atualização não são hierárquicos: eles são mais semelhantes a uma marcação simples.

O diagrama a seguir mostra que três domínios de atualização são distribuídos em três domínios de falha. Ele também mostra um possível posicionamento para três réplicas diferentes de um serviço com estado, em que cada um fica em domínios de atualização e de falha diferentes. Esse posicionamento permite perder um domínio de falha durante uma atualização de serviço e ainda ter uma cópia dos códigos e dos dados.

<center>
![Posicionamento com domínios de falha e atualização][Image3]
</center>

Há prós e contras em relação a ter um grande número de domínios de atualização. A vantagem é que cada etapa da atualização é mais granular e, assim, afeta um número menor de nós ou serviços. Isso resulta em menos serviços precisando ser movidos de cada vez, introduzindo menos variação no sistema. Isso tende a aumentar a confiabilidade também (já que uma parte menor do serviço é afetada por qualquer problema introduzido durante a atualização). Mais domínios de atualização também significa que você precisa de menos sobrecarga disponível em outros nós para lidar com o impacto da atualização. Por exemplo, se você tiver cinco domínios de atualização, os nós em cada um estarão lidando com aproximadamente 20% do tráfego. Se você precisar desativar esse domínio de atualização para uma atualização, essa carga precisará ir para algum lugar. Mais domínios de atualização significa menos sobrecarga que deve ser mantida nos outros nós no cluster.

A desvantagem de ter vários domínios de atualização é que as atualizações tendem a levar mais tempo. O Service Fabric aguarda um curto período de tempo após a conclusão de um domínio de atualização antes de continuar. Esse atraso é para que os problemas introduzidos pela atualização possam aparecer e ser detectados. Essa compensação é aceitável, pois isso evita que alterações incorretas afetem grande parte do serviço de uma só vez.

Ter um número reduzido de domínios de atualização tem seus próprios efeitos colaterais: enquanto cada domínio de atualização individual está inoperante e é atualizado, uma grande parte de sua capacidade geral fica indisponível. Por exemplo, se tiver apenas três domínios de atualização, você desativará cerca de um terço de sua capacidade geral de serviço ou de cluster de uma vez. Ter uma parte tão grande do seu serviço inoperante ao mesmo tempo não é desejável, uma vez que é necessário ter capacidade suficiente no restante do cluster para lidar com a carga de trabalho. Manter esse buffer significa que no caso normal esses nós são menos carregados que eles seriam de outra forma, aumentando o custo de execução do seu serviço.

Não há um limite real para o número total de domínios de falha ou de atualização em um ambiente nem restrições sobre como eles se sobrepõem. As estruturas comuns que vimos são:

* Domínios de falha e domínios de atualização mapeados 1:1
* Um domínio de atualização por nó (instância de sistema operacional física ou virtual)
* Um modelo "distribuído" ou de "matriz" em que os domínios de falha e os domínios de atualização formam uma matriz em que os computadores geralmente são executados diagonalmente

<center>
![Layouts de domínios de falha e de atualização][Image4]
</center>

Não existe uma solução ideal sobre qual layout deve ser escolhido. Cada um deles tem prós e contras. Por exemplo, o modelo de 1FD:1UD é bastante simples de configurar. O modelo de 1 UD por Nó é mais parecido com o que as pessoas costumavam usar ao gerenciar pequenos conjuntos de computadores no passado, em que cada um era desativado independentemente.

O modelo mais comum (e o usado no Azure) é a matriz de FD/UD, em que os FDs e os UDs formam uma tabela e os nós são posicionados começando ao longo da diagonal. Se isso fica compactado ou esparso depende do número total de nós em comparação com o número de FDs e UDs. Em outras palavras, para clusters suficientemente grandes, quase tudo acaba ficando parecido com o padrão de matriz denso, mostrado na opção inferior direita da imagem acima.

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Restrições de domínio de falha e de atualização e o comportamento resultante
O Cluster Resource Manager trata o desejo de manter um serviço balanceado entre domínios de falha e de atualização como uma restrição. Você pode encontrar mais informações sobre restrições [neste artigo](service-fabric-cluster-resource-manager-management-integration.md). O estado das restrições do domínio de falha e de atualização: “para uma partição de serviço específica, nunca deverá haver uma diferença *maior que um* no número de objetos de serviço (instâncias de serviço sem estado ou réplicas de serviço com estado) entre dois domínios”.  Isso significa na prática que para um determinado serviço certos movimentos ou disposições podem não ser válidas, porque ele poderia violar as restrições de domínio de falha ou de atualização.

Vejamos um exemplo. Digamos que temos um cluster com seis nós, configurados com cinco domínios de falha e cinco domínios de atualização.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

Agora digamos que criamos um serviço com um TargetReplicaSetSize de cinco. As réplicas recaem sobre N1 a N5. Na verdade, N6 nunca será usado, independentemente de quantos serviços que você criar. Mas por quê? Vamos observar a diferença entre o layout atual e o que aconteceria se N6 fosse escolhido.

Aqui está o nosso layout e o número total de réplicas por domínio de falha e de atualização:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Este layout é balanceado em termos de nós por domínio de falha e domínio de atualização. Ele também é balanceado em termos de número de réplicas por domínio de falha e de atualização. Cada domínio possui o mesmo número de nós e o mesmo número de réplicas.

Agora, vamos ver o que aconteceria se tivéssemos usado N6 em vez de N2. Como as réplicas seriam distribuídas?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

Observou algo? Esse layout viola nossa definição para a restrição de domínio de falha. FD0 tem duas réplicas, enquanto FD1 tem zero, tornando a diferença entre FD0 e FD1 um total de dois. O Cluster Resource Manager não permite essa disposição. Da mesma forma, se escolhêssemos N2 e N6 (em vez de N1 e N2), teríamos:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Embora esse layout seja equilibrado em termos de domínios de falha, agora ele viola a restrição do domínio de atualização (uma vez que UD0 tem zero réplicas enquanto UD1 tem duas). Esse layout também é inválido

## <a name="configuring-fault-and-upgrade-domains"></a>Configurando domínios de falha e atualização
A definição de domínios de falha e domínios de atualização é feita automaticamente em implantações hospedadas do Azure Service Fabric. O Service Fabric seleciona e usa as informações de ambiente do Azure.

Se estiver criando seu próprio cluster (ou desejar executar uma topologia específica no desenvolvimento), você mesmo fornecerá as informações de domínio de falha e de domínio de atualização. Neste exemplo, definimos um cluster de desenvolvimento local de nove nós que abrange três "datacenters" (cada um com três racks). Este cluster também tem três domínios de atualização distribuídos entre esses três datacenters. No modelo do manifesto do cluster, ele é semelhante a:

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

por meio de ClusterConfig.json para implantações autônomas

```json
"nodes": [
  {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm3",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm4",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm5",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm6",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm7",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm8",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm9",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD3"
  }
],
```

> [!NOTE]
> Em implantações do Azure, os domínios de falha e os domínios de atualização são atribuídos pelo Azure. Portanto, a definição dos seus nós e funções na opção infraestrutura do Azure não inclui informações sobre o domínio de falha ou o domínio de atualização.
>
>

## <a name="placement-constraints-and-node-properties"></a>Restrições de posicionamento e propriedades do nó
Às vezes (na verdade, na maioria das vezes), convém assegurar que determinadas cargas de trabalho sejam executadas apenas em alguns nós ou em certos conjuntos de nós no cluster. Por exemplo, algumas cargas de trabalho podem exigir GPUs ou SSDs, enquanto outras, não. Um ótimo exemplo de direcionamento de hardware para cargas de trabalho específicas é praticamente toda arquitetura de n camadas por aí. Nessas arquiteturas determinados computadores servem como o lado de serviço do front-end/interface do aplicativo (e, portanto, provavelmente são expostos à Internet). Diferentes conjuntos de computadores (normalmente com recursos de hardware diferentes) tratam do trabalho das camadas de computação ou armazenamento (e geralmente não são expostos à Internet). O Service Fabric espera que, mesmo em um mundo de microsserviços, haja casos em que cargas de trabalho específicas precisem ser executadas em configurações de hardware específicas, por exemplo:

* um aplicativo de n camadas existente foi "transferido e posicionado" em um ambiente do Service Fabric
* uma carga de trabalho deseja ser executada em um hardware específico por motivos de desempenho, escala ou isolamento de segurança
* Uma carga de trabalho deve ser isolada de outras cargas de trabalho por motivos de política ou consumo de recursos

Para dar suporte a esses tipos de configurações, o Service Fabric tem uma noção avançada das marcas que podem ser aplicadas aos nós. Elas são chamadas de restrições de posicionamento. As restrições de posicionamento podem ser usadas para indicar onde determinados serviços devem ser executados. O conjunto de restrições é extensível, qualquer par chave-valor pode funcionar.

<center>
![Cargas de trabalho diferentes do layout do cluster][Image5]
</center>

As diferentes marcas de chave-valor em nós são conhecidas como *propriedades* de posicionamento do nó (ou apenas propriedades do nó). O valor especificado na propriedade do nó pode ser uma cadeia de caracteres, bool ou signed long. A instrução no serviço é chamada de uma *restrição* de posicionamento, uma vez que ela restringe onde o serviço pode ser executado no cluster. A restrição pode ser qualquer instrução booliana que opera sobre as diferentes propriedades de nó no cluster. Os seletores válidos nessas instruções boolianas são:

1) verificações condicionais para a criação de instruções

| Instrução | Sintaxe |
| --- |:---:|
| "igual a" | "==" |
| "diferente de" | "!=" |
| "maior que" | ">" |
| "maior ou igual a" | ">=" |
| "menor que" | "<" |
| "menor ou igual a" | "<=" |

2) instruções boolianas para operações de agrupamento e lógicas

| Instrução | Sintaxe |
| --- |:---:|
| "e" | "&&" |
| "ou" | "&#124;&#124;" |
| "não" | "!" |
| "agrupar como instrução única" | "()" |

Aqui estão alguns exemplos de instruções de restrição básicas.

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

Somente nós em que a instrução geral é avaliada como "True" podem ter o serviço colocado nos mesmos. Os nós que não têm uma propriedade definida não correspondem a nenhuma restrição de posicionamento que contém essa propriedade.

O Service Fabric define algumas propriedades de nó padrão que podem ser usadas automaticamente sem que o usuário precise defini-las. Neste artigo, as propriedades padrão definidas em cada nó são **NodeType** e **NodeName**. Por exemplo, você poderia escrever uma restrição de posicionamento como `"(NodeType == NodeType03)"`. Em geral, descobrimos que NodeType é uma das propriedades mais comumente usadas. Ela é útil, pois corresponde 1:1 a um tipo de computador, que, por sua vez, corresponde a um tipo de carga de trabalho em uma arquitetura de aplicativo de n camadas tradicional.

<center>
![Restrições de posicionamento e propriedades do nó][Image6]
</center>

Vamos supor que as seguintes propriedades de nó foram definidas para um tipo de nó específico:

ClusterManifest.xml

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

via ClusterConfig.json para implantações Autônomas ou Template.json para clusters hospedados pelo Azure. Em seu modelo do Azure Resource Manager para um cluster, itens como o nome do tipo de nó provavelmente são parametrizados e teriam uma aparência semelhante a "[parameters('vmNodeType1Name')]" em vez de "NodeType01".

```json
"nodeTypes": [
    {
        "name": "NodeType01",
        "placementProperties": {
            "HasSSD": "true",
            "NodeColor": "green",
            "SomeProperty": "5"
        },
    }
],
```

Você pode criar *restrições* de posicionamento de serviço para um serviço da seguinte forma:

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

Se você tiver certeza de que todos os nós de NodeType01 são válidos, também poderá selecionar esse tipo de nó.

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

As propriedades em um nó são definidas por meio da definição de cluster e, portanto, não podem ser atualizadas sem uma atualização do cluster. A atualização das propriedades do nó e requer que cada nó afetado seja desativado e, depois, seja novamente ativado.

## <a name="capacity"></a>Capacidade
Um dos trabalhos mais importantes de qualquer orquestrador é ajudar a gerenciar o consumo de recursos no cluster. A última coisa que você deseja se estiver tentando executar serviços de forma eficiente é um monte de nós com muito interesse enquanto outros estão sem interesse. Os nós com muito interesse levam à contenção de recurso e desempenho ruim e os nós sem interesse representam recursos desperdiçados/aumento do custo. Antes de falarmos sobre balanceamento, que tal apenas garantir que os nós não fiquem sem recursos em primeiro lugar?

O Service Fabric representa recursos como `Metrics`. As métricas são qualquer recurso lógico ou físico que você queira descrever para o Service Fabric. Exemplos de métricas são itens como "WorkQueueDepth" ou "MemoryInMb". Para obter informações sobre como configurar as métricas e seus usos, consulte [este artigo](service-fabric-cluster-resource-manager-metrics.md)

As métricas são diferentes das restrições de posicionamento e das propriedades de nó. As propriedades de nó são descritores estáticos dos próprios nós, enquanto as métricas se referem a recursos que os nós têm e que os serviços consomem quando são executados em um nó. Uma propriedade do nó pode ser "HasSSD" e pode ser definida como true ou false. A quantidade de espaço disponível no SSD (e consumida pelos serviços) seria uma métrica como "DriveSpaceInMb". O nó teria sua capacidade de "DriveSpaceInMb" para a quantidade total de espaço não reservado na unidade. Os serviços informariam quanto a métrica eles usaram durante o tempo de execução.

É importante observar que assim como as restrições de posicionamento e as propriedades de nó, o Cluster Resource Manager do Service Fabric não entende o que os nomes das métricas significam. Os nomes de métrica são apenas cadeias de caracteres. É uma boa prática declarar as unidades como parte dos nomes de métrica que você criar quando puderem ser ambíguos.

Se você desativasse todo o *balanceamento* de recursos, o Cluster Resource Manager do Service Fabric ainda tentaria garantir que nenhum nó ficasse acima de sua capacidade. Geralmente isso é possível, a menos que o cluster como um todo esteja muito cheio. A capacidade é outra *restrição* que usa o Cluster Resource Manager para entender o quanto um nó tem de um recurso. A capacidade restante também é rastreada para o cluster como um todo. A capacidade e o consumo no nível de serviço são expressos em termos de métricas. Então, por exemplo, a métrica poderia ser "MemoryInMb" e um determinado nó poderia ter a capacidade de "MemoryInMb" 2048. Algum serviço em execução no nó pode dizer que ele está consumindo atualmente 64 de "MemoryInMb".

Durante o tempo de execução, o Cluster Resource Manager controla a quantidade de cada recurso que está presente em cada nó e a quantidade restante. Ele faz isso subtraindo qualquer uso declarado de cada serviço em execução no nó da capacidade do nó. Com essas informações, o Cluster Resource Manager do Service Fabric pode descobrir onde inserir ou mover réplicas para que os nós não ultrapassem a capacidade.

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
<center>
![Capacidade e nós de cluster][Image7]
</center>

Você pode ver as capacidades definidas no manifesto do cluster:

ClusterManifest.xml

```xml
    <NodeType Name="NodeType02">
      <Capacities>
        <Capacity Name="MemoryInMb" Value="2048"/>
        <Capacity Name="DiskInMb" Value="512000"/>
      </Capacities>
    </NodeType>
```

via ClusterConfig.json para implantações Autônomas ou Template.json para clusters hospedados pelo Azure. Em seu modelo do Azure Resource Manager para um cluster, itens como o nome do tipo de nó provavelmente são parametrizados e teriam uma aparência semelhante a "[parameters('vmNodeType2Name')]" em vez de "NodeType02".

```json
"nodeTypes": [
    {
        "name": "NodeType02",
        "capacities": {
            "MemoryInMb": "2048",
            "DiskInMb": "512000"
        }
    }
],
```

Também é possível (e comum na verdade) que a carga de um serviço seja alterada dinamicamente. Digamos que a carga de uma réplica é alterada de 64 para 1024, mas o nó em que ela estava em execução tinha somente 512 (da métrica "MemoryInMb") restantes. Agora essa réplica ou posicionamento da instância é inválido, pois não há espaço suficiente no nó. Isso também pode ocorrer se o uso combinado de réplicas e instâncias no nó excede a capacidade do nó. Em ambos os casos o Cluster Resource Manager precisou entrar em ação e colocar o nó abaixo da capacidade novamente. Ele faz isso movendo uma ou mais réplicas ou instâncias desse nó para nós diferentes. Ao mover réplicas, o Cluster Resource Manager tenta minimizar o custo dessas movimentações. O custo da movimentação é discutido [neste artigo](service-fabric-cluster-resource-manager-movement-cost.md).

## <a name="cluster-capacity"></a>Capacidade do cluster
Então, como impedir que o cluster geral fique muito cheio? Bem, com carga dinâmica não há muito que o Cluster Resource Manager possa fazer. Os serviços podem ter seu pico de carga independentemente das ações executadas pelo Cluster Resource Manager. Como resultado, o cluster com bastante reserva dinâmica hoje pode não ter espaço suficiente quando você ficar famoso amanhã. Dito isso, há alguns controles que são embutidas para evitar problemas básicos. A primeira coisa a fazer é evitar a criação de novas cargas de trabalho que fariam com que o cluster ficasse cheio.

Digamos que você crie um serviço sem estado e haja alguma carga associada a ele (falaremos mais sobre relatórios de carga padrão e dinâmica posteriormente). Digamos que o serviço se preocupa com a métrica "DiskSpaceInMb". Digamos também que ele consumirá cinco unidades de "DiskSpaceInMb" para cada instância do serviço. Você deseja criar três instâncias do serviço. Ótimo! Isso significa que é preciso que 15 unidades de "DiskSpaceInMb" estejam presentes no cluster para podermos criar essas instâncias de serviço. O Cluster Resource Manager está calculando continuamente a capacidade total e o consumo de cada métrica, portanto ele pode determinar facilmente se há espaço suficiente no cluster. Se não há espaço suficiente, o Cluster Resource Manager rejeita a chamada de serviço de criação.

Uma vez que o requisito é apenas que haja 15 unidades disponíveis, esse espaço poderia ser alocado de várias maneiras diferentes. Por exemplo, poderia haver uma unidade restante de capacidade em 15 nós diferentes ou três unidades restantes de capacidade em cinco nós diferente. Contanto que o Cluster Resource Manager possa reorganizar as coisas para que haja cinco unidades disponíveis em três nós, ele eventualmente posicionará o serviço. Essa reorganização quase sempre é possível, a menos que o cluster como um todo esteja quase totalmente cheio, os serviços sejam todos muito “pesados” ou ambos.

## <a name="buffered-capacity"></a>Capacidade de buffer
Outro recurso que o Cluster Resource Manager tem que ajuda a gerenciar a capacidade de clister geral é a noção de buffer reservado para a capacidade especificada em cada nó. A capacidade em buffer permite a reserva de uma parte da capacidade total do nó para que ela seja usada apenas para posicionar os serviços durante atualizações e falhas do nó. Atualmente, o buffer é especificado globalmente por métrica para todos os nós por meio da definição do cluster. O valor que você escolher para a capacidade reservada é uma função do número de domínios de falha e de atualização no cluster e quanta sobrecarga que deseja. Mais domínios de falha e de atualização significa que você pode escolher um número menor para a capacidade em buffer. Se você tiver mais domínios, poderá esperar que quantidades menores de cluster fiquem indisponíveis durante atualizações e falhas. A especificação do percentual do buffer só fará sentido se você também tiver especificado a capacidade do nó para uma métrica.

Aqui está um exemplo de como especificar a capacidade do buffer:

ClusterManifest.xml

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="DiskSpace" Value="0.10" />
            <Parameter Name="Memory" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

via ClusterConfig.json para implantações Autônomas ou Template.json para clusters hospedados pelo Azure:

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "DiskSpace",
          "value": "0.10"
      },
      {
          "name": "Memory",
          "value": "0.15"
      },
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      }
    ]
  }
]
```

A criação de novos serviços falha quando o cluster está sem capacidade em buffer para uma métrica. Isso garante que o cluster retém sobrecarga sobressalente suficiente para que as atualizações e falhas não façam com que os nós fiquem acima da capacidade. A capacidade em buffer é opcional, mas é recomendada em qualquer cluster que define uma capacidade para uma métrica.

O Cluster Resource Manager expõe essas informações por meio do PowerShell e das APIs de consulta. Isso permite ver as configurações de capacidade em buffer, a capacidade total e o consumo atual para cada métrica em uso no cluster. Aqui, vemos um exemplo dessa saída:

```posh
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2016 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2016 12:54:59 AM
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

## <a name="next-steps"></a>Próximas etapas
* Para obter informações sobre arquitetura e o fluxo de informações no Cluster Resource Manager, confira [este artigo ](service-fabric-cluster-resource-manager-architecture.md)
* Definir as Métricas de Desfragmentação é uma forma de consolidar a carga em nós, em vez de distribuí-la. Para saber como configurar a desfragmentação, leia [este artigo](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* Comece do princípio e [veja uma introdução ao Resource Manager de Cluster do Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
* Para descobrir como o Gerenciador de Recursos de Cluster gerencia e balanceia carga no cluster, confira o artigo sobre [como balancear carga](service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png

