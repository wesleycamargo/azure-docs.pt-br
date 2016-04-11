

Este artigo fornece informações básicas e considerações sobre o uso das instâncias A8, A9, A10 e A11 do Azure, também conhecidas como instâncias de *computação intensiva*. Os principais recursos dessas instâncias incluem:

* **Hardware de alto desempenho** – O hardware de datacenter do Azure que executa essas instâncias é projetado e otimizado para aplicativos de uso intensivo de computação e rede, incluindo aplicativos, modelagem e simulações de cluster HPC (computação de alto desempenho).

* **Conexão de rede RDMA para aplicativos MPI** – Configure as instâncias A8 e A9 para se comunicarem com outras instâncias A8 e A9 em uma rede de alta taxa de transferência e baixa latência no Azure que se baseia na tecnologia RDMA (acesso remoto direto à memória). Esse recurso pode impulsionar o desempenho de determinados aplicativos MPI (Interface de Passagem de Mensagens) do Windows.

* **Suporte para clusters HPC** – Implante um software de agendamento de trabalho e gerenciamento de cluster nas instâncias A8, A9, A10 e A11 no Azure para criar um cluster HPC autônomo ou para adicionar capacidade a um cluster local.

>[AZURE.NOTE]As instâncias A10 e A11 têm as mesmas otimizações de desempenho e especificações das instâncias A8 e A9. No entanto, elas não incluem o acesso à rede RDMA no Azure. Elas foram projetadas para aplicativos HPC que não exigem comunicação constante e de baixa latência entre os nós, também conhecidos como aplicativos paramétricos ou totalmente paralelos.


## Especificações

### CPU e memória

As instâncias de computação intensiva A8, A9, A10 e A11 do Azure apresentam alta velocidade, CPUs com vários núcleos e grandes quantidades de memória, conforme mostra a tabela a seguir.

Tamanho | CPU | Memória
------------- | ----------- | ----------------
A8 e A10 | Intel Xeon E5-2670<br/>8 núcleos a 2,6 GHz | DDR3-1600 MHz<br/>56 GB
A9 e A11 | Intel Xeon E5-2670<br/>16 núcleos a 2,6 GHz | DDR3-1600 MHz<br/>112 GB


>[AZURE.NOTE]Encontre detalhes adicionais do processador, incluindo extensões do conjunto de instruções com suporte, no site Intel.com. Para obter os detalhes de disco e as capacidades de armazenamento de VM, confira [Tamanhos das máquinas virtuais](../articles/virtual-machines/virtual-machines-linux-sizes.md).

### Adaptadores de rede

As instâncias A8 e A9 têm dois adaptadores de rede que se conectam com as duas redes backend do Azure a seguir.


Rede | Descrição
-------- | -----------
Ethernet 10 Gbps | Conecta-se aos serviços do Azure (por exemplo, Armazenamento e Rede Virtual) e à Internet.
Back-end de 32 Gbps, compatível com RDMA | Permite a comunicação de aplicativo de baixa latência e alta taxa de transferência entre instâncias em um único serviço de nuvem ou conjunto de disponibilidade. Reservado para o tráfego de MPI apenas.


>[AZURE.IMPORTANT]Confira [Acesso à rede RDMA](#access-the-rdma-network), neste artigo, para ver requisitos adicionais a fim de que os aplicativos MPI acessem a rede RDMA.

As instâncias A10 e A11 têm um único adaptador de rede Ethernet de 10 Gbps que se conecta à Internet e aos serviços do Azure.

## Considerações de implantação

* **Conta do Azure** – Se desejar implantar mais do que um pequeno número de instâncias de computação intensiva, considere uma assinatura pré-paga ou outras opções de compra. Você também pode usar sua assinatura do MSDN. Consulte [Benefícios do Azure para assinantes do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Se estiver usando uma [conta gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/), você poderá usar apenas um número limitado de núcleos de computação do Azure.

* **Cota de núcleos** – Talvez você precise aumentar a cota de núcleos em sua assinatura do Azure do padrão de 20 núcleos por assinatura (se você usar o modelo de implantação clássica) ou 20 núcleos por região (se você usar o modelo de implantação do Azure Resource Manager). Para solicitar um aumento de cota, abra um tíquete de suporte gratuitamente, conforme mostrado em [Understanding Azure limits and increases](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/).

    >[AZURE.NOTE]Cotas do Azure são limites de crédito, não garantias de capacidade. Você é cobrado apenas pelo núcleos que usa.

* **Rede virtual** – Não há necessidade de ter uma [rede virtual](https://azure.microsoft.com/documentation/services/virtual-network/) do Azure para usar instâncias com computação intensiva. No entanto, talvez seja necessário ter pelo menos uma rede virtual do Azure baseada em nuvem para muitos cenários de IaaS, ou uma conexão entre sites se você precisar acessar recursos locais, como um servidor de licença de aplicativo. Você precisará criar uma nova rede virtual para implantar as instâncias. Não há suporte para a adição de uma VM A8, A9, A10 ou A11 a uma rede virtual em um grupo de afinidades.

* **Serviço de nuvem ou conjunto de disponibilidade** – Para se conectar por meio da rede RDMA, as instâncias A8 e A9 deverão ser implantadas no mesmo serviço de nuvem (se você usar o modelo de implantação clássica) ou no mesmo conjunto de disponibilidade (se você usar o modelo de implantação do Azure Resource Manager).

* **Preços** - Os tamanhos de VM A8-A11 só estão disponíveis no tipo de preço Standard.

* **Redimensionamento** – Não é possível redimensionar uma instância com um tamanho diferente de A8-A11 para um dos tamanhos da instância de computação intensiva (A8-11) e não é possível redimensionar uma instância de computação intensiva para um tamanho que não seja de computação intensiva. Isso ocorre devido ao hardware especializado e às otimizações de desempenho específicas para as instâncias de computação intensiva.

* **Espaço de endereço de rede RDMA** - A rede RDMA no Azure reserva o espaço de endereço 172.16.0.0/12. Se você planeja executar aplicativos MPI em instâncias A8 e A9 em uma rede virtual do Azure, verifique se o espaço do endereço de rede virtual não se sobrepõe à rede RDMA.

<!-----------HONumber=AcomDC_0330_2016-->