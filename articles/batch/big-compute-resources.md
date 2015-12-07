<properties
   pageTitle="Big Compute: recursos técnicos para Lote e HPC (Computação de Alto Desempenho) | Microsoft Azure"
   description="Lista os recursos técnicos que podem ajudá-lo a executar suas cargas de trabalho paralelas de grande escala, de lote e de HPC no Azure."
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
   ms.date="09/29/2015"
   ms.author="danlep"/>

# Big Compute no Azure: recursos técnicos para lote e HPC (Computação de Alto Desempenho)
Este é um guia para recursos técnicos para ajudá-lo a executar suas cargas de trabalho paralelas de grande escala, de lote e de HPC no Azure. Amplie suas cargas de trabalho de lote ou HPC existentes para a nuvem do Azure ou crie novas soluções Big Compute no Azure usando diversos serviços do Azure.

## Opções de soluções

Saiba mais sobre as opções do Big Compute no Azure e escolha a abordagem certa para suas necessidades de negócios e cargas de trabalho.

* [Soluções de HPC e Lote](batch-hpc-solutions.md)

* [Vídeo: Big Compute na nuvem com o Azure e HPC](http://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)


## Lote do Azure

O [Lote](http://azure.microsoft.com/services/batch/) é um serviço de plataforma que facilita a habilitação de seus aplicativos para a nuvem e a execução de trabalhos sem configurar e gerenciar um cluster e um agendador de trabalhos. Use o SDK para integrar aplicativos clientes ao Lote do Azure por meio de uma variedade de linguagens, preparar dados no Azure e criar pipelines de execução de trabalho.

* [Documentação](http://azure.microsoft.com/documentation/services/batch/)

* [Referência de API](https://msdn.microsoft.com/library/azure/dn820177.aspx)

* [Tutorial: Introdução à biblioteca de lote do Azure para .NET](batch-dotnet-get-started.md)

* [Fórum do lote](https://social.msdn.microsoft.com/Forums/home?forum=azurebatch)

* [Vídeos de lote](https://azure.microsoft.com/documentation/videos/index/?services=batch)

## Soluções de cluster HPC

Implante ou amplie seu cluster HPC existente do Windows ou Linux para executar cargas de trabalho com uso intensivo de computação no Azure.

### Microsoft HPC Pack

O HPC Pack é a solução de HPC gratuita da Microsoft fundamentada nas tecnologias Microsoft Azure e Windows Server, capaz de executar cargas de trabalho do HPC no Windows e Linux.

* [Baixe a atualização 3 do HPC Pack 2012 R2](https://www.microsoft.com/pt-BR/download/details.aspx?id=49922)

* [Documentação](https://technet.microsoft.com/library/jj899572.aspx)


* [Opções de cluster do HPC com Microsoft HPC Pack no Azure](../virtual-machines/virtual-machines-hpcpack-cluster-options.md)

* [Aumento para instâncias de trabalho do Azure com o HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)

* [Aumento para o lote do Azure com o HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)


* [Fóruns do Windows HPC](https://social.microsoft.com/Forums/home?category=windowshpc)

### Soluções de cluster do Linux e OSS

Use esses modelos de início rápido do Azure para implantar clusters HPC Linux com ferramentas de software livre.

* [Crie um cluster SLURM](http://azure.microsoft.com/documentation/templates/slurm/) e uma [postagem de blog](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)

* [Crie um cluster de Torque](http://azure.microsoft.com/documentation/templates/torque-cluster/)

## Microsoft MPI

[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) (MS-MPI) é uma implementação da Microsoft do padrão Message Passing Interface para desenvolver e executar aplicativos paralelos na plataforma Windows.


* [Baixe o MS-MPI](http://go.microsoft.com/FWLink/p/?LinkID=389556)

* [Referência do MS-MPI](https://msdn.microsoft.com/library/dn473458.aspx)

* [Fórum do MPI](https://social.microsoft.com/Forums/home?forum=windowshpcmpi)


## Instâncias de computação intensiva

O Azure oferece uma [variedade de tamanhos](../virtual-machines/virtual-machines-size-specs.md), incluindo [instâncias A8, A9, A10 e A11](../virtual-machines/virtual-machines-a8-a9-a10-a11-specs.md) com uso intensivo de computação para executar cargas de trabalho de HPC para Linux e Windows.

* [Configurar um cluster de RDMA do Linux para executar aplicativos MPI](../virtual-machines/virtual-machines-linux-cluster-rdma.md)

* [Configurar um cluster de RDMA do Windows com o Microsoft HPC Pack para executar aplicativos MPI](../virtual-machines/virtual-machines-windows-hpcpack-cluster-rdma.md)

## Plano gráfico da arquitetura

* [Computação de grande escala - Serviços financeiros](http://go.microsoft.com/fwlink/?LinkId=536378) (PDF) mostra como colocar em operação e gerenciar a análise de dados e a computação de larga escala na nuvem para gerenciamento de riscos, relatórios e simulações.

## Exemplos e scripts

* [Exemplos de código do Lote do Azure](https://github.com/Azure/azure-batch-samples)

* [Exemplo de misturador de aplicativos do Lote](https://github.com/Azure/azure-batch-apps-blender) e [postagem de blog](http://azure.microsoft.com/blog/2015/01/26/blender-on-azure-batch/)

## Serviços do Azure relacionados

* [Fábrica de dados](http://azure.microsoft.com/documentation/services/data-factory/)

* [Aprendizado de máquina](http://azure.microsoft.com/documentation/services/machine-learning/)

* [HDInsight](http://azure.microsoft.com/documentation/services/hdinsight/)

* [Máquinas virtuais](http://azure.microsoft.com/documentation/services/virtual-machines/)

* [Serviços de Nuvem](http://azure.microsoft.com/documentation/services/cloud-services/)

* [Serviços de Mídia](http://azure.microsoft.com/documentation/services/media-services/)



## Próximas etapas

* Para os anúncios mais recentes, consulte o [blog da equipe do Microsoft HPC e Lote](http://blogs.technet.com/b/windowshpc/) e o [blog do Azure](http://azure.microsoft.com/blog/tag/hpc/).
* Consulte também [o que há de novo no Lote](http://azure.microsoft.com/updates/?service=batch) ou assine o [RSS feed](http://azure.microsoft.com/updates/feed/?service=batch).

<!---HONumber=AcomDC_1125_2015-->