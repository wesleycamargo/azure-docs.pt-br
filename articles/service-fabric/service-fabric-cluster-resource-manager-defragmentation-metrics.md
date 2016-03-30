<properties
   pageTitle="Empacotamento proativo de métricas | Microsoft Azure"
   description="Uma visão geral de como usar o empacotamento proativo de métricas (desfragmentação) no Service Fabric"
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

# Desfragmentação de métricas e carga no Service Fabric
O Gerenciador de Recursos de Cluster do Service Fabric está basicamente relacionado ao balanceamento em termos de distribuição da carga, garantindo que todos os nós sejam igualmente utilizados. Normalmente, esse é o layout mais seguro e inteligente em termos de sobrevivência a falhas, já que ele assegura que uma determinada falha não destrua grande parte de uma carga de trabalho. O Gerenciador de Recursos do Service Fabric também dá suporte a uma estratégia diferente, que é a desfragmentação. Em geral, a desfragmentação significa que, em vez de tentar distribuir a utilização de uma métrica em todo o cluster, devemos, de fato, tentar consolidá-la. Isso realmente nem era muito difícil para implementarmos – em vez de aumentar a pontuação de uma solução com base na minimização do desvio padrão médio da carga da métrica para determinada métrica, nós a diminuímos (em outras palavras, começamos a otimizar para conseguir aumentos no desvio, em vez de tentar minimizá-los). Mas por que você usaria essa estratégia?

Bem, caso tenha distribuído a carga de maneira uniforme entre os nós no cluster, você já terá consumido alguns dos recursos que os nós têm a oferecer. Normalmente, isso não é um problema, mas, às vezes, algumas cargas de trabalho criam serviços que são excepcionalmente grandes e que consomem grande parte de um nó – digamos que 75% a 95% dos recursos de um nó acabariam sendo dedicados a um único serviço. Isso não é um problema: na criação, o Gerenciador de Recursos detectará que ele precisa reorganizar o cluster, a fim de liberar espaço para essa carga de trabalho grande e definir como fará com que isso aconteça; contudo, enquanto isso, a carga de trabalho tem de esperar.

Considerando que o agendamento de novas cargas de trabalho, geralmente, é, pelo menos, um pouco sensível à latência, se não fizermos nada diferente, às vezes, poderemos negligenciar os SLAs caso haja vários serviços e estados para mover. Na verdade, quando medimos os tempos de criação em simulações baseadas em dados reais do cluster, observamos que, se os serviços fossem grandes o suficiente e se o cluster fosse utilizado razoavelmente, a criação desses serviços grandes seria reduzida, e que podíamos melhorar isso introduzindo a noção de métricas de desfragmentação.

Assim como a criação ou o acesso de arquivo poderia ter sua velocidade reduzida se o disco rígido do computador fosse fragmentado e pudesse ser acelerado com a desfragmentação da unidade, é possível configurar métricas de desfragmentação para que o Gerenciador de Recursos tente de forma proativa condensar a carga dos serviços em menos nós, de modo que (quase) sempre haja espaço para serviços ainda maiores, permitindo que eles sejam criados rapidamente. A maioria das pessoas não precisará delas, pois os serviços geralmente deveriam ser pequenos (até mesmo micro… sim!) e, portanto, não é difícil encontrar espaço para eles; no entanto, caso tenha serviços grandes e precise que eles sejam criados rapidamente (e caso esteja disposto a aceitar as outras compensações), as métricas de desfragmentação são ideais para você.

## Prós e contras da desfragmentação
Quais são as outras compensações conceituais? Recomendamos fazer uma medição completa das cargas de trabalho antes de ativar as métricas de desfragmentação. Aqui está uma tabela rápida de itens a ser considerados:

| Vantagens da desfragmentação | Desvantagens da desfragmentação |
|----------------------|----------------------|
|Agiliza a criação de serviços grandes |	Concentra a carga em menos nós, aumentando a contenção
|Permite a redução da movimentação de dados durante a criação | Falhas podem afetar mais serviços e causar mais variação
|Permite a descrição detalhada dos requisitos e a recuperação de espaço |	Configuração geral mais complexa do Gerenciamento de Recursos

É possível combinar métricas desfragmentadas e normais no mesmo cluster, e o Gerenciador de Recursos fará o melhor para garantir que você obtenha um layout que consolide o máximo das métricas de desfragmentação possível, ao mesmo tempo que ele tentará distribuir as restantes. Os resultados exatos que você obterá dependerão do número de métricas de balanceamento comparado ao número de métricas de desfragmentação, seus pesos, etc.

## Configurando métricas de desfragmentação
Configurar métricas de desfragmentação é uma decisão global no cluster, e métricas individuais podem ser selecionadas para desfragmentação:

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Disk" Value="true" />
    <Parameter Name="CPU" Value="false" />
</Section>
```

## Próximas etapas
- O Gerenciador de Recursos de Cluster tem muitas opções para descrever o cluster. Para saber mais sobre elas, confira este artigo sobre a [descrição de um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
- As métricas são como o Gerenciador de Recursos de Cluster do Service Fabric gerencia o consumo e a capacidade no cluster. Para saber mais sobre elas e como configurá-las, confira [este artigo](service-fabric-cluster-resource-manager-metrics.md)

[Image1]: ./media/service-fabric-resource-balancer-proactive-metric-packing/PMP.png

<!---HONumber=AcomDC_0316_2016-->