---
title: Opções de cluster HPC Pack do Linux na nuvem | Microsoft Docs
description: Saiba mais sobre as opções do Microsoft HPC Pack para criar e gerenciar um cluster HPC (computação de alto desempenho) do Linux na nuvem do Azure
services: virtual-machines-linux,cloud-services
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 09/26/2016
ms.author: danlep

---
# Opções com o HPC Pack para criar e gerenciar um cluster HPC no Azure para cargas de trabalho do Linux
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Este artigo se concentra nas opções de uso do HPC Pack para executar cargas de trabalho do Linux. Também há opções para executar [cargas de trabalho de HPC do Windows com o HPC Pack](virtual-machines-windows-hpcpack-cluster-options.md).

## Executar um cluster HPC Pack em VMs do Azure
### Modelos do Azure
* (Marketplace) [Cluster HPC Pack para cargas de trabalho do Linux](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)
* (Início rápido) [Criar um cluster HPC Pack com nós de computação do Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)

### Script de implantação do PowerShell
* [Criar um cluster Linux HPC com o script de implantação de IaaS do HPC Pack](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md)

### Tutoriais
* [Tutorial: Introdução a nós de computação Linux em um cluster de HPC Pack no Azure](virtual-machines-linux-classic-hpcpack-cluster.md)
* [Tutorial: executar o NAMD com o Microsoft HPC Pack em nós de computação Linux no Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
* [Tutorial: Executar OpenFOAM com o Microsoft HPC Pack em um cluster de RDMA do Linux no Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)
* [Tutorial: Executar o STAR-CCM+ com o Microsoft HPC Pack em um cluster de RDMA do Linux no Azure](virtual-machines-linux-classic-hpcpack-cluster-starccm.md)

### Gerenciamento de clusters
* [Enviar trabalhos para um cluster HPC Pack no Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)
* [Gerenciamento de trabalho no HPC Pack](https://technet.microsoft.com/library/jj899585.aspx)

## Criar clusters de RDMA para cargas de trabalho MPI
* [Tutorial: Executar OpenFOAM com o Microsoft HPC Pack em um cluster de RDMA do Linux no Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)
* [Configurar um cluster de RDMA do Linux para executar aplicativos MPI](virtual-machines-linux-classic-rdma-cluster.md)

<!---HONumber=AcomDC_0928_2016-->