---
title: "Gerenciador de Recursos de Cluster do Service Fabric – Grupos de Aplicativos | Microsoft Docs"
description: "Visão geral da funcionalidade de Grupo de Aplicativos no Gerenciador de Recursos de Cluster do Service Fabric"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 4cae2370-77b3-49ce-bf40-030400c4260d
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: eef9cbca0479801cbac53ae1ae5713d4dac6a4bf
ms.openlocfilehash: 57525cd41d518b12f3f465d5dea7247659e3c8b6


---
# <a name="introduction-to-application-groups"></a>Introdução aos grupos de aplicativos
O Cluster Resource Manager do Service Fabric geralmente gerencia os recursos de cluster distribuindo a carga (representada por meio de [Métricas](service-fabric-cluster-resource-manager-metrics.md)) uniformemente em todo o cluster. O Service Fabric também gerencia a capacidade dos nós no cluster e o cluster como um todo por meio da noção da [capacidade](service-fabric-cluster-resource-manager-cluster-description.md). As métricas e a capacidade funcionam muito bem para muitas cargas de trabalho, mas padrões que fazem uso intenso de diferentes Instâncias de Aplicativos do Service Fabric às vezes trazem requisitos adicionais. Alguns dos requisitos adicionais típicos são:

* Habilidade de reservar capacidade para os serviços de uma Instância de Aplicativo no cluster
* Habilidade de limitar o número total de nós em que os serviços em um aplicativo são executados
* Definindo as capacidades na instância do aplicativo em si para limitar o número ou o consumo de recursos total dos serviços dentro dele

Para atender a esses requisitos, o Cluster Resource Manager do Service Fabric dá suporte a Grupos de Aplicativos.

## <a name="managing-application-capacity"></a>Gerenciando a capacidade do Aplicativo
A capacidade do aplicativo pode ser usada para limitar o número de nós distribuídos por um aplicativo. Ela também pode limitar a carga de métrica dos serviços dos aplicativos em nós individuais e o total. Ele também pode ser usado para reservar recursos no cluster para o aplicativo.

A capacidade do aplicativo pode ser configurada para novos aplicativos quando eles são criados e pode ser atualizada para aplicativos existentes.

### <a name="limiting-the-maximum-number-of-nodes"></a>Limitando o número máximo de nós
O caso de uso mais simples para capacidade do Aplicativo é quando uma instanciação de aplicativo precisa ser limitada a um determinado número máximo de nós. Se nenhuma Capacidade do Aplicativo for especificada, o Cluster Resource Manager do Service Fabric criará e posicionará os serviços de acordo com as regras normais (balanceamento ou desfragmentação).

A imagem a seguir mostra uma instância de aplicativo com e sem um número máximo de nós definido. Não são feitas garantias sobre quais réplicas ou instâncias de quais serviços são colocadas juntas ou quais nós específicos são usados.

<center>
![Instância do aplicativo definindo o número máximo de nós][Image1]
</center>

No exemplo à esquerda, o aplicativo não tem uma Capacidade de Aplicativo definida e ele tem três serviços. O Cluster Resource Manager distribuiu todas as réplicas em seis nós disponíveis para obter o melhor equilíbrio no cluster. No exemplo à direita, vemos o mesmo aplicativo limitado a três nós.

O parâmetro que controla esse comportamento é chamado de MaximumNodes. Esse parâmetro pode ser definido durante a criação do aplicativo ou atualizado para uma instância do aplicativo que já estava em execução.

Powershell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –Name fabric:/AppName –MaximumNodes 5
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MaximumNodes = 3;
fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;

adUpdate.Metrics.Add(appMetric);
```

## <a name="application-metrics-load-and-capacity"></a>Métrica, carga e capacidade do aplicativo
Os grupos de aplicativos também permitem definir métricas associadas a uma determinada instância do aplicativo e a capacidade do aplicativo para essas métricas. Isso permite controlar, reservar e limitar o consumo de recursos dos serviços dentro dessa instância do aplicativo.

Para cada métrica do aplicativo, há dois valores que podem ser definidos:

* **Capacidade Total do Aplicativo** – essa configuração representa a capacidade total do aplicativo para uma determinada métrica. O Cluster Resource Manager não permite a criação de quaisquer novos serviços dentro dessa instância do aplicativo que fariam com que esse valor fosse excedido. Por exemplo, digamos que a instância do aplicativo tinha a capacidade de 10 e já tinha a carga de cinco. Nesse caso, a criação de um serviço com uma carga padrão total de 10 não deve ser permitida.
* **Capacidade Máxima do Nó** – essa configuração especifica a carga total máxima de réplicas de serviços dentro do aplicativo em um único nó. Se a carga total no nó ultrapassar essa capacidade, o Cluster Resource Manager tentará mover réplicas para outros nós para que a restrição de capacidade seja respeitada.

## <a name="reserving-capacity"></a>Reserva de capacidade
Outro uso comum de grupos de aplicativos é para garantir que os recursos dentro do cluster sejam reservados para uma determinada instância do aplicativo. Isso acontece mesmo se a instância do aplicativo ainda não tem nenhum serviço dentro dela ou mesmo se eles não estiverem consumindo recursos ainda. Vejamos como isso funcionaria.

### <a name="specifying-a-minimum-number-of-nodes-and-resource-reservation"></a>Especificar um número mínimo de nós e reserva de recursos
Reservar recursos para uma instância de aplicativo requer especificar alguns parâmetros adicionais: *MinimumNodes* e *NodeReservationCapacity*

* **MinimumNodes** – assim como especificar um número máximo de nós em que os serviços dentro de um aplicativo podem ser executados, você também pode especificar o número mínimo. Essa configuração define o número de nós em que os recursos devem ser reservados, garantindo a capacidade dentro do cluster como parte da criação da instância do aplicativo.
* **NodeReservationCapacity** – a NodeReservationCapacity pode ser definida para cada métrica dentro do aplicativo. Essa configuração define a quantidade de carga métrica reservada para o aplicativo em qualquer nó dentro do qual é colocada qualquer uma das réplicas ou instâncias dos serviços.

Vejamos um exemplo de reserva de capacidade:

<center>
Instâncias do aplicativo definindo a capacidade reservada![][Image2]
</center>

No exemplo à esquerda, os aplicativos não têm nenhuma Capacidade de Aplicativo definida. O Cluster Resource Manager equilibra tudo de acordo com as regras normais.

No exemplo à direita, digamos que o aplicativo foi criado com as seguintes configurações:

* MinimumNodes definido como dois
* MaximumNodes definido como três
* Uma métrica de aplicativo definida com
  * NodeReservationCapacity de 20
  * MaximumNodeCapacity de 50
  * TotalApplicationCapacity de 100

O Service Fabric reserva a capacidade em dois nós para o aplicativo azul e não permite que serviços de outras instâncias do aplicativo no cluster consumam essa capacidade. Essa capacidade reservada do aplicativo é considerada consumida e contada em relação à capacidade restante naquele nó e dentro do cluster.

Quando um aplicativo é criado com reserva, o Cluster Resource Manager reserva a capacidade igual a MinimumNodes * NodeReservationCapacity (para cada métrica). No entanto, a capacidade é reservada em um nó específico apenas quando pelo menos uma réplica é colocada nele. Essa reserva posterior permite flexibilidade e melhor utilização de recursos já que os recursos são reservados nos nós apenas quando necessário.

## <a name="obtaining-the-application-load-information"></a>Obtendo as informações de carga do aplicativo
Para cada aplicativo que tem capacidade de aplicativos definida, você pode obter as informações sobre a carga de agregada relatada por réplicas de seus serviços.

Por exemplo, a carga pode ser recuperada usando o seguinte cmdlet do PowerShell:

``` posh
Get-ServiceFabricApplicationLoad –ApplicationName fabric:/MyApplication1

```

A consulta ApplicationLoad retorna as informações básicas sobre a Capacidade do Aplicativo que foi especificada para o aplicativo. Essas informações incluem as informações de Nós Mínimos e Nós Máximos e o número que o aplicativo está ocupando atualmente. Elas também incluem informações sobre cada métrica de carga do aplicativo, incluindo:

* Nome da Métrica: o nome da métrica.
* Capacidade de Reserva: a Capacidade do Cluster reservada no cluster para esse Aplicativo.
* Carga do Aplicativo: a Carga Total de réplicas filho deste Aplicativo.
* Capacidade do Aplicativo: valor máximo permitido de Carga do Aplicativo.

## <a name="removing-application-capacity"></a>Removendo a capacidade do aplicativo
Depois que os parâmetros de Capacidade do Aplicativo estão definidos para um aplicativo, eles podem ser removidos usando cmdlets de Atualizar APIs de Aplicativo ou PowerShell. Por exemplo:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Esse comando remove todos os parâmetros de Capacidade do Aplicativo do aplicativo. O efeito do comando é imediato. Após a conclusão do comando, o Cluster Resource Manager é revertido para usar o comportamento padrão para o gerenciamento de aplicativos. Os parâmetros da Capacidade do Aplicativo podem ser especificados novamente por meio de Update-ServiceFabricApplication.

### <a name="restrictions-on-application-capacity"></a>Restrições à capacidade do aplicativo
Há várias restrições aos parâmetros de Capacidade de Aplicativo que devem ser respeitadas. Se houver erros de validação, a criação ou atualização do aplicativo será rejeitada com um erro e não ocorrerá nenhuma alteração.

* Todos os parâmetros de inteiro devem ser números não negativos.
* MinimumNodes nunca deve ser maior que MaximumNodes.
* Se as capacidades de uma métrica de carga forem definidas, elas deverão seguir estas regras:
  * A Capacidade de Reserva do Nó não deve ser maior que a Capacidade Máxima do Nó. Por exemplo, você não pode limitar a capacidade para a "CPU" métrica no nó a duas unidades e tentar reservar três unidades em cada nó.
  * Se MaximumNodes for especificado, o produto de MaximumNodes e a Capacidade Máxima do Nó não deverão ser maiores que a Capacidade Total do Aplicativo. Por exemplo, digamos que a Capacidade Máxima do Nó da métrica de carga "CPU" seja definida como oito. Suponhamos também que você defina os Nós Máximos como&10;. Nesse caso, a Capacidade Total do Aplicativo deve ser maior do que 80 para esta métrica de carga.

As restrições são aplicadas tanto durante a criação do aplicativo (no lado do cliente) quanto durante a atualização do aplicativo (no lado do servidor).

## <a name="how-not-to-use-application-capacity"></a>Como não usar a Capacidade do Aplicativo
* Não tente usar os recursos de Grupo de Aplicativos para restringir o aplicativo a um subconjunto de nós _específico_. Em outras palavras, você pode especificar que o aplicativo seja executado em no máximo cinco nós, mas não quais cinco nós específicos no cluster. A restrição de um aplicativo a nós específicos pode ser obtida usando restrições de posicionamento para os serviços.
* Não tente usar a Capacidade do Aplicativo para garantir que dois serviços do mesmo aplicativo sejam colocados lado a lado. A garantia de que os serviços são executados no mesmo nó pode ser obtida usando a afinidade ou com restrições de posicionamento dependendo dos requisitos específicos.

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre outras opções disponíveis para a configuração de serviços, confira o tópico sobre outras configurações disponíveis no Cluster Resource Manager em [Saiba mais sobre o a configuração dos serviços](service-fabric-cluster-resource-manager-configure-services.md)
* Para descobrir como o Gerenciador de Recursos de Cluster gerencia e balanceia carga no cluster, confira o artigo sobre [como balancear carga](service-fabric-cluster-resource-manager-balancing.md)
* Comece do princípio e [veja uma introdução ao Gerenciador de Recursos de Cluster do Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
* Para obter mais informações sobre como as métricas funcionam em geral, leia sobre [Métricas de Carga do Service Fabric](service-fabric-cluster-resource-manager-metrics.md)
* O Cluster Resource Manager tem muitas opções para descrever o cluster. Para saber mais sobre elas, confira este artigo sobre a [descrição de um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png



<!--HONumber=Jan17_HO1-->


