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
ms.date: 03/14/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: a2307f7055966ec7146b5da0b4daf1ad469abe2b
ms.contentlocale: pt-br
ms.lasthandoff: 05/11/2017

---
# <a name="about-h-series-and-compute-intensive-a-series-vms-for-linux"></a>Sobre as VMs da série H e da série A de computação intensiva para Linux
Apresentamos a seguir informações básicas e algumas considerações sobre o uso da série H mais recente e dos tamanhos A8, A9, A10 e A11 do Azure, também conhecidos como instâncias de *computação intensiva* . Este artigo ressalta o uso desses tamanhos para VMs Linux. Você também pode usá-los para [VMs Windows](../windows/a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Para obter as especificações básicas, as capacidades de armazenamento e os detalhes de disco, consulte [Tamanhos das máquinas virtuais](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>Acesso à rede RDMA
Você pode criar clusters de VMs do Linux compatíveis com RDMA que executam uma das seguintes distribuições de Linux HPC com suporte e uma implementação MPI com suporte para tirar proveito da rede RDMA do Azure. Veja [Configurar um cluster de RDMA do Linux para executar aplicativos MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) para obter opções de implantação e etapas de configuração de exemplo.

* **Distribuições** – Você deve implantar as VMs de imagens HPC com base em Rogue Wave Software (anteriormente OpenLogic) CentOS ou SLES (SUSE Linux Enterprise Server) compatíveis com RDMA no Azure Marketplace. As seguintes imagens do Marketplace dão suporte à conectividade RDMA:
  
    * SLES 12 SP1 para HPC, SLES 12 SP1 para HPC (Premium)
    
    * HPC baseado em CentOS 7.1, HPC baseado em CentOS 6.5  
 
        > [!NOTE]
        > Para VMs da série H, recomendamos o SLES 12 SP1 para imagem do HPC ou imagem do HPC 7.1 baseado em CentOS.
        >
        > Nas imagens do HPC baseado em CentOS, as atualizações de kernel estão desabilitadas no arquivo de configuração **yum** . Isso ocorre porque os drivers RDMA do Linux são distribuídos como um pacote RPM, e as atualizações de driver poderão não funcionar caso o kernel seja atualizado.
        > 
        > 
* **MPI** - Intel MPI Library 5.x
  
    Dependendo da imagem do Marketplace escolhida, podem ser necessários licenciamento, instalação ou configuração separada do Intel MPI, da seguinte maneira: 
  
  * **SLES 12 SP1 para imagem HPC** – pacotes Intel MPI são distribuídos na VM. Instale o executando o seguinte comando:

      ```bash
      sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
      ```

  * **Imagens do HPC baseado em CentOS** – O Intel MPI 5.1 já está instalado.  
    
    Configurações adicionais do sistema são necessárias para executar trabalhos MPI em máquinas virtuais clusterizadas. Por exemplo, em um cluster de máquinas virtuais, você precisa estabelecer confiança entre os nós de computação. Para configurações típicas, consulte [Configurar um cluster de RDMA do Linux para executar aplicativos MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="considerations-for-hpc-pack-and-linux"></a>Considerações para HPC Pack e Linux
[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), solução gratuita da Microsoft de gerenciamento de trabalho e cluster HPC, fornece uma opção para usar as instâncias de computação intensiva com o Linux. As últimas versões do HPC Pack dão suporte a várias distribuições Linux para execução em nós de computação implantados nas VMs do Azure, gerenciados por um nó de cabeçalho do Windows Server. Com os nós de computação Linux compatíveis com RDMA executando o Intel MPI, o HPC Pack pode agendar e executar os aplicativos Linux MPI que acessam a rede RDMA. Para se familiarizar, veja [Introdução aos nós de computação do Linux em um cluster HPC Pack no Azure](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="network-topology-considerations"></a>Considerações de topologia de rede
* Em VMs Linux habilitadas para RDMA no Azure, Eth1 é reservado para o tráfego de rede RDMA. Não altere as configurações de Eth1 ou as informações no arquivo de configuração que se refere a essa rede. Eth0 é reservado para o tráfego de rede regular do Azure.
* IP sobre InfiniBand (IB) não tem suporte no Azure. Apenas RDMA sobre IB é compatível.



## <a name="next-steps"></a>Próximas etapas
* Para obter detalhes sobre a disponibilidade e os preços dos tamanhos de computação intensiva, confira [Preços das máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux).
* Para obter os detalhes de disco e as capacidades de armazenamento, consulte [Tamanhos das máquinas virtuais](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Para se familiarizar com a implantação e o uso dos tamanhos de computação intensiva com o RDMA no Linux, veja [Configurar um cluster de RDMA do Linux para executar aplicativos MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).


