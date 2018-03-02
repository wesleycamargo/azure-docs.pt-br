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
ms.date: 02/22/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 72c46efdb5ccddde59660d49d323d79de8d87050
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/24/2018
---
# <a name="set-up-gpu-drivers-for-n-series-vms-running-windows-server"></a>Configurar drivers GPU para VMs da série N executando o Windows Server
Para aproveitar os recursos de GPU das VMs série N do Azure que estão executando o Windows Server 2016 ou o Windows Server 2012 R2, instale os drivers gráficos NVIDIA com suporte. Este artigo apresenta etapas de instalação do driver depois que você implanta uma VM da série N. Também há informações de instalação de driver disponíveis para [VMs Linux](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para especificações básicas, capacidades de armazenamento e detalhes de disco, consulte [tamanhos de VM Windows da GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 


[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]



## <a name="driver-installation"></a>Instalação do driver

1. Conecte-se pela Área de Trabalho Remota a cada VM da série N.

2. Baixe, extraia e instale o driver com suporte para o sistema operacional Windows.

Em VMs NV do Azure, uma reinicialização é necessária após a instalação de drivers. Em VMs NC, uma reinicialização não é necessária.

## <a name="verify-driver-installation"></a>Verificar a instalação de drivers

É possível verificar a instalação de drivers no Gerenciador de Dispositivos. O exemplo a seguir mostra uma configuração bem-sucedida da placa Tesla K80 em uma VM NC do Azure.

![Propriedades do driver GPU](./media/n-series-driver-setup/GPU_driver_properties.png)

Para consultar o estado do dispositivo GPU, execute o utilitário de linha de comando [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) instalado com o driver.

1. Abra um prompt de comando e mude para o diretório **C:\Program Files\NVIDIA Corporation\NVSMI**.

2. Execute `nvidia-smi`. Se o driver estiver instalado você verá uma saída parecida com a seguinte. Observe que **GPU-Util** mostrará **0%**, a menos que você esteja executando uma carga de trabalho da GPU na VM. Sua versão de driver e os detalhes GPU podem ser diferentes daqueles mostrados.

![Status do dispositivo NVIDIA](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>Conectividade de rede RDMA

A conectividade de rede RDMA pode ser habilitada em VMs da série N habilitadas para RDMA, como a NC24r, implantada no mesmo conjunto de disponibilidade. A extensão HpcVmDrivers deve ser adicionada para instalar drivers de dispositivo de rede do Windows que habilitam a conectividade RDMA. Para adicionar a extensão de VM a uma VM da série N habilitada para RDMA, use cmdlets do [Azure PowerShell](/powershell/azure/overview) para o Azure Resource Manager.

Para instalar a última extensão HpcVMDrivers versão 1.1 em uma VM compatível com RDMA existente chamada myVM na região Oeste dos EUA:
  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Para obter mais informações, consulte [Recursos e extensões da máquina virtual para Windows](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

A rede RDMA dá suporte ao tráfego da Interface de transmissão de mensagens (MPI) para aplicativos executados com [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) ou Intel MPI 5.x. 


## <a name="next-steps"></a>Próximas etapas

* Os desenvolvedores que criam aplicativos com aceleração de GPU para GPUs NVIDIA Tesla também podem baixar e instalar o [CUDA Toolkit 9.1](https://developer.nvidia.com/cuda-downloads). Para obter mais informações, consulte o [Guia de instalação do CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).


