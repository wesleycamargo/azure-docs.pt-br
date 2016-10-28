<properties
   pageTitle="Escalabilidade de serviços do Service Fabric | Microsoft Azure"
   description="Descreve como dimensionar os serviços de malha do serviço"
   services="service-fabric"
   documentationCenter=".net"
   authors="appi101"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"
   ms.author="aprameyr"/>

# Colocando em escala aplicativos do Service Fabric
O Service Fabric do Azure facilita a compilação de aplicativos escalonáveis balanceando a carga dos serviços, partições e réplicas em todos os nós em um cluster. Isso permite a utilização máxima dos recursos.

Uma alta escala para aplicativos do Service Fabric pode ser obtida de duas maneiras:

1. Dimensionamento no nível da partição

2. Dimensionamento no nível do nome do serviço

## Dimensionamento no nível da partição
A malha de serviço dá suporte ao particionamento de um serviço individual em várias partições menores. A [Visão geral de particionamento](service-fabric-concepts-partitioning.md) fornece informações sobre os tipos de esquemas de particionamento para os quais há suporte. As réplicas de cada partição são distribuídas entre os nós em um cluster. Considere um serviço usando o esquema de particionamento de intervalo com uma chave de baixa de 0, uma chave de alta de 99 e quatro partições. Em um cluster de três nós, o serviço pode ser disposto com quatro réplicas que compartilham os recursos em cada nó, conforme mostrado aqui:

![Layout de partição com três nós](./media/service-fabric-concepts-scalability/layout-three-nodes.png)

Aumentar o número de nós permite ao Service Fabric utilizar os recursos nos novos nós movendo algumas das réplicas para nós vazios. Assim, aumentando o número de nós para quatro, o serviço agora tem três réplicas em execução em cada nó (de diferentes partições) permitindo um melhor desempenho e utilização de recursos.

![Layout de partição com quatro nós](./media/service-fabric-concepts-scalability/layout-four-nodes.png)

## Dimensionamento no nível do nome do serviço
Uma instância de serviço é uma instância específica de um nome de aplicativo e um nome de tipo de serviço (consulte [Ciclo de vida do aplicativo do Service Fabric](service-fabric-application-lifecycle.md)). Durante a criação de um serviço, você especifica o esquema de partição ([Particionamento de serviços do Service Fabric](service-fabric-concepts-partitioning.md)) a ser usado.

O primeiro nível de colocação em escala é por nome de serviço. Você pode criar novas instâncias de um serviço, com níveis diferentes de particionamento, conforme as suas instâncias de serviço mais antigas ficam ocupadas. Isso permite que os consumidores do novo serviço usem instâncias de serviço menos ocupadas, em vez das mais ocupadas.

Uma opção para aumentar a capacidade e também para aumentar ou diminuir a contagem de partições é criar uma nova instância de serviço com um novo esquema de partição. Isso acrescenta complexidade, no entanto, já que os clientes de consumo precisam saber quando e como usar um serviço de nome diferente.

### Cenário de exemplo: datas inseridas
Um possível cenário seria usar informações de data como parte do nome do serviço. Por exemplo, você pode usar uma instância de serviço com um nome específico para todos os clientes que ingressaram em 2013 e outro nome para os clientes que ingressaram em 2014. Esse esquema de nomeação permite um aumento programático nos nomes dependendo da data (conforme 2014 se aproxima, a instância do serviço de 2014 pode ser criada sob demanda).

No entanto, essa abordagem baseia-se em clientes usando informações de nomenclatura específicas do aplicativo que estão fora do escopo do conhecimento do Service Fabric.

- *Usando uma convenção de nomenclatura*: em 2013, quando seu aplicativo é ativado, você cria um serviço chamado fabric:/app/service2013. Perto do segundo trimestre de 2013, você cria outro serviço chamado fabric:/app/service2014. Ambos os serviços são do mesmo tipo de serviço. Nessa abordagem, o cliente precisará fazer uso de lógica sobre como construir o nome de serviço apropriado com base no ano.

- *Usando um serviço de pesquisa*: Outro padrão é fornecer um serviço de pesquisa secundário, que pode fornecer o nome do serviço para uma chave desejada. Em seguida, novas instâncias de serviço podem ser criadas pelo serviço de pesquisa. O próprio serviço de pesquisa não retém nenhum dados do aplicativo, somente os dados sobre os nomes de serviço que ele cria. Assim, para o exemplo baseado no ano acima, o cliente primeiro entraria em contato com o serviço de pesquisa para descobrir o nome do serviço de gerenciamento de dados para um determinado ano e, em seguida, usaria esse nome de serviço para executar a operação em si. O resultado da primeira pesquisa pode ser armazenado em cache.

## Próximas etapas

Para obter informações sobre os conceitos do Service Fabric, consulte o seguinte:

- [Disponibilidade dos serviços de malha do serviço](service-fabric-availability-services.md)

- [Particionando serviços da Malha do Serviço](service-fabric-concepts-partitioning.md)

- [Definindo e gerenciando o estado](service-fabric-concepts-state.md)

<!---HONumber=AcomDC_0810_2016-->