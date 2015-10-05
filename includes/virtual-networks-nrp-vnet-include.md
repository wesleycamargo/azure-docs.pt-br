## Rede Virtual
VNETs (Redes virtuais) e sub-redes ajudam a definir um limite de segurança para cargas de trabalho em execução no Azure. Uma VNET é caracterizada por um conjunto de espaços de endereço, também conhecido como blocos CIDR.

Uma sub-rede é um recurso filho de uma VNET e ajuda a definir segmentos de espaços de endereço em um bloco CIDR, usando prefixos de endereço IP. NICs podem ser adicionadas a sub-redes e conectadas às VMs, fornecendo conectividade para diversas cargas de trabalho.

![NICs em uma única VM](./media/resource-groups-networking/Figure4.png)

As propriedades principais de um recurso VNET incluem:

- Espaço de endereço IP (bloco CIDR) 
- Nome da VNET
- sub-redes
- Servidores DNS

Uma VNET também pode ser associada aos seguintes recursos de rede:

- Gateway de VPN

### Sub-rede

As propriedades principais de uma sub-rede incluem:

- Prefixo de endereço IP
- Nome da sub-rede

Uma sub-rede também pode ser associada aos seguintes recursos de rede:

- NSG

<!---HONumber=Sept15_HO4-->