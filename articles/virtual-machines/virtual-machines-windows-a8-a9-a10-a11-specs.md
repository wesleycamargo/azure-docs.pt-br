---
title: "Sobre VMs de computação intensiva com Windows | Microsoft Docs"
description: "Obter informações básicas e considerações sobre o uso da série H e dos tamanhos A8, A9, A10 e A11 do Azure com computação intensiva para VMs do Windows e serviços de nuvem"
services: virtual-machines-windows, cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/21/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 5a46b53f42fce7577485517e9d345cf2ad3f0926
ms.openlocfilehash: d4d0a221d531c4f6eda7bb6410fbc4dcebf5074b


---
# <a name="about-h-series-and-compute-intensive-a-series-vms"></a>Sobre VMs série H ou série A com computação intensiva
Apresentamos a seguir informações básicas e algumas considerações sobre o uso da série H mais recente e das instâncias A8, A9, A10 e A11 do Azure, também conhecidas como instâncias de *computação intensiva* . Este artigo se concentra no uso destas instâncias para VMs Windows. Este artigo também está disponível para [VMs Linux](virtual-machines-linux-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para obter as especificações básicas, as capacidades de armazenamento e os detalhes de disco, consulte [Tamanhos das máquinas virtuais](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>Acesso à rede RDMA
Para acessar a rede RDMA do Azure para obter o tráfego de MPI do Windows, as instâncias compatíveis com RDMA devem atender aos seguintes requisitos: 

* **Sistema operacional**
  
  * **Máquinas virtuais** – Windows Server 2012 R2, Windows Server 2012
  * **Serviços de nuvem** – Família de SO convidado Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2
* **MPI** – Microsoft MPI (MS-MPI) 2012 R2 ou posterior, Intel MPI Library 5.x

  Implementações de MPI com suporte usam a interface direta da rede Microsoft para comunicação entre instâncias. 
* **Extensão de VM HpcVmDrivers** – em VMs compatíveis com RDMA, a extensão HpcVmDrivers deve ser adicionada para instalar drivers de dispositivo de rede do Windows que habilitam a conectividade RDMA. (Em algumas implantações das instâncias A8 e A9, a extensão HpcVmDrivers é adicionada automaticamente.) Se precisar adicionar a extensão de VM a uma VM, use cmdlets do [Azure PowerShell](/powershell/azureps-cmdlets-docs) para o Azure Resource Manager.

  Para obter informações sobre a última extensão HpcVmDrivers:

  ```PowerShell
  Get-AzureVMAvailableExtension -ExtensionName  "HpcVmDrivers"
  ```

  Para instalar a última extensão HpcVMDrivers versão 1.1 em uma VM compatível com RDMA existente chamada myVM:
  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Para obter mais informações, consulte [Gerenciar extensões de VM](virtual-machines-windows-classic-manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Também é possível trabalhar com extensões de VMs no [modelo de implantação clássico](virtual-machines-windows-classic-manage-extensions.md).


## <a name="considerations-for-hpc-pack-and-windows"></a>Considerações para HPC Pack e Windows
[Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), solução gratuita da Microsoft de gerenciamento de trabalho e cluster HPC, não é necessário para você usar as instâncias de computação intensiva com o Windows Server. No entanto, é uma opção para criar um cluster de cálculo no Azure para executar aplicativos MPI baseados no Windows e outras cargas de trabalho de HPC. HPC Pack 2012 R2 e versões posteriores incluem um ambiente de tempo de execução para MS-MPI que pode usar a rede RDMA do Azure quando implantado em VMs compatíveis com RDMA.

Para obter mais informações e listas de verificação para usar as instâncias de computação intensiva com o HPC Pack no Windows Server, veja [Configurar um cluster de RDMA do Windows com o HPC Pack para executar aplicativos MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="next-steps"></a>Próximas etapas
* Para obter detalhes sobre disponibilidade e preços dos tamanhos de computação intensiva, veja [Preços das máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows) e [Preços dos serviços de nuvem](https://azure.microsoft.com/pricing/details/cloud-services/).
* Para obter os detalhes de disco e as capacidades de armazenamento, consulte [Tamanhos das máquinas virtuais](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Para começar a implantar e usar instâncias de computação intensiva com o HPC Pack no Windows, veja [Configurar um cluster de RDMA do Windows com o HPC Pack para executar aplicativos MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Para obter informações sobre como usar instâncias de computação intensiva para executar aplicativos MPI com o Lote do Azure, consulte [Usar tarefas de várias instâncias para executar aplicativos MPI (Interface de Transmissão de Mensagens) no Lote do Azure](../batch/batch-mpi.md).




<!--HONumber=Jan17_HO1-->


