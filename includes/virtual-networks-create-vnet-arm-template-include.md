## Como criar uma VNet usando o PowerShell

Para criar uma VNet usando o PowerShell, siga as etapas abaixo.

1. Se você nunca tiver usado o Azure PowerShell, consulte [Como instalar e configurar o Azure PowerShell](powershell-install-configure.md) e siga as instruções até o final para entrar no Azure e selecionar sua assinatura.
2. Em um prompt do Azure PowerShell, execute o cmdlet **Switch-AzureMode** para alternar para modo Gerenciador de Recursos, conforme mostrado abaixo.

	Switch-AzureMode AzureResourceManager

	AVISO: O cmdlet Switch-AzureMode foi preterido e será removido em uma versão futura.

	>[AZURE.WARNING]O cmdlet Switch-AzureMode será preterido em breve. Quando isso acontecer, todos os cmdlets do Gerenciador de Recursos serão renomeados.
	
3. Se necessário, execute o cmdlet **New-AzureResourceGroup** para criar um novo grupo de recursos, conforme mostrado abaixo. Para nosso cenário, crie um grupo de recursos denominado *RG1*.

	New-AzureResourceGroup -Name RG1 -Location centralus

	ResourceGroupName : RG1 Location : centralus ProvisioningState : Succeeded Tags : Permissions : Actions NotActions ======= ========== *
	
	ResourceId : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1

4. Execute o cmdlet **New-AzureVirtualNetwork** para criar uma nova VNet, conforme mostrado abaixo.

	New-AzureVirtualNetwork -ResourceGroupName RG1 -Name TestVNet ` -AddressPrefix 192.168.0.0/16 -Location centralus
	
	Nome: TestVNet ResourceGroupName : RG1 Location : centralus Id : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworks/TestVNet Etag : W/"5b89894f-db7f-4634-9870-f9b97e209510" ProvisioningState : Succeeded Tags : AddressSpace : { "AddressPrefixes": [ "192.168.0.0/16" ] } DhcpOptions : { "DnsServers": null } NetworkInterfaces : null Subnets :

5. Execute o cmdlet **Get-AzureVirtualNetwork** para armazenar o objeto de rede virtual em uma variável, conforme mostrado abaixo.

	$vnet = Get-AzureVirtualNetwork -ResourceGroupName RG1 -Name TestVNet

	>[AZURE.TIP]Você pode combinar as etapas 4 e 5 executando **$vnet = New-AzureVirtualNetwork -ResourceGroupName RG1 -Name TestVNet -AddressPrefix 192.168.0.0/16 -Location centralus**.

6. Execute o cmdlet **Add-AzureVirtualNetworkSubnetConfig** para adicionar uma sub-rede à nova Vnet, como mostrado abaixo.

	Add-AzureVirtualNetworkSubnetConfig -Name FrontEnd ` -VirtualNetwork $vnet -AddressPrefix 192.168.1.0/24
	
	Nome: TestVNet ResourceGroupName : RG1 Location : centralus Id : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworks/TestVNet Etag : W/"5b89894f-db7f-4634-9870-f9b97e209510" ProvisioningState : Succeeded Tags : AddressSpace : { "AddressPrefixes": [ "192.168.0.0/16" ] } DhcpOptions : { "DnsServers": null } NetworkInterfaces : null Subnets : [ { "Name": "FrontEnd", "Etag": null, "Id": null, "AddressPrefix": "192.168.1.0/24", "IpConfigurations": null, "NetworkSecurityGroup": null, "RouteTable": null, "ProvisioningState": null } ]

7. Repita a etapa 6 acima para cada sub-rede que você deseja criar. O comando a seguir cria a sub-rede *BackEnd* para nosso cenário.

	Add-AzureVirtualNetworkSubnetConfig -Name BackEnd ` -VirtualNetwork $vnet -AddressPrefix 192.168.2.0/24

8. Embora você crie sub-redes, elas atualmente só existem na variável local usada para recuperar a VNet criada na etapa 4 acima. Para salvar as alterações no Azure, execute o cmdlet **Set-AzureVirtualNetwork** como mostrado abaixo.

	Set-AzureVirtualNetwork -VirtualNetwork $vnet
	
	Name : TestVNet ResourceGroupName : RG1 Location : centralus Id : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworks/TestVNet Etag : W/"2d3496d8-2b85-4238-bde2-377fe660aa4a" ProvisioningState : Succeeded Tags : AddressSpace : { "AddressPrefixes": [ "192.168.0.0/16" ] } DhcpOptions : { "DnsServers": } NetworkInterfaces : null Subnets : [ { "Name": "FrontEnd", "Etag": "W/"2d3496d8-2b85-4238-bde2-377fe660aa4a"", "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1/providers/Micro soft.Network/virtualNetworks/TestVNet/subnets/FrontEnd", "AddressPrefix": "192.168.1.0/24", "IpConfigurations": , "NetworkSecurityGroup": null, "RouteTable": null, "ProvisioningState": "Succeeded" }, { "Name": "BackEnd", "Etag": "W/"2d3496d8-2b85-4238-bde2-377fe660aa4a"", "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1/providers/Micro soft.Network/virtualNetworks/TestVNet/subnets/BackEnd", "AddressPrefix": "192.168.2.0/24", "IpConfigurations": , "NetworkSecurityGroup": null, "RouteTable": null, "ProvisioningState": "Succeeded" } ]

<!---HONumber=August15_HO8-->