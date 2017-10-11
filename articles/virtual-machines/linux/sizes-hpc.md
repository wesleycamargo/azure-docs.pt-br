---
title: "Tamanhos de VM Linux do Azure — HPC | Microsoft Docs"
description: "Lista os diferentes tamanhos disponíveis para máquinas virtuais de computação de alto desempenho Linux no Azure."
services: virtual-machines-linux
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/28/2017
ms.author: jonbeck
ms.openlocfilehash: b7a3844ce86b4efac8a4fc3c2540e7b6460873a2
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2017
---
# <a name="high-performance-compute-linux-vm-sizes"></a>Tamanhos de VM Linux de computação de alto desempenho

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="rdma-capable-instances"></a>Instâncias compatíveis com RDMA
Um subconjunto das instâncias de computação intensiva (H16r, H16mr, A8 e A9) apresenta um adaptador de rede para conectividade RDMA (acesso remoto direto à memória). Essa interface é além do adaptador de rede do Azure padrão disponível para outros tamanhos de VM. 
  
Essa interface permite que instâncias compatíveis com RDMA se comuniquem em uma rede InfiniBand, operando em taxas FDR para máquinas virtuais H16r e H16mr e taxas QDR para máquinas virtuais A8 e A9. Esses recursos RDMA podem melhorar a escalabilidade e o desempenho de aplicativos MPI (Interface de Transmissão de Mensagens).

Estes são os requisitos para VMs Linux compatíveis com RDMA acessarem a rede RDMA do Azure:
 
* **Distribuições** – Você deve implantar as VMs de imagens HPC com base em Rogue Wave Software (anteriormente OpenLogic) CentOS ou SLES (SUSE Linux Enterprise Server) compatíveis com RDMA no Azure Marketplace. As seguintes imagens do Marketplace dão suporte à conectividade RDMA:
  
    * SLES 12 SP1 para HPC ou SLES 12 SP1 para HPC (Premium)
    
    * HPC 7.3, 7.1, 6.8 ou 6.5 baseado em CentOS  
 
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
    
    Configurações adicionais do sistema são necessárias para executar trabalhos MPI em máquinas virtuais clusterizadas. Por exemplo, em um cluster de máquinas virtuais, você precisa estabelecer confiança entre os nós de computação. Para configurações típicas, consulte [Configurar um cluster de RDMA do Linux para executar aplicativos MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="network-topology-considerations"></a>Considerações de topologia de rede
* Em VMs Linux habilitadas para RDMA no Azure, Eth1 é reservado para o tráfego de rede RDMA. Não altere as configurações de Eth1 ou as informações no arquivo de configuração que se refere a essa rede. Eth0 é reservado para o tráfego de rede regular do Azure.

* IP sobre InfiniBand (IB) não tem suporte no Azure. Apenas RDMA sobre IB é compatível.

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




