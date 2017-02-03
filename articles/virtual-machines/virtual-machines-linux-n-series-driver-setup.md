---
title: "Instalação de drivers da série N do Azure para Linux | Microsoft Docs"
description: "Como configurar drivers NVIDIA GPU para VMs da série N que executam o Linux no Azure"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/07/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 4bf470737f961219250c37d890ed81eb62409626
ms.openlocfilehash: f03026212dfcbe2faa22188ebfb2e953114a87fd


---
# <a name="set-up-gpu-drivers-for-n-series-vms"></a>Configurar drivers GPU para VMs da série N
Para aproveitar as funcionalidades de GPU das VMs da série N do Azure que executam uma distribuição Linux com suporte, é necessário instalar os drivers gráficos NVIDIA em cada VM após a implantação. Este artigo também está disponível para [VMs Windows](virtual-machines-windows-n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Para obter as especificações de VMs da série N, as capacidades de armazenamento e os detalhes de disco, consulte [Tamanhos das máquinas virtuais](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).



## <a name="supported-gpu-drivers"></a>Drivers GPU com suporte


> [!NOTE]
> No momento, o suporte ao GPU do Linux está disponível apenas em VMs NC do Azure que executam o Ubuntu Server 16.04 LTS.

### <a name="nvidia-tesla-drivers-for-nc-vms"></a>Drivers NVIDIA Tesla para VMs NC

* [Ubuntu 16.04 LTS](https://go.microsoft.com/fwlink/?linkid=836899) (instalador autoextraível .run)

## <a name="tesla-driver-installation-on-ubuntu-1604-lts"></a>Instalação do driver Tesla no Ubuntu 16.04 LTS

1. Faça uma conexão SSH à VM da série N do Azure.

2. Para verificar se o sistema tem uma GPU compatível com CUDA, execute o seguinte comando:

    ```bash
    lspci | grep -i NVIDIA
    ```
    Você verá uma saída semelhante ao seguinte exemplo (mostrando uma placa NVIDIA Tesla K80):

    ![Saída do comando lspci](./media/virtual-machines-linux-n-series-driver-setup/lspci.png)

3. Baixe o arquivo .run do driver de sua distribuição. O seguinte comando de exemplo baixa o driver Ubuntu 16.04 LTS Tesla no diretório /tmp:

    ```bash
    wget -O /tmp/NVIDIA-Linux-x86_64-367.48.run https://go.microsoft.com/fwlink/?linkid=836899
    ```

4. Se precisar instalar `gcc` e `make` no sistema (necessário para os drivers Tesla), digite o seguinte:

    ```bash
    sudo apt-get update
    
    sudo apt install gcc

    sudo apt install make
    ```

4. Mude para o diretório que contém o instalador de drivers e execute comandos semelhantes aos seguintes:

    ```bash
    chmod +x NVIDIA-Linux-x86_64-367.48.run
    
    sudo sh ./NVIDIA-Linux-x86_64-367.48.run
    ```

## <a name="verify-driver-installation"></a>Verificar a instalação de drivers


Para consultar o estado do dispositivo GPU, execute o utilitário de linha de comando [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) instalado com o driver. 

![Status do dispositivo NVIDIA](./media/virtual-machines-linux-n-series-driver-setup/smi.png)

## <a name="optional-installation-of-nvidia-cuda-toolkit-on-ubuntu-1604-lts"></a>Instalação opcional do NVIDIA CUDA Toolkit no Ubuntu 16.04 LTS

Opcionalmente, você pode instalar o NVIDIA CUDA Toolkit 8.0 em VMs NC que executam o Ubuntu 16.04 LTS. Além dos drivers GPU, o Toolkit fornece um ambiente de desenvolvimento abrangente para desenvolvedores de C e C++ que criam aplicativos com aceleração de GPU.

Para instalar o CUDA Toolkit, execute comandos semelhantes aos seguintes:

```bash
CUDA_REPO_PKG=cuda-repo-ubuntu1604_8.0.44-1_amd64.deb

wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

sudo dpkg -i /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo apt-get update

sudo apt-get install cuda-drivers
```

A instalação poderá levar vários minutos.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre as GPUs NVIDIA nas VMs da série N, consulte:
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (para VMs NC do Azure)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (para VMs NV do Azure)




<!--HONumber=Dec16_HO2-->


