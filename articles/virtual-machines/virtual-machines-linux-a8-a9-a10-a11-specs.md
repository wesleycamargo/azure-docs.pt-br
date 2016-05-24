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
 ms.date="05/09/2016"
 ms.author="danlep"/>

# Sobre as instâncias de computação intensiva A8, A9, A10 e A11 

Apresentamos a seguir informações básicas e algumas considerações sobre o uso das instâncias A8, A9, A10 e A11 do Azure, também conhecidas como instâncias de *computação intensiva*. Este artigo ressalta o uso destas instâncias para VMs Linux. Este artigo também está disponível para [VMs Windows](virtual-machines-windows-a8-a9-a10-a11-specs.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## Acesso à rede RDMA

Em um único serviço de nuvem ou conjunto de disponibilidade, clusters de VMs Linux de tamanho A8 e A9 que executam uma das seguintes distribuições Linux HPC com suporte e uma implementação MPI com suporte podem acessar a rede RDMA no Azure para executar aplicativos MPI do Linux. Veja [Configurar um cluster de RDMA do Linux para executar aplicativos MPI](virtual-machines-linux-classic-rdma-cluster.md) para obter opções de implantação e etapas de configuração de exemplo.

* **Distribuições** - SUSE Linux Enterprise Server (SLES) 12 para HPC, SLES 12 para HPC (Premium), HPC baseado em CentOS 7.1 ou HPC baseado em CentOS 6.5, implantado com base em uma imagem do Azure Marketplace

* **MPI** - Intel MPI Library 5.x

    >[AZURE.NOTE] O Intel MPI 5.1.3.181 já está instalado nas imagens do HPC baseado em CentOS no Marketplace. Para usar o Intel MPI em VMs do HPC SLES 12, é necessário instalá-lo separadamente.

Atualmente, os drivers RDMA do Linux do Azure são instalados apenas quando são implantadas imagens do HPC SLES 12 e HPC baseado em CentOS habilitadas para RDMA no Azure Marketplace. Não é possível instalar os drivers em outras VMs Linux implantadas.

>[AZURE.NOTE]Nas imagens do HPC baseado em CentOS do Marketplace, as atualizações de kernel estão desabilitadas no arquivo de configuração **yum**. Isso ocorre porque os drivers RDMA do Linux são distribuídos como um pacote RPM, e as atualizações de driver poderão não funcionar caso o kernel seja atualizado.


## Atualizações de driver RDMA para SLES 12
Depois de criar a VM baseada em uma imagem do HPC SLES 12, é necessário atualizar os drivers RDMA nas VMs para a conectividade de rede RDMA.

>[AZURE.IMPORTANT]Esta etapa é **necessária** para implantações de VM do HPC SLES 12 em todas as regiões do Azure. Essa etapa não será necessária se você tiver implantado uma VM do HPC 7.1 ou 6.5 baseado em CentOS.

Antes de atualizar os drivers, interrompa todos os processos **zypper** ou os processos que bloqueiam os bancos de dados de repositório SUSE na VM. Caso contrário, os drivers não poderão ser atualizados corretamente.

Para atualizar os drivers RDMA do Linux em cada VM, execute um dos seguintes conjuntos de comandos da CLI do Azure no computador cliente.

**Para uma VM do HPC SLES 12 provisionada no modelo de implantação clássica**

```
azure config mode asm

azure vm extension set <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

**Para uma VM do HPC SLES 12 provisionada no modelo de implantação do Gerenciador de Recursos**

```
azure config mode arm

azure vm extension set <resource-group> <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

>[AZURE.NOTE]A instalação dos drivers pode levar algum tempo e o comando retornará sem saída. Após a atualização, a VM será reiniciada e deve estar pronta para uso em alguns minutos.

### Script de exemplo para atualizações de driver

Se você tiver um cluster de VMs do HPC SLES 12, será possível escrever scripts da atualização de driver em todos os nós no cluster. Por exemplo, o script a seguir atualiza os drivers em um cluster de 8 nós.

```

#!/bin/bash -x

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Plug in appropriate numbers in the for loop below.

for (( i=11; i<19; i++ )); do

# For VMs created in the classic deployment model use the following command in your script.

azure vm extension set $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

# For VMs created in the Resource Manager deployment model, use the following command in your script.

# azure vm extension set <resource-group> $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

done

```


## Considerações para HPC Pack e Linux

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) é a solução de gerenciamento de trabalhos e de cluster HPC gratuita da Microsoft. As versões mais recentes do HPC Pack 2012 R2 são compatíveis com várias distribuições Linux para execução nos nós de computação implantados nas VMs do Azure, gerenciados por um nó de cabeçalho do Windows Server. Nós de computação do Linux implantados em VMs A8 ou A9 e que executam uma implementação MPI com suporte podem executar aplicativos MPI que acessam a rede RDMA. Para se familiarizar, veja [Introdução aos nós de computação do Linux em um cluster HPC Pack no Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

## Considerações de topologia de rede

* Em VMs Linux de tamanho A8 ou A9 no Azure, Eth1 é reservado para o tráfego de rede RDMA. Não altere as configurações de Eth1 ou as informações no arquivo de configuração que se refere a essa rede. Eth0 é reservado para o tráfego de rede regular do Azure.

* IP sobre Infiniband (IB) não é compatível com o Azure. Apenas RDMA sobre IB é compatível.


## Próximas etapas

* Para obter detalhes sobre a disponibilidade e os preços das instâncias A8, A9, A10 e A11, confira [Preços das Máquinas Virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux).

* Para obter os detalhes de disco e as capacidades de armazenamento, consulte [Tamanhos das máquinas virtuais](virtual-machines-linux-sizes.md).

* Para se familiarizar com a implantação e o uso das instâncias A8 e A9 com o RDMA no Linux, veja [Configurar um cluster de RDMA do Linux para executar aplicativos MPI](virtual-machines-linux-classic-rdma-cluster.md).

<!---HONumber=AcomDC_0511_2016-->