As conexões Site a Site para uma rede local exigem um dispositivo VPN. Enquanto não fornecemos as etapas de configuração para todos os dispositivos VPN, você pode achar úteis as informações nos links a seguir:

- Para obter informações sobre os dispositivos VPN compatíveis, consulte [Dispositivos VPN](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md). 
- Para obter links para as configurações do dispositivo, consulte [Dispositivos VPN Validados](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable). Os links de configuração do dispositivo são fornecidos em uma base de melhor esforço. Sempre é melhor verificar com o fabricante do dispositivo para obter as últimas informações de configuração.
- Para obter informações sobre a edição dos exemplos de configuração do dispositivo, consulte [Edição de exemplos](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#editing).
- Para obter os parâmetros IPsec/IKE, consulte [Parâmetros](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec).
- Antes de configurar seu dispositivo VPN, verifique se há [Problemas de compatibilidade de dispositivo conhecidos](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#known) em relação ao dispositivo VPN que você deseja usar.

Ao configurar seu dispositivo VPN, você precisará dos seguintes itens:

- Uma chave compartilhada. Essa é a mesma chave compartilhada especificada ao criar a conexão VPN Site a Site. Em nossos exemplos, usamos uma chave compartilhada básica. Recomendamos gerar uma chave mais complexa para uso.

- O endereço IP público do seu gateway de rede virtual. Você pode exibir o endereço IP público usando o portal do Azure, o PowerShell ou a CLI.