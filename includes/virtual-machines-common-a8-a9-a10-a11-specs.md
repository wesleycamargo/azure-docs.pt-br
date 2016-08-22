
Os principais recursos dessas instâncias incluem:

* **Hardware de alto desempenho** – O hardware de datacenter do Azure que executa essas instâncias foi projetado e otimizado para aplicativos de computação e rede intensivas, incluindo aplicativos HPC (computação de alto desempenho) e de lote, modelagem e simulações em larga escala.

* **Conexão de rede RDMA para aplicativos MPI** – Configure as instâncias A8 e A9 para se comunicarem com outras instâncias A8 e A9 em uma rede com alta taxa de transferência e baixa latência no Azure que se baseia na tecnologia RDMA (acesso remoto direto à memória). Esse recurso pode aumentar o desempenho de determinados aplicativos MPI (Interface de Troca de Mensagens) do Linux e Windows.

* **Suporte para clusters HPC** – Implante um software de agendamento de trabalho e gerenciamento de cluster nas instâncias A8, A9, A10 e A11 no Azure para criar um cluster HPC autônomo ou para adicionar capacidade a um cluster local.

>[AZURE.NOTE]As instâncias A10 e A11 têm as mesmas otimizações de desempenho e especificações das instâncias A8 e A9. No entanto, elas não incluem o acesso à rede RDMA no Azure. Elas foram projetadas para aplicativos HPC que não exigem comunicação constante e de baixa latência entre os nós, também conhecidos como aplicativos paramétricos ou totalmente paralelos.


## Especificações

### CPU e memória

As instâncias de computação intensiva A8, A9, A10 e A11 do Azure apresentam alta velocidade, CPUs com vários núcleos e grandes quantidades de memória, conforme mostra a tabela a seguir.

Tamanho | CPU | Memória
------------- | ----------- | ----------------
A8 e A10 | Intel Xeon E5-2670<br/>8 núcleos a 2,6 GHz | DDR3-1600 MHz<br/>56 GB
A9 e A11 | Intel Xeon E5-2670<br/>16 núcleos a 2,6 GHz | DDR3-1600 MHz<br/>112 GB


>[AZURE.NOTE]Encontre detalhes adicionais do processador, incluindo extensões do conjunto de instruções com suporte, no site Intel.com.

### Adaptadores de rede

As instâncias A8 e A9 têm dois adaptadores de rede que se conectam com as duas redes backend do Azure a seguir.


Rede | Descrição
-------- | -----------
Ethernet 10 Gbps | Conecta-se aos serviços do Azure (por exemplo, Armazenamento e Rede Virtual) e à Internet.
Back-end de 32 Gbps, compatível com RDMA | Permite a comunicação de aplicativo de baixa latência e alta taxa de transferência entre instâncias em um único serviço de nuvem ou conjunto de disponibilidade. Reservado para o tráfego de MPI apenas.


>[AZURE.IMPORTANT]Confira [Acesso à rede RDMA](#access-to-the-rdma-network), neste artigo para obter requisitos adicionais a fim de que os aplicativos MPI acessem a rede RDMA.

As instâncias A10 e A11 têm um único adaptador de rede Ethernet de 10 Gbps que se conecta à Internet e aos serviços do Azure.

## Considerações de implantação

* **Assinatura do Azure** – Se desejar implantar mais do que um pequeno número de instâncias de computação intensiva, considere uma assinatura pré-paga ou outras opções de compra. Se estiver usando uma [conta gratuita do Azure](https://azure.microsoft.com/free/), você poderá usar apenas um número limitado de núcleos de computação do Azure.

* **Cota de núcleos** – Talvez você precise aumentar a cota de núcleos em sua assinatura do Azure do padrão de 20 núcleos por assinatura (se usar o modelo de implantação clássica) ou 20 núcleos por região (se usar o modelo de implantação do Gerenciador de Recursos). Para solicitar um aumento de cota, abra um tíquete de suporte gratuitamente, conforme mostrado em [Understanding Azure limits and increases](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) (Entendendo os limites e aumentos do Azure). (Os limites padrão podem variar dependendo de sua categoria de assinatura.)

    >[AZURE.NOTE]Entre em contato com o Suporte do Azure se precisar de capacidade em larga escala. Cotas do Azure são limites de crédito, não garantias de capacidade. Independentemente de sua cota, você é cobrado apenas pelo núcleos utilizados.

* **Rede virtual** – Não é necessário ter uma [rede virtual](https://azure.microsoft.com/documentation/services/virtual-network/) do Azure para usar instâncias de computação intensiva. No entanto, talvez você precise ter, pelo menos, uma rede virtual do Azure baseada em nuvem para vários cenários ou uma conexão site a site se precisar acessar recursos locais, como um servidor de licença de aplicativo. Se for necessário, você precisará criar uma nova rede virtual para implantar as instâncias. Não há suporte para a adição de uma VM A8, A9, A10 ou A11 a uma rede virtual em um grupo de afinidades.

* **Serviço de nuvem ou conjunto de disponibilidade** – Para se conectar por meio da rede RDMA, deverão ser implantadas VMs de tamanho A8 e A9 no mesmo serviço de nuvem (se usar o modelo de implantação clássica) ou no mesmo conjunto de disponibilidade (se usar o modelo de implantação do Azure Resource Manager).

* **Preços** - Os tamanhos de VM A8-A11 só estão disponíveis no tipo de preço Standard.

* **Redimensionamento** – Não é possível redimensionar uma instância com um tamanho diferente de A8-A11 para um dos tamanhos da instância de computação intensiva (A8-11) e não é possível redimensionar uma instância de computação intensiva para um tamanho que não seja de computação intensiva. Isso ocorre devido ao hardware especializado e às otimizações de desempenho específicas para as instâncias de computação intensiva.

* **Espaço de endereço de rede RDMA** - A rede RDMA no Azure reserva o espaço de endereço 172.16.0.0/16. Se você planeja executar aplicativos MPI em instâncias A8 e A9 em uma rede virtual do Azure, verifique se o espaço do endereço de rede virtual não se sobrepõe à rede RDMA.

<!---HONumber=AcomDC_0810_2016-->