<properties 
   pageTitle="Perguntas frequentes sobre o Gateway de VPN de rede virtual | Microsoft Azure"
   description="Perguntas frequentes sobre o Gateway de VPN. Perguntas frequentes para conexões entre locais de rede virtual do Microsoft Azure, conexões de configuração híbrida e gateways de VPN"
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/10/2016"
   ms.author="yushwang" />

# Perguntas frequentes de gateway de VPN

## Conectando-se a Redes Virtuais

### Posso conectar redes virtuais em diferentes regiões do Azure?
Sim. Na verdade, não há nenhuma restrição de região. Uma rede virtual pode se conectar a outra rede virtual na mesma região ou em outra região do Azure.

### Posso conectar redes virtuais em assinaturas diferentes?
Sim.

### Posso me conectar vários sites de uma única rede virtual?

Você pode se conectar a vários sites usando o Windows PowerShell e as APIs REST do Azure. Consulte a seção [Conectividade multissite e de VNet para VNet](#multi-site-and-vnet-to-vnet-connectivity) das perguntas frequentes.
## Quais são minhas opções de conexão entre locais?

Há suporte para as seguintes conexões entre locais:

- [Site a Site](vpn-gateway-site-to-site-create.md) – conexão VPN sobre IPsec (IKE v1 e IKE v2). Esse tipo de conexão exige um dispositivo VPN ou RRAS.

- [Ponto a Site](vpn-gateway-point-to-site-create.md) – conexão VPN sobre SSTP (Secure Socket Tunneling Protocol). Essa conexão não exige um dispositivo VPN.

- [VNet a VNet](virtual-networks-configure-vnet-to-vnet-connection.md) - Esse tipo de conexão é o mesmo que uma configuração de site a site. VNet a VNet é uma conexão VPN sobre IPsec (IKE v1 e IKE v2). Ela não requer um dispositivo VPN.

- [Multissite](vpn-gateway-multi-site.md) - Essa é uma variação de uma configuração de site a site que permite conectar vários sites locais a uma rede virtual.

- [Rota Expressa](../expressroute/expressroute-introduction.md) - Rota Expressa é uma conexão direta para o Azure de sua WAN, não pela Internet pública. Consulte a [Visão geral técnica da Rota Expressa](../expressroute/expressroute-introduction.md) e as [Perguntas frequentes sobre a Rota Expressa](../expressroute/expressroute-faqs.md) para obter mais informações.

Para saber mais sobre conexões, confira [Sobre Gateway de VPN](vpn-gateway-about-vpngateways.md).

### Qual é a diferença entre uma conexão site a site e uma ponto a site?

As conexões **site a site** permitem que você conecte entre qualquer um dos computadores localizados em suas instalações para qualquer máquina virtual ou instância de função em sua rede virtual, dependendo de como você optar por configurar o roteamento. É uma ótima opção para uma conexão entre locais sempre disponível e é bastante adequada para configurações híbridas. Esse tipo de conexão se baseia em um dispositivo VPN IPsec (dispositivo de hardware ou software), que deve ser implantado na borda de sua rede. Para criar esse tipo de conexão, você deverá ter o hardware de VPN necessário e um endereço IPv4 voltado para o exterior.

As conexões de **ponto a site** permitem que você se conecte de um único computador em qualquer lugar para qualquer item localizado em sua rede virtual. Elas usam o cliente de VPN integrado ao Windows. Como parte da configuração de ponto a site, você pode instalar um certificado e um pacote de configuração de cliente VPN, que contém as configurações que permitem que o computador se conecte a qualquer máquina virtual ou instância de função na rede virtual. É ótimo quando você deseja se conectar a uma rede virtual, mas não está localizado no local. Também é uma boa opção quando você não tem acesso a hardware de VPN ou a um endereço IPv4 voltado para o exterior, sendo que ambos são necessários para uma conexão site a site.

Você pode configurar sua rede virtual para usar as opções de site a site e ponto a site simultaneamente, desde que crie sua conexão site a site usando um tipo de VPN com base em rota para seu gateway. Tipos de VPN baseados em rota são chamados de gateways dinâmicos no modelo de implantação clássica.

### O que é Rota Expressa?

A Rota Expressa permite a criação de conexões privadas entre os datacenters da Microsoft e a infraestrutura no seu local ou em um ambiente de colocalização. Com a Rota Expressa, é possível estabelecer conexões com os serviços em nuvem da Microsoft como o Microsoft Azure e Office 365 em uma instalação de colocalização de parceiro da Rota Expressa, ou conectar-se diretamente pela rede WAN existente (como VPN MLPS fornecidas por um provedor de serviços de rede).

As conexões de rota expressa oferecem maior segurança, mais confiabilidade, maior largura de banda e latências menores do que as conexões típicas pela Internet. Em alguns casos, o uso de conexões de Rota Expressa para transferir dados entre sua rede local e o Azure também pode proporcionar relações custo/benefício significativas. Se você já tiver criado uma conexão entre locais de sua rede local para o Azure, você pode migrar para uma conexão de Rota Expressa, mantendo a sua rede virtual intacta.

Consulte as [Perguntas Frequentes sobre Rota Expressa](../expressroute/expressroute-faqs.md) para obter mais detalhes.

## Conexões de site a site e dispositivos VPN

### O que devo considerar ao escolher um dispositivo VPN?

Validamos um conjunto de dispositivos de VPN site a site padrão em parceria com fornecedores de dispositivos. Uma lista de dispositivos de VPN compatíveis conhecidos, as instruções de configuração correspondentes ou exemplos e especificações de dispositivo pode ser encontrados [aqui](vpn-gateway-about-vpn-devices.md). Todos os dispositivos das famílias de dispositivos listadas como compatíveis e conhecidas devem funcionar com a Rede Virtual. Para ajudar a configurar seu dispositivo VPN, consulte o exemplo de configuração do dispositivo ou o link que corresponde à família de dispositivos apropriada.

### O que devo fazer se tiver um dispositivo VPN que não esteja na lista de dispositivos compatíveis conhecidos?

Se não encontrar seu dispositivo listado como um dispositivo VPN compatível conhecido e quiser usá-lo para a conexão VPN, você precisará verificar se ele atende às opções de configuração de IPsec/IKE e aos parâmetros listados com suporte [aqui](vpn-gateway-about-vpn-devices.md#devices-not-on-the-compatible-list). Os dispositivos que atendem aos requisitos mínimos devem funcionar bem com os gateways de VPN. Entre em contato com o fabricante do dispositivo para obter instruções adicionais de configuração e suporte.

### Por que meu túnel VPN baseado em políticas é desativado quando o tráfego está ocioso?

Esse comportamento é esperado para gateways de VPN baseados em políticas (também conhecidos como roteamento estático). Quando o tráfego pelo túnel de fica ocioso por mais de 5 minutos, o túnel é interrompido. Assim que o tráfego começa a fluir em qualquer direção, o túnel é restabelecido imediatamente. Se tiver um gateway de VPN baseado em rotas (também conhecido como dinâmico), você não terá esse comportamento.

### Posso usar VPNs de software para me conectar ao Azure?

Há suporte para servidores RRAS (Roteamento e Acesso Remoto) do Windows Server 2012 para configuração entre locais site a site.

Outras soluções VPN de software devem funcionar com nosso gateway, contanto que estejam em conformidade com implementações de IPsec padrão do setor. Contate o fornecedor do software para obter instruções de configuração e suporte.

## Conexões Ponto a Site

### Quais sistemas operacionais posso usar com ponto a site?

Há suporte para os seguintes sistemas operacionais:

- Windows 7 (32 bits e 64 bits)

- Windows Server 2008 R2 (somente 64 bits)

- Windows 8 (32 bits e 64 bits)

- Windows 8.1 (32 bits e 64 bits)

- Windows Server 2012 (somente 64 bits)

- Windows Server 2012 R2 (somente 64 bits)

- Windows 10

### Posso usar qualquer cliente de VPN de software para ponto a site que dê suporte a SSTP?

Não. O suporte é limitado somente às versões do sistema operacional Windows listadas acima.

### Quantos pontos de extremidade de cliente VPN posso ter em minha configuração ponto a site?

Damos suporte para até 128 clientes VPN para poderem se conectar a uma rede virtual ao mesmo tempo.

### Posso usar minha própria CA de raiz de PKI interna para a conectividade ponto a site?

Sim. Anteriormente, somente os certificados raiz autoassinados podiam ser usados. Você ainda pode carregar 20 certificados raiz.

### Posso atravessar proxies e firewalls usando o recurso de ponto a site?

Sim. Usamos o protocolo SSTP (Secure Socket Tunneling Protocol) para túnel através de firewalls. Esse túnel aparecerá como uma conexão HTTPs.

### Se eu reiniciar um computador cliente configurado para ponto a site, a VPN se reconectará automaticamente?

Por padrão, o computador cliente não restabelecerá a conexão VPN automaticamente.

### A opção de ponto para site dá suporte a reconexão automática e DDNS nos clientes de VPN?

A reconexão automática e o DDNS atualmente não têm suporte em VPNs ponto a site.

### Posso ter configurações site a site e ponto a site que coexistam para a mesma rede virtual?

Sim. Essas duas soluções funcionarão se você tiver um tipo VPN Baseada em Rota para o gateway. Para o modelo de implantação clássica, você precisará de um gateway dinâmico. Não damos suporte a ponto a site para o roteamento estático de gateways VPN ou gateways usando -VpnType PolicyBased.

### Posso configurar um cliente de ponto a site para se conectar a várias redes virtuais ao mesmo tempo?

Sim, isso é possível. Porém, as redes virtuais não podem ter prefixos IP sobrepostos, e os espaços de endereço de ponto para site não devem causar sobreposição entre as redes virtuais.

### Quanta taxa de transferência posso esperar por meio de conexões site a site ou ponto a site?

É difícil manter a taxa de transferência exata dos túneis de VPN. IPsec e SSTP são protocolos VPN de criptografia pesada. A taxa de transferência também é limitada pela latência e pela largura de banda entre seus locais e na Internet.

## Gateways

### O que é um gateway baseado em políticas (roteamento estático)?

Os gateways baseados em política implementam VPNs baseadas em política. As VPNs baseadas em política criptografam e direcionam pacotes por meio de túneis IPsec com base em combinações de prefixos de endereço entre sua rede local e a VNet do Azure. A política (ou o seletor de tráfego) normalmente é definida como uma lista de acesso na configuração de VPN.

### O que é um gateway baseado em rotas (roteamento dinâmico)?

Gateways baseados em rota implementam VPNs baseadas em rota. As VPNs baseadas em rota usam "rotas" da tabela de roteamento ou de encaminhamento de IP para direcionar pacotes para as interfaces de túnel correspondentes. As interfaces de túnel criptografam ou descriptografam então os pacotes para dentro e para fora dos túneis. O seletor de política ou de tráfego para as VPNs baseadas em rota são configurados como qualquer para qualquer (ou curingas).

### Posso obter o endereço IP do gateway de VPN antes de criá-lo?

Não. Você precisa criar seu gateway primeiro para obter o endereço IP. O endereço IP será alterado se você excluir e recriar o gateway de VPN.

### Como meu túnel de VPN é autenticado?

A VPN do Azure usa autenticação PSK (Chave Pré-Compartilhada). Geramos uma PSK (chave pré-compartilhada) durante a criação do túnel de VPN. Você pode alterar a PSK gerada automaticamente para a sua própria com o cmdlet do PowerShell Definir Chave Pré-Compartilhada ou a API REST.

### Posso usar a API Definir chave pré-compartilhada para configurar minha VPN de gateway baseada em política (roteamento estático)?

Sim, a API Definir chave pré-compartilhada e o cmdlet do PowerShell podem ser usados para configurar VPNs baseadas em política (estáticas) do Azure e as VPNs de roteamento baseadas em rota (dinâmicas).

### É possível usar outras opções de autenticação?

Estamos limitados ao uso de PSK (chaves pré-compartilhadas) para autenticação.

### O que é a "sub-rede de gateway" e por que ela é necessária?

Temos um serviço de gateway que executamos para habilitar a conectividade entre locais.

Você precisará criar uma sub-rede de gateway para sua rede virtual a fim de configurar um gateway de VPN. Todas as sub-redes de gateway devem ser nomeadas como GatewaySubnet para funcionar adequadamente. Não dê outro nome à sua sub-rede de gateway. E não implante VMs ou qualquer outra coisa na sub-rede de gateway.

O tamanho mínimo de sub-rede de gateway depende totalmente da configuração que você deseja criar. Embora seja possível criar uma sub-rede de gateway tão pequena quanto /29 em algumas configurações, recomendamos que você crie uma sub-rede de gateway de /28 ou maior (/28, /27, /26 etc.).

### Posso implantar máquinas virtuais ou instâncias de função na minha sub-rede de gateway?

Nº

### Como especificar qual tráfego passa pelo gateway de VPN?

Se estiver usando o Portal do Azure, adicione cada intervalo que deseja enviar pelo gateway para sua rede virtual na página Redes, em Redes Locais.

### Posso configurar o Túnel Forçado?

Sim. Consulte [Configurar o túnel forçado](vpn-gateway-about-forced-tunneling.md).

### Posso configurar meu próprio servidor de VPN no Azure e usá-lo para me conectar à minha rede local?

Sim, você pode implantar seus próprios gateways de VPN ou servidores no Azure, por meio do Azure Marketplace, ou criar seus próprios roteadores de VPN. Você precisará configurar Rotas Definidas pelo Usuário em sua rede virtual para garantir que o tráfego seja roteado corretamente entre suas redes locais e suas sub-redes de rede virtual.

### Por que determinadas portas serão abertas no meu gateway VPN?

Elas são necessárias para a comunicação de infraestrutura do Azure. Elas são protegidas (bloqueadas) por certificados do Azure. Sem certificados apropriados, entidades externas, incluindo os clientes desses gateways, não poderão causar efeitos nesses pontos de extremidade.

Um gateway de VPN é fundamentalmente um dispositivo de hospedagem múltipla com um toque de NIC para a rede privada do cliente e uma NIC voltada para a rede pública. As entidades de infraestrutura do Azure não podem tocar em redes privadas de cliente por motivos de conformidade, devendo utilizar pontos de extremidade públicos para comunicação de infraestrutura. Os pontos de extremidade públicos são verificados periodicamente pela auditoria de segurança do Azure.


### Mais informações sobre tipos de gateway, requisitos e taxa de transferência

Para saber mais, confira [Sobre configurações de Gateway de VPN](vpn-gateway-about-vpn gateway-settings.md).

## Conectividade multissite e de VNet para VNet

### Que tipo de gateways podem dar suporte a vários locais e conectividade VNet para VNet?

Somente VPNs baseadas em rota (roteamento dinâmico).

### Posso conectar uma rede virtual a um tipo de VPN RouteBased para outra rede virtual com um tipo de VPN PolicyBased?

Não, ambas as redes virtuais DEVEM estar usando VPNs baseadas em rota (roteamento dinâmico).

### O tráfego de VNet para VNet é seguro?

Sim, ele é protegido por criptografia IPsec/IKE.

### Tráfego Rede Virtual para Rede Virtual passa pelo backbone do Azure?

Sim.

### A quantos sites locais e redes virtuais uma rede virtual pode se conectar?

Máx. Dez combinados para os gateways Basic e Standard de Roteamento Dinâmico; 30 para os gateways de VPN de Alto Desempenho.

### Posso usar VPNs de ponto a site com minha rede virtual com vários túneis de VPN?

Sim, as VPNs P2S (ponto a site) podem ser usadas com os gateways de VPN conectando a vários sites locais e outras redes virtuais.

### Posso configurar vários túneis entre minha rede virtual e meu site local usando uma VPN multissite?

Não, não há suporte para túneis redundantes entre uma rede virtual do Azure e um site local.

### Pode haver sobreposição de espaços de endereço entre as redes virtuais conectadas e sites local locais?

Não. Espaços de endereço sobrepostos farão com que o carregamento de arquivo de configuração de rede ou a “Criação de Rede Virtual” falhe.

### Obtenho mais largura de banda com mais VPNs site a site do que com uma única rede virtual?

Não, todos os túneis de VPN, incluindo VPNs site a ponto, compartilham o mesmo gateway de VPN do Azure e a largura de banda disponível.

### Posso usar o gateway de VPN do Azure para o tráfego entre meus sites locais ou para outra rede virtual?

**Modelo de implantação clássica**<br> O tráfego via Gateway de VPN do Azure é possível usando o modelo de implantação clássica, mas se baseia em espaços de endereço estaticamente definidos no arquivo de configuração de rede. Ainda não há suporte a BGP com Redes Virtuais do Azure e Gateways de VPN usando o modelo de implantação clássica. Sem BGP, a definição manual de espaços de endereço de trânsito é muito propensa a erros e não é recomendada.<br> **Modelo de implantação do Resource Manager**<br> Se você estiver usando o modelo de implantação do Resource Manager, consulte a seção [BGP](#bgp) para saber mais.

### O Azure gera a mesma chave pré-compartilhada IPsec/IKE para todas as minhas conexões VPN para a mesma rede virtual?

Não, por padrão, o Azure gera chaves pré-compartilhadas diferentes para conexões VPN diferentes. No entanto, você pode usar a API REST Definir Chave de Gateway de VPN ou o cmdlet do PowerShell para definir o valor de chave que preferir. A chave deve ser uma cadeia de caracteres alfanumérica com comprimento entre 1 e 128 caracteres.

### O Azure cobra pelo tráfego entre redes virtuais?

Para o tráfego entre diferentes redes virtuais do Azure, o Azure cobra somente pelo tráfego que passa de uma região do Azure para outra. A taxa de custo é listada na página [Preços do Gateway de VPN do Azure](https://azure.microsoft.com/pricing/details/vpn-gateway/).


### Posso conectar uma rede virtual com VPNs IPsec a meu circuito Rota Expressa?

Sim, isso é suportado. Para obter mais informações, consulte [Configurar conexões de VPN Site a Site e de Rota Expressa que coexistam](../expressroute/expressroute-howto-coexist-classic.md).

## <a name="bgp"></a>BGP

[AZURE.INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)]



## Conectividade entre locais e VMs

### Se minha máquina virtual estiver em uma rede virtual e eu tiver uma conexão entre locais, como devo me conectar à VM?

Você tem algumas opções. Se tiver um protocolo RDP habilitado e tiver criado um ponto de extremidade, você poderá se conectar à sua máquina virtual usando o VIP. Nesse caso, você especificaria o VIP e a porta à qual deseja se conectar. Você precisará configurar a porta em sua máquina virtual para o tráfego. Normalmente, você deve ir para o Portal Clássico do Azure e salvar as configurações para a conexão de RDP em seu computador. As configurações conterão as informações de conexão necessárias.

Se tiver uma rede virtual com conectividade entre locais configurada, você poderá se conectar à sua máquina virtual usando o DIP interno ou o endereço IP privado. Você também pode se conectar à sua máquina virtual por DIP interno de outra máquina virtual localizada na mesma rede virtual. Você não poderá RDP para sua máquina virtual usando o DIP se estiver conectando de um local fora de sua rede virtual. Por exemplo, se tiver uma rede virtual de ponto para site configurada e não estabelecer uma conexão do seu computador, você não poderá se conectar à máquina virtual por DIP.

### Se a minha máquina virtual estiver em uma rede virtual com conectividade entre locais, todo o tráfego de minha VM passará por essa conexão?

Não. Somente o tráfego com um destino IP contido em intervalos de endereços IP de rede Local virtual de rede especificado passará pelo gateway de rede virtual. O tráfego que tiver um destino IP localizado na rede virtual permanecerá na rede virtual. Outro tráfego é enviado pelo balanceador de carga para as redes públicas ou, se for usado o túnel forçado, enviado pelo gateway da VPN do Azure. Se você está solucionando problemas, é importante verificar se tem todos os intervalos listados em sua Rede Local que você deseja enviar pelo gateway. Verifique se os intervalos de endereços de Rede Local não correspondem a nenhum dos intervalos de endereços na rede virtual. Além disso, convém verificar se o servidor DNS que você está usando está resolvendo o nome para o endereço IP apropriado.


## Perguntas frequentes sobre rede virtual

As informações adicionais de rede virtual são exibidas em [Perguntas Frequentes sobre Rede Virtual](../virtual-network/virtual-networks-faq.md).
 

<!---HONumber=AcomDC_0831_2016-->