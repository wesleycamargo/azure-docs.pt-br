<properties
   pageTitle="Planejamento de capacidade para aplicativos do Service Fabric | Microsoft Azure"
   description="Descreve como identificar o número de nós de computação necessários para um aplicativo do Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="coreysa"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/12/2015"
   ms.author="subramar"/>


# Planejamento de capacidade para Aplicativos do Service Fabric


Este documento ensina a você como estimar a quantidade de recursos (CPU, RAM, armazenamento de disco) necessários para a execução dos aplicativos do Service Fabric. É comum os requisitos de recursos mudarem ao longo do tempo. Normalmente, você precisa de alguns recursos enquanto desenvolve/testa seu serviço e, posteriormente, precisa de mais recursos à medida que entra na fase de produção e a popularidade de seu aplicativo aumenta. Ao projetar o aplicativo é melhor pensar hoje nos requisitos de longo prazo e fazer escolhas agora que permitam a ampliação do serviço, a fim de atender à alta demanda dos clientes. Ao criar um cluster do Service Fabric, você decide quais tipos de máquinas virtuais (VMs) compõem o cluster. Cada VM é acompanhada por uma quantidade limitada de recursos na forma de CPU (núcleos e velocidade), largura de banda de rede, RAM e armazenamento em disco. À medida que o serviço cresce com o tempo, você pode atualizar para VMs que oferecem mais recursos e/ou adicionar mais VMs ao seu cluster. É claro que, para executar a última sugestão, você deverá arquitetar seu serviço inicialmente para que ele possa tirar proveito de novas VMs adicionadas dinamicamente ao cluster.

Alguns serviços gerenciam poucos ou nenhum dados nas VMs em si e, portanto, o planejamento da capacidade deve se concentrar principalmente no desempenho. Isso significa que você deve considerar cuidadosamente o desempenho dos algoritmos do código e a CPU (núcleos e velocidade) das VMs necessária para executar seus algoritmos. Além disso, você também deve considerar a largura de banda da rede e, especificamente, a frequência das transferências de rede e a quantidade de dados que está sendo transferida. Se o serviço precisar ser executado com qualidade à medida que o uso do serviço aumenta, você poderá adicionar mais VMs ao cluster e balancear a carga das solicitações de rede em todas as VMs.

Para serviços que gerenciam muitos dados nas VMs, o planejamento de capacidade deve se concentrar principalmente no tamanho. Isso significa que você deve considerar cuidadosamente a capacidade de RAM e o armazenamento de disco da VM. O sistema de gerenciamento de memória virtual do Windows faz o espaço em disco se parecer com a memória RAM para o código do aplicativo. Isso permite que aplicativos usem mais memória do que está fisicamente disponível na VM. Ter mais RAM simplesmente aumenta o desempenho, pois a VM pode manter mais armazenamento em disco na RAM. Ao escolher uma VM, selecione uma cujo espaço em disco possa conter todos os dados desejados, e escolha um tamanho de RAM que permita o acesso a esses dados na velocidade desejada. Se os dados do serviço crescerem com o tempo, você poderá adicionar mais VMs ao cluster e particionar os dados em todas as VMs.

## De quantos nós você precisa?

O particionamento de seu serviço permite escalar horizontalmente os dados do serviço (veja [Particionamento do Service Fabric](service-fabric-concepts-partitioning.md) para obter mais detalhes sobre o particionamento). Cada partição deve se ajustar a uma única VM, mas várias partições (pequenas) podem ser colocadas em uma única VM. Portanto, ter um número maior de partições pequenas fornece maior flexibilidade, ao contrário de ter um pequeno número de partições maiores. A desvantagem é que ter muitas partições aumenta a sobrecarga do Service Fabric, e você não pode executar operações transacionadas entre partições. Também há a possibilidade demais tráfego de rede se o seu código de serviço precisar constantemente acessar partes dos dados que residem em partições diferentes. Ao projetar seu serviço, você deve considerar cuidadosamente esses prós e contras para chegar a uma estratégia de particionamento eficiente.

Vamos supor que seu aplicativo tenha um único serviço com estado com um tamanho de armazenamento com crescimento esperado para DB\_Size GB em um ano, e você pretende adicionar mais aplicativos (e partições) à medida que enfrenta um crescimento após esse ano. Para localizar o tamanho total do banco de dados em todas as réplicas, também devemos considerar o Fator de Replicação, RF, que determina o número de réplicas para o serviço (o tamanho total do banco de dados em todas as réplicas é igual ao Fator de Replicação multiplicado por DB\_size). Node\_Size representa a RAM/espaço em disco por nó que você deseja usar para seu serviço. Para obter o melhor desempenho, convém ajustar o banco de dados à memória no cluster, e convém colocar em um Node\_Size próximo à capacidade de RAM da VM escolhida. Ao alocar um Node\_Size maior do que a capacidade de RAM, você depende da paginação do sistema operacional e, assim, talvez o desempenho não seja o ideal, mas ainda pode ser suficiente para o serviço.

Dessa forma, o número de nós exigidos para obter o desempenho máximo pode ser calculado da seguinte maneira:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## Pense no crescimento

Convém calcular o número de nós com base no crescimento esperado do tamanho do banco de dados de seu serviço, além do tamanho do banco de dados inicial, e aumentar o número de nós à medida que seu serviço cresce, para não provisionar em excesso o número de nós. Porém, o número de partições deve ter base no número de nós necessários ao executar seu serviço no crescimento máximo. É uma boa ideia ter algumas máquinas sobressalentes (excesso de capacidade) disponíveis a qualquer momento, para que você possa manipular quaisquer picos inesperados ou qualquer falha de infraestrutura (por exemplo, se algumas VMs ficarem inativas). Embora isso seja algo que deva ser determinado usando seus picos esperados, um bom ponto de partida seria reservar algumas VMs adicionais (5 a 10% extra).

A declaração acima supõe a existência de um único serviço com estado. Se você tiver mais de um serviço com estado, também será necessário adicionar o tamanho do BD associado a outros serviços na equação, ou calcular o número de nós separadamente para cada serviço com estado. O serviço pode ter réplicas ou partições que não estão equilibradas (por exemplo, algumas partições podem ter mais dados do que outras. Veja o artigo sobre particionamento para conhecer as melhores práticas). No entanto, a equação acima é independente do número de partições ou réplicas, pois o Service Fabric garantirá que as réplicas sejam distribuídas entre os nós de uma forma otimizada.


## Planilha para cálculo de custo

Agora, vamos colocar alguns números reais na fórmula acima. Um exemplo de planilha [fornecido aqui](https://servicefabricsdkstorage.blob.core.windows.net/publicrelease/SF%20VM%20Cost%20calculator-NEW.xlsx) mostra como planejar a capacidade de um aplicativo que contém três tipos de objetos de dados. Para cada objeto, podemos supor seu tamanho e quantos objetos são esperados. Também selecionamos quantas réplicas queremos de cada tipo de objeto. A planilha calcula a quantidade total de memória a ser armazenada no cluster. Em seguida, inserimos um tamanho de VM e o custo mensal. Com base no tamanho da VM, a planilha informa o número mínimo de partições nas quais você deverá dividir seus dados para ajustar fisicamente nos nós. Talvez você queira que um número maior de partições acomode as necessidades de tráfego de computação e de rede específico de seu aplicativo. A planilha mostra o aumento do número de partições que gerenciam os objetos de perfil do usuário de 1 para 6.

Agora, com base em todas essas informações, a planilha mostra que você pode obter fisicamente todos os dados com as partições e réplicas desejadas em um cluster com 26 nós. No entanto, esse cluster é densamente compactado e, por isso, convém adicionar alguns nós para acomodar atualizações e falhas de nó. A planilha também mostra que ter mais de 57 nós não representa um valor adicional, pois você teria nós vazios. Novamente, convém ultrapassar os 57 nós a fim de acomodar falhas de nós e atualizações. É possível ajustar a planilha a fim de atender às necessidades específicas de seu aplicativo.

![][Image1]



## Próximas etapas

Confira [Particionamento de serviços do Service Fabric][10] para saber mais sobre como particionar o seu serviço.



<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md

<!---HONumber=AcomDC_1125_2015-->