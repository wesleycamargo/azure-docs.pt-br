---
title: Opções de cluster HPC Pack do Linux no Azure | Microsoft Docs
description: Saiba mais sobre as opções do Microsoft HPC Pack para criar e gerenciar um cluster HPC (computação de alto desempenho) do Linux na nuvem do Azure
services: virtual-machines-linux,cloud-services
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: ac60624e-aefa-40c3-8bc1-ef6d5c0ef1a2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 05/14/2018
ms.author: danlep
ms.openlocfilehash: 281867e30c78c7ed36ac739c8ae1a902463199cd
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166447"
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-linux-hpc-workloads-in-azure"></a>Opções com o HPC Pack para criar e gerenciar um cluster para cargas de trabalho de HPC do Linux no Azure
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Este artigo se concentra nas opções do Azure para usar o HPC Pack para executar cargas de trabalho do Linux. Também há opções para executar [cargas de trabalho de HPC do Windows com o HPC Pack](../windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>Cluster HPC Pack em VMs do Azure e conjuntos de dimensionamento de VMs
### <a name="azure-resource-manager-templates"></a>Modelos do Gerenciador de Recursos do Azure
* (GitHub) [Modelos de cluster HPC Pack 2016 Update 1](https://github.com/MsHpcPack/HPCPack2016)
* (GitHub) [Modelos de cluster HPC Pack 2012 R2](https://github.com/MsHpcPack/HPCPack2012R2)
* (Início Rápido) [Criar um cluster HPC Pack 2012 R2 com nós de computação do Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)

### <a name="azure-vm-images"></a>Imagens de VM do Azure
Procure [imagens do HPC Pack no Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?page=1&search=%22HPC%20%20Pack%22) se você desejar criar seu próprio cluster HPC Pack no Azure.

## <a name="hpc-pack-2012-r2-cluster-in-classic-deployment-model"></a>Cluster HPC Pack 2012 R2 no modelo de implantação clássico
* [Criar um cluster Linux HPC com o script de implantação de IaaS do HPC Pack](../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Configurar um cluster de RDMA do Linux para executar aplicativos MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Tutorial: introdução a nós de computação Linux em um cluster de HPC Pack no Azure](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Tutorial: executar o NAMD com o Microsoft HPC Pack em nós de computação Linux no Azure](classic/hpcpack-cluster-namd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Tutorial: executar OpenFOAM com o Microsoft HPC Pack em um cluster de RDMA do Linux no Azure](classic/hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Tutorial: executar o STAR-CCM+ com o Microsoft HPC Pack em um cluster de RDMA do Linux no Azure](classic/hpcpack-cluster-starccm.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

## <a name="job-submisssion"></a>Envio do trabalho
* [Enviar trabalhos para um cluster HPC Pack no Azure](../windows/hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




