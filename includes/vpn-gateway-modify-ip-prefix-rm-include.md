### Adicionar ou remover prefixos se você ainda não criou uma conexão de gateway de VPN

- **Para adicionar** outros prefixos de endereço a um gateway de rede local que você criou, mas que ainda não tem uma conexão de gateway de VPN, use o exemplo a seguir.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


- **Para remover** um prefixo de endereço de um gateway de rede local que não tem uma conexão VPN, use o exemplo abaixo. Exclua os prefixos de que você não precisa mais. Neste exemplo, não é mais necessário prefixar 20.0.0.0/24 (do exemplo anterior), portanto, atualizaremos o gateway de rede local e excluiremos o prefixo.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### Adicionar ou remover prefixos se você já tiver criado uma conexão de gateway de VPN

Se tiver criado a conexão VPN e desejar adicionar ou remover os prefixos de endereço IP contidos no site local, você precisará executar as etapas a seguir, nessa ordem. Isso resultará em algum tempo de inatividade para a conexão VPN, pois você precisará remover e recompilar o gateway. No entanto, já que você solicitou um endereço IP para a conexão, você não precisará reconfigurar seu roteador VPN local novamente, a menos que você decida alterar os valores usados anteriormente.
 
1. Remova a conexão de gateway. 
2. Modifique os prefixos para o gateway de rede local. 
3. Crie uma nova conexão de gateway. 

Você pode usar o exemplo a seguir como uma diretriz.

	$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

	Remove-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg

	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
	Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
	
	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

<!---HONumber=AcomDC_0323_2016-->