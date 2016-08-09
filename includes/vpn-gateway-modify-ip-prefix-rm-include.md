### <a name="noconnection"></a>Como adicionar ou remover prefixos sem uma conexão de gateway de VPN

- **Para adicionar** outros prefixos de endereço a um gateway de rede local que você criou, mas que ainda não tem uma conexão de gateway de VPN, use o exemplo a seguir.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


- **Para remover** um prefixo de endereço de um gateway de rede local que não tem uma conexão VPN, use o exemplo abaixo. Exclua os prefixos de que você não precisa mais. Neste exemplo, não é mais necessário prefixar 20.0.0.0/24 (do exemplo anterior), portanto, atualizaremos o gateway de rede local e excluiremos o prefixo.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### <a name="withconnection"></a>Como adicionar ou remover prefixos com uma conexão de gateway de VPN

Se tiver criado a conexão VPN e desejar adicionar ou remover os prefixos de endereço IP contidos no site local, você precisará executar as etapas a seguir, nessa ordem. Isso resultará em algum tempo de inatividade para a conexão VPN. Ao atualizar os prefixos, primeiro você removerá a conexão, modificará os prefixos e, depois, criará uma nova conexão.

>[AZURE.IMPORTANT] Não exclua o gateway de VPN. Se fizer isso, você precisará refazer as etapas para recriá-lo, bem como reconfigurar seu roteador local com as novas configurações.
 
1. Especifique as variáveis.

		$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

2. Remova a conexão.

		Remove-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg

3. Modifique os prefixos.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
		-AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')

4. Crie a conexão. Neste exemplo, estamos configurando um tipo de conexão IPsec. Para outros tipos de conexão, consulte a página [Cmdlet do PowerShell](https://msdn.microsoft.com/library/mt603611.aspx).
	
		New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
		-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec `
		-RoutingWeight 10 -SharedKey 'abc123'

<!---HONumber=AcomDC_0803_2016-->