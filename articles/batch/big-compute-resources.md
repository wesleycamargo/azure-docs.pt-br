<properties
   pageTitle="Recursos para lote e cargas de trabalho de HPC na nuvem | Microsoft Azure"
   description="Lista os recursos técnicos que podem ajudá-lo a executar suas cargas de trabalho paralelas de grande escala, de lote e de HPC (computação de alto desempenho) no Azure."
   services="batch, cloud-services, virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-compute"
   ms.date="01/26/2016"
   ms.author="danlep"/>

# Big Compute no Azure: recursos técnicos para lote e HPC (Computação de Alto Desempenho)
Este é um guia para recursos técnicos para ajudá-lo a executar suas cargas de trabalho paralelas de grande escala, de lote e de HPC no Azure. Amplie suas cargas de trabalho de lote ou HPC existentes para a nuvem do Azure ou crie novas soluções Big Compute no Azure usando diversos serviços do Azure.

## Opções de soluções

Saiba mais sobre as opções do Big Compute no Azure e escolha a abordagem certa para suas necessidades de negócios e cargas de trabalho.

* [Soluções de HPC e Lote](batch-hpc-solutions.md)

* [Vídeo: Big Compute na nuvem com o Azure e HPC](https://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)


## Lote do Azure

O [Lote](https://azure.microsoft.com/services/batch/) é um serviço de plataforma que facilita a habilitação de seus aplicativos para a nuvem e a execução de trabalhos sem configurar e gerenciar um cluster e um agendador de trabalhos. Use o SDK para integrar aplicativos clientes ao Lote do Azure por meio de uma variedade de linguagens, preparar dados no Azure e criar pipelines de execução de trabalho.

* [Documentação](https://azure.microsoft.com/documentation/services/batch/)

* Referência da API [.NET](https://msdn.microsoft.com/library/azure/mt348682.aspx) e [REST](https://msdn.microsoft.com/library/azure/dn820158.aspx)

* [Tutorial: Introdução à biblioteca de lote do Azure para .NET](batch-dotnet-get-started.md)

* [Fórum do lote](https://social.msdn.microsoft.com/Forums/pt-BR/home?forum=azurebatch)

* [Vídeos de lote](https://azure.microsoft.com/documentation/videos/index/?services=batch)

## Soluções de cluster HPC

Implante ou amplie seu cluster HPC existente do Windows ou Linux para executar cargas de trabalho com uso intensivo de computação no Azure.

### Microsoft HPC Pack

O HPC Pack é a solução de HPC gratuita da Microsoft fundamentada nas tecnologias Microsoft Azure e Windows Server, capaz de executar cargas de trabalho do HPC no Windows e Linux.

* [Baixe a atualização 3 do HPC Pack 2012 R2](https://www.microsoft.com/download/details.aspx?id=49922)

* [Documentação](https://technet.microsoft.com/library/jj899572.aspx)


* [Opções de cluster do HPC com Microsoft HPC Pack no Azure](../virtual-machines/virtual-machines-hpcpack-cluster-options.md)

* [Aumento para instâncias de trabalho do Azure com o HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)

* [Aumento para o lote do Azure com o HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)


* [Fóruns do Windows HPC](https://social.microsoft.com/Forums/home?category=windowshpc)

### Soluções de cluster do Linux e OSS

Use esses modelos do Azure para implantar clusters HPC do Linux.

* [Crie um cluster SLURM](https://azure.microsoft.com/documentation/templates/slurm/) e uma [postagem de blog](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)

* [Crie um cluster de Torque](https://azure.microsoft.com/documentation/templates/torque-cluster/)

* [Intel Cloud Edition for Software Lustre - Eval](https://azure.microsoft.com/marketplace/partners/intel/lustre-cloud-edition-evaleval-lustre-2-7/)

## Microsoft MPI

[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) (MS-MPI) é uma implementação da Microsoft do padrão Message Passing Interface para desenvolver e executar aplicativos paralelos na plataforma Windows.


* [Baixe o MS-MPI](http://go.microsoft.com/FWLink/p/?LinkID=389556)

* [Referência do MS-MPI](https://msdn.microsoft.com/library/dn473458.aspx)

* [Fórum do MPI](https://social.microsoft.com/Forums/pt-BR/home?forum=windowshpcmpi)


## Instâncias de computação intensiva

O Azure oferece [diversos tamanhos](../virtual-machines/virtual-machines-size-specs.md), inclusive instâncias [A8 e A9](../virtual-machines/virtual-machines-a8-a9-a10-a11-specs.md) com uso intensivo de computação capazes de se conectar a uma rede RDMA de back-end, para executar cargas de trabalho de HPC para Linux e Windows.

* [Configurar um cluster de RDMA do Linux para executar aplicativos MPI](../virtual-machines/virtual-machines-linux-cluster-rdma.md)

* [Configurar um cluster de RDMA do Windows com o Microsoft HPC Pack para executar aplicativos MPI](../virtual-machines/virtual-machines-windows-hpcpack-cluster-rdma.md)

## Plano gráfico da arquitetura

* [HPC e orquestração de dados usando o Lote do Azure e o Azure Data Factory](http://go.microsoft.com/fwlink/?linkid=717686) (PDF) e [artigo](../data-factory/data-factory-data-processing-using-batch.md)

## Exemplos e scripts

* [Exemplos de código do Lote do Azure](https://github.com/Azure/azure-batch-samples)

## Serviços do Azure relacionados

* [Fábrica de dados](https://azure.microsoft.com/documentation/services/data-factory/)

* [Aprendizado de máquina](https://azure.microsoft.com/documentation/services/machine-learning/)

* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)

* [Máquinas virtuais](https://azure.microsoft.com/documentation/services/virtual-machines/)

* [Serviços de Nuvem](https://azure.microsoft.com/documentation/services/cloud-services/)

* [Serviços de Mídia](https://azure.microsoft.com/documentation/services/media-services/)

## Relatos de clientes

* [Towers Watson](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18222)

* [Milliman](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=14967)

* [ANEO](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=4168)

* [d3View](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=22088)

* [Schlumberger](http://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)

* [Ludwig Institute of Cancer Research](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5830)

* [Microsoft Research](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=15634)

## Próximas etapas

* Para os anúncios mais recentes, consulte o [blog da equipe do Microsoft HPC e Lote](http://blogs.technet.com/b/windowshpc/) e o [blog do Azure](https://azure.microsoft.com/blog/tag/hpc/).
* Consulte também [o que há de novo no Lote](https://azure.microsoft.com/updates/?service=batch) ou assine o [RSS feed](https://azure.microsoft.com/updates/feed/?service=batch).

<!---HONumber=AcomDC_0211_2016-->