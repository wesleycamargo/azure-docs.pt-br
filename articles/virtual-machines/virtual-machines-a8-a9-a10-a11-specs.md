<properties
 pageTitle="Sobre as instâncias A8, A9, A10 e A11 | Microsoft Azure"
 description="Este artigo fornece informações básicas e considerações sobre o uso das instâncias de computação intensiva do Azure A8, A9, A10 e A11."
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
 ms.date="06/09/2015"
 ms.author="danlep"/>

# Sobre as instâncias de computação intensiva A8, A9, A10 e A11

Este artigo fornece informações básicas e considerações sobre o uso das instâncias do Azure A8, A9, A10 e A11, também conhecidas como instâncias de *computação intensiva*. Entre os principais recursos que diferenciam essas instâncias estão:

* **Hardware de alto desempenho** - o hardware de datacenter do Azure que executa essas instâncias é projetado e otimizado para aplicativos de computação e rede intensiva, incluindo aplicativos cluster HPC (computação de alto desempenho), modelagem e simulações.

* **Conexão de rede RDMA** - as instâncias A8 e A9 podem se comunicar por uma rede com alta taxa de transferência e baixa latência no Azure e baseada na tecnologia RDMA (acesso remoto direto à memória). Esse recurso pode melhorar o desempenho de aplicativos MPI (Message Passing Interface) paralelos. (No momento, o acesso à rede RDMA é suportado apenas para serviços de nuvem e VMs baseadas no Windows Server).

>[AZURE.NOTE]As instâncias A10 e A11 têm as mesmas otimizações de desempenho e especificações das instâncias A8 e A9. No entanto, elas não incluem o acesso à rede RDMA no Azure. Elas foram projetadas para aplicativos HPC que não exigem comunicação constante e de baixa latência entre os nós, também conhecidos como aplicativos paramétricos ou totalmente paralelos.

Assim como [outros tamanhos de VM do Azure](virtual-machines-size-specs.md), as instâncias A8, A9, A10 e A11 oferecem suporte a imagens padrão ou personalizadas de sistema operacional Windows Server e Linux em VMs do Azure (IaaS), ou versões do SO convidado do Azure em serviços de nuvem (PaaS).

## Especificações

### CPU e memória

As instâncias de computação intensiva A8, A9, A10 e A11 do Azure apresentam alta velocidade, CPUs com vários núcleos e grandes quantidades de memória, conforme mostra a tabela a seguir.

Tamanho | CPU | Memória
------------- | ----------- | ----------------
A8 e A10 | Intel® Xeon® E5-2670<br/>8 núcleos a 2.6 GHz | DDR3-1600 MHz<br/>56 GB
A9 e A11 | Intel® Xeon® E5-2670<br/>16 núcleos a 2.6 GHz | DDR3-1600 MHz<br/>112 GB


>[AZURE.NOTE]Encontre detalhes adicionais do processador, incluindo extensões do conjunto de instruções com suporte, no site Intel.com.


### Adaptadores de rede

As instâncias A8 e A9 têm dois adaptadores de rede que se conectam com as duas redes backend do Azure a seguir.


Rede | Descrição
-------- | -----------
Ethernet 10 Gbps | Conecta-se aos serviços do Azure (por exemplo, armazenamento e rede virtual) e à Internet
Back-end de 32 Gbps, compatível com RDMA | Permite a comunicação de aplicativo de baixa latência e alta taxa de transferência entre instâncias em um único serviço de nuvem


>[AZURE.IMPORTANT]No momento, o acesso à rede RDMA está habilitado somente por meio de aplicativos que usam a interface direta de rede da Microsoft. Consulte [Acessar a rede RDMA](#access-the-RDMA-network) neste artigo.


As instâncias A10 e A11 têm um único adaptador de rede Ethernet de 10 Gbps que se conecta à Internet e aos serviços do Azure.

## Considerações sobre a assinatura do Azure

* **Conta do azure** - se que você quiser implantar mais do que um pequeno número de instâncias de computação intensiva, considere uma assinatura paga ou outras opções de compra. Você também pode usar sua assinatura do MSDN. Consulte [Benefícios do Azure para assinantes do MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Se você estiver usando uma [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/), use apenas um número limitado de núcleos de computação do Azure.

* **Cota de núcleos** -talvez seja necessário aumentar a cota de núcleos em sua assinatura do Azure, cujo padrão é de 20 núcleos, que não é suficiente para muitos cenários com instâncias de 8 núcleos ou 16 núcleos. Para os testes iniciais, você pode solicitar um aumento de cota para 100 núcleos. Para fazer isso, abra um tíquete de suporte gratuito, conforme mostra [Entendendo os limites e aumentos do Azure](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/).

>[AZURE.NOTE]Cotas do Azure são limites de crédito, não garantias de capacidade. Você é cobrado apenas pelo núcleos que usa.

* **Grupo de afinidade** – um grupo de afinidade pode ajudar a otimizar o desempenho agrupando os serviços ou VMs no mesmo datacenter do Azure. Para agrupar as instâncias com computação intensiva, recomendamos a criação de um novo grupo de afinidade em uma região na qual você planeja implantar as instâncias. Como prática recomendada, use apenas o grupo de afinidades para as instâncias de computação intensiva e não instâncias de outros tamanhos.

* **Rede Virtual** -não há necessidade de ter uma rede virtual do Azure para usar instâncias de computação intensiva. No entanto, talvez seja necessário ter pelo menos uma rede virtual do Azure baseada em nuvem para muitos cenários de IaaS, ou uma conexão entre sites se você precisar acessar recursos locais. Será necessário criar uma nova rede virtual (regional) antes de implantar as instâncias. Não há suporte para a adição de uma VM A8, A9, A10 ou A11 a uma rede virtual em um grupo de afinidades. Para saber mais, consulte [Criar uma rede virtual](https://msdn.microsoft.com/library/azure/dn631643.aspx) e [Configurar uma rede virtual com uma conexão VPN entre sites](../vpn-gateway/vpn-gateway-site-to-site-create.md).

## Considerações para o uso do HPC Pack

[HPC Pack](https://technet.microsoft.com/library/cc514029), a solução gratuita de gerenciamento de trabalho e de cluster HPC da Microsoft, não é necessário para que você possa usar as instâncias A8, A9, A10 e A11, mas é uma ferramenta recomendada para a criação de clusters do Windows HPC no Azure. No caso das instâncias A8 e A9, o HPC Pack é a maneira mais eficiente de executar aplicativos MPI do Windows que acessam a rede RDMA no Azure. O HPC Pack inclui um ambiente de tempo de execução para a implementação por parte da Microsoft da MS-MPI (Message Passing Interface) para Windows.

Para saber mais e obter listas de verificação para usar instâncias de computação intensiva com o HPC Pack, consulte [ Instâncias de computação intensiva A8 e A9: início rápido com o HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx).

## Acesso à rede RDMA

Em um único serviço de nuvem, as instâncias A8 e A9 podem acessar a rede RDMA no Azure ao executar aplicativos MPI que utilizam a interface direta da rede da Microsoft para comunicação entre instâncias. No momento, a rede direta só recebe suporte do MS-MPI da Microsoft para Windows. As instâncias A10 e A11 não incluem acesso à rede RDMA.

>[AZURE.NOTE]Assim como outras instâncias do Azure, as instâncias A8 e A9 podem executar cargas de trabalho diferentes de aplicativos MPI, usando seus núcleos de CPU, memória e espaço em disco disponíveis. Entretanto, nesses casos, as instâncias não se conectam à rede RDMA e são funcionalmente equivalentes às instâncias A10 e A11.


Consulte a tabela a seguir a fim de obter os pré-requisitos para que os aplicativos MPI acessem a rede RDMA em implantações de máquina virtual (IaaS) e de serviço de nuvem (PaaS) das instâncias A8 ou A9. Para consultar os cenários de implantação, consulte [Instâncias A8 e A9 com uso intensivo de computação: Início Rápido com o HPC Pack.](https://msdn.microsoft.com/library/azure/dn594431.aspx)


Pré-requisito | Máquinas virtuais (IaaS) | Serviços de nuvem (PaaS)
---------- | ------------ | -------------
Sistema operacional | VMs do Windows Server 2012 R2 ou Windows Server 2012 | Família de sistemas operacionais convidados do Windows Server 2012 R2 ou Windows Server 2008 R2
MPI | MS-MPI 2012 R2 ou posterior, autônomo ou instalado por meio do HPC Pack 2012 R2 ou posterior | MS-MPI 2012 R2 ou posterior, instalado por meio do HPC Pack 2012 R2 ou posterior


>[AZURE.NOTE]Para cenários de IaaS, a [extensão HpcVmDrivers](https://msdn.microsoft.com/library/azure/dn690126.aspx) deve ser adicionada às VMs a fim de instalar os drivers do Windows necessários para a conectividade RDMA.


## Informações adicionais importantes

* Não é possível redimensionar uma VM Azure existente para o tamanho A8, A9, A10 ou A11.

* As instâncias A8, A9, A10 e A11 não podem ser implantadas atualmente por meio de um serviço de nuvem que faça parte de um grupo de afinidades existente. Da mesma forma, um grupo de afinidades com um serviço de nuvem que contém as instâncias A8, A9, A10 e A11 não pode ser usado para implantações de outros tamanhos de instância. Se você tentar fazer essas implantações, verá uma mensagem de erro semelhante a `Azure deployment failure (Compute.OverconstrainedAllocationRequest): The VM size (or combination of VM sizes) required by this deployment cannot be provisioned due to deployment request constraints.`


## Próximas etapas

* Para obter detalhes sobre a disponibilidade e preços das instâncias A8, A9, A10 e A11, consulte [Preços de máquinas virtuais](http://azure.microsoft.com/pricing/details/virtual-machines/) e [Preços de serviços de nuvem](http://azure.microsoft.com/pricing/details/cloud-services/).
* Para começar a implantar e usar as instâncias A8 e A9 com o HPC Pack, consulte [Instâncias de computação intensiva A8 e A9: início rápido com HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx) e [Executar aplicativos MPI em instâncias A8 e A9](https://msdn.microsoft.com/library/azure/dn592104.aspx).
 

<!---HONumber=62-->