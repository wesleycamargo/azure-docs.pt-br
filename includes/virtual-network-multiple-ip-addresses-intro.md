> [!div class="op_single_selector"]
> * [Portal](../articles/virtual-network/virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](../articles/virtual-network/virtual-network-multiple-ip-addresses-powershell.md)
> * [CLI 2.0](../articles/virtual-network/virtual-network-multiple-ip-addresses-cli.md)
> * [CLI 1.0](../articles/virtual-network/virtual-network-multiple-ip-addresses-cli-nodejs.md)
> * [Modelo](../articles/virtual-network/virtual-network-multiple-ip-addresses-template.md)
>

Uma VM (máquina virtual) do Azure tem uma ou mais NICs (adaptadores de rede) conectadas a ele. Qualquer NIC pode ter um ou mais endereços IP públicos e privados estáticos ou dinâmicos atribuídos a ele. A atribuição de vários endereços IP a uma VM permite as seguintes capacidades:

* Hospede vários sites ou serviços com diferentes endereços IP e os certificados SSL em um único servidor.
* Funcione como um dispositivo virtual de rede, como um firewall ou balanceador de carga.
* A capacidade de adicionar qualquer um dos endereços IP privados para qualquer uma das NICs a um pool de back-end do Azure Load Balancer. No passado, somente o endereço IP primário para a NIC primária pode ser adicionado a um pool de back-end. Para saber mais sobre como balancear a carga de várias configurações de IP, leia o artigo [Balanceamento de carga de várias configurações de IP](../articles/load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Todas as NICs anexadas a uma VM têm uma ou mais configurações IP associadas a elas. Cada configuração recebe um endereço IP privado estático ou dinâmico. Cada configuração também pode ter um recurso de endereço IP público associado a ele. Um recurso de endereço IP público tem um endereço IP público dinâmico ou estático atribuído a ele. Para saber mais sobre endereços IP no Azure, leia o artigo [Endereços IP no Azure](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md). 

Você pode atribuir até 250 endereços IP privado a cada NIC. Você pode atribuir vários endereços IP públicos para cada NIC, mas há limites para quantos endereços IP públicos que podem ser usados em uma assinatura do Azure. Leia o artigo [Limites de Azure](../articles/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) para obter detalhes.
