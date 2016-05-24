<properties
 pageTitle="Sobre as instâncias A8 a A11 com o Windows | Microsoft Azure"
 description="Obter informações básicas e considerações sobre o uso dos tamanhos A8, A9, A10 e A11 do Azure com computação intensiva para VMs do Windows e serviços de nuvem"
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
 ms.date="04/26/2016"
 ms.author="danlep"/>

# Sobre as instâncias de computação intensiva A8, A9, A10 e A11

Apresentamos a seguir informações básicas e algumas considerações sobre o uso das instâncias A8, A9, A10 e A11 do Azure, também conhecidas como instâncias de *computação intensiva*. Este artigo se concentra no uso destas instâncias para VMs Windows. Este artigo também está disponível para [VMs Linux](virtual-machines-linux-a8-a9-a10-a11-specs.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## Acesso à rede RDMA

Em um único serviço de nuvem, conjunto de disponibilidade ou pool do Lote do Azure, as instâncias A8 e A9 podem acessar a rede RDMA no Azure para executar aplicativos MPI do Windows que utilizam a interface direta da rede da Microsoft para a comunicação entre instâncias.

Consulte a tabela a seguir a fim de obter os pré-requisitos para que os aplicativos MPI acessem a rede RDMA em máquinas virtuais Windows, serviços de nuvem e pools do Lote do Azure das instâncias A8 e A9. Para cenários comuns de implantação, consulte [Configurar um cluster do Windows RDMA com o HPC Pack para executar aplicativos MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md) e [Usar tarefas de várias instâncias para executar aplicativos de MPI (interface de transmissão de mensagens) no Lote do Azure](../batch/batch-mpi.md).


Pré-requisito | Máquinas virtuais | Serviços de nuvem ou pool do Lote 
---------- | ------------ | ------------- 
Sistema operacional | Windows Server 2012 R2 ou Windows Server 2012 | Família de SO convidado Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2 
MPI | MS-MPI 2012 R2 ou posterior ou Intel MPI Library 5 | MS-MPI 2012 R2 ou posterior ou Intel MPI Library 5 


>[AZURE.NOTE]Em máquinas virtuais do tamanho A8 e A9, a extensão HpcVmDrivers deve ser adicionada às VMs a fim de instalar os drivers de dispositivo de rede do Windows necessários para a conectividade RDMA. Dependendo do método de implantação, a extensão HpcVmDrivers pode ser adicionada a uma VM de tamanho A8 ou A9 automaticamente ou você pode precisar adicioná-la por conta própria. Para adicionar a extensão por conta própria, veja [Gerenciar extensões de VM](virtual-machines-windows-classic-manage-extensions.md).

## Considerações para HPC Pack e Windows

O HPC Pack não é necessário para usar as instâncias A8, A9, A10 e A11 com o Windows Server, mas pode ser uma ferramenta útil para executar aplicativos MPI baseados em Windows que acessam a rede RDMA no Azure. O HPC Pack 2012 R2 e versões posteriores incluem um ambiente de tempo de execução para a implementação da Microsoft do MS-MPI (interface de transmissão de mensagens) para Windows que pode usar a rede RDMA do Azure quando implantado nas instâncias A8 e A9.

Para obter mais informações e listas de verificação para usar as instâncias de computação intensiva com o HPC Pack no Windows Server, veja [Configurar um cluster de RDMA do Windows com o HPC Pack para executar aplicativos MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).




## Próximas etapas

* Para obter detalhes sobre a disponibilidade e preços das instâncias A8, A9, A10 e A11, confira [Preços das Máquinas Virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows) e [Preços dos Serviços de Nuvem](https://azure.microsoft.com/pricing/details/cloud-services/).

* Para obter os detalhes de disco e as capacidades de armazenamento, consulte [Tamanhos das máquinas virtuais](virtual-machines-linux-sizes.md).

* Para começar a implantar e usar instâncias A8 e A9 com o HPC Pack no Windows, veja [Configurar um cluster de RDMA do Windows com o HPC Pack para executar aplicativos MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).

* Para obter informações sobre como usar as instâncias A8 e A9 para executar aplicativos MPI com o Lote do Azure, veja [Usar tarefas de várias instâncias para executar aplicativos de MPI (interface de transmissão de mensagens) no Lote do Azure](../batch/batch-mpi.md).

<!---HONumber=AcomDC_0511_2016-->