Se você precisar alterar os prefixos para seu site local, use as instruções a seguir. Dois conjuntos de instruções são fornecidos e dependem de você já ter criado ou não a conexão de gateway de VPN.

### Adicionar ou remover prefixos sem uma conexão de gateway de VPN


- **Para adicionar** outros prefixos de endereço a um site local que você criou, mas que ainda não tenha uma conexão de gateway de VPN, use o exemplo a seguir.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


- **Para remover** um prefixo de endereço de um site local que não tem uma conexão VPN, use o exemplo abaixo. Exclua os prefixos de que você não precisa mais. Neste exemplo, não é mais necessário prefixar 20.0.0.0/24 (do exemplo anterior), portanto, iremos atualizar o site local e excluir o prefixo.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### Adicionar ou remover prefixos com uma conexão de gateway de VPN

Se você criou sua conexão VPN e deseja adicionar ou remover os prefixos de endereço IP contidos no site local, você precisará fazer as seguintes etapas, nessa ordem. Isso resultará em algum tempo de inatividade para a conexão VPN, pois você precisará remover e recompilar o gateway. No entanto, já que você solicitou um endereço IP para a conexão, você não precisará reconfigurar seu roteador VPN local novamente, a menos que você decida alterar os valores usados anteriormente.

 
1. Remova a conexão de gateway. 
2. Modifique os prefixos para seu site local. 
3. Crie uma nova conexão de gateway. 

Você pode usar o exemplo a seguir como uma diretriz.


	$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

	Remove-AzureRmVirtualNetworkGatewayConnection -Name vnetgw1 -ResourceGroupName testrg

	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
	Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
	
	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

<!---HONumber=AcomDC_0107_2016-->