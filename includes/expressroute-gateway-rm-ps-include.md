As etapas para essa tarefa usam uma Rede Virtual com base nos valores abaixo. Nomes e configurações adicionais também são descritos nesta lista. Não usamos essa lista diretamente em nenhuma uma das etapas, embora adicionemos variáveis com base nos valores contidos nessa lista. É possível fazer uma cópia da lista para usá-la como referência, substituindo os valores pelos seus próprios.

Lista de referência de configuração:

* Nome da Rede Virtual = “TestVNet”
* Espaço de endereço da Rede Virtual = 192.168.0.0/16
* Grupo de recursos = “TestRG”
* Nome de Subnet1 = “FrontEnd” 
* Espaço de endereço de Subnet1 = “192.168.0.0/16”
* Nome da Sub-rede do Gateway: “GatewaySubnet” Deve-se sempre nomear uma sub-rede do gateway como *GatewaySubnet*.
* Espaço de endereço da Sub-Rede do Gateway = “192.168.200.0/26”
* Região = “Leste dos EUA”
* Nome do Gateway = “GW”
* Nome do IP do Gateway = “GWIP”
* Nome da configuração de IP do Gateway = “gwipconf”
* Tipo = “Rota Expressa” Este tipo é necessário para uma configuração de Rota Expressa.
* Nome do IP público do Gateway = “gwpip”

## <a name="add-a-gateway"></a>Adicionar um gateway
1. Conecte-se à sua Assinatura do Azure. 
   
        Login-AzureRmAccount
        Get-AzureRmSubscription 
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"
2. Declare as variáveis para este exercício. Este exemplo usará as variáveis mostradas no exemplo abaixo. Lembre-se de editá-las para que elas reflitam as configurações que deseja usar. 
   
        $RG = "TestRG"
        $Location = "East US"
        $GWName = "GW"
        $GWIPName = "GWIP"
        $GWIPconfName = "gwipconf"
        $VNetName = "TestVNet"
3. Armazene o objeto de rede virtual como uma variável.
   
        $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
4. Adicione uma sub-rede de gateway à sua Rede Virtual. A sub-rede de gateway deve ser nomeada “GatewaySubnet”. Você desejará criar um gateway que seja /27 ou maior (/26, /25, etc.).
   
        Add-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
5. Defina a configuração.
   
            Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
6. Armazene a sub-rede de gateway como uma variável.
   
        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
7. Solicite um endereço IP público. O endereço IP é solicitado antes da criação do gateway. Não é possível especificar o endereço IP que você deseja usar; ele é alocado dinamicamente. Você usará esse endereço IP na próxima seção de configuração. O AllocationMethod deve ser Dynamic.
   
        $pip = New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
8. Crie a configuração de seu gateway. A configuração do gateway define a sub-rede e o endereço IP público a serem usados. Nesta etapa, você especificará a configuração que será usada quando o gateway for criado. Essa etapa não cria, de fato, o objeto de gateway. Use o exemplo a seguir para criar a configuração do gateway. 
   
        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
9. Crie o gateway. Nesta etapa, o **-GatewayType** é especialmente importante. É necessário usar o valor **Rota Expressa**. Observe que, depois de executar esses cmdlets, o gateway pode levar 20 minutos ou mais para ser criado.
   
        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard

## <a name="verify-the-gateway-was-created"></a>Verificar se o gateway foi criado
Use o comando abaixo para verificar se o gateway foi criado.

    Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG

## <a name="resize-a-gateway"></a>Redimensionar um gateway
Há uma série de [SKUs de Gateway](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Você pode usar o comando a seguir para alterar a SKU de gateway a qualquer momento.

> [!IMPORTANT]
> Esse comando não funciona para o gateway UltraPerformance. Para alterar o gateway para um gateway UltraPerformance, primeiro remova o gateway do ExpressRoute existente e crie um novo gateway UltraPerformance. Para fazer downgrade do gateway de um gateway UltraPerformance, primeiro remova o gateway UltraPerformance e crie um novo gateway.
> 
> 

    $gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
    Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance

## <a name="remove-a-gateway"></a>Remover um gateway
Use o comando a seguir para remover um gateway

    Remove-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG  


<!--HONumber=Oct16_HO2-->


