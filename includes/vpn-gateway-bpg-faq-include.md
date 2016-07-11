### O BGP tem suporte em todas as SKUs de Gateway de VPN do Azure?

Não, o BGP tem suporte nos gateways de VPN **Standard** e **HighPerformance** do Azure. A SKU **Basic** não tem suporte.

### Posso usar o BGP com gateways de VPN Baseados em Política do Azure?

Não, há suporte ao o BGP somente em gateways de VPN Baseados em Rota.

### Posso usar ASNs (Números de Sistema Autônomo) privados?

Sim, você pode usar seu próprio ASNs públicos ou privados para suas redes locais e para redes virtuais do Azure.

#### Há ASNs reservados pelo Azure?

Sim, os seguintes ASNs estão reservados pelo Azure para emparelhamentos internos e externos:

- ASNs públicos: 8075, 8076, 12076
- ASNs privados: 65515, 65517, 65518, 65519, 65520

Não é possível especificar esses ASNs para seus dispositivos VPN locais ao conectar-se a gateways de VPN do Azure.

### Posso usar o mesmo ASN para redes de VPN locais e VNets do Azure?

Não, você deverá atribuir ASNs diferentes entre suas redes locais e as VNets do Azure se os estiver conectando junto com o BGP. Os Gateways de VPN do Azure têm um ASN padrão de 65515 atribuído, quer o BGP esteja habilitado ou não para a conectividade entre locais. Você pode substituir esse padrão atribuindo um ASN diferente ao criar o gateway de VPN ou alterar o ASN depois de criar o gateway. Você precisará atribuir ASNs locais aos Gateways de Rede Local do Azure correspondentes.

### Quais prefixos de endereço os gateways de VPN do Azure anunciarão para mim?

O Gateway de VPN do Azure anunciará as seguintes rotas para seus dispositivos de BGP locais:

- Seus prefixos de endereços de VNet
- Prefixos de endereços para cada Gateway de Rede Local conectado ao gateway de VPN do Azure
- As rotas obtidas de outras sessões de emparelhamento de BGP conectadas ao gateway de VPN do Azure, **exceto rotas padrão sobrepostas com qualquer prefixo de VNet**.

#### Posso anunciar a rota padrão (0.0.0.0/0) para gateways de VPN do Azure?

Não no momento.

#### Posso anunciar os prefixos exatamente como meus prefixos de Rede Virtual?

Não, o anúncio dos mesmos prefixos como qualquer um de seus prefixos de endereço de sua Rede Virtual será bloqueado ou filtrado pela plataforma do Azure.

### Posso usar o BGP com minhas conexões VNet para VNet?

Sim, você pode usar o BGP para conexões entre locais e conexões de VNet para VNet.

### Posso combinar o BGP a conexões não BGP para meu gateways de VPN do Azure?

Sim, você pode combinar conexões BGP e não BGP para o mesmo gateway de VPN do Azure.

### O gateway de VPN do Azure dá suporte ao roteamento de trânsito de BGP?

Sim, o roteamento de trânsito de BGP tem suporte, com a exceção de que os gateways de VPN do Azure **NÃO** anunciarão rotas padrão para outros pares de BGP. Para habilitar o roteamento de trânsito entre vários gateways de VPN do Azure, você deve habilitar o BGP em todas as conexões de VNet para VNet intermediárias.

### Posso ter mais de um túnel entre meu gateway de VPN do Azure e minha rede local?

Sim, você pode estabelecer mais de um túnel de VPN S2S entre um gateway de VPN do Azure e sua rede local. Observe que todos esses túneis serão contados em relação ao número total de túneis para seus gateways de VPN do Azure. Por exemplo, se você tiver dois túneis redundantes entre o gateway de VPN do Azure e uma de suas redes locais, elas consumirão dois túneis da cota total de seu gateway de VPN do Azure (10 para Standard e 30 para HighPerformance).

### Posso ter vários túneis entre duas VNets do Azure com o BGP?

Não, não há suporte a túneis redundantes entre um par de redes virtuais.

### Posso usar BGP para VPN S2S em uma configuração de coexistência de VPN S2S/Rota Expressa?

Não no momento.

### Que endereço o gateway de VPN do Azure usa para o IP de Par de BGP?

O gateway de VPN do Azure alocará um único endereço IP do intervalo de GatewaySubnet definido para a rede virtual. Por padrão, ele é o penúltimo endereço do intervalo. Por exemplo, se o GatewaySubnet for 10.12.255.0/27, que varia de 10.12.255.0 a 10.12.255.31, o endereço IP do Par de BGP no gateway de VPN do Azure será 10.12.255.30. Você pode localizar essas informações ao listar as informações de gateway de VPN do Azure.

### Quais são os requisitos para os endereços IP de Par de BGP em meu dispositivo VPN?

Seu endereço de par do BGP local **NÃO DEVE** ser o mesmo que o endereço IP público do dispositivo VPN. Use um endereço IP diferente no dispositivo VPN para o IP de Par de BGP. Pode ser um endereço atribuído à interface de loopback no dispositivo. Especifique esse endereço no Gateway de Rede Local correspondente que representa o local.

### O que devo especificar como meus prefixos de endereço para o Gateway de Rede Local ao usar o BGP?

O Gateway de Rede Local do Azure especifica os prefixos de endereços iniciais para a rede local. Com o BGP, você deve alocar o prefixo de host (prefixo /32) de seu endereço IP de Par de BGP como o espaço de endereço da rede local. Se o IP de Par de BGP for 10.52.255.254, você deverá especificar "10.52.255.254/32" como localNetworkAddressSpace do Gateway de Rede Local que representa essa rede local. Isso é para garantir que o gateway de VPN do Azure estabeleça a sessão de BGP através do túnel de VPN S2S.

### O que devo adicionar a meu dispositivo VPN local para a sessão de emparelhamento de BGP?

Você deve adicionar uma rota de host do endereço IP de Par de BGP do Azure em seu dispositivo VPN apontando para o túnel de VPN S2S IPsec. Por exemplo, se o IP de Par de VPN do Azure for "10.12.255.30", você deverá adicionar uma rota de host para "10.12.255.30" com uma interface de nexthop da interface de túnel IPsec correspondente em seu dispositivo VPN.

<!---HONumber=AcomDC_0629_2016-->