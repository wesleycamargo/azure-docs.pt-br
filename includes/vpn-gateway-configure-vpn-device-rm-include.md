As conexões Site a Site para uma rede local exigem um dispositivo VPN. Há muitos dispositivos VPN diferentes que funcionam com o Azure. Para saber mais sobre dispositivos VPN e as definições de configuração, confira [Dispositivos VPN](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md). Antes de configurar seu dispositivo VPN, verifique se há [Problemas de compatibilidade de dispositivo conhecidos](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#known) em relação ao dispositivo VPN que você deseja usar. Para obter informações específicas de configuração do dispositivo VPN, fale com o fabricante do dispositivo.

Ao configurar seu dispositivo VPN, você precisará dos seguintes itens:

- **O endereço IP público** do seu gateway de rede virtual.

    -  Para localizar o endereço IP público usando o portal do Azure, navegue até **gateways de rede virtual** e clique no nome do seu gateway. 

    - Para localizar o endereço IP público do seu gateway de rede virtual usando o PowerShell, use o exemplo a seguir substituindo os valores pelos seus próprios.

            Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG
- **Uma chave compartilhada**. Essa é a mesma chave compartilhada especificada ao criar a conexão VPN Site a Site. Em nossos exemplos, usamos uma chave compartilhada muito básica. Você deve gerar uma chave mais complexa para uso.




