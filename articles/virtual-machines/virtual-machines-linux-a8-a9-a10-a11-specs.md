<properties
 pageTitle="Sobre as instâncias A8 a A11 e o Linux | Microsoft Azure"
 description="Obter informações básicas e considerações sobre o uso dos tamanhos A8, A9, A10 e A11 do Azure com computação intensiva para VMs do Linux"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="01/26/2016"
 ms.author="danlep"/>

# Sobre o uso das instâncias A8, A9, A10 e A11 com computação intensiva com o Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## Acesso à rede RDMA

Em um único serviço de nuvem ou em um conjunto de disponibilidade, as instâncias A8 e A9 podem acessar a rede RDMA no Azure para executar aplicativos MPI Linux. No momento, há suporte para o RDMA do Linux no Azure apenas com a [Intel MPI Library 5](https://software.intel.com/pt-BR/intel-mpi-library/).

>[AZURE.NOTE] Atualmente drivers RDMA Linux do Azure não estão disponíveis para instalação por meio de extensões do driver. Eles só estão disponíveis usando imagens compatíveis com RDMA SLES 12 do Azure Marketplace.

A tabela a seguir resume pré-requisitos para aplicativos MPI Linux para acessar a rede RDMA em clusters de nós de computação (IaaS). Veja [Configurar um cluster de RDMA do Linux para executar aplicativos MPI](virtual-machines-linux-classic-rdma-cluster.md) para obter as opções de implantação e as etapas de configuração.

Pré-requisito | Máquinas virtuais (IaaS)
------------ | -------------
Sistema operacional | Imagem SLES 12 HPC do Azure Marketplace
MPI | Intel MPI Library 5

## Considerações para HPC Pack e Linux

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) é o cluster HPC gratuito da Microsoft e a solução de gerenciamento de trabalho para o Windows. As versões mais recentes do HPC Pack 2012 R2 são compatíveis com várias distribuições Linux para execução nos nós de computação implantados nas VMs do Azure, gerenciados por um nó de cabeçalho do Windows Server. Nós de computação do Linux implantados em VMs A8 ou A9 e que executam uma implementação MPI com suporte podem executar aplicativos MPI que acessam a rede RDMA. Para se familiarizar, veja [Introdução aos nós de computação do Linux em um cluster HPC Pack no Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

## Próximas etapas

* Para obter detalhes sobre a disponibilidade e os preços das instâncias A8, A9, A10 e A11, confira [Preços das Máquinas Virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/).

* Para começar a implantar e usar as instâncias A8 e A9 com o RDMA no Linux, veja [Configurar um cluster RDMA do Linux para executar aplicativos MPI](virtual-machines-linux-classic-rdma-cluster.md).

<!---HONumber=AcomDC_0323_2016-->