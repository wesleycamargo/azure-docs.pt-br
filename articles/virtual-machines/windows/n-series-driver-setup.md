---
title: Configuração de drivers da série N do Azure para Windows | Microsoft Docs
description: Como configurar drivers de GPU NVIDIA para VMs da série N executando Windows Server ou Windows no Azure
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f3950c34-9406-48ae-bcd9-c0418607b37d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/29/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bc4b9cb9940f073034df01143f4d9e77a47cb19b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654378"
---
# <a name="set-up-gpu-drivers-for-n-series-vms-running-windows"></a>Configurar drivers de GPU para VMs da série N executando Windows 
Para aproveitar os recursos de GPU das VMs da série N do Azure executando uma versão com suporte do Windows Server ou Windows, os drivers gráficos NVIDIA devem ser instalados. Este artigo apresenta etapas de instalação do driver depois que você implanta uma VM da série N. Também há informações de instalação de driver disponíveis para [VMs Linux](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

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

A conectividade de rede RDMA pode ser habilitada em VMs da série N compatível com RDMA, como NC24r implantado no mesmo conjunto de disponibilidade ou em um único grupo de posicionamento em um conjunto de dimensionamento de VM. A extensão HpcVmDrivers deve ser adicionada para instalar drivers de dispositivo de rede do Windows que habilitam a conectividade RDMA. Para adicionar a extensão de VM a uma VM da série N habilitada para RDMA, use cmdlets do [Azure PowerShell](/powershell/azure/overview) para o Azure Resource Manager.

Para instalar a última extensão HpcVMDrivers versão 1.1 em uma VM compatível com RDMA existente chamada myVM na região Oeste dos EUA:
  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Para obter mais informações, consulte [Recursos e extensões da máquina virtual para Windows](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

A rede RDMA dá suporte ao tráfego da Interface de transmissão de mensagens (MPI) para aplicativos executados com [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) ou Intel MPI 5.x. 


## <a name="next-steps"></a>Próximas etapas

* Desenvolvedores compilando aplicativos acelerados por GPU para GPUs NVIDIA Tesla também podem baixar e instalar o [CUDA Toolkit](https://developer.nvidia.com/cuda-downloads). Para obter mais informações, consulte o [Guia de instalação do CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).


