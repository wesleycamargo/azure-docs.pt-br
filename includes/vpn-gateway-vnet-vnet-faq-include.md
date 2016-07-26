- As redes virtuais podem estar na mesma região ou em regiões diferentes do Azure (locais).

- Um serviço de nuvem ou um ponto de extremidade de balanceamento de carga NÃO PODE se estender entre redes virtuais, mesmo que elas estejam conectadas.

- A conexão de várias redes virtuais do Azure não requer gateways de VPN locais, a menos que a conectividade entre locais seja necessária.

- A rede virtual com rede virtual dá suporte à conexão de redes virtuais. Ele não dá suporte à conexão de máquinas virtuais ou serviços de nuvem que NÃO estão em uma rede virtual.

- A rede virtual com rede virtual exige gateways de VPN com tipos de VPN RouteBased (anteriormente chamados roteamento dinâmico).

- A conectividade de rede virtual pode ser usada simultaneamente com VPNs de vários sites, com um máximo 10 túneis de VPN (Gateways padrão) ou 30 (Gateways de alto desempenho) para um gateway de VPN de rede virtual conectando a outras redes virtuais ou a sites locais.

- Os espaços de endereço das redes virtuais e os sites de redes locais não devem se sobrepor. A sobreposição de espaços de endereço resultará na falha da criação de conexões de rede virtual com rede virtual.

- Não há suporte a túneis redundantes entre um par de redes virtuais.

- Todos os túneis de VPN da rede virtual compartilham a largura de banda disponível no gateway de VPN do Azure e o mesmo SLA de tempo de atividade de gateway de VPN no Azure.

- O tráfego de rede virtual com rede virtual viaja pela Microsoft Network, não pela Internet.

- O tráfego de rede virtual com rede virtual na mesma região é gratuito para ambas as direções. O tráfego de saída de rede virtual com rede virtual entre regiões é cobrado de acordo com as taxas de transferência de dados entre redes virtuais de saída com base nas regiões de origem. Consulte a [página de preços](https://azure.microsoft.com/pricing/details/vpn-gateway/) para obter detalhes.

<!---HONumber=AcomDC_0720_2016-->