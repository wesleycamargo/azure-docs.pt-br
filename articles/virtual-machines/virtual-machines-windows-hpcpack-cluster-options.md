---
title: "Opções de cluster do Windows HPC Pack na nuvem | Microsoft Docs"
description: "Saiba mais sobre as opções do Microsoft HPC Pack para criar e gerenciar um cluster HPC (computação de alto desempenho) do Windows na nuvem do Azure"
services: virtual-machines-windows,cloud-services,batch
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: 02c5566d-2129-483c-9ecf-0d61030442d7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 09/26/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 3de3d1a80dc823f7a5dfce844e45d08bb1804af6


---
# <a name="options-with-hpc-pack-to-create-and-manage-a-windows-hpc-cluster-in-azure"></a>Opções com o HPC Pack para criar e gerenciar um cluster HPC do Windows no Azure
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Este artigo se concentra nas opções para criar clusters HPC Pack para executar cargas de trabalho do Windows. Também há opções para criar clusters para executar [cargas de trabalho de HPC do Linux com o HPC Pack](virtual-machines-linux-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="run-an-hpc-pack-cluster-in-azure-vms"></a>Executar um cluster HPC Pack em VMs do Azure
### <a name="azure-templates"></a>Modelos do Azure
* (Marketplace) [Cluster HPC Pack para cargas de trabalho do Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)
* (Marketplace) [Cluster HPC Pack para cargas de trabalho do Excel](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterexcelcn/)
* (Guia de início rápido) [Criar um cluster HPC](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)
* (Guia de início rápido) [Criar um cluster HPC com uma imagem personalizada de nó de computação](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### <a name="azure-vm-images"></a>Imagens de VM do Azure
* [HPC Pack no Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)
* [Nó de computação do HPC Pack no Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)
* [Nó de computação do HPC Pack com Excel no Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)

### <a name="powershell-deployment-script"></a>Script de implantação do PowerShell
* [Criar um cluster HPC com o script de implantação de IaaS do HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

### <a name="tutorials"></a>Tutoriais
* [Tutorial: Introdução a um cluster de HPC Pack no Azure para executar cargas de trabalho do Excel e SOA](virtual-machines-windows-excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="manual-deployment-with-the-azure-portal"></a>Implantação manual com o portal do Azure
* [Configurar o nó de cabeçalho de um cluster HPC Pack em uma VM do Azure](virtual-machines-windows-hpcpack-cluster-headnode.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="cluster-management"></a>Gerenciamento de clusters
* [Gerenciar nós de computação em um cluster HPC Pack no Azure](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Aumentar ou reduzir os recursos de computação do Azure em um cluster HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Enviar trabalhos para um cluster HPC Pack no Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Gerenciamento de trabalho no HPC Pack](https://technet.microsoft.com/library/jj899585.aspx)

## <a name="add-worker-role-nodes-to-an-hpc-pack-cluster"></a>Adicionar nós de função de trabalho a um cluster HPC Pack
* [Aumento para instâncias de trabalho do Azure com o HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)
* [Tutorial: Configurar um cluster híbrido com o HPC Pack no Azure](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)
* [Adicionar nós de “disparo contínuo” do Azure a um nó de cabeçalho do HPC Pack no Azure](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="integrate-with-azure-batch"></a>Integrar ao lote do Azure
* [Aumento para o lote do Azure com o HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)

## <a name="create-rdma-clusters-for-mpi-workloads"></a>Criar clusters de RDMA para cargas de trabalho MPI
* [Configurar um cluster de RDMA do Windows com o HPC Pack para executar aplicativos MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)




<!--HONumber=Nov16_HO3-->


