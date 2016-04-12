### Como verificar sua conexão usando o Portal do Azure

Você pode verificar uma conexão VPN no Portal do Azure navegando até **Gateways de rede virtual** **>** ***clique no nome do gateway*** **>** **Configurações** **>** **Conexões**. Ao selecionar o nome da conexão, você pode exibir mais informações sobre a conexão. No exemplo abaixo, a conexão não está conectada, e assim os dados não fluem.


![Verificar a conexão](./media/vpn-gateway-verify-connection-rm-include/connectionverify450.png)


### Como verificar sua conexão usando o PowerShell

Também é possível verificar se a conexão foi bem-sucedida usando `Get-AzureRmVirtualNetworkGatewayConnection –Debug`. Você pode usar o seguinte exemplo de cmdlet, configurando os valores para que correspondam aos seus próprios. Quando solicitado, selecione *A* para executar Todos.

	Get-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Debug

 Após o cmdlet ter sido concluído, role pelos valores para exibi-los. No exemplo abaixo, o status da conexão é exibido como *Conectado* e é possível ver os bytes de entrada e saída.

	Body:
	{
	  "name": "localtovon",
	  "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/connections/loca
	ltovon",
	  "properties": {
	    "provisioningState": "Succeeded",
	    "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
	    "virtualNetworkGateway1": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworkGa
	teways/vnetgw1"
	    },
	    "localNetworkGateway2": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/localNetworkGate
	ways/LocalSite"
	    },
	    "connectionType": "IPsec",
	    "routingWeight": 10,
	    "sharedKey": "abc123",
	    "connectionStatus": "Connected",
	    "ingressBytesTransferred": 33509044,
	    "egressBytesTransferred": 4142431
	  }

<!---HONumber=AcomDC_0406_2016-->