Você pode usar um balanceador de carga para fornecer alta disponibilidade para suas cargas de trabalho no Azure. Um balanceador de carga do Azure é um balanceador de carga do tipo Camada 4 (TCP, UDP) que distribui tráfego de entrada entre instâncias íntegras de serviço, em serviços de nuvem ou máquinas virtuais, definidos em um conjunto de balanceador de carga.
 
Você pode configurar um balanceador de carga para:

- Balancear a carga do tráfego de entrada na Internet para VMs (máquinas virtuais). Nós nos referimos a um balanceador de carga neste cenário como um [Balanceador de carga voltado para a Internet](../articles/load-balancer/load-balancer-internet-overview.md).
- Balancear a carga do tráfego entre máquinas virtuais em uma VNet (rede virtual), entre máquinas virtuais em serviços de nuvem ou entre computadores locais e máquinas virtuais em uma rede virtual entre instalações. Nós nos referimos a um balanceador de carga neste cenário como um [ILB (Balanceador de carga interno)](../articles/load-balancer/load-balancer-internal-overview.md).
- 	Encaminhar tráfego externo para uma instância VM específica.

<!---HONumber=AcomDC_0224_2016-->