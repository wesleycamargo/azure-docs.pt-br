<a id="is-custom-ipsecike-policy-supported-on-all-azure-vpn-gateway-skus" class="xliff"></a>

### A política de IPsec/IKE personalizada tem suporte em todos os SKUs de Gateway de VPN do Azure?
A política de IPsec/IKE personalizada tem suporte nos gateways de VPN do Azure **VpnGw1, VpnGw2, VpnGw3, Standard** e **HighPerformance**. **Basic** não tem suporte.

<a id="how-many-policies-can-i-specify-on-a-connection" class="xliff"></a>

### Quantas políticas eu posso especificar em uma conexão?
Você só pode especificar ***uma*** combinação de políticas para uma determinada conexão.

<a id="can-i-specify-a-partial-policy-on-a-connection-eg-only-ike-algorithms-but-not-ipsec" class="xliff"></a>

### Eu posso especificar uma política parcial em uma conexão? (Por exemplo, somente os algoritmos de IKE mas não IPsec)
Não, você deve especificar todos os algoritmos e parâmetros para IKE (modo principal) e IPsec (modo rápido). A especificação de política parcial não é permitida.

<a id="what-are-the-algorithms-and-key-strengths-supported-in-the-custom-policy" class="xliff"></a>

### Quais são os algoritmos e as restrições principais suportadas na política personalizada?
A tabela a seguir lista os algoritmos de criptografia compatíveis e restrições de chave configuráveis pelos clientes. Você deve selecionar uma opção para cada campo.

| **IPsec/IKEv2**  | **Opções**                                                                 |
| ---              | ---                                                                         |
| Criptografia IKEv2 | AES256, AES192, AES128, DES3, DES                                           |
| Integridade do IKEv2  | SHA384, SHA256, SHA1, MD5                                                   |
| Grupo DH         | ECP384, ECP256, DHGroup24, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, nenhum |
| Criptografia IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, nenhum    |
| Integridade do IPsec  | GCMAES256, GCMAES192, GCMAES128, SHA256, SHA1, MD5                          |
| Grupo PFS        | ECP384, ECP256, PFS24, PFS2048, PFS14, PFS2, PFS1, nenhum                     |
| Tempo de vida da QM SA*  | Segundos (número inteiro; **mínimo 300**) e KBytes (número inteiro; **min. 1024**)                                      |
| Seletor de tráfego | UsePolicyBasedTrafficSelectors** ($True/$False; padrão $False)                             |
|                  |                                                                             |

* (*) O tempo de vida da SA de modo principal IKEv2 é fixo em 28.800 segundos nos gateways de VPN do Azure
* (**) Confira o próximo item de Perguntas frequentes para "UsePolicyBasedTrafficSelectors"

<a id="does-everything-need-to-match-between-the-azure-vpn-gateway-policy-and-my-on-premises-vpn-device-configurations" class="xliff"></a>

### As configurações do dispositivo VPN local e a política do gateway de VPN do Azure devem corresponder em todos os aspectos?
A configuração do dispositivo VPN local deve corresponder ou conter os seguintes algoritmos e parâmetros que você especifica na política de IPsec/IKE do Azure:

* Algoritmo de criptografia IKE
* Algoritmo de integridade de IKE
* Grupo DH
* Algoritmo de criptografia IPsec
* Algoritmo de integridade de IPsec
* Grupo PFS
* Seletor de tráfego (*)

Os tempos de vida da SA são apenas especificações locais, portanto não precisam ser correspondentes.

Caso habilite **UsePolicyBasedTrafficSelectors**, você precisa garantir que o seu dispositivo VPN tem os seletores de tráfego correspondentes definidos com todas as combinações de prefixos de rede local (gateway de rede local) de/para prefixos de rede virtual 'do Azure, em vez de "qualquer para qualquer". Por exemplo, se os prefixos da rede local são 10.1.0.0/16 e 10.2.0.0/16, e os prefixos da rede virtual são 192.168.0.0/16 e 172.16.0.0/16, você precisa especificar os seguintes seletores de tráfego:
* 10.1.0.0/16 <====> 192.168.0.0/16
* 10.1.0.0/16 <====> 172.16.0.0/16
* 10.2.0.0/16 <====> 192.168.0.0/16
* 10.2.0.0/16 <====> 172.16.0.0/16

Consulte [Como conectar dispositivos VPN baseados em várias políticas locais](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) para obter mais detalhes sobre como usar essa opção.

<a id="does-the-custom-policy-replace-the-default-ipsecike-policy-sets-for-azure-vpn-gateways" class="xliff"></a>

### A política personalizada substitui os conjuntos de políticas de IPsec/IKE padrão para gateways de VPN do Azure?
Sim, depois que uma política personalizada for especificada em uma conexão, o gateway de VPN do Azure usará a política na conexão, como iniciador do IKE e respondente do IKE.

<a id="if-i-remove-a-custom-ipsecike-policy-does-the-connection-become-unprotected" class="xliff"></a>

### Se eu remover uma política de IPsec/IKE personalizada, a conexão ficará desprotegida?
Não, a conexão ainda estará protegida por IPsec/IKE. Após você remover a política personalizada de uma conexão, o gateway de VPN do Azure reverterá para a [lista padrão de propostas de IPsec/IKE](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md) e reiniciará o handshake do IKE novamente com o dispositivo VPN local.

<a id="would-adding-or-updating-an-ipsecike-policy-disrupt-my-vpn-connection" class="xliff"></a>

### Adicionar ou atualizar uma política de IPsec/IKE pode atrapalhar minha conexão VPN?
Sim, isso pode causar uma interrupção pequena (alguns segundos) uma vez que o gateway de VPN do Azure subdividirá a conexão existente e reiniciará o handshake do IKE para restabelecer o túnel IPsec com os algoritmos de criptografia e os parâmetros novos. Certifique-se de que o dispositivo VPN local também esteja configurado com os algoritmos correspondentes e as restrições de chave para minimizar a interrupção.

<a id="can-i-use-different-policies-on-different-connections" class="xliff"></a>

### Eu posso usar políticas diferentes em conexões diferentes?
Sim. A política personalizada é aplicada em uma base por conexão. Você pode criar e aplicar políticas de IPsec/IKE diferentes em conexões diferentes. Também é possível aplicar políticas personalizadas em um subconjunto de conexões. As restantes usarão os conjuntos de políticas de IPsec/IKE padrão do Azure.

<a id="can-i-use-the-custom-policy-on-vnet-to-vnet-connection-as-well" class="xliff"></a>

### Eu também posso usar a política personalizada na conexão de VNet para VNet?
Sim, você pode aplicar a política personalizada em conexões entre locais de IPsec ou conexões de VNet para VNet.

<a id="do-i-need-to-specify-the-same-policy-on-both-vnet-to-vnet-connection-resources" class="xliff"></a>

### É necessário especificar a mesma política em ambos os recursos de conexão de VNet para VNet?
Sim. Um túnel de VNet para VNet consiste em dois recursos de conexão no Azure, uma para cada sentido. Você precisa garantir que ambos os recursos de conexão tenham a mesma política, caso contrário a conexão de VNet para VNet não se estabelecerá.

<a id="does-custom-ipsecike-policy-work-on-expressroute-connection" class="xliff"></a>

### A política de IPsec/IKE personalizada funciona em conexão de ExpressRoute?
Não. A política IPsec/IKE só funciona em conexões VNet para VNet e VPN S2S por meio de gateways de VPN do Azure.
