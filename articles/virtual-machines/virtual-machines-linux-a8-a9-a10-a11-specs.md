---
title: "Sobre VMs de computação intensiva com Linux | Microsoft Docs"
description: "Obter informações básicas e considerações sobre o uso da série H e dos tamanhos A8, A9, A10 e A11 com computação intensiva para VMs do Linux"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 16cf6e2d-f401-4b22-af8c-9a337179f5f6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/18/2016
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: f3b1593a80e6eedc08005988d485a317951efd33
ms.openlocfilehash: ad2beff5bbc6aaa18247939ccfe310460f9ccdee
ms.lasthandoff: 03/01/2017

---
# <a name="about-h-series-and-compute-intensive-a-series-vms-for-linux"></a>Sobre as VMs da série H e da série A de computação intensiva para Linux
Apresentamos a seguir informações básicas e algumas considerações sobre o uso da série H mais recente e dos tamanhos A8, A9, A10 e A11 do Azure, também conhecidos como instâncias de *computação intensiva* . Este artigo ressalta o uso desses tamanhos para VMs Linux. Este artigo também está disponível para [VMs Windows](virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Para obter as especificações básicas, as capacidades de armazenamento e os detalhes de disco, consulte [Tamanhos das máquinas virtuais](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>Acesso à rede RDMA
Você pode criar clusters de VMs do Linux compatíveis com RDMA que executam uma das seguintes distribuições de Linux HPC com suporte e uma implementação MPI com suporte para tirar proveito da rede RDMA do Azure. Veja [Configurar um cluster de RDMA do Linux para executar aplicativos MPI](virtual-machines-linux-classic-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) para obter opções de implantação e etapas de configuração de exemplo.

* **Distribuições** – Você deve implantar as VMs de imagens HPC com base em OpenLogic CentOS ou SLES (SUSE Linux Enterprise Server) compatíveis com RDMA no Azure Marketplace. Somente as seguintes imagens do Marketplace dão suporte aos drivers de RDMA do Linux necessários:
  
  * SLES 12 SP1 para HPC, SLES 12 SP1 para HPC (Premium)
  * SLES 12 para HPC, SLES 12 para HPC (Premium)
  * HPC 7.1 baseado em CentOS
  * HPC 6.5 baseado em CentOS
    
    > [!NOTE]
    > Para VMs da série H, recomendamos o SLES 12 SP1 para imagem do HPC ou imagem do HPC 7.1 baseado em CentOS.
    > 
    > Nas imagens do HPC baseado em CentOS, as atualizações de kernel estão desabilitadas no arquivo de configuração **yum** . Isso ocorre porque os drivers RDMA do Linux são distribuídos como um pacote RPM, e as atualizações de driver poderão não funcionar caso o kernel seja atualizado.
    > 
    > 
* **MPI** - Intel MPI Library 5.x
  
    Dependendo da imagem do Marketplace escolhida, podem ser necessários licenciamento, instalação ou configuração separada do Intel MPI, da seguinte maneira: 
  
  * **SLES 12 SP1 para imagem do HPC** – Instale os pacotes Intel MPI distribuídos na VM executando o seguinte comando:
    
          sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  * **SLES 12 para imagem do HPC** – Separadamente, você deve registrar para baixar e instalar o Intel MPI. Para obter instruções, veja a [documentação da Biblioteca do Intel MPI](https://software.intel.com/en-us/intel-mpi-library/documentation).
  * **Imagens do HPC baseado em CentOS** – O Intel MPI 5.1 já está instalado.  
    
    Configurações adicionais do sistema são necessárias para executar trabalhos MPI em máquinas virtuais clusterizadas. Por exemplo, em um cluster de máquinas virtuais, você precisa estabelecer confiança entre os nós de computação. Para configurações típicas, consulte [Configurar um cluster de RDMA do Linux para executar aplicativos MPI](virtual-machines-linux-classic-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="considerations-for-hpc-pack-and-linux"></a>Considerações para HPC Pack e Linux
[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), solução gratuita da Microsoft de gerenciamento de trabalho e cluster HPC, fornece uma opção para usar as instâncias de computação intensiva com o Linux. As versões mais recentes do HPC Pack 2012 R2 são compatíveis com várias distribuições Linux para execução nos nós de computação implantados nas VMs do Azure, gerenciados por um nó de cabeçalho do Windows Server. Com os nós de computação Linux compatíveis com RDMA executando o Intel MPI, o HPC Pack pode agendar e executar os aplicativos Linux MPI que acessam a rede RDMA. Para se familiarizar, veja [Introdução aos nós de computação do Linux em um cluster HPC Pack no Azure](virtual-machines-linux-classic-hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="network-topology-considerations"></a>Considerações de topologia de rede
* Em VMs Linux habilitadas para RDMA no Azure, Eth1 é reservado para o tráfego de rede RDMA. Não altere as configurações de Eth1 ou as informações no arquivo de configuração que se refere a essa rede. Eth0 é reservado para o tráfego de rede regular do Azure.
* IP sobre InfiniBand (IB) não tem suporte no Azure. Apenas RDMA sobre IB é compatível.

## <a name="rdma-driver-updates-for-sles-12"></a>Atualizações de driver RDMA para SLES 12
Depois de criar a VM baseada em uma imagem do HPC SLES 12, talvez seja necessário atualizar os drivers RDMA nas VMs para a conectividade de rede RDMA. 

> [!IMPORTANT]
> Esta etapa é **necessária** para implantações de VM do HPC SLES 12 em todas as regiões do Azure. Essa etapa não será necessária se você implantar um SLES 12 SP1 para HPC, HPC 7.1 baseado em CentOS ou uma VM do HPC 6.5 baseado em CentOS. 
> 
> 

Antes de atualizar os drivers, interrompa todos os processos **zypper** ou os processos que bloqueiam os bancos de dados de repositório SUSE na VM. Caso contrário, os drivers não poderão ser atualizados corretamente.  

Para atualizar os drivers RDMA do Linux em cada VM, execute um dos seguintes conjuntos de comandos da CLI do Azure no computador cliente.

**SLES 12 para VM do HPC provisionada no modelo de implantação clássica**

```
azure config mode asm

azure vm extension set <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

**SLES 12 para VM do HPC provisionada no modelo de implantação do Resource Manager**

```
azure config mode arm

azure vm extension set <resource-group> <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

> [!NOTE]
> A instalação dos drivers pode levar algum tempo e o comando retorna sem saída. Após a atualização, a VM será reiniciada e deve estar pronta para uso em alguns minutos.
> 
> 

### <a name="sample-script-for-driver-updates"></a>Script de exemplo para atualizações de driver
Se você tiver um cluster SLES 12 para VMs do HPC, será possível escrever scripts da atualização de driver em todos os nós no cluster. Por exemplo, o script a seguir atualiza os drivers em um cluster de 8 nós.

```

#!/bin/bash -x

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Plug in appropriate numbers in the for loop below.

for (( i=11; i<19; i++ )); do

# For VMs created in the classic deployment model, use the following command in your script.

azure vm extension set $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

# For VMs created in the Resource Manager deployment model, use the following command in your script.

# azure vm extension set <resource-group> $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

done

```


## <a name="next-steps"></a>Próximas etapas
* Para obter detalhes sobre a disponibilidade e os preços dos tamanhos de computação intensiva, confira [Preços das máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux).
* Para obter os detalhes de disco e as capacidades de armazenamento, consulte [Tamanhos das máquinas virtuais](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Para se familiarizar com a implantação e o uso dos tamanhos de computação intensiva com o RDMA no Linux, veja [Configurar um cluster de RDMA do Linux para executar aplicativos MPI](virtual-machines-linux-classic-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).


