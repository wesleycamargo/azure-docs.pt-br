---
title: Recursos para lote e HPC na nuvem do Azure | Microsoft Docs
description: "Lista os recursos técnicos que podem ajudá-lo a executar suas cargas de trabalho paralelas de grande escala, de lote e de HPC (computação de alto desempenho) no Azure."
services: batch, cloud-services, virtual-machines
documentationcenter: 
author: dlepow
manager: timlt
editor: 
ms.assetid: 6f8be911-c841-41ae-88d3-3bcfc029eb7f
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: big-compute
ms.date: 03/17/2017
ms.author: danlep
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: 18be9f503b57117a7e8f5f0a4e9c93614cc7755b
ms.contentlocale: pt-br
ms.lasthandoff: 07/12/2017

---
# <a name="big-compute-in-azure-technical-resources-for-batch-and-high-performance-computing"></a>Big Compute no Azure: recursos técnicos para computação em lote e de alto desempenho
Este é um guia para os recursos técnicos que podem ajudá-lo a executar suas cargas de trabalho paralelas de grande escala, de lote e de HPC (computação de alto desempenho) no Azure. Amplie suas cargas de trabalho de lote ou HPC existentes para a nuvem do Azure ou crie novas soluções Big Compute usando diversos serviços do Azure.

## <a name="solutions-options"></a>Opções de soluções
Saiba mais sobre as opções do Big Compute no Azure e escolha a abordagem certa para suas necessidades de negócios e cargas de trabalho.

* [Soluções de HPC e Lote](batch-hpc-solutions.md)
* [Vídeo: Big Compute na nuvem com o Azure e HPC](https://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)

## <a name="azure-batch"></a>Lote do Azure
[Lote](https://azure.microsoft.com/services/batch/) é um serviço de plataforma que facilita a habilitação de seus aplicativos Linux e Windows para a nuvem e a execução de trabalhos sem configurar e gerenciar um cluster e um agendador de trabalhos. Use o SDK para integrar aplicativos clientes ao Lote do Azure por meio de diversas linguagens, preparar dados no Azure e criar pipelines de execução de trabalho.

* [Documentação](https://azure.microsoft.com/documentation/services/batch/)
* Referência do [.NET](https://msdn.microsoft.com/library/azure/mt348682.aspx), [Python](http://azure-sdk-for-python.readthedocs.io/latest/), [Node.js](http://azure.github.io/azure-sdk-for-node/azure-batch/latest/), [Java](http://azure.github.io/azure-sdk-for-java/) e da API [REST](https://msdn.microsoft.com/library/azure/dn820158.aspx)
* [Biblioteca do .NET de gerenciamento do lote](https://msdn.microsoft.com/library/mt463120.aspx) 
* Tutoriais: Introdução à [Biblioteca de lote do Azure para .NET](batch-dotnet-get-started.md) e para o [cliente Python do Lot](batch-python-tutorial.md)
* [Fórum do lote](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurebatch)
* [Vídeos de lote](https://azure.microsoft.com/documentation/videos/index/?services=batch)

## <a name="hpc-cluster-solutions"></a>Soluções de cluster HPC
Implante ou amplie seu cluster HPC existente do Windows ou Linux para executar cargas de trabalho com uso intensivo de computação no Azure.  

### <a name="microsoft-hpc-pack"></a>Microsoft HPC Pack
O HPC Pack é a solução de HPC gratuita da Microsoft fundamentada nas tecnologias Microsoft Azure e Windows Server, capaz de executar cargas de trabalho do HPC no Windows e Linux.  

* [Baixe o HPC Pack 2016](https://www.microsoft.com/download/details.aspx?id=54507)
* [Baixe a atualização 3 do HPC Pack 2012 R2](https://www.microsoft.com/download/details.aspx?id=49922)
* [Documentação](https://technet.microsoft.com/library/jj899572.aspx)
* Opções de cluster HPC Pack no Azure: [Linux](../virtual-machines/linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e [Windows](../virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 
* [Aumento para instâncias de trabalho do Azure com o HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)
* [Aumento para o lote do Azure com o HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)
* [Fóruns do Windows HPC](https://social.microsoft.com/Forums/home?category=windowshpc)

### <a name="linux-and-oss-cluster-solutions"></a>Soluções de cluster do Linux e OSS
Use esses modelos do Azure para implantar clusters HPC do Linux.

* [Crie um cluster SLURM](https://azure.microsoft.com/documentation/templates/slurm/) e uma [postagem de blog](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)
* [Crie um cluster de Torque](https://azure.microsoft.com/documentation/templates/torque-cluster/)
* [Modelos de grade de computação com o PBS Professional](https://github.com/xpillons/azure-hpc/tree/master/Compute-Grid-Infra)

## <a name="hpc-storage"></a>Armazenamento HPC
* [Sistemas de arquivos paralelos para o armazenamento HPC no Azure](https://blogs.msdn.microsoft.com/azurecat/2017/03/17/parallel-file-systems-for-hpc-storage-on-azure/)
* [Intel Cloud Edition for Software Lustre - Eval](https://azure.microsoft.com/marketplace/partners/intel/lustre-cloud-edition-evaleval-lustre-2-7/)
* [BeeGFS no modelo do CentOS 7.2](https://github.com/smith1511/hpc/tree/master/beegfs-shared-on-centos7.2)




## <a name="microsoft-mpi"></a>Microsoft MPI
[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) (MS-MPI) é uma implementação da Microsoft do padrão Message Passing Interface para desenvolver e executar aplicativos paralelos na plataforma Windows.

* [Baixe o MS-MPI](http://go.microsoft.com/FWLink/p/?LinkID=389556)
* [Referência do MS-MPI](https://msdn.microsoft.com/library/dn473458.aspx)
* [Fórum do MPI](https://social.microsoft.com/Forums/en-us/home?forum=windowshpcmpi)

## <a name="compute-intensive-instances"></a>Instâncias com uso computacional intensivo
O Azure oferece [diversos tamanhos de VM](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), inclusive instâncias de [computação intensiva da série H](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) capazes de se conectar a uma rede RDMA de back-end, a fim de executar cargas de trabalho de HPC para Linux e Windows. 

* [Configurar um cluster de RDMA do Linux para executar aplicativos MPI](../virtual-machines/linux/classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Configurar um cluster de RDMA do Windows com o Microsoft HPC Pack para executar aplicativos MPI](../virtual-machines/windows/classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

Para cargas de trabalho com uso intensivo de GPU, confira [Tamanhos de NC e NV](https://azure.microsoft.com/blog/azure-n-series-general-availability-on-december-1/).

## <a name="samples-and-demos"></a>Exemplos e scripts
* [Exemplos de código do C# e Python para o Lote do Azure](https://github.com/Azure/azure-batch-samples)
* [Shipyard de Lote](https://azure.github.io/batch-shipyard/) , que facilita a implantação de cargas de trabalho do Docker no estilo do Lote do Azure
* Pacote R [doAzureParallel](http://www.github.com/Azure/doAzureParallel), criado com base no Lote do Azure
* [Testar o SUSE Linux Enterprise Server para HPC](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)

## <a name="related-azure-services"></a>Serviços do Azure relacionados
* [Fábrica de dados](https://azure.microsoft.com/documentation/services/data-factory/)
* [Aprendizado de máquina](https://azure.microsoft.com/documentation/services/machine-learning/)
* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)
* [Máquinas virtuais](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Conjuntos de Escala de Máquina Virtual](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/)
* [Serviços de Nuvem](https://azure.microsoft.com/documentation/services/cloud-services/)
* [Serviço de Aplicativo](https://azure.microsoft.com/documentation/services/app-service/)
* [Serviços de Mídia](https://azure.microsoft.com/documentation/services/media-services/)
* [Funções](https://azure.microsoft.com/documentation/services/functions/)

## <a name="architecture-blueprints"></a>Plano gráfico da arquitetura
* [HPC e orquestração de dados usando o Lote do Azure, o Azure Data Factory](http://go.microsoft.com/fwlink/?linkid=717686) (PDF) e o [artigo](../data-factory/data-factory-data-processing-using-batch.md)

## <a name="industry-solutions"></a>Soluções do setor
* [Bancos e mercados de capitais](https://finance.azure.com/)
* [Simulações de engenharia](https://simulation.azure.com/) 

## <a name="customer-stories"></a>Relatos de clientes
* [ANEO](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=4168) 
* [d3View](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=22088)
* [Ludwig Institute of Cancer Research](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5830)
* [Microsoft Research](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=15634)
* [Milliman](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=14967)
* [Mitsubishi UFJ Securities International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=26266)
* [Schlumberger](http://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
* [Towers Watson](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18222)
* [UberCloud](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)

## <a name="next-steps"></a>Próximas etapas
* Para os anúncios mais recentes, consulte o [blog da equipe do Microsoft HPC e Lote](http://blogs.technet.com/b/windowshpc/) e o [blog do Azure](https://azure.microsoft.com/blog/tag/hpc/).
* Consulte também [o que há de novo no Lote](https://azure.microsoft.com/updates/?service=batch) ou assine o [RSS feed](https://azure.microsoft.com/updates/feed/?service=batch).


