Ao criar um gateway de rede virtual, você precisa especificar o SKU do gateway que você deseja usar. Quando você seleciona um SKU de gateway maior, mais CPUs e largura de banda da rede são alocados para o gateway e como resultado, o gateway pode dar suporte a uma taxa de transferência de rede mais alta para a rede virtual.

O Gateway de VPN pode usar os SKUs a seguir:

- Basic
- Standard
- HighPerformance

Ao selecionar uma SKU, considere as seguintes limitações:

- Se quiser usar um tipo PolicyBased VPN, você deverá usar a SKU de gateway Básica. VPNs PolicyBased (anteriormente chamadas de Roteamento Estático) não têm suporte em qualquer outro tipo de SKU.
- BGP não tem suporte na SKU Básica.
- Configurações de coexistência de ExpressRoute-Gateway de VPN não têm suporte na SKU Básica.


<!--HONumber=Oct16_HO2-->


