---
title: Desfragmentação de Métricas no Azure Service Fabric | Microsoft Docs
description: Uma visão geral do uso de desfragmentação ou como uma estratégia de métricas no Service Fabric
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
# Desfragmentação de métricas e carga no Service Fabric
O Resource Manager de Cluster do Service Fabric está basicamente relacionado ao balanceamento em termos de distribuição da carga, garantindo que todos os nós no cluster sejam igualmente utilizados. Normalmente, esse é o layout mais seguro e inteligente em termos de sobrevivência a falhas, já que ele assegura que uma determinada falha não destrua grande parte de uma dada carga de trabalho. O Resource Manager do Service Fabric Cluster também dá suporte a uma estratégia diferente, que é a desfragmentação. Em geral, a desfragmentação significa que, em vez de tentar distribuir a utilização de uma métrica em todo o cluster, devemos, de fato, tentar consolidá-la. Esta é uma inversão favorável de nossa estratégia normal – em vez de otimizar o cluster com base na minimização do desvio padrão médio da carga de métrica de uma determinada métrica, começaremos otimizando os aumentos no desvio. Mas por que você usaria essa estratégia?

Bem, caso tenha distribuído a carga de maneira uniforme entre os nós no cluster, você já terá consumido alguns dos recursos que os nós têm a oferecer. Normalmente, isso não é um problema, mas, às vezes, algumas cargas de trabalho criam serviços que são excepcionalmente grandes e que consomem grande parte de um nó – digamos que 75% a 95% dos recursos de um nó acabariam sendo dedicados a um único serviço ou réplica. Isso não é um problema: o Gerenciador de Recursos de Cluster detectará no momento da criação do serviço que ele precisa reorganizar o cluster a fim de liberar espaço para essa carga de trabalho grande e definir como fará com que isso aconteça. Contudo, enquanto isso, a carga de trabalho deverá esperar para ser agendada no cluster.

Considerando que o agendamento de novas cargas de trabalho geralmente é, pelo menos, um pouco sensível à latência, se não fizermos nada diferente, às vezes, poderemos negligenciar os SLAs caso haja vários serviços e estados para mover, principalmente se as cargas de trabalho no cluster forem grandes (e, portanto, demorarem mais para serem movidas no cluster). Na verdade, quando medimos os tempos de criação em simulações baseadas em dados reais do cluster, observamos que, se os serviços fossem grandes o suficiente e se o cluster fosse utilizado razoavelmente de forma que a velocidade da criação desses serviços grandes fosse reduzida, poderíamos melhorar isso introduzindo a política de métricas de desfragmentação.

Assim como a criação ou o acesso de arquivo poderia ter sua velocidade reduzida se o disco rígido do computador fosse fragmentado e pudesse ser acelerado com a desfragmentação da unidade, de forma que haveria grandes blocos contíguos disponíveis, seria possível configurar métricas de desfragmentação para que o Gerenciador de Recursos de Cluster tentasse, de forma proativa, condensar a carga dos serviços em menos nós, de modo que (quase) sempre haveria espaço para serviços ainda maiores, permitindo que eles fossem criados rapidamente. A maioria das pessoas não precisará disso, uma vez que os serviços, em geral, deverão ser pequenos e, portanto, não será difícil encontrar espaço para eles. Mas, se você tiver serviços grandes e precisar que eles sejam criados rapidamente (e estiver disposto a aceitar outras vantagens e desvantagens, como o aumento impactante de falhas e alguns recursos sendo inutilizados enquanto aguardam as cargas de trabalho serem agendadas), a estratégia de desfragmentação será ideal para você.

O diagrama a seguir fornece uma representação visual de dois clusters diferentes, um deles desfragmentado e o outro não. No caso do equilibrado, considere os movimentos que seriam necessários para colocar um dos objetos de serviço maiores, se um novo fosse criado, em comparação com o cluster desfragmentado, em que ele pode ser imediatamente colocado em nós 4 ou 5.

![Comparando clusters balanceados e desfragmentados][Image1]

## Prós e contras da desfragmentação
Quais são as outras compensações conceituais? Recomendamos fazer uma medição completa das cargas de trabalho antes de ativar as métricas de desfragmentação. Aqui está uma tabela rápida de itens a ser considerados:

| Vantagens da desfragmentação | Desvantagens da desfragmentação |
| --- | --- |
| Agiliza a criação de serviços grandes |Concentra a carga em menos nós, aumentando a contenção |
| Permite a redução da movimentação de dados durante a criação |Falhas podem afetar mais serviços e causar mais variação |
| Permite a descrição detalhada dos requisitos e a recuperação de espaço |Configuração geral mais complexa do Gerenciamento de Recursos |

É possível combinar métricas desfragmentadas e normais no mesmo cluster, e o Gerenciador de Recursos fará o melhor para garantir que você obtenha um layout que consolide o máximo das métricas de desfragmentação possível, ao mesmo tempo que ele tentará distribuir as restantes. Os resultados exatos que você obterá dependerão do número de métricas de balanceamento em comparação ao número de métricas de desfragmentação, seus pesos, cargas atuais, etc.

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
* O Gerenciador de Recursos de Cluster tem muitas opções para descrever o cluster. Para saber mais sobre elas, confira este artigo sobre a [descrição de um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
* As métricas são como o Gerenciador de Recursos de Cluster do Service Fabric gerencia o consumo e a capacidade no cluster. Para saber mais sobre elas e como configurá-las, confira [este artigo](service-fabric-cluster-resource-manager-metrics.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png

<!---HONumber=AcomDC_0824_2016-->