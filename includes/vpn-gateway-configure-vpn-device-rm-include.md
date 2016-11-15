
Para configurar seu dispositivo VPN, você precisará do endereço IP público do gateway de rede virtual para configurar seu dispositivo VPN local. Trabalhe com o fabricante do dispositivo para obter informações específicas de configuração e configurar seu dispositivo. Confira [Dispositivos VPN](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md) para saber mais sobre dispositivos VPN que funcionam bem com o Azure.

Para localizar o endereço IP público do seu gateway de rede virtual usando o PowerShell, use o exemplo a seguir:

    Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG

Você também pode exibir o endereço IP público do seu gateway de rede virtual usando o Portal do Azure. Navegue até **Gateways de Rede Virtual**, em seguida, clique no nome do seu gateway.



<!--HONumber=Nov16_HO2-->


