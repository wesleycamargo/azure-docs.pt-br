<properties
 pageTitle="Sobre VMs de computação intensiva com Windows | Microsoft Azure"
 description="Obter informações básicas e considerações sobre o uso da série H e dos tamanhos A8, A9, A10 e A11 do Azure com computação intensiva para VMs do Windows e serviços de nuvem"
 services="virtual-machines-windows, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="09/21/2016"
 ms.author="danlep"/>

# Sobre VMs série H ou série A com computação intensiva

Apresentamos a seguir informações básicas e algumas considerações sobre o uso da série H mais recente e das instâncias A8, A9, A10 e A11 do Azure, também conhecidas como instâncias de *computação intensiva*. Este artigo se concentra no uso destas instâncias para VMs Windows. Este artigo também está disponível para [VMs Linux](virtual-machines-linux-a8-a9-a10-a11-specs.md).


[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## Acesso à rede RDMA

Você pode criar clusters de instâncias do Windows Server compatíveis com RDMA e implantar uma das implementações de MPI com suporte para tirar proveito da rede RDMA do Azure. Essa rede de baixa latência e alta taxa de transferência é reservada para o tráfego de MPI apenas.

* **Sistema operacional**
    * **Máquinas virtuais** – Windows Server 2012 R2, Windows Server 2012
    * **Serviços de nuvem** – Família de SO convidado Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2

* **MPI** – Microsoft MPI (MS-MPI) 2012 R2 ou posterior, Intel MPI Library 5.x

Implementações de MPI com suporte usam a interface direta da rede Microsoft para comunicação entre instâncias. Consulte [Configurar um cluster do Windows RDMA com o HPC Pack para executar aplicativos MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md) e [Usar tarefas de várias instâncias para executar aplicativos de MPI (Interface de Transmissão de Mensagens) no Lote do Azure](../batch/batch-mpi.md) para ver opções de implantação e etapas de configuração de exemplo.


>[AZURE.NOTE]Em VMs de computação intensiva compatíveis com RDMA, a extensão HpcVmDrivers deve ser adicionada às VMs a fim de instalar os drivers de dispositivo de rede do Windows necessários para a conectividade RDMA. Na maioria das implantações, a extensão HpcVmDrivers é adicionada automaticamente. Se você precisar adicionar a extensão por conta própria, veja [Gerenciar extensões de VM](virtual-machines-windows-classic-manage-extensions.md).

## Considerações para HPC Pack e Windows

[Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), solução gratuita da Microsoft de gerenciamento de trabalho e cluster HPC, não é necessário para você usar as instâncias de computação intensiva com o Windows Server. No entanto, é uma opção para criar um cluster de cálculo no Azure para executar aplicativos MPI baseados no Windows e outras cargas de trabalho de HPC. HPC Pack 2012 R2 e versões posteriores incluem um ambiente de tempo de execução para MS-MPI que pode usar a rede RDMA do Azure quando implantado em VMs compatíveis com RDMA.

Para obter mais informações e listas de verificação para usar as instâncias de computação intensiva com o HPC Pack no Windows Server, veja [Configurar um cluster de RDMA do Windows com o HPC Pack para executar aplicativos MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).




## Próximas etapas

* Para obter detalhes sobre disponibilidade e preços dos tamanhos de computação intensiva, confira [Preços das máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows) e [Preços dos serviços de nuvem](https://azure.microsoft.com/pricing/details/cloud-services/).

* Para obter os detalhes de disco e as capacidades de armazenamento, consulte [Tamanhos das máquinas virtuais](virtual-machines-linux-sizes.md).

* Para começar a implantar e usar instâncias de computação intensiva com o HPC Pack no Windows, veja [Configurar um cluster de RDMA do Windows com o HPC Pack para executar aplicativos MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).

* Para obter informações sobre como usar as instâncias A8 e A9 para executar aplicativos MPI com o Lote do Azure, veja [Usar tarefas de várias instâncias para executar aplicativos de MPI (interface de transmissão de mensagens) no Lote do Azure](../batch/batch-mpi.md).

<!---HONumber=AcomDC_0928_2016-->