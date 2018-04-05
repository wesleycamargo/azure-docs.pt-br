---
title: Instalação de drivers da série N do Azure para Linux | Microsoft Docs
description: Como configurar drivers NVIDIA GPU para VMs da série N que executam o Linux no Azure
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/20/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d97afd2b5dccca64db2df7cb0d4f110987642cfb
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Instalar drivers NVIDIA GPU em VMs da série N que executam o Linux

Para aproveitar as funcionalidades de GPU das VMs da série N do Azure que executam Linux, instale os drivers gráficos NVIDIA. Este artigo apresenta etapas de instalação do driver depois que você implanta uma VM da série N. Também há informações de instalação de driver disponíveis para [VMs do Windows](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Para obter as especificações de VMs da série N, as capacidades de armazenamento e os detalhes de disco, consulte [Tamanhos de VM Linux para GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-for-nc-ncv2-ncv3-and-nd-series-vms"></a>Instalar drivers CUDA para VMs série NC, NCv2, NCv3 e ND

Aqui estão as etapas para instalar os drivers CUDA a partir do NVIDIA CUDA Toolkit em VMs série N. 


Os desenvolvedores de C e C++ podem opcionalmente instalar o Kit de ferramentas completo para criar aplicativos com aceleração de GPU. Para obter mais informações, consulte o [Guia de instalação do CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).

Para instalar os drivers de CUDA, realize uma conexão SSH para cada VM. Para verificar se o sistema tem uma GPU compatível com CUDA, execute o seguinte comando:

```bash
lspci | grep -i NVIDIA
```
Você verá uma saída semelhante ao seguinte exemplo (mostrando uma placa NVIDIA Tesla K80):

![Saída do comando lspci](./media/n-series-driver-setup/lspci.png)

Em seguida, execute os comandos de instalação específicos para a sua distribuição.

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

1. Baixe e instale os drivers CUDA.
  ```bash
  CUDA_REPO_PKG=cuda-repo-ubuntu1604_9.1.85-1_amd64.deb

  wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

  sudo dpkg -i /tmp/${CUDA_REPO_PKG}

  sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 

  rm -f /tmp/${CUDA_REPO_PKG}

  sudo apt-get update

  sudo apt-get install cuda-drivers

  ```

  A instalação poderá levar vários minutos.

2. Para opcionalmente instalar o Kit de ferramentas CUDA completo, digite:

  ```bash
  sudo apt-get install cuda
  ```

3. Reinicie a VM e prossiga para verificar a instalação.

#### <a name="cuda-driver-updates"></a>Atualizações de driver CUDA

É recomendável que você atualize periodicamente os drivers CUDA após a implantação.

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux-73-or-74"></a>CentOS ou Red Hat Enterprise Linux 7.3 ou 7.4

1. Atualize o kernel.

  ```
  sudo yum install kernel kernel-tools kernel-headers kernel-devel
  
  sudo reboot

2. Install the latest [Linux Integration Services for Hyper-V and Azure](https://www.microsoft.com/download/details.aspx?id=55106).

  ```bash
  wget https://aka.ms/lis
 
  tar xvzf lis
 
  cd LISISO
 
  sudo ./install.sh
 
  sudo reboot
  ```
 
3. Reconecte-se à VM e continue a instalação com os seguintes comandos:

  ```bash
  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

  sudo yum install dkms

  CUDA_REPO_PKG=cuda-repo-rhel7-9.1.85-1.x86_64.rpm

  wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

  sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

  rm -f /tmp/${CUDA_REPO_PKG}

  sudo yum install cuda-drivers
  ```

  A instalação poderá levar vários minutos. 

4. Para opcionalmente instalar o Kit de ferramentas CUDA completo, digite:

  ```bash
  sudo yum install cuda
  ```

5. Reinicie a VM e prossiga para verificar a instalação.

### <a name="verify-driver-installation"></a>Verificar a instalação de drivers

Para consultar o estado do dispositivo GPU, conecte-se à VM por SSH e execute o utilitário de linha de comando [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) instalado com o driver. 

Se o driver estiver instalado, você verá uma saída parecida com a mostrada a seguir. Observe que **GPU-Util** mostrará 0%, a menos que você esteja executando uma carga de trabalho da GPU na VM. Sua versão de driver e os detalhes GPU podem ser diferentes daqueles mostrados.

![Status do dispositivo NVIDIA](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>Conectividade de rede RDMA

A conectividade de rede RDMA pode ser habilitada em VMs da série N habilitadas para RDMA, como a NC24r, implantada no mesmo conjunto de disponibilidade ou conjunto de dimensionamento de VM. A rede RDMA dá suporte ao tráfego da Interface de transmissão de mensagens (MPI) para aplicativos executados com Intel MPI 5.x ou uma versão posterior. Requisitos adicionais são listados a seguir:

### <a name="distributions"></a>Distribuições

Implante VMs da série N habilitadas para RDMA de uma das imagens no Azure Marketplace que é compatível com a conectividade RDMA nas VMs da série N:
  
* **Ubuntu 16.04 LTS** – Configure os drivers RDMA na VM e registre-se na Intel para baixar a MPI da Intel:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **CentOS-based 7.4 HPC** - Os drivers RDMA e o Intel MPI 5.1 são instalados na VM.

## <a name="install-grid-drivers-for-nv-series-vms"></a>Instalar drivers GRID para VMs série NV

Para instalar drivers NVIDIA GRID em VMs serie NV, faça uma conexão SSH com cada VM e siga as etapas para sua distribuição do Linux. 

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

1. Execute o comando `lspci`. Verifique se a placa ou placas NVIDIA M60 estão visíveis como dispositivos PCI.

2. Instale as atualizações.

  ```bash
  sudo apt-get update

  sudo apt-get upgrade -y

  sudo apt-get dist-upgrade -y

  sudo apt-get install build-essential ubuntu-desktop -y
  ```
3. Desabilite o driver de kernel Nouveau, que é incompatível com o driver NVIDIA. (Use somente o driver NVIDIA em VMs NV.) Para fazer isso, crie um arquivo em `/etc/modprobe.d `chamado `nouveau.conf` com o conteúdo a seguir:

  ```
  blacklist nouveau

  blacklist lbm-nouveau
  ```


4. Reinicie a VM e reconecte. Saia do servidor X:

  ```bash
  sudo systemctl stop lightdm.service
  ```

5. Baixe e instale o driver GRID:

  ```bash
  wget -O NVIDIA-Linux-x86_64-384.111-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-384.111-grid.run

  sudo ./NVIDIA-Linux-x86_64-384.111-grid.run
  ``` 

6. Quando você for questionado se deseja executar o utilitário nvidia-xconfig para atualizar seu arquivo de configuração X, selecione **Sim**.

7. Após a conclusão da instalação, copie /etc/nvidia/gridd.conf.template para um novo arquivo gridd.conf no local /etc/hosts nvidia/

  ```bash
  sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
  ```

8. Adicione o seguinte a `/etc/nvidia/gridd.conf`:
 
  ```
  IgnoreSP=TRUE
  ```
9. Reinicie a VM e prossiga para verificar a instalação.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS ou Red Hat Enterprise Linux 

1. Atualize o kernel e o DKMS.
 
  ```bash  
  sudo yum update
 
  sudo yum install kernel-devel
 
  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
  sudo yum install dkms
  ```

2. Desabilite o driver de kernel Nouveau, que é incompatível com o driver NVIDIA. (Use somente o driver NVIDIA em VMs NV.) Para fazer isso, crie um arquivo em `/etc/modprobe.d `chamado `nouveau.conf` com o conteúdo a seguir:

  ```
  blacklist nouveau

  blacklist lbm-nouveau
  ```
 
3. Reinicie a VM, reconecte e instale o último [Integration Services do Linux para Hyper-V e Azure](https://www.microsoft.com/download/details.aspx?id=55106).
 
  ```bash
  wget https://aka.ms/lis

  tar xvzf lis

  cd LISISO

  sudo ./install.sh

  sudo reboot

  ```
 
4. Reconecte-se à VM e execute o comando `lspci`. Verifique se a placa ou placas NVIDIA M60 estão visíveis como dispositivos PCI.
 
5. Baixe e instale o driver GRID:

  ```bash
  wget -O NVIDIA-Linux-x86_64-384.111-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-384.111-grid.run

  sudo ./NVIDIA-Linux-x86_64-384.111-grid.run
  ``` 
6. Quando você for questionado se deseja executar o utilitário nvidia-xconfig para atualizar seu arquivo de configuração X, selecione **Sim**.

7. Após a conclusão da instalação, copie /etc/nvidia/gridd.conf.template para um novo arquivo gridd.conf no local /etc/hosts nvidia/
  
  ```bash
  sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
  ```
  
8. Adicione o seguinte a `/etc/nvidia/gridd.conf`:
 
  ```
  IgnoreSP=TRUE
  ```
9. Reinicie a VM e prossiga para verificar a instalação.

### <a name="verify-driver-installation"></a>Verificar a instalação de drivers


Para consultar o estado do dispositivo GPU, conecte-se à VM por SSH e execute o utilitário de linha de comando [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) instalado com o driver. 

Se o driver estiver instalado, você verá uma saída parecida com a mostrada a seguir. Observe que **GPU-Util** mostrará 0%, a menos que você esteja executando uma carga de trabalho da GPU na VM. Sua versão de driver e os detalhes GPU podem ser diferentes daqueles mostrados.

![Status do dispositivo NVIDIA](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>Servidor X11
Se você precisar de um servidor X11 para conexões remotas com uma VM NV, o [x11vnc](http://www.karlrunge.com/x11vnc/) é recomendado porque ele permite a aceleração de hardware do elemento gráfico. A BusID do dispositivo M60 deve ser adicionada manualmente ao arquivo xconfig (`etc/X11/xorg.conf` no Ubuntu 16.04 LTS, `/etc/X11/XF86config` no CentOS 7.3 ou Red Hat Enterprise Server 7.3). Adicione uma seção `"Device"` semelhante à seguinte:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "your-BusID:0:0:0"
EndSection
```
 
Além disso, atualize sua seção `"Screen"` para usar este dispositivo.
 
A BusID decimal pode ser encontrada executando

```bash
echo $((16#`/usr/bin/nvidia-smi --query-gpu=pci.bus_id --format=csv | tail -1 | cut -d ':' -f 1`))
```
 
A BusID pode mudar quando uma VM é realocada ou reinicializada. Portanto, convém usar um script para atualizar a BusID na configuração do X11 quando uma VM é reiniciada. Por exemplo: 

```bash 
#!/bin/bash
BUSID=$((16#`/usr/bin/nvidia-smi --query-gpu=pci.bus_id --format=csv | tail -1 | cut -d ':' -f 1`))

if grep -Fxq "${BUSID}" /etc/X11/XF86Config; then     echo "BUSID is matching"; else   echo "BUSID changed to ${BUSID}" && sed -i '/BusID/c\    BusID          \"PCI:0@'${BUSID}':0:0:0\"' /etc/X11/XF86Config; fi
```

Esse arquivo pode ser invocado como raiz na inicialização criando uma entrada para ele em `/etc/rc.d/rc3.d`.

## <a name="troubleshooting"></a>solução de problemas

* Você pode definir o modo de persistência usando `nvidia-smi`, de modo que o resultado do comando seja mais rápido quando você precisar consultar cartões. Para definir o modo de persistência, execute `nvidia-smi -pm 1`. Observe que, se a VM for reiniciada, a configuração do modo desaparecerá. Você sempre pode gerar um script da configuração de modo para ser executada na inicialização.

## <a name="next-steps"></a>Próximas etapas

* Para capturar uma imagem de VM do Linux na qual você tenha instalado drivers NVIDIA, consulte [Como generalizar e capturar uma máquina virtual Linux](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
