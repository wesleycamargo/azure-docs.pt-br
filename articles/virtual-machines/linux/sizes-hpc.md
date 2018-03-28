---
title: Tamanhos de VM Linux do Azure — HPC | Microsoft Docs
description: Lista os diferentes tamanhos disponíveis para máquinas virtuais de computação de alto desempenho Linux no Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como a taxa de transferência de armazenamento e largura de banda de rede para cada tamanho nessa série.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: timlt
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: jonbeck
ms.openlocfilehash: 5f867140981649b73bf6d0bc13eca539c7dc2209
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2018
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Tamanhos de máquina virtual de computação de alto desempenho

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Há suporte para apenas as versões 5.x do Intel MPI. Versões posteriores (2017, 2018) da biblioteca de tempo de execução do Intel MPI não são compatíveis com os drivers RDMA do Azure para Linux.


### <a name="distributions"></a>Distribuições
 
Implante uma VM de computação intensiva de uma das imagens no Azure Marketplace que dá suporte à conectividade RDMA:
  
* **Ubuntu** – Ubuntu Server 16.04 LTS. Configure os drivers RDMA na VM e registre-se na Intel para baixar o Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **SUSE Linux Enterprise Server** - SLES 12 SP3 para HPC, SLES 12 SP3 para HPC (Premium), SLES 12 SP1 para HPC, SLES 12 SP1 para HPC (Premium). Os drivers RDMA são instalados e os pacotes do Intel MPI são distribuídos na VM. Instale o MPI executando o seguinte comando:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
    
* **HPC baseado em HPC** - HPC baseado em CentOS 6.5 ou versão superior (para série H, recomenda-se a versão 7.1 ou posterior). OS drivers RDMA e o Intel MPI 5.1 são instalados na VM.  
 
  > [!NOTE]
  > Nas imagens do HPC baseado em CentOS, as atualizações de kernel estão desabilitadas no arquivo de configuração **yum** . Isso ocorre porque os drivers RDMA do Linux são distribuídos como um pacote RPM, e as atualizações de driver poderão não funcionar caso o kernel seja atualizado.
  > 
 
### <a name="cluster-configuration"></a>Configuração do cluster 
    
Configurações adicionais do sistema são necessárias para executar trabalhos MPI em máquinas virtuais clusterizadas. Por exemplo, em um cluster de máquinas virtuais, você precisa estabelecer confiança entre os nós de computação. Para configurações típicas, consulte [Configurar um cluster de RDMA do Linux para executar aplicativos MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

### <a name="network-topology-considerations"></a>Considerações de topologia de rede
* Em VMs Linux habilitadas para RDMA no Azure, Eth1 é reservado para o tráfego de rede RDMA. Não altere as configurações de Eth1 ou as informações no arquivo de configuração que se refere a essa rede. Eth0 é reservado para o tráfego de rede regular do Azure.

* A rede RDMA no Azure reserva o espaço de endereço 172.16.0.0/16. 


## <a name="using-hpc-pack"></a>Usando o HPC Pack
[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), a solução de gerenciamento de trabalho e cluster HPC gratuita da Microsoft, é uma opção para usar as instâncias de computação intensiva com o Linux. As últimas versões do HPC Pack dão suporte a várias distribuições Linux para execução em nós de computação implantados nas VMs do Azure, gerenciados por um nó de cabeçalho do Windows Server. Com os nós de computação Linux compatíveis com RDMA executando o Intel MPI, o HPC Pack pode agendar e executar os aplicativos Linux MPI que acessam a rede RDMA. Consulte [Introdução a nós de computação Linux em um cluster de HPC Pack no Azure](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="other-sizes"></a>Outros tamanhos
- [Propósito geral](sizes-general.md)
- [Computação otimizada](sizes-compute.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)


## <a name="next-steps"></a>Próximas etapas

- Para se familiarizar com a implantação e o uso dos tamanhos de computação intensiva com o RDMA no Linux, veja [Configurar um cluster de RDMA do Linux para executar aplicativos MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

- Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.




