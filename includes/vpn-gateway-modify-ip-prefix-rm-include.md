### <a name="a-namenoconnectionahow-to-add-or-remove-prefixes-no-gateway-connection"></a><a name="noconnection"></a>Como adicionar ou remover prefixos sem uma conexão de gateway
* **Para adicionar** outros prefixos de endereço a um gateway de rede local que você criou, mas que ainda não tem uma conexão de gateway, use o exemplo a seguir. Altere os valores para o seu próprio.
  
        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
* **Para remover** um prefixo de endereço de um gateway de rede local que não tem uma conexão VPN, use o exemplo abaixo. Exclua os prefixos de que você não precisa mais. Neste exemplo, não é mais necessário prefixar 20.0.0.0/24 (do exemplo anterior), portanto, atualizaremos o gateway de rede local e excluiremos o prefixo.
  
        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### <a name="a-namewithconnectionahow-to-add-or-remove-prefixes-existing-gateway-connection"></a><a name="withconnection"></a>Como adicionar ou remover prefixos com uma conexão de gateway existente
Se você criou a conexão de gateway e deseja adicionar ou remover os prefixos do endereço IP contidos no gateway de rede local, precisará executar as etapas a seguir nessa ordem. Isso resultará em algum tempo de inatividade para a conexão VPN. Ao atualizar os prefixos, primeiro você removerá a conexão, modificará os prefixos e, depois, criará uma nova conexão. Nos exemplos a seguir, altere os valores para o seu próprio.

> [!IMPORTANT]
> Não exclua o gateway de VPN. Se fizer isso, você precisará refazer as etapas para recriá-lo, bem como reconfigurar seu roteador local com as novas configurações.
> 
> 

1. Remova a conexão.
   
        Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName
2. Modifique os prefixos do endereço para seu gateway de rede local.
   
    Defina a variável para LocalNetworkGateway.
   
        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName
   
    Modifique os prefixos.
   
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
3. Crie a conexão. Neste exemplo, estamos configurando um tipo de conexão IPsec. Quando você recriar a conexão, use o tipo de conexão especificado para sua configuração. Para outros tipos de conexão, consulte a página [Cmdlet do PowerShell](https://msdn.microsoft.com/library/mt603611.aspx) .
   
     Defina a variável para VirtualNetworkGateway.
   
        $gateway1 = Get-AzureRmVirtualNetworkGateway -Name RMGateway  -ResourceGroupName MyRGName
   
    Crie a conexão. Observe que este exemplo usa a variável $local que você definiu na etapa anterior.

        New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
        -ResourceGroupName MyRGName -Location 'West US' `
        -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
        -ConnectionType IPsec `
        -RoutingWeight 10 -SharedKey 'abc123'


<!--HONumber=Nov16_HO2-->


