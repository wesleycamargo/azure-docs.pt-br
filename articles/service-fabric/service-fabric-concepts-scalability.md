<properties
   pageTitle="Escalabilidade dos serviços da malha de serviço"
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
   ms.date="08/26/2015"
   ms.author="aprameyr"/>

# Dimensionamento de aplicativos da malha de serviço
A malha de serviços facilita a criação de aplicativos escalonáveis balanceando a carga dos serviços, partições e réplicas em todos os nós em um cluster. Isso permite a utilização máxima dos recursos.

Uma alta escala para aplicativos de malha do serviço pode ser obtida de duas maneiras:

1. Dimensionamento no nível da partição

2. Dimensionamento no nível do nome do serviço

## Dimensionamento no nível da partição
A malha de serviço dá suporte ao particionamento de um serviço individual em várias partições menores. A [Visão geral de particionamento](service-fabric-concepts-partitioning.md) fornece informações sobre os tipos de esquemas de particionamento que possuem suporte. As réplicas de cada partição são distribuídas entre os nós no cluster. Considere um serviço usando o esquema de particionamento de intervalo com uma chave de baixa da chave de alta 0, das partições 4 e 99. Em um cluster de 3 nós, o serviço pode ser disposto com quatro réplicas que compartilham os recursos em cada nó, conforme mostrado abaixo.

![Layout de partição com três nós](./media/service-fabric-concepts-scalability/layout-three-nodes.png)

Aumentar o número de nós permitirá à malha de serviço utilizar os recursos nos novos nós movendo algumas das réplicas para nós vazios. Assim, aumentar o número de nós para quatro resultará em três réplicas em execução em cada nó (de diferentes partições) permitindo um melhor desempenho e utilização de recursos.

![Layout de partição com quatro nós](./media/service-fabric-concepts-scalability/layout-four-nodes.png)

## Dimensionamento no nível do nome do serviço
Uma instância de serviço é uma instância nomeada específica de um nome de aplicativo e um nome de tipo de serviço (consulte [Ciclo de vida do aplicativo da malha de serviço](service-fabric-application-lifecycle.md)). É durante a criação de serviços que você especifica o esquema de partição ([Particionamento de serviços da malha de serviço](service-fabric-concepts-partitioning.md)) a ser usado.

O primeiro nível de dimensionamento é por nomes de serviço. Você pode criar novas instâncias de um serviço, com um nível diferente de particionamento, conforme as suas instâncias de serviço mais antigas ficam ocupadas. Isso permite que os consumidores do novo serviço usem instâncias de serviços menos ocupados em favor dos mais ocupados.

Uma opção para aumentar a capacidade, ou para aumentar ou diminuir o número de partição é criar uma nova instância de serviço com um novo esquema de partição. A complexidade é que os clientes de consumo precisam saber quando e como usar um serviço de nome diferente.

### Cenário de exemplo – datas inseridas
Um possível cenário seria usar informações de data como parte do nome do serviço. Por exemplo, você pode usar a instância de serviço com um nome específico para todos os clientes que ingressaram em 2013 e um outro nome para os clientes que ingressaram em 2014. Esse esquema de nomeação permite um aumento programático nos nomes dependendo da data (assim como as abordagens de 2014, a instância do serviço de 2014 pode ser criada sob demanda).

No entanto, essa abordagem baseia-se em clientes usando informações de nomenclatura específicas do aplicativo que estão fora do escopo do conhecimento de malha do serviço.

- *Usando uma convenção de nomenclatura*: Em 2013 quando seu aplicativo for ativado, você cria um serviço chamado fabric:/app/service2013. Para o segundo trimestre de 2013, você cria outro serviço chamado fabric:/app/service2014. Ambos os serviços são do mesmo tipo de serviço. Nessa abordagem, o cliente precisa ter lógica sobre como construir o nome de serviço apropriado com base no ano.

- *Usando um serviço de pesquisa*: Outro padrão é fornecer um "serviço de pesquisa" secundário, que pode fornecer o nome do serviço para uma chave desejada. Em seguida, novas instâncias de serviço podem ser criadas pelo serviço de pesquisa. O próprio serviço de pesquisa não retém nenhum dados do aplicativo, mas somente os dados sobre os nomes de serviço que ele cria. Assim, no mesmo exemplo baseado no ano que o acima cliente primeiro entraria em contato com o serviço de pesquisa para descobrir o nome do serviço de gerenciamento de dados para um determinado ano e, em seguida, usaria esse nome de serviço para executar a operação em si. O resultado da primeira pesquisa pode ser armazenado em cache.

## Próximas etapas

Para obter informações sobre os conceitos de malha do serviço, consulte:

- [Disponibilidade dos serviços de malha do serviço](service-fabric-availability-services.md)

- [Particionamento dos serviços da malha de serviço](service-fabric-concepts-partitioning.md)

- [Definir e gerenciar o estado](service-fabric-concepts-state.md)
 

<!---HONumber=Oct15_HO3-->