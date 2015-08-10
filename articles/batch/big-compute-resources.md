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
   ms.date="07/28/2015"
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

HPC Pack é a solução HPC gratuita da Microsoft fundamentada em tecnologias Microsoft Azure e o Windows Server.

* [Baixe a atualização 2 do HPC Pack 2012 R2](https://www.microsoft.com/pt-br/download/details.aspx?id=47755)

* [Documentação](https://technet.microsoft.com/library/jj899572.aspx)

* **Imagens de VM do Azure**

    * [HPC Pack no Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)

    * [Nó de computação do HPC Pack no Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)

    * [Nó de computação do HPC Pack com Excel no Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)

* **Scripts e modelos de início rápido do Azure**

    * [Criar um cluster HPC](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/)

    * [Criar um cluster de HPC com a imagem de um nó de computação personalizado](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/)

    * [Script do PowerShell de implantação do HPC Pack IaaS](https://www.microsoft.com/download/details.aspx?id=44949)

* **Tutoriais do Azure**

    * [Tutorial: Introdução a nós de computação Linux em um cluster de HPC Pack no Azure](../virtual-machines/virtual-machines-linux-cluster-hpcpack.md)

    * [Tutorial: Introdução com um cluster de HPC Pack no Azure para executar cargas de trabalho do Excel e SOA](../virtual-machines/virtual-machines-excel-cluster-hpcpack.md)

    * [Tutorial: Configurar um cluster híbrido com o HPC Pack no Azure](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

    * [HPC Pack em VMs do Azure](https://msdn.microsoft.com/library/azure/dn518135.aspx)

    * [Intermitência no Azure com o HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)


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

* [Instâncias A8 e A9: Início Rápido com HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx)

* [Executar aplicativos MPI em instâncias A8 e A9](https://msdn.microsoft.com/library/azure/dn592104.aspx)

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

<!---HONumber=July15_HO5-->