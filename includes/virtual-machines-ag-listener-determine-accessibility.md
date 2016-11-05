É importante perceber que há duas maneiras de configurar um ouvinte do Grupo de Disponibilidade no Azure. Esses métodos são diferentes do tipo de Balanceador de carga do Azure que você utiliza ao criar o ouvinte. A tabela a seguir lista as diferenças:

| Balanceador de carga | Implementação | Use quando: |
| --- | --- | --- |
| **Externo** |Usa o **Endereço de IP virtual público** do serviço de nuvem que hospeda as Máquinas Virtuais. |Você precisa acessar ao ouvinte de fora da rede virtual, inclusive da internet. |
| **Interna** |Usa **Balanceamento de carga interno (ILB)** com um endereço privado para o ouvinte. |Você só pode acessar ao ouvinte de dentro da mesma rede virtual. Isso inclui VPN site a site em cenários híbridos. |

> [!IMPORTANT]
> Para um ouvinte que está usando o VIP público (balanceador externo de carga) do serviço de nuvem, desde que o cliente, o ouvinte e os bancos de dados estejam na mesma região do Azure, você não incorrerá em encargos de saída. Caso contrário, os dados retornados através do ouvinte são considerados dados de saída e serão cobradas as taxas de transferência de dados normais.
> 
> 

ILB só pode ser configurado em redes virtuais com um escopo regional. Redes virtuais existentes configuradas para um grupo de afinidade não podem usar o ILB. Para obter mais informações, consulte [Balanceador de carga interno](../articles/load-balancer/load-balancer-internal-overview.md).

