---
title: Opções de cluster HPC Pack do Windows no Azure | Microsoft Docs
description: Saiba mais sobre as opções do Microsoft HPC Pack para criar e gerenciar um cluster HPC (computação de alto desempenho) do Windows na nuvem do Azure
services: virtual-machines-windows,cloud-services,batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: 02c5566d-2129-483c-9ecf-0d61030442d7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 05/14/2018
ms.author: danlep
ms.openlocfilehash: 1232228d5e2fcd05f41a096a933072dffcca2119
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34165750"
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-windows-hpc-workloads-in-azure"></a>Opções com o HPC Pack para criar e gerenciar um cluster para cargas de trabalho de HPC do Windows no Azure
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Este artigo se concentra nas opções do Azure para criar clusters HPC Pack para executar cargas de trabalho do Windows. Também há opções para a criação de clusters HPC Pack para executar [cargas de trabalho HPC do Linux](../linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>Cluster HPC Pack em VMs do Azure e conjuntos de dimensionamento de VMs
### <a name="azure-resource-manager-templates"></a>Modelos do Gerenciador de Recursos do Azure
* (GitHub) [Modelos de cluster HPC Pack 2016 Update 1](https://github.com/MsHpcPack/HPCPack2016)
* (GitHub) [Modelos de cluster HPC Pack 2012 R2](https://github.com/MsHpcPack/HPCPack2012R2)
* (Guia de Início Rápido) [Criar um cluster HPC Pack 2012 R2](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)
* (Guia de Início Rápido) [Criar um cluster HPC Pack 2012 R2 com uma imagem personalizada de nó de computação](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### <a name="azure-vm-images"></a>Imagens de VM do Azure
Procure [imagens do HPC Pack no Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?page=1&search=%22HPC%20%20Pack%22) se você desejar criar seu próprio cluster HPC Pack no Azure.


### <a name="tutorials"></a>Tutoriais
* [Tutorial: Implantar um cluster HPC Pack 2016 no Azure](hpcpack-2016-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Gerenciar um cluster HPC Pack 2016 no Azure usando o Azure Active Directory](hpcpack-cluster-active-directory.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="hpc-pack-2012-r2-cluster-deployment-in-the-classic-deployment-model"></a>A implantação do cluster HPC Pack 2012 R2 no modelo de implantação clássico
* [Criar um cluster HPC com o script de implantação de IaaS do HPC Pack](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Tutorial: Introdução a um cluster de HPC Pack no Azure para executar cargas de trabalho do Excel e SOA](excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Gerenciar nós de computação em um cluster HPC Pack no Azure](classic/hpcpack-cluster-node-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Aumentar ou reduzir os recursos de computação do Azure em um cluster HPC Pack](classic/hpcpack-cluster-node-autogrowshrink.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Configurar um cluster de RDMA do Windows com o HPC Pack para executar aplicativos MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="burst-to-azure-from-hpc-pack-2012-r2"></a>Intermitência para Azure a partir do HPC Pack 2012 R2
* [Intermitência para Instâncias de Trabalho do Azure com Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)
* [Aumento para o lote do Azure com o HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)
* [Tutorial: Configurar um cluster híbrido com o HPC Pack no Azure](../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

## <a name="job-submission"></a>Envio do trabalho

* [Enviar trabalhos para um cluster HPC Pack no Azure](hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)






