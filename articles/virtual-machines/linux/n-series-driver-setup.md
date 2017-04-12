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
ms.date: 03/10/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: e7f6c840be3a284f635114287a69c151f671531d
ms.lasthandoff: 04/03/2017


---

# <a name="set-up-gpu-drivers-for-n-series-vms-running-linux"></a>Configurar drivers GPU para VMs da série N executando Linux

Para aproveitar as funcionalidades de GPU das VMs da série N do Azure que executam uma distribuição Linux com suporte, é necessário instalar os drivers gráficos NVIDIA em cada VM após a implantação. Também há informações de instalação de driver disponíveis para [VMs do Windows](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


> [!IMPORTANT]
> No momento, o suporte ao GPU do Linux está disponível apenas em VMs NC do Azure que executam o Ubuntu Server 16.04 LTS.
> 

Para obter as especificações de VMs da série N, as capacidades de armazenamento e os detalhes de disco, consulte [Tamanhos das máquinas virtuais](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Consulte também [Considerações gerais sobre as VMs da Série N](#general-considerations-for-n-series-vms).



## <a name="install-nvidia-cuda-drivers"></a>Instalar os drivers NVIDIA CUDA

Aqui estão as etapas para instalar drivers NVIDIA em VMs NC Linux do Kit de ferramentas NVIDIA CUDA 8.0. Os desenvolvedores de C e C++ podem opcionalmente instalar o Kit de ferramentas completo para criar aplicativos com aceleração de GPU. Para obter mais informações, consulte o [Guia de instalação do CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).


> [!NOTE]
> Links de download do driver fornecidos aqui estão atualmente no momento da publicação. Para os drivers mais recentes, visite o site da [NVIDIA](http://www.nvidia.com/).

Para instalar o Kit de ferramentas de CUDA, realize uma conexão SSH para cada VM. Para verificar se o sistema tem uma GPU compatível com CUDA, execute o seguinte comando:

```bash
lspci | grep -i NVIDIA
```
Você verá uma saída semelhante ao seguinte exemplo (mostrando uma placa NVIDIA Tesla K80):

![Saída do comando lspci](./media/n-series-driver-setup/lspci.png)

Em seguida, execute comandos específicos para a sua distribuição.

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

```bash
CUDA_REPO_PKG=cuda-repo-ubuntu1604_8.0.61-1_amd64.deb

wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

sudo dpkg -i /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo apt-get update

sudo apt-get install cuda-drivers

```
A instalação poderá levar vários minutos.

Para opcionalmente instalar o Kit de ferramentas CUDA completo, digite:

```bash
sudo apt-get install cuda
```

Reinicie a VM e prossiga para verificar a instalação.

## <a name="verify-driver-installation"></a>Verificar a instalação de drivers


Para consultar o estado do dispositivo GPU, conecte-se à VM por SSH e execute o utilitário de linha de comando [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) instalado com o driver. 

![Status do dispositivo NVIDIA](./media/n-series-driver-setup/smi.png)

## <a name="cuda-driver-updates"></a>Atualizações de driver CUDA

É recomendável que você atualize periodicamente os drivers CUDA após a implantação.

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers
```

Após a atualização, reinicie a VM.


[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* Não recomendamos instalar o X server nem outros sistemas que usam o driver nouveau em VMs do Ubuntu NC. Antes de instalar os drivers de GPU NVIDIA, você precisa desabilitar o driver nouveau.  

* Se você quiser capturar uma imagem de uma VM Linux na qual você tenha instalado drivers NVIDIA, consulte [Como generalizar e capturar uma máquina virtual Linux](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre as GPUs NVIDIA nas VMs da série N, consulte:
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (para VMs NC do Azure)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (para VMs NV do Azure)


