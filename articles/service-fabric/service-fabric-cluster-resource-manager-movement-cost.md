<properties
   pageTitle="Gerenciador de Recursos de Cluster do Service Fabric – custo de movimento | Microsoft Azure"
   description="Visão geral do custo das movimentações para os Serviços do Service Fabric"
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
   ms.date="03/10/2016"
   ms.author="masnider"/>

# Custo de movimento do serviço que influencia as escolhas do Gerenciador de Recursos
Outro fator importante que levamos em consideração durante a tentativa de determinar quais alterações devem ser feitas em um cluster e a pontuação de determinada solução é o Custo geral de alcançar essa solução.

Mover instâncias ou réplicas de serviços custa, no mínimo, tempo de CPU e largura de banda de rede, e para os serviços com estado, também custa a quantidade de espaço em disco necessária para a criação de uma cópia do estado antes de desligar réplicas antigas. Obviamente, você gostaria de minimizar o custo de qualquer solução criada pelo Gerenciador de Recursos, mas não deseja ignorar soluções que melhorem significativamente a alocação de recursos no cluster.

O Gerenciador de Recursos do Service Fabric tem duas maneiras de calcular custos e limitá-los. A primeira é que, quando ele planeja um novo layout para o cluster, ele conta cada uma das movimentações que faria. Em um caso simples, se você obtiver duas soluções com praticamente o mesmo saldo geral (pontuação) no final, escolha aquela com o menor custo (número total de movimentações). Isso funciona muito bem, até que nos deparamos com o mesmo problema que tivemos com cargas padrão ou estáticas – é improvável que em qualquer sistema complexo todas as movimentações sejam iguais; provavelmente, algumas serão muito mais caras.

## Alterando o custo de movimento de uma réplica e fatores a serem considerados
Assim como ao relatar carga (outro recurso do Gerenciador de Recursos de Cluster), fornecemos ao serviço uma forma de reportar automaticamente quão caro é o serviço de movimento em um dado momento.

Código

```csharp
this.ServicePartition.ReportMoveCost(MoveCost.Medium);
```

O MoveCost tem quatro níveis: Zero, Baixo, Médio e Alto. Mais uma vez, isso refere-se apenas a relação entre eles, com exceção de Zero, que significa que a movimentação de uma réplica é gratuita e não deve contar na pontuação da solução. Definir o custo de movimentação para Alto não é uma garantia de que a réplica não será movida, apenas que não a moveremos, a menos que encontremos um bom motivo para isso.

![Custo das movimentações como um fator na seleção de réplicas para movimentação][Image1]

O MoveCost nos ajuda a encontrar as soluções que causam, em geral, o mínimo de interrupções, ao mesmo tempo que alcançam um equilíbrio equivalente. A noção de custo de um serviço pode ser relativa a muitas coisas, mas as coisas mais comuns são:

1.	A quantidade de estados ou dados que o serviço deve mover
2.	O custo de desconexão de clientes (para que o custo de movimentação de uma réplica Primária seja mais alto do que o de uma réplica Secundária)
3.	O custo de interrupção de alguma operação em andamento (algumas operações no nível de armazenamento de dados são caras e, depois de determinado ponto, não será conveniente anulá-las se não houver necessidade). Portanto, durante a operação, você aumenta o custo para reduzir a probabilidade de que a réplica ou a instância de serviço seja movida e, em seguida, quando a operação for concluída, você a coloca novamente no estado normal.

## Próximas etapas
- As métricas são como o Gerenciador de Recursos de Cluster do Service Fabric gerencia o consumo e a capacidade no cluster. Para saber mais sobre elas e como configurá-las, confira [este artigo](service-fabric-cluster-resource-manager-metrics.md)
- Para descobrir como o Gerenciador de Recursos de Cluster gerencia e balanceia carga no cluster, confira o artigo sobre [como balancear carga](service-fabric-cluster-resource-manager-balancing.md).

[Image1]: ./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png

<!---HONumber=AcomDC_0316_2016-->