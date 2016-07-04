<properties
 pageTitle="Opções de cluster do Windows HPC Pack na nuvem | Microsoft Azure"
 description="Saiba mais sobre as opções do Microsoft HPC Pack para criar e gerenciar um cluster HPC (computação de alto desempenho) do Windows na nuvem do Azure"
 services="virtual-machines-windows,cloud-services,batch"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="06/17/2016"
 ms.author="danlep"/>

# Opções para criar e gerenciar um cluster HPC (computação de alto desempenho) do Windows no Azure com o Microsoft HPC Pack

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Este artigo se concentra nas opções de uso do HPC Pack para executar cargas de trabalho do Windows. Também há opções para executar [cargas de trabalho de HPC do Linux com o HPC Pack](virtual-machines-linux-hpcpack-cluster-options.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Executar um cluster HPC Pack em VMs do Azure

### Modelos do Azure

* (Marketplace) [Cluster HPC Pack para cargas de trabalho do Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)

* (Marketplace) [Cluster HPC Pack para cargas de trabalho do Excel](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterexcelcn/)

* (Guia de início rápido) [Criar um cluster HPC](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)

* (Guia de início rápido) [Criar um cluster HPC com uma imagem personalizada de nó de computação](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### Imagens de VM do Azure

* [HPC Pack no Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)

* [Nó de computação do HPC Pack no Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)

* [Nó de computação do HPC Pack com Excel no Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)



### Script de implantação do PowerShell

* [Criar um cluster HPC com o script de implantação de IaaS do HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)

### Tutoriais

* [Tutorial: Introdução com um cluster de HPC Pack no Azure para executar cargas de trabalho do Excel e SOA](virtual-machines-windows-excel-cluster-hpcpack.md)



### Implantação manual com o portal do Azure

* [Configurar o nó de cabeçalho de um cluster HPC Pack em uma VM do Azure](virtual-machines-windows-hpcpack-cluster-headnode.md)

### Gerenciamento de clusters

* [Gerenciar nós de computação em um cluster HPC Pack no Azure](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md)


* [Aumentar ou reduzir os recursos de computação do Azure em um cluster HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md)

* [Enviar trabalhos para um cluster HPC Pack no Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)


## Adicionar nós de função de trabalho a um cluster HPC Pack


* [Aumento para instâncias de trabalho do Azure com o HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)

* [Tutorial: Configurar um cluster híbrido com o HPC Pack no Azure](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

* [Adicionar nós de “disparo contínuo” do Azure a um nó de cabeçalho do HPC Pack no Azure](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md)

* [Aumentar ou reduzir os recursos de computação do Azure em um cluster HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md)

## Integrar ao lote do Azure 

* [Aumento para o lote do Azure com o HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)

## Criar clusters de RDMA para cargas de trabalho MPI

* [Configurar um cluster de RDMA do Windows com o HPC Pack para executar aplicativos MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)

<!---HONumber=AcomDC_0622_2016-->