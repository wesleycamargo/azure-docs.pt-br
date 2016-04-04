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
 ms.date="01/26/2016"
 ms.author="danlep"/>

# Sobre o uso das instâncias A8, A9, A10 e A11 com computação intensiva com o Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## Acesso à rede RDMA

Em um único serviço de nuvem ou conjunto de disponibilidade, as instâncias A8 e A9 podem acessar a rede RDMA no Azure para executar aplicativos MPI que utilizam a Interface Direta da Rede da Microsoft para comunicação entre instâncias.

Consulte a tabela a seguir a fim de obter os pré-requisitos para que os aplicativos MPI acessem a rede RDMA em implantações de máquina virtual (IaaS) e de serviço de nuvem (PaaS) das instâncias A8 ou A9. Para cenários típicos de implantação, veja [Configurar um cluster de RDMA do Windows com o HPC Pack para executar aplicativos MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).


Pré-requisito | Máquinas virtuais (IaaS) | Serviços de nuvem (PaaS)
---------- | ------------ | -------------
Sistema operacional | Windows Server 2012 R2 ou Windows Server 2012 | Família de SO convidado Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2
MPI | MS-MPI 2012 R2 ou posterior, autônomo ou instalado por meio do HPC Pack 2012 R2 ou posterior<br/><br/>Intel MPI Library 5 | MS-MPI 2012 R2 ou posterior, instalado por meio do HPC Pack 2012 R2 ou posterior<br/><br/>Intel MPI Library 5


>[AZURE.NOTE]Em cenários de IaaS, a extensão HpcVmDrivers deve ser adicionada às VMs a fim de instalar os drivers de dispositivo de rede do Windows necessários para a conectividade RDMA. Dependendo do método de implantação, a extensão HpcVmDrivers pode ser adicionada a uma VM de tamanho A8 ou A9 automaticamente, ou você pode precisar adicioná-la por conta própria. Para adicionar a extensão, veja [Gerenciar extensões de VM](virtual-machines-windows-classic-manage-extensions.md).

## Considerações para HPC Pack e Windows

O HPC Pack não é necessário para usar as instâncias A8, A9, A10 e A11 com o Windows Server, mas é uma ferramenta recomendada para executar aplicativos MPI baseados em Windows que acessam a rede RDMA no Azure. O HPC Pack inclui um ambiente de tempo de execução para a implementação por parte da Microsoft da MS-MPI (Message Passing Interface) para Windows.

Para obter mais informações e listas de verificação para usar as instâncias de computação intensiva com o HPC Pack no Windows Server, veja [Configurar um cluster de RDMA do Windows com o HPC Pack para executar aplicativos MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).

## Próximas etapas

* Para obter detalhes sobre a disponibilidade e preços das instâncias A8, A9, A10 e A11, confira [Preços das Máquinas Virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/) e [Preços dos Serviços de Nuvem](https://azure.microsoft.com/pricing/details/cloud-services/).

* Para começar a implantar e usar instâncias A8 e A9 com o HPC Pack no Windows, veja [Configurar um cluster de RDMA do Windows com o HPC Pack para executar aplicativos MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).

* Para obter informações sobre como usar as instâncias A8 e A9 para executar aplicativos MPI com o Lote do Azure, veja [Usar tarefas de várias instâncias para executar aplicativos MPI (Interface de Transmissão de Mensagens) no Lote do Azure](../batch/batch-mpi.md).

<!---HONumber=AcomDC_0323_2016-->