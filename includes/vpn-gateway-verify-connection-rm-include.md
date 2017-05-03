### <a name="to-verify-your-connection-by-using-powershell"></a>Para verificar sua conexão usando o PowerShell

Você pode verificar se a conexão foi bem-sucedida usando o cmdlet ‘Get-AzureRmVirtualNetworkGatewayConnection’, com ou sem ‘-Debug’. 

1. Use o seguinte exemplo de cmdlet, configurando os valores para coincidirem com os seus. Quando solicitado, selecione ‘A’ para executar ‘Todos’. No exemplo, ‘-Name’ refere-se ao nome da conexão que você criou e deseja testar.

  ```powershell
  Get-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName MyRG
  ```
2. Após o cmdlet ter sido concluído, exiba os valores. No exemplo abaixo, o status da conexão é exibido como ‘Conectado’ e é possível ver os bytes de entrada e saída.

  ```
  "connectionType": "IPsec",
  "routingWeight": 10,
  "sharedKey": "abc123",
  "connectionStatus": "Connected",
  "ingressBytesTransferred": 33509044,
  "egressBytesTransferred": 4142431
  ```

### <a name="to-verify-your-connection-by-using-the-azure-portal"></a>Para verificar sua conexão usando o portal do Azure

No portal do Azure, você pode exibir o status da conexão navegando para a conexão. Há várias maneiras de fazer isso. As etapas a seguir mostram uma maneira de navegar para a conexão e verificar.

1. No [portal do Azure](http://portal.azure.com), clique em **Todos os recursos** e navegue até o gateway de rede virtual.
2. Na folha do gateway de rede virtual, clique em **Conexões**. Você pode ver o status de cada conexão.
3. Clique no nome da conexão que você deseja verificar para abrir **Essentials**. No Essentials, você pode exibir mais informações sobre a conexão. O **Status** será 'Êxito' e 'Conectado' quando você tiver feito uma conexão bem-sucedida.
   
    ![Verificar a conexão](./media/vpn-gateway-verify-connection-rm-include/connectionsucceeded.png)