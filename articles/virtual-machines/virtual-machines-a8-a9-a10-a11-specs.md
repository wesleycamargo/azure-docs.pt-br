<properties
 pageTitle="Sobre as instâncias A8, A9, A10 e A11 | Microsoft Azure"
 description="Obtenha informações básicas e considerações sobre o uso das instâncias de uso intensivo de computação do Azure A8, A9, A10 e A11."
 services="virtual-machines, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="07/22/2015"
 ms.author="danlep"/>

# Sobre as instâncias de computação intensiva A8, A9, A10 e A11

Este artigo fornece informações básicas e considerações sobre o uso das instâncias do Azure A8, A9, A10 e A11, também conhecidas como instâncias de *computação intensiva*. Os principais recursos dessas instâncias incluem:

* **Hardware de alto desempenho** - o hardware de datacenter do Azure que executa essas instâncias é projetado e otimizado para aplicativos de uso intensivo de computação e rede, incluindo aplicativos cluster HPC (computação de alto desempenho), modelagem e simulações.

* **Conexão de rede RDMA para aplicativos MPI** – quando configurada com os drivers de rede necessários, as instâncias A8 e A9 podem se comunicar com outras instâncias A8 e A9 por uma rede de baixa latência e alta taxa de transferência no Azure baseada na tecnologia RDMA (acesso direto a memória remota). Esse recurso pode melhorar o desempenho de aplicativos que usam implementações Linux ou Windows de MPI (Message Passing Interface) com suporte.

* **Suporte para clusters HPC do Linux e do Windows** – implante software de agendamento de trabalho e gerenciamento de cluster nas instâncias A8, A9, A10 e A11 no Azure para criar um cluster HPC autônomo ou adicionar capacidade a um cluster local. Assim como outros tamanhos de VM do Azure, as instâncias A8, A9, A10 e A11 oferecem suporte a imagens padrão ou personalizadas de sistema operacional Windows Server e Linux ou modelos do Gerenciador de Recursos do Azure (IaaS) em VMs, ou versões do SO convidado do Azure em serviços de nuvem (PaaS, apenas para Windows Server).

>[AZURE.NOTE]As instâncias A10 e A11 têm as mesmas otimizações de desempenho e especificações das instâncias A8 e A9. No entanto, elas não incluem o acesso à rede RDMA no Azure. Elas foram projetadas para aplicativos HPC que não exigem comunicação constante e de baixa latência entre os nós, também conhecidos como aplicativos paramétricos ou totalmente paralelos. E ao executar cargas de trabalho que não aplicativos MPI, as instâncias A8 e A9 não acessam a rede RDMA e são funcionalmente equivalentes às instâncias A10 e A11.


## Especificações

### CPU e memória

As instâncias de computação intensiva A8, A9, A10 e A11 do Azure apresentam alta velocidade, CPUs com vários núcleos e grandes quantidades de memória, conforme mostra a tabela a seguir.

Tamanho | CPU | Memória
------------- | ----------- | ----------------
A8 e A10 | Intel Xeon E5-2670<br/>8 núcleos a 2.6 GHz | DDR3-1600 MHz<br/>56 GB
A9 e A11 | Intel Xeon E5-2670<br/>16 núcleos a 2.6 GHz | DDR3-1600 MHz<br/>112 GB


>[AZURE.NOTE]Encontre detalhes adicionais do processador, incluindo extensões do conjunto de instruções com suporte, no site Intel.com. Para as capacidades de armazenamento de VM e detalhes do disco, consulte [Tamanhos das máquinas virtuais](virtual-machines-size-specs.md).

### Adaptadores de rede

As instâncias A8 e A9 têm dois adaptadores de rede que se conectam com as duas redes backend do Azure a seguir.


Rede | Descrição
-------- | -----------
Ethernet 10 Gbps | Conecta-se aos serviços do Azure (por exemplo, Armazenamento e Rede Virtual) e à Internet.
Back-end de 32 Gbps, compatível com RDMA | Permite a comunicação de aplicativo de baixa latência e alta taxa de transferência entre instâncias em um único serviço de nuvem ou conjunto de disponibilidade. Reservado para o tráfego de MPI apenas.


>[AZURE.IMPORTANT]Em VMs A8 e A9 que executam Linux em IaaS, acesso à rede RDMA está habilitado por meio de aplicativos que usam o Azure Linux RDMA e Intel MPI Library 5.0 no SUSE Linux Enterprise Server 12 (SLES 12). Em instâncias A8 e A9 que executam o Windows Server em IaaS ou PaaS, o acesso à rede RDMA é habilitado por meio de aplicativos que usam a interface direta da rede Microsoft. Consulte [Acessar à rede RDMA](#access-the-rdma-network) neste artigo para obter os requisitos adicionais.

As instâncias A10 e A11 têm um único adaptador de rede Ethernet de 10 Gbps que se conecta à Internet e aos serviços do Azure.

## Considerações sobre a assinatura do Azure

* **Conta do azure** - se que você quiser implantar mais do que um pequeno número de instâncias de computação intensiva, considere uma assinatura pré-paga ou outras opções de compra. Você também pode usar sua assinatura do MSDN. Consulte [Benefícios do Azure para assinantes do MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Se você estiver usando uma [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/), use apenas um número limitado de núcleos de computação do Azure.

* **Cota de núcleos** -talvez seja necessário aumentar a cota de núcleos em sua assinatura do Azure, cujo padrão é de 20 núcleos, que não é suficiente para muitos cenários com instâncias de 8 núcleos ou 16 núcleos. Para os testes iniciais, você pode solicitar um aumento de cota para 100 núcleos. Para fazer isso, abra um tíquete de suporte gratuito, conforme mostra [Entendendo os limites e aumentos do Azure](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/).

>[AZURE.NOTE]Cotas do Azure são limites de crédito, não garantias de capacidade. Você é cobrado apenas pelo núcleos que usa.

* **Grupo de afinidades** – atualmente um grupo de afinidades não é recomendado para a maioria das novas implantações. No entanto, observe que se estiver usando um grupo de afinidades que contenha instâncias de tamanhos diferentes A8 - A11, você não poderá usá-lo também para instâncias A8–A11, e vice-versa.

* **Rede Virtual** -não há necessidade de ter uma rede virtual do Azure para usar instâncias de computação intensiva. No entanto, talvez seja necessário ter pelo menos uma rede virtual do Azure baseada em nuvem para muitos cenários de IaaS, ou uma conexão entre sites se você precisar acessar recursos locais, como um servidor de licença de aplicativo. Será necessário criar uma nova rede virtual (regional) antes de implantar as instâncias. Não há suporte para a adição de uma VM A8, A9, A10 ou A11 a uma rede virtual em um grupo de afinidades. Para obter mais informações, consulte [Como criar uma rede virtual (VNet)](../virtual-network/virtual-networks-create-vnet.md) e [Configurar uma rede virtual com uma conexão VPN entre sites](../vpn-gateway/vpn-gateway-site-to-site-create.md).

* **Conjunto de disponibilidade ou serviço de nuvem** – para se conectar por meio da rede RDMA, as instâncias A8 e A9 devem ser implantadas no mesmo serviço de nuvem (para cenários de IaaS com VMs do Linux ou máquinas virtuais do Gerenciamento de Serviços do Azure, ou cenários de PaaS com o Windows Server) ou a mesma disponibilidade definida (para Linux ou VMs do Windows no Gerenciador de Recursos do Azure).

## Considerações para o uso do HPC Pack

### Considerações para HPC Pack e Linux

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) é o cluster HPC gratuito da Microsoft e a solução de gerenciamento de trabalho para o Windows. A partir do HPC Pack 2012 R2 Update 2, o HPC Pack dá suporte a várias distribuições Linux para executar em nós de computação implantados em VMs do Azure, gerenciadas por um nó principal do Windows Server. Com a versão mais recente do HPC Pack, você pode implantar um cluster baseado em Linux que pode executar aplicativos MPI que acessam a rede RDMA no Azure. Para obter mais informações, consulte a [Documentação do HPC Pack](http://go.microsoft.com/fwlink/?LinkId=617894).

### Considerações para HPC Pack e Windows

O HPC Pack não é necessário para usar instâncias A8, A9, A10 e A11 com o Windows Server, mas é uma ferramenta recomendada para criar clusters do Windows HPC no Azure. No caso das instâncias A8 e A9, o HPC Pack é a maneira mais eficiente de executar aplicativos MPI do Windows que acessam a rede RDMA no Azure. O HPC Pack inclui um ambiente de tempo de execução para a implementação por parte da Microsoft da Message Passing Interface para Windows.

Para obter mais informações e listas de verificação para implantar e usar instâncias com computação intensiva em cenários de IaaS e PaaS com HPC Pack no Windows Server, consulte [Instâncias de computação intensiva A8 e A9: início rápido com HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx).

## Acesso à rede RDMA

### Acesso por meio de VMs Linux A8 e A9

Dentro de um único serviço de nuvem ou conjunto de disponibilidade, as instâncias A8 e A9 podem acessar a rede RDMA no Azure ao executar aplicativos MPI que utilizem os drivers de RDMA Linux para comunicação entre instâncias. No momento, o RDMA Linux do Azure é compatível apenas com [Intel MPI Library 5.0](https://software.intel.com/pt-br/intel-mpi-library/).

>[AZURE.NOTE]Atualmente drivers RDMA Linux do Azure não estão disponíveis para instalação por meio de extensões do driver. Eles só estão disponíveis usando imagens compatíveis com RDMA SLES 12 do Azure Marketplace.

Consulte a tabela a seguir para obter os pré-requisitos para aplicativos MPI Linux acessarem a rede RDMA em clusters de nós de computação (IaaS). Consulte [Configurar um cluster de RDMA do Linux para executar aplicativos MPI](virtual-machines-linux-cluster-rdma.md) para ver opções de implantação e etapas de configuração.

Pré-requisito | Máquinas virtuais (IaaS)
------------ | -------------
Sistema operacional | Imagem SLES 12 HPC do Azure Marketplace
MPI | Intel MPI Library 5.0

### Acesso de instâncias do Windows A8 e A9

Em um único serviço de nuvem ou conjunto de disponibilidade, as instâncias A8 e A9 podem acessar a rede RDMA no Azure ao executar aplicativos MPI que utilizam a interface direta da rede da Microsoft para comunicação entre instâncias. As instâncias A10 e A11 não incluem acesso à rede RDMA.

Consulte a tabela a seguir a fim de obter os pré-requisitos para que os aplicativos MPI acessem a rede RDMA em implantações de máquina virtual (IaaS) e de serviço de nuvem (PaaS) das instâncias A8 ou A9. Para consultar os cenários de implantação, consulte [Instâncias A8 e A9 com uso intensivo de computação: Início Rápido com o HPC Pack.](https://msdn.microsoft.com/library/azure/dn594431.aspx)


Pré-requisito | Máquinas virtuais (IaaS) | Serviços de nuvem (PaaS)
---------- | ------------ | -------------
Sistema operacional | Windows Server 2012 R2 ou Windows Server 2012 | Família de SO convidado Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2
MPI | MS-MPI 2012 R2 ou posterior, autônomo ou instalado por meio do HPC Pack 2012 R2 ou posterior<br/><br/>Intel MPI Library 5.0 | MS-MPI 2012 R2 ou posterior, instalado por meio do HPC Pack 2012 R2 ou posterior<br/><br/>Intel MPI Library 5.0


>[AZURE.NOTE]Para cenários de IaaS, a [extensão HpcVmDrivers](https://msdn.microsoft.com/library/azure/dn690126.aspx) deve ser adicionada às VMs a fim de instalar os drivers do Windows necessários para a conectividade RDMA.


## Informações adicionais importantes

* Os tamanhos de VM A8–A11 só estão disponíveis na camada de preços padrão.

* Não é possível redimensionar uma VM Azure existente para o tamanho A8, A9, A10 ou A11.

* As instâncias A8, A9, A10 e A11 não podem ser implantadas atualmente por meio de um serviço de nuvem que faça parte de um grupo de afinidades existente. Da mesma forma, um grupo de afinidades com um serviço de nuvem que contém as instâncias A8, A9, A10 e A11 não pode ser usado para implantações de outros tamanhos de instância. Se você tentar fazer essas implantações, verá uma mensagem de erro semelhante a `Azure deployment failure (Compute.OverconstrainedAllocationRequest): The VM size (or combination of VM sizes) required by this deployment cannot be provisioned due to deployment request constraints.`

* A rede RDMA no Azure reserva o espaço de endereço 172.16.0.0/12. Se você planeja executar aplicativos MPI em instâncias A8 e A9 implantadas em uma rede virtual do Azure, verifique se o espaço do endereço de rede virtual não se sobrepõe à rede RDMA.

## Próximas etapas

* Para obter detalhes sobre a disponibilidade e preços das instâncias A8, A9, A10 e A11, consulte [Preços de máquinas virtuais](http://azure.microsoft.com/pricing/details/virtual-machines/) e [Preços de serviços de nuvem](http://azure.microsoft.com/pricing/details/cloud-services/).
* Para implantar e configurar um cluster do Linux com instâncias A8 e A9 para acessar a rede RDMA do Azure, consulte [Configurar um cluster de RDMA do Linux para executar aplicativos MPI](virtual-machines-linux-cluster-rdma.md).
* Para começar a implantar e usar as instâncias A8 e A9 com o HPC Pack em Windows, consulte [Instâncias de computação intensiva A8 e A9: início rápido com HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx) e [Executar aplicativos MPI em instâncias A8 e A9](https://msdn.microsoft.com/library/azure/dn592104.aspx).

<!---HONumber=July15_HO5-->