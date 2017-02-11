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
ms.date: 11/30/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 4bf470737f961219250c37d890ed81eb62409626
ms.openlocfilehash: feb9370f0241fd860749d32f5db5842cb18463f1


---
# <a name="set-up-gpu-drivers-for-n-series-vms"></a>Configurar drivers GPU para VMs da série N
Para aproveitar as funcionalidades de GPU das VMs da série N do Azure que executam o Windows Server, é necessário instalar os drivers gráficos NVIDIA em cada VM após a implantação. Este artigo também está disponível para [VMs Linux](virtual-machines-linux-n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para obter as especificações básicas, as capacidades de armazenamento e os detalhes de disco, consulte [Tamanhos das máquinas virtuais](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




## <a name="supported-gpu-drivers"></a>Drivers GPU com suporte

Conecte-se pela Área de Trabalho Remota a cada VM da série N. Baixe, extraia e instale o driver com suporte para o sistema operacional Windows. 

### <a name="nvidia-grid-drivers-for-nv-vms"></a>Drivers NVIDIA GRID para VMs NV

* [Windows Server 2016](https://go.microsoft.com/fwlink/?linkid=836843) (.zip)

* [Windows Server 2012 R2](https://go.microsoft.com/fwlink/?linkid=836844) (.zip)

### <a name="nvidia-tesla-drivers-for-nc-vms"></a>Drivers NVIDIA Tesla para VMs NC

* [Windows Server 2016](https://go.microsoft.com/fwlink/?linkid=836841) (.zip)

* [Windows Server 2012 R2](https://go.microsoft.com/fwlink/?linkid=836842) (.zip)



## <a name="verify-driver-installation"></a>Verificar a instalação de drivers

Em VMs NV do Azure, uma reinicialização é necessária após a instalação de drivers. Em VMs NC, uma reinicialização não é necessária.

É possível verificar a instalação de drivers no Gerenciador de Dispositivos. O exemplo a seguir mostra uma configuração bem-sucedida da placa K80 em uma VM NC do Azure.

![Propriedades do driver GPU](./media/virtual-machines-windows-n-series-driver-setup/GPU_driver_properties.png)

Para consultar o estado do dispositivo GPU, execute o utilitário de linha de comando [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) instalado com o driver. 

![Status do dispositivo NVIDIA](./media/virtual-machines-windows-n-series-driver-setup/smi.png)  

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre as GPUs NVIDIA nas VMs da série N, consulte:
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (para VMs NC do Azure)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (para VMs NV do Azure)

* Os desenvolvedores que criam aplicativos com aceleração de GPU para GPUs NVIDIA Tesla também podem baixar e instalar o [CUDA Toolkit 8](https://developer.nvidia.com/cuda-downloads).





<!--HONumber=Dec16_HO2-->


