Para modificar o endereço IP do gateway, use o cmdlet `New-AzureRmVirtualNetworkGatewayConnection`. Desde que você mantenha o nome do gateway de rede local exatamente como o nome existente, as configurações serão substituídas. Ao mesmo tempo, o cmdlet "Set" não oferece suporte para modificar o endereço IP do gateway.

### <a name="gwipnoconnection"></a>Como modificar o endereço IP do gateway sem uma conexão de gateway
Para atualizar o endereço IP do gateway para seu gateway de rede local que ainda não tem uma conexão, use o exemplo a seguir. Você também pode atualizar os prefixos do endereço ao mesmo tempo. As configurações especificadas substituirão as configurações existentes. Use o nome existente do seu gateway de rede local. Se você não fizer isso, criará um novo gateway de rede local, sem substituir o existente.

Use o exemplo a seguir, substituindo os valores pelos seus.

    New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
    -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
    -GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName


### <a name="gwipwithconnection"></a>Como modificar o endereço IP do gateway com uma conexão de gateway existente
Se uma conexão de gateway já existir, primeiro você precisará remover a conexão. Então, você poderá modificar o endereço IP do gateway e recriar uma nova conexão. Isso resultará em algum tempo de inatividade para a conexão VPN.

> [!IMPORTANT]
> Não exclua o gateway de VPN. Se fizer isso, você precisará refazer as etapas para recriá-lo, bem como reconfigurar seu roteador local com o endereço IP que será atribuído ao gateway recém-criado.
> 
> 

1. Remova a conexão. Você pode encontrar o nome da conexão usando o cmdlet `Get-AzureRmVirtualNetworkGatewayConnection`.
   
        Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
        -ResourceGroupName MyRGName
2. Modifique o valor de GatewayIpAddress. Você também pode modificar os prefixos do endereço no momento, se necessário. Observe que isso substituirá as configurações do gateway de rede local existentes. Use o nome existente do seu gateway de rede local ao modificar para que as configurações sejam substituídas. Se você não fizer isso, criará um novo gateway de rede local, sem modificar o existente.
   
        New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
        -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
        -GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName
3. Crie a conexão. Neste exemplo, estamos configurando um tipo de conexão IPsec. Quando você recriar a conexão, use o tipo de conexão especificado para sua configuração. Para ver outros tipos de conexão, consulte a página [Cmdlet do PowerShell](https://msdn.microsoft.com/library/mt603611.aspx). Para obter o nome do VirtualNetworkGateway, você pode executar o cmdlet `Get-AzureRmVirtualNetworkGateway`.
   
    Defina as variáveis:
   
        $local = Get-AzureRMLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        $vnetgw = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName MyRGName
   
    Crie a conexão:
   
        New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName `
        -Location "West US" `
        -VirtualNetworkGateway1 $vnetgw `
        -LocalNetworkGateway2 $local `
        -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

<!---HONumber=AcomDC_0810_2016-->