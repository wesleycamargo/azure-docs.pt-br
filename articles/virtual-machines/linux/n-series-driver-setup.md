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
ms.date: 05/02/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: de674af369080ad7eb608608685e293f2326c8e6
ms.openlocfilehash: 69f1363c26d8b5a18ffd5629c6a49c34306dd7c0
ms.contentlocale: pt-br
ms.lasthandoff: 05/04/2017


---

# <a name="set-up-gpu-drivers-for-n-series-vms-running-linux"></a>Configurar drivers GPU para VMs da série N executando Linux

Para aproveitar as funcionalidades de GPU das VMs da série N do Azure que executam o Linux, é necessário instalar os drivers gráficos NVIDIA em cada VM. Este artigo apresenta etapas de instalação do driver depois que você implanta uma VM da série N. Também há informações de instalação de driver disponíveis para [VMs do Windows](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


Para obter as especificações de VMs da série N, as capacidades de armazenamento e os detalhes de disco, consulte [Tamanhos de VM Linux para GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 



## <a name="supported-distributions-and-drivers"></a>Distribuições e drivers com suporte

> [!IMPORTANT]
> No momento, o suporte ao GPU do Linux está disponível apenas em VMs do Azure NC. 

As seguintes distribuições do Azure Marketplace têm suporte para executar drivers de gráficos NVIDIA em VMs do Linux da série N.

### <a name="nc-vms-tesla-k80-card"></a>VMs NC (placa Tesla K80)
* Ubuntu 16.04 LTS 
* Red Hat Enterprise Linux 7.3 
* CentOS 7.3 

**Drivers com suporte**: NVIDIA CUDA 8.0, ramificação do driver R375. [Etapas de instalação](#install-cuda-drivers-for-nc-vms)




> [!WARNING] 
> A instalação de software de terceiros em produtos do Red Hat pode afetar os termos de suporte do Red Hat. Consulte o [artigo da Base de conhecimento do Red Hat](https://access.redhat.com/articles/1067).
>




## <a name="install-cuda-drivers-for-nc-vms"></a>Instalar drivers CUDA Tesla para VMs NC

Aqui estão as etapas para instalar drivers NVIDIA em VMs NC Linux do Kit de ferramentas NVIDIA CUDA 8.0. 

Os desenvolvedores de C e C++ podem opcionalmente instalar o Kit de ferramentas completo para criar aplicativos com aceleração de GPU. Para obter mais informações, consulte o [Guia de instalação do CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).


> [!NOTE]
> Os links de download do driver CUDA fornecidos aqui são atuais no momento da publicação. Para os drivers mais recentes, visite o site da [NVIDIA](http://www.nvidia.com/).
>

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

### <a name="centos-73-or-red-hat-enterprise-linux-73"></a>CentOS 7.3 ou Red Hat Enterprise Linux 7.3

> [!IMPORTANT] 
> Devido a um problema conhecido, a instalação do driver NVIDIA CUDA falha em VMs NC24r nas quais CentOS 7.3 ou Red Hat Enterprise Linux 7.3 esteja em execução.
>

Primeiro, obtenha as atualizações. 

```bash
sudo yum update

sudo reboot
```

Reconecte-se à VM e continue a instalação com os seguintes comandos:

```bash
sudo yum install kernel-devel

sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

sudo yum install dkms

CUDA_REPO_PKG=cuda-repo-rhel7-8.0.61-1.x86_64.rpm

wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo yum install cuda-drivers
```

A instalação poderá levar vários minutos. Para opcionalmente instalar o Kit de ferramentas CUDA completo, digite:

```bash
sudo yum install cuda
```

Reinicie a VM e prossiga para verificar a instalação.


### <a name="verify-driver-installation"></a>Verificar a instalação de drivers


Para consultar o estado do dispositivo GPU, conecte-se à VM por SSH e execute o utilitário de linha de comando [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) instalado com o driver. 

Saída semelhante à seguinte exibida:

![Status do dispositivo NVIDIA](./media/n-series-driver-setup/smi.png)


### <a name="cuda-driver-updates"></a>Atualizações de driver CUDA

É recomendável que você atualize periodicamente os drivers CUDA após a implantação.

#### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers
```

Após a atualização, reinicie a VM.

#### <a name="centos-73-or-red-hat-enterprise-linux-73"></a>CentOS 7.3 ou Red Hat Enterprise Linux 7.3

```bash
sudo yum update
```

Após a atualização, reinicie a VM.



## <a name="troubleshooting"></a>Solucionar problemas

* Há um problema conhecido com drivers CUDA em VMs do Azure série N executando o kernel do Linux 4.4.0-75 no Ubuntu 16.04 LTS. Para manter a função do driver quando você atualiza o kernel, atualize para pelo meno a versão do kernel 4.4.0-77. 



## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre as GPUs NVIDIA nas VMs da série N, consulte:
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (para VMs NC do Azure)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (para VMs NV do Azure)

* Para capturar uma imagem de VM do Linux na qual você tenha instalado drivers NVIDIA, consulte [Como generalizar e capturar uma máquina virtual Linux](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
