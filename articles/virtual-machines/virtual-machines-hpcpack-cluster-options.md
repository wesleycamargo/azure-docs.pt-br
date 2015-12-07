<properties
 pageTitle="Opções de cluster do HPC Pack na nuvem | Microsoft Azure"
 description="Saiba mais sobre as opções com o Microsoft HPC Pack para criar e gerenciar um cluster HPC (computação de alto desempenho) na nuvem do Azure."
 services="virtual-machines,cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="10/08/2015"
 ms.author="danlep"/>

# Opções para criar e gerenciar um cluster HPC (computação de alto desempenho) no Azure com o Microsoft HPC Pack

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


Aproveite os serviços de computação e de infraestrutura do Microsoft HPC Pack e do Azure para criar e gerenciar um cluster HPC (computação de alto desempenho) baseado em nuvem. [HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) é a solução de HPC gratuita da Microsoft fundamentada nas tecnologias Microsoft Azure e Windows Server e que dá suporte a cargas de trabalho do HPC no Windows e Linux. Um cluster HPC Pack baseado em nuvem oferece a um administrador de cluster ou a um ISV (fornecedor de software independente) uma plataforma flexível e escalonável para executar aplicativos de computação intensiva, ao mesmo tempo que reduz o investimento em uma infraestrutura de cluster de cálculo local.


## Executar um cluster HPC Pack em VMs do Azure


### Imagens de VM do Azure

* [HPC Pack no Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)

* [Nó de computação do HPC Pack no Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)

* [Nó de computação do HPC Pack com Excel no Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)

### Modelos de início rápido do Azure

* [Criar um cluster HPC](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/)

* [Implantar um cluster de HPC Pack com nós de computação Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)

* [Criar um cluster HPC com uma imagem personalizada de nó de computação](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/)

### Script de implantação do PowerShell

* [Criar um cluster HPC com o script de implantação de IaaS do HPC Pack](virtual-machines-hpcpack-cluster-powershell-script.md)

### Tutoriais

* [Tutorial: Introdução a nós de computação Linux em um cluster de HPC Pack no Azure](../virtual-machines/virtual-machines-linux-cluster-hpcpack.md)

* [Tutorial: executar o NAMD com o Microsoft HPC Pack em nós de computação Linux no Azure](../virtual-machines/virtual-machines-linux-cluster-hpcpack-namd.md)

* [Tutorial: Introdução com um cluster de HPC Pack no Azure para executar cargas de trabalho do Excel e SOA](../virtual-machines/virtual-machines-excel-cluster-hpcpack.md)



### Implantação manual com o portal do Azure



* [Configurar o nó de cabeçalho de um cluster HPC Pack em uma VM do Azure](virtual-machines-hpcpack-cluster-headnode.md)

### Gerenciamento de clusters

* [Gerenciar nós de computação em um cluster HPC Pack no Azure](virtual-machines-hpcpack-cluster-node-manage.md)

* [Adicionar nós de “disparo contínuo” do Azure a um nó de cabeçalho do HPC Pack no Azure](virtual-machines-hpcpack-cluster-node-burst.md)

* [Aumentar ou reduzir os recursos de computação do Azure em um cluster HPC Pack](virtual-machines-hpcpack-cluster-node-autogrowshrink.md)

* [Enviar trabalhos para um cluster HPC Pack no Azure](virtual-machines-hpcpack-cluster-submit-jobs.md)


## Adicionar nós de função de trabalho a um cluster HPC Pack


* [Aumento para instâncias de trabalho do Azure com o HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)

* [Tutorial: Configurar um cluster híbrido com o HPC Pack no Azure](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

* [Adicionar nós de “disparo contínuo” do Azure a um nó de cabeçalho do HPC Pack no Azure](virtual-machines-hpcpack-cluster-node-burst.md)

* [Aumentar ou reduzir os recursos de computação do Azure em um cluster HPC Pack](virtual-machines-hpcpack-cluster-node-autogrowshrink.md)

## Integrar ao lote do Azure 

* [Aumento para o lote do Azure com o HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)

## Criar clusters de RDMA para cargas de trabalho MPI

* [Configurar um cluster de RDMA do Windows com o HPC Pack para executar aplicativos MPI](virtual-machines-windows-hpcpack-cluster-rdma.md)

* [Tutorial: Executar OpenFOAM com o Microsoft HPC Pack em um cluster de RDMA do Linux no Azure](virtual-machines-linux-cluster-hpcpack-openfoam.md)

<!-- * [Set up a Linux RDMA cluster to run MPI applications](virtual-machines-linux-hpcpack-cluster-rdma.md) -->

<!---HONumber=AcomDC_1125_2015-->