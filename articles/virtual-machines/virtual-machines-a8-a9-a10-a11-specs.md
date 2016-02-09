<properties
 pageTitle="Sobre as instâncias A8, A9, A10 e A11 | Microsoft Azure"
 description="Obtenha informações básicas e considerações sobre o uso dos tamanhos de computação intensiva A8, A9, A10 e A11 do Azure para máquinas virtuais e serviços de nuvem."
 services="virtual-machines, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="10/30/2015"
 ms.author="danlep"/>

# Sobre as instâncias de computação intensiva A8, A9, A10 e A11

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


Este artigo fornece informações básicas e considerações sobre o uso das instâncias A8, A9, A10 e A11 do Azure, também conhecidas como instâncias de *computação intensiva*. Os principais recursos dessas instâncias incluem:

* **Hardware de alto desempenho** – O hardware de datacenter do Azure que executa essas instâncias é projetado e otimizado para aplicativos de uso intensivo de computação e rede, incluindo aplicativos, modelagem e simulações de cluster HPC (computação de alto desempenho).

* **Conexão de rede RDMA para aplicativos MPI** – Quando configurada com os drivers de rede necessários, as instâncias A8 e A9 podem se comunicar com outras instâncias A8 e A9 por uma rede de baixa latência e alta taxa de transferência no Azure, baseada na tecnologia RDMA (Acesso Remoto Direto à Memória). Esse recurso pode melhorar o desempenho de aplicativos que usam implementações Linux ou Windows de MPI (Message Passing Interface) com suporte.

* **Suporte para clusters HPC do Linux e Windows** – Implante um software de agendamento de trabalho e gerenciamento de cluster nas instâncias A8, A9, A10 e A11 no Azure para criar um cluster HPC autônomo ou para adicionar capacidade a um cluster local. Assim como outros tamanhos de VM do Azure, as instâncias A8, A9, A10 e A11 oferecem suporte a imagens padrão ou personalizadas de sistema operacional Windows Server e Linux ou modelos do Gerenciador de Recursos do Azure (IaaS) em VMs, ou versões do SO convidado do Azure em serviços de nuvem (PaaS, apenas para Windows Server).

>[AZURE.NOTE]As instâncias A10 e A11 têm as mesmas otimizações de desempenho e especificações das instâncias A8 e A9. No entanto, elas não incluem o acesso à rede RDMA no Azure. Elas foram projetadas para aplicativos HPC que não exigem comunicação constante e de baixa latência entre os nós, também conhecidos como aplicativos paramétricos ou totalmente paralelos.


## Especificações

### CPU e memória

As instâncias de computação intensiva A8, A9, A10 e A11 do Azure apresentam alta velocidade, CPUs com vários núcleos e grandes quantidades de memória, conforme mostra a tabela a seguir.

Tamanho | CPU | Memória
------------- | ----------- | ----------------
A8 e A10 | Intel Xeon E5-2670<br/>8 núcleos a 2,6 GHz | DDR3-1600 MHz<br/>56 GB
A9 e A11 | Intel Xeon E5-2670<br/>16 núcleos a 2,6 GHz | DDR3-1600 MHz<br/>112 GB


>[AZURE.NOTE]Encontre detalhes adicionais do processador, incluindo extensões do conjunto de instruções com suporte, no site Intel.com. Para obter os detalhes de disco e as capacidades de armazenamento de VM, confira [Tamanhos das máquinas virtuais](virtual-machines-size-specs.md).

### Adaptadores de rede

As instâncias A8 e A9 têm dois adaptadores de rede que se conectam com as duas redes backend do Azure a seguir.


Rede | Descrição
-------- | -----------
Ethernet 10 Gbps | Conecta-se aos serviços do Azure (por exemplo, Armazenamento e Rede Virtual) e à Internet.
Back-end de 32 Gbps, compatível com RDMA | Permite a comunicação de aplicativo de baixa latência e alta taxa de transferência entre instâncias em um único serviço de nuvem ou conjunto de disponibilidade. Reservado para o tráfego de MPI apenas.


>[AZURE.IMPORTANT]Em VMs A8 e A9 que executam Linux, o acesso à rede RDMA é habilitado no momento por meio de aplicativos que usam o Azure Linux RDMA e Intel MPI Library 5 no SUSE Linux Enterprise Server 12 (SLES 12). Em instâncias A8 e A9 que executam o Windows Server, o acesso à rede RDMA é habilitado no momento por meio de aplicativos que usam a interface direta da rede Microsoft. Veja [Acesso à rede RDMA](#access-the-rdma-network) neste artigo para obter os requisitos adicionais.

As instâncias A10 e A11 têm um único adaptador de rede Ethernet de 10 Gbps que se conecta à Internet e aos serviços do Azure.

## Considerações sobre a assinatura

* **Conta do Azure** – Se desejar implantar mais do que um pequeno número de instâncias de computação intensiva, considere uma assinatura pré-paga ou outras opções de compra. Você também pode usar sua assinatura do MSDN. Consulte [Benefícios do Azure para assinantes do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Se estiver usando uma [avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/), é possível usar apenas um número limitado de núcleos de computação do Azure.

* **Cota de núcleos** – Talvez seja necessário aumentar a cota de núcleos em sua assinatura do Azure do padrão de 20 núcleos por assinatura (para implantações do Gerenciamento de Serviços do Azure) ou de 20 núcleos por região (para implantações do Gerenciador de Recursos do Azure), o que não é suficiente para muitos cenários com instâncias de 8 ou 16 núcleos. Para os testes iniciais, você pode solicitar um aumento de cota para 100 núcleos. Para isso, abra um tíquete de suporte gratuito, como mostrado em [Noções básicas sobre limites e aumentos do Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/).

    >[AZURE.NOTE]Cotas do Azure são limites de crédito, não garantias de capacidade. Você é cobrado apenas pelo núcleos que usa.

* **Rede Virtual** -não há necessidade de ter uma rede virtual do Azure para usar instâncias de computação intensiva. No entanto, talvez seja necessário ter pelo menos uma rede virtual do Azure baseada em nuvem para muitos cenários de IaaS, ou uma conexão entre sites se você precisar acessar recursos locais, como um servidor de licença de aplicativo. Será necessário criar uma nova rede virtual (regional) antes de implantar as instâncias. Não há suporte para a adição de uma VM A8, A9, A10 ou A11 a uma rede virtual em um grupo de afinidades. Para obter mais informações, veja a [documentação da rede virtual](https://azure.microsoft.com/documentation/services/virtual-network/).

* **Serviço de nuvem ou conjunto de disponibilidade** – Para se conectar por meio da rede RDMA, as instâncias A8 e A9 devem ser implantadas no mesmo serviço de nuvem (para cenários de IaaS com VMs baseadas em Linux ou Windows no Gerenciamento de Serviços do Azure, ou cenários de PaaS com o Windows Server) ou no mesmo conjunto de disponibilidade (para VMs baseadas em Linux ou Windows no Gerenciador de Recursos do Azure).

## Considerações para o uso do HPC Pack

### Considerações para HPC Pack e Linux

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) é o cluster HPC gratuito da Microsoft e a solução de gerenciamento de trabalho para o Windows. A partir do HPC Pack 2012 R2 Update 2, o HPC Pack dá suporte a várias distribuições Linux para executar em nós de computação implantados em VMs do Azure, gerenciadas por um nó principal do Windows Server. Com a versão mais recente do HPC Pack, você pode implantar um cluster baseado em Linux que pode executar aplicativos MPI que acessam a rede RDMA no Azure. Para obter mais informações, veja [Introdução aos nós de computação do Linux em um cluster do HPC Pack no Azure](virtual-machines-linux-cluster-hpcpack.md).

### Considerações para HPC Pack e Windows

O HPC Pack não é necessário para usar instâncias A8, A9, A10 e A11 com o Windows Server, mas é uma ferramenta recomendada para criar clusters do Windows HPC no Azure. No caso das instâncias A8 e A9, o HPC Pack é a maneira mais eficiente de executar aplicativos MPI do Windows que acessam a rede RDMA no Azure. O HPC Pack inclui um ambiente de tempo de execução para a implementação por parte da Microsoft da Message Passing Interface para Windows.

Para obter mais informações e listas de verificação para usar as instâncias de computação intensiva com o HPC Pack no Windows Server, veja [Configurar um cluster de RDMA do Windows com o HPC Pack para executar aplicativos MPI](virtual-machines-windows-hpcpack-cluster-rdma.md).

## Acesso à rede RDMA

### Acesso por meio de VMs Linux A8 e A9

Dentro de um único serviço de nuvem ou conjunto de disponibilidade, as instâncias A8 e A9 podem acessar a rede RDMA no Azure ao executar aplicativos MPI que utilizem os drivers de RDMA Linux para comunicação entre instâncias. No momento, há suporte apenas ao RDMA do Linux do Azure da [Intel MPI Library 5](https://software.intel.com/pt-BR/intel-mpi-library/).

>[AZURE.NOTE] Atualmente drivers RDMA Linux do Azure não estão disponíveis para instalação por meio de extensões do driver. Eles só estão disponíveis usando imagens compatíveis com RDMA SLES 12 do Azure Marketplace.

Consulte a tabela a seguir para obter os pré-requisitos para aplicativos MPI Linux acessarem a rede RDMA em clusters de nós de computação (IaaS). Veja [Configurar um cluster de RDMA do Linux para executar aplicativos MPI](virtual-machines-linux-cluster-rdma.md) para obter as opções de implantação e as etapas de configuração.

Pré-requisito | Máquinas virtuais (IaaS)
------------ | -------------
Sistema operacional | Imagem SLES 12 HPC do Azure Marketplace
MPI | Intel MPI Library 5

### Acesso de instâncias do Windows A8 e A9

Em um único serviço de nuvem ou conjunto de disponibilidade, as instâncias A8 e A9 podem acessar a rede RDMA no Azure ao executar aplicativos MPI que utilizam a interface direta da rede da Microsoft para comunicação entre instâncias. As instâncias A10 e A11 não incluem acesso à rede RDMA.

Consulte a tabela a seguir a fim de obter os pré-requisitos para que os aplicativos MPI acessem a rede RDMA em implantações de máquina virtual (IaaS) e de serviço de nuvem (PaaS) das instâncias A8 ou A9. Para cenários típicos de implantação, veja [Configurar um cluster de RDMA do Windows com o HPC Pack para executar aplicativos MPI](virtual-machines-windows-hpcpack-cluster-rdma.md).


Pré-requisito | Máquinas virtuais (IaaS) | Serviços de nuvem (PaaS)
---------- | ------------ | -------------
Sistema operacional | Windows Server 2012 R2 ou Windows Server 2012 | Família de SO convidado Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2
MPI | MS-MPI 2012 R2 ou posterior, autônomo ou instalado por meio do HPC Pack 2012 R2 ou posterior<br/><br/>Intel MPI Library 5 | MS-MPI 2012 R2 ou posterior, instalado por meio do HPC Pack 2012 R2 ou posterior<br/><br/>Intel MPI Library 5


>[AZURE.NOTE]Em cenários de IaaS, a extensão HpcVmDrivers deve ser adicionada às VMs a fim de instalar os drivers de dispositivo de rede do Windows necessários para a conectividade RDMA. Dependendo do método de implantação, a extensão HpcVmDrivers pode ser adicionada a uma VM de tamanho A8 ou A9 automaticamente, ou você pode precisar adicioná-la por conta própria. Para adicionar a extensão, veja [Gerenciar extensões de VM](virtual-machines-extensions-install.md).


## Informações adicionais importantes

* **Preços** - Os tamanhos de VM A8-A11 só estão disponíveis no tipo de preço Standard.

* **Redimensionamento** – Não é possível redimensionar uma instância com um tamanho diferente de A8-A11 para um dos tamanhos da instância de computação intensiva (A8-11) e não é possível redimensionar uma instância de computação intensiva para um tamanho que não seja de computação intensiva. Isso ocorre devido ao hardware especializado e às otimizações de desempenho específicas para as instâncias de computação intensiva.

* **Espaço de endereço de rede RDMA** - A rede RDMA no Azure reserva o espaço de endereço 172.16.0.0/12. Se você planeja executar aplicativos MPI em instâncias A8 e A9 em uma rede virtual do Azure, verifique se o espaço do endereço de rede virtual não se sobrepõe à rede RDMA.

## Próximas etapas

* Para obter detalhes sobre a disponibilidade e preços das instâncias A8, A9, A10 e A11, confira [Preços das Máquinas Virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/) e [Preços dos Serviços de Nuvem](https://azure.microsoft.com/pricing/details/cloud-services/).
* Para implantar e configurar um cluster baseado em Linux com instâncias A8 e A9 para acessar a rede RDMA do Azure, veja [Configurar um cluster de RDMA do Linux para executar aplicativos MPI](virtual-machines-linux-cluster-rdma.md).
* Para começar a implantar e usar instâncias A8 e A9 com o HPC Pack no Windows, veja [Configurar um cluster de RDMA do Windows com o HPC Pack para executar aplicativos MPI](virtual-machines-windows-hpcpack-cluster-rdma.md).

<!---HONumber=AcomDC_0128_2016-->