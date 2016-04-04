<properties
 pageTitle="Opções de cluster HPC Pack do Linux na nuvem | Microsoft Azure"
 description="Saiba mais sobre as opções do Microsoft HPC Pack para criar e gerenciar um cluster HPC (computação de alto desempenho) do Linux na nuvem do Azure"
 services="virtual-machines-linux,cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="02/04/2016"
 ms.author="danlep"/>

# Opções para criar e gerenciar um cluster HPC (computação de alto desempenho) do Linux no Azure com o Microsoft HPC Pack

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

## Executar um cluster HPC Pack em VMs do Azure

### Modelos do Azure


* (Marketplace) [Cluster HPC Pack para cargas de trabalho do Linux](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)

* (Início rápido) [Criar um cluster HPC Pack com nós de computação do Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)


### Imagens de VM do Azure

* [HPC Pack no Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)



### Script de implantação do PowerShell

* [Criar um cluster HPC com o script de implantação de IaaS do HPC Pack](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md)

### Tutoriais

* [Tutorial: Introdução a nós de computação Linux em um cluster de HPC Pack no Azure](virtual-machines-linux-classic-hpcpack-cluster.md)

* [Tutorial: executar o NAMD com o Microsoft HPC Pack em nós de computação Linux no Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)

* [Tutorial: Executar OpenFOAM com o Microsoft HPC Pack em um cluster de RDMA do Linux no Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)



### Gerenciamento de clusters

* [Enviar trabalhos para um cluster HPC Pack no Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)



## Criar clusters de RDMA para cargas de trabalho MPI

* [Tutorial: Executar OpenFOAM com o Microsoft HPC Pack em um cluster de RDMA do Linux no Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

* [Configurar um cluster de RDMA do Linux para executar aplicativos MPI](virtual-machines-linux-classic-rdma-cluster.md)

<!---HONumber=AcomDC_0323_2016-->