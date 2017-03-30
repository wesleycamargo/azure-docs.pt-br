---
title: "Configuração de drivers da série N do Azure para Windows | Microsoft Docs"
description: "Como configurar drivers NVIDIA GPU para VMs da série N que executam o Windows no Azure"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f3950c34-9406-48ae-bcd9-c0418607b37d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 562ebbf815f421343c580fced111cda481aa4a5c
ms.lasthandoff: 03/17/2017


---
# <a name="set-up-gpu-drivers-for-n-series-vms-running-windows-server"></a>Configurar drivers GPU para VMs da série N executando o Windows Server
Para aproveitar as funcionalidades de GPU das VMs da série N do Azure que executam o Windows Server 2016 ou o Windows Server 2012 R2, é necessário instalar os drivers gráficos NVIDIA em cada VM após a implantação. Também há informações de instalação de driver disponíveis para [VMs Linux](virtual-machines-linux-n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para obter as especificações básicas, as capacidades de armazenamento e os detalhes de disco, consulte [Tamanhos das máquinas virtuais](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Consulte também [Considerações gerais sobre as VMs da Série N](#general-considerations-for-n-series-vms).



## <a name="supported-gpu-drivers"></a>Drivers GPU com suporte

Conecte-se pela Área de Trabalho Remota a cada VM da série N. Baixe, extraia e instale o driver com suporte para o sistema operacional Windows. 

### <a name="nvidia-tesla-drivers-for-nc-vms-tesla-k80"></a>Drivers NVIDIA Tesla para VMs NC (Tesla K80)



| SO | Versão do driver |
| -------- |------------- |
| Windows Server 2016 | [376.84](http://us.download.nvidia.com/Windows/Quadro_Certified/376.84/376.84-tesla-desktop-winserver2016-international-whql.exe) (.exe) |
| Windows Server 2012 R2 | [376.84](http://us.download.nvidia.com/Windows/Quadro_Certified/376.84/376.84-tesla-desktop-winserver2008-2012r2-64bit-international-whql.exe) (.exe) |

> [!NOTE]
> Os links de download do driver Tesla fornecidos aqui são atuais no momento da publicação. Para os drivers mais recentes, visite o site da [NVIDIA](http://www.nvidia.com/).
>

### <a name="nvidia-grid-drivers-for-nv-vms-tesla-m60"></a>Drivers NVIDIA GRID para VMs NV (Tesla M60)

| SO | Versão do driver |
| -------- |------------- |
| Windows Server 2016 | [369.71](https://go.microsoft.com/fwlink/?linkid=836843) (.zip) |
| Windows Server 2012 R2 | [369.71](https://go.microsoft.com/fwlink/?linkid=836844) (.zip)  |



## <a name="verify-gpu-driver-installation"></a>Verificar a instalação de drivers de GPU

Em VMs NV do Azure, uma reinicialização é necessária após a instalação de drivers. Em VMs NC, uma reinicialização não é necessária.

É possível verificar a instalação de drivers no Gerenciador de Dispositivos. O exemplo a seguir mostra uma configuração bem-sucedida da placa Tesla K80 em uma VM NC do Azure.

![Propriedades do driver GPU](./media/virtual-machines-windows-n-series-driver-setup/GPU_driver_properties.png)

Para consultar o estado do dispositivo GPU, execute o utilitário de linha de comando [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) instalado com o driver. 

![Status do dispositivo NVIDIA](./media/virtual-machines-windows-n-series-driver-setup/smi.png)  

## <a name="rdma-network-for-nc24r-vms"></a>Rede RDMA para VMs NC24r

A conectividade de rede RDMA pode ser habilitada em VMs NC24r implantadas no mesmo conjunto de disponibilidade. A extensão HpcVmDrivers deve ser adicionada para instalar drivers de dispositivo de rede do Windows que habilitam a conectividade RDMA. Para adicionar a extensão de VM a uma VM NC24r, use cmdlets do [Azure PowerShell](/powershell/azureps-cmdlets-docs) para o Azure Resource Manager.

> [!NOTE]
> Atualmente, apenas o Windows Server 2012 R2 dá suporte à rede RDMA em VMs NC24r.
> 

Para instalar a última extensão HpcVMDrivers versão 1.1 em uma VM compatível com RDMA existente chamada myVM na região Oeste dos EUA:
  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Para obter mais informações, consulte [Recursos e extensões da máquina virtual para Windows](virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

A rede RDMA dá suporte ao tráfego da Interface de transmissão de mensagens (MPI) para aplicativos executados com [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) ou Intel MPI 5.x. 

[!INCLUDE [virtual-machines-n-series-considerations](../../includes/virtual-machines-n-series-considerations.md)]

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre as GPUs NVIDIA nas VMs da série N, consulte:
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (para VMs NC do Azure)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (para VMs NV do Azure)

* Os desenvolvedores que criam aplicativos com aceleração de GPU para GPUs NVIDIA Tesla também podem baixar e instalar o CUDA Toolkit 8 para [Windows Server 2016](https://developer.nvidia.com/compute/cuda/8.0/Prod2/local_installers/cuda_8.0.61_win10-exe) ou [Windows Server 2012 R2](https://developer.nvidia.com/compute/cuda/8.0/Prod2/local_installers/cuda_8.0.61_windows-exe). Para obter mais informações, consulte o [Guia de instalação do CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).



