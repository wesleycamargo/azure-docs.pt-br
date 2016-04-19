<properties 
	pageTitle="Conectar um aplicativo a uma Rede Virtual usando o PowerShell" 
	description="Instruções sobre como se conectar e trabalhar com Redes Virtuais V1 ou V2 usando o PowerShell" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor="cephalin"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/07/2016" 
	ms.author="ccompy"/>

# Conectar seu aplicativo à sua Rede Virtual com o PowerShell #

## Visão geral ##

No Serviço de Aplicativo do Azure, você pode conectar seu aplicativo (Web, móvel ou API) a uma Rede Virtual em sua assinatura. Esse recurso é chamado de Integração VNET. O recurso de Integração VNET não deve ser confundido com o recurso de Ambiente de Serviço de Aplicativo, que permite a execução de uma instância do Serviço de Aplicativo do Azure em sua Rede Virtual.

A Integração VNET tem uma interface de usuário no novo portal e permite a integração com Redes Virtuais V1 ou V2. Se você quiser saber mais sobre o recurso, acesse: Integrar seu aplicativo a uma Rede Virtual do Azure.

Este documento não é sobre como usar a interface de usuário, mas como habilitar a integração usando o PowerShell. Os comandos para as Redes Virtuais V1 são diferentes dos comandos para as Redes Virtuais V2, portanto, há duas seções.

Antes ler este documento, veja se você tem:

1. O SDK mais recente do Azure PowerShell instalado. Você pode instalá-lo usando o Web Platform Installer.
1. Um aplicativo no Serviço de Aplicativo do Azure executando em um SKU Standard ou Premium

## Redes virtuais V1 (Clássica) ##

Este documento aborda os três itens para Redes Virtuais V1

- que estejam conectando seu aplicativo a uma Rede Virtual V1 pré-existente que tenha um gateway e que esteja configurada para ponto a site
- que estejam atualizando as informações da sua integração VNET para seu aplicativo
- que estejam desconectando seu aplicativo de sua Rede Virtual V1

### Conexão de um aplicativo a uma Rede Virtual V1 (Clássica) ###

A conexão de um aplicativo a uma Rede Virtual (VNET) é um processo de três etapas:

1. Declare ao aplicativo Web que ele ingressará em uma Rede Virtual específica. O aplicativo gerará um certificado que será fornecido à Rede Virtual para a conectividade ponto a site
1. Carregue o certificado do aplicativo Web na Rede Virtual e recupere o URI do pacote VPN ponto a site
1. Atualize a conexão de Rede Virtual de aplicativos Web com o URI do pacote ponto a site

As etapas 1) e 3) acima são totalmente programáveis por scripts, mas a etapa 2) exige uma única ação manual no portal, ou acesso para executar ações PUT ou PATCH no ponto de extremidade do ARM da rede virtual (entre em contato com o Suporte do Azure para habilitar esse recurso). Antes de começar, certifique-se de que tenha uma rede virtual clássica com ponto a site já habilitado e com um Gateway criado/implantado. Para criar o gateway e habilitar o ponto a site, você precisa usar o portal conforme descrito aqui: [Criando um gateway de VPN][createvpngateway]

A rede virtual V1 precisa estar na mesma assinatura que seu Plano do Serviço de Aplicativo que contém o aplicativo com o qual você está integrando.

#####Configurar o SDK do Azure PowerShell#####

Abra uma janela do PowerShell e configure sua conta e assinatura do azure usando:

	Login-AzureRmAccount

Esse comando abrirá um prompt para obter suas credenciais do Azure. Depois de entrar, use

	Select-AzureRmSubscription –SubscriptionName [WebAppSubscriptionName]

ou o

	Select-AzureRmSubscription –SubscriptionId [WebAppSubscriptionId]

para selecionar a assinatura que você quer usar. Use a assinatura que contém sua Rede Virtual e o Plano de Serviço de Aplicativo.

#####Variáveis usadas neste documento#####

Para simplificar os comandos a seguir, definiremos uma variável $Configuration do PowerShell com a configuração específica.

Defina uma variável no PowerShell da seguinte forma com os parâmetros indicados:

	$Configuration = @{} 
	$Configuration.WebAppResourceGroup = "[Your web app resource group]"
	$Configuration.WebAppName = "[Your web app name]"
	$Configuration.VnetSubscriptionId = "[Your vnet subscription id]" 
	$Configuration.VnetResourceGroup = "[Your vnet resource group]" 
	$Configuration.VnetName = "[Your vnet name]"
 
O local do aplicativo deve ser o local sem espaços, por exemplo, Oeste dos EUA é westus

	$Configuration.WebAppLocation = "[Your web app Location]" 

O próximo item é onde o certificado deve ser gravado. Deve ser um caminho gravável em seu sistema local. Inclua .cer no final.

	$Configuration.GeneratedCertificatePath = "[C:\Path\To\Certificate.cer]" 

Para ver o que você definiu, digite $Configuration.

	> $Configuration
	
	Name                           Value
	----                           -----
	GeneratedCertificatePath       C:\vnetcert.cer
	VnetSubscriptionId             efc239a4-88f9-2c5e-a9a1-3034c21ad496
	WebAppResourceGroup            vnetdemo-rg
	VnetResourceGroup              testase1-rg
	VnetName                       TestNetwork
	WebAppName                     vnetintdemoapp
	WebAppLocation                 centralus

O restante do documento pressupõe que você criou uma variável como acabamos de descrever.

#####Declarar a Rede Virtual para o aplicativo#####

Use o comando a seguir para dizer ao aplicativo que ele usará essa Rede Virtual específica. Isso fará com que o aplicativo gere os certificados necessários:

	$vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -PropertyObject @{"VnetResourceId" = "/subscriptions/$($Configuration.VnetSubscriptionId)/resourceGroups/$($Configuration.VnetResourceGroup)/providers/Microsoft.ClassicNetwork/virtualNetworks/$($Configuration.VnetName)"} -Location $Configuration.WebAppLocation -ApiVersion 2015-07-01

Se este comando for bem-sucedido, $vnet deverá ter uma variável Properties nele. A variável Properties deve conter uma impressão digital do certificado, bem como os dados do certificado.

#####Carregar o certificado do aplicativo Web na rede Virtual#####

Há uma etapa manual única necessária para cada combinação de Rede Virtual e assinatura. Ou seja, se você estiver conectando aplicativos na assinatura A, na Rede Virtual A, será necessário executar esta etapa apenas uma vez, independentemente de quantos aplicativos você configurar. Se você estiver adicionando um novo aplicativo a outra Rede Virtual, será necessário fazer isso novamente. O motivo disso é que um conjunto de certificados é gerado em um nível de assinatura no Serviço de Aplicativo do Azure, e é gerado uma vez para cada Rede Virtual a qual os aplicativos se conectarão.

Os certificados já terão sido definidos se você seguiu as etapas ou se realizou a integração com a mesma Rede Virtual usando o portal.

A primeira etapa é gerar o arquivo .cer. A segunda etapa é carregar o arquivo .cer em sua Rede Virtual. Para gerar o arquivo .cer da chamada à API na etapa anterior, execute os seguintes comandos.

	$certBytes = [System.Convert]::FromBase64String($vnet.Properties.certBlob)
	[System.IO.File]::WriteAllBytes("$($Configuration.GeneratedCertificatePath)", $certBytes)

O certificado estará no local especificado com $Configuration.GeneratedCertificatePath

Para carregar o certificado manualmente use o novo portal no [Portal do Azure][azureportal] e procure Rede Virtual (clássica) –> Conexões VPN –> Ponto a site –> Gerenciar certificados. Nesse local, carregue seu certificado.

#####Obtenha o pacote de ponto a site#####

A próxima etapa na configuração de uma conexão de Rede Virtual em um aplicativo Web é obter o pacote de ponto a site e fornecê-lo ao seu aplicativo Web.

Salve o modelo a seguir em um arquivo chamado GetNetworkPackageUri.json em algum lugar em seu computador, por exemplo, C:\\Azure\\Templates\\GetNetworkPackageUri.json

	{ 
		"$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#", 
		"contentVersion": "1.0.0.0", 
		"parameters": { 
			"certData": { 
				"type": "string" 
			}, 
			"certThumbprint": { 
				"type": "string" 
			}, 
			"networkName": { 
				"type": "string" 
			} 
		}, 
		"variables": { 
			"legacyVnetName": "[concat('Group ', resourceGroup().name, ' ', parameters('networkName'))]" 
			}, 
			"resources": [ 
			], 
		"outputs" : { 
			"PackageUri" : 
			{ 
			"value" : "[listPackage(resourceId('Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates', parameters('networkName'), 'primary', parameters('certThumbprint')), '2014-06-01').packageUri]", "type" : "string" 
			} 
		} 
	} 


Define os parâmetros de entrada:

	$parameters = @{"certData" = $vnet.Properties.certBlob ; 
	certThumbprint = $vnet.Properties.certThumbprint ; 
	"networkName" = $Configuration.VnetName } 

Chame o script:

	$output = New-AzureRmResourceGroupDeployment -Name unused -ResourceGroupName $Configuration.VnetResourceGroup -TemplateParameterObject $parameters -TemplateFile C:\PATH\TO\GetNetworkPackageUri.json 


A variável $output.Outputs.packageUri conterá agora o URI do pacote a ser fornecido ao seu aplicativo Web.

#####Carregue o pacote de ponto a site em seu aplicativo#####

A etapa final é fornecer o aplicativo com este pacote. Basta execute o próximo comando:

	$vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)/primary" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-07-01 -PropertyObject @{"VnetName" = $Configuration.VnetName ; "VpnPackageUri" = $($output.Outputs.packageUri).Value } -Location $Configuration.WebAppLocation 

Você pode receber uma mensagem pedindo para você confirmar que está substituindo um recurso existente. Permita essa ação.

Após o êxito desse comando, seu aplicativo deve estar conectado à Rede Virtual. Você pode garantir isso acessando o console do aplicativo e digitando o seguinte:

	SET WEBSITE_

Se houver uma variável de ambiente chamada WEBSITE\_VNETNAME com um valor correspondente ao nome da Rede Virtual de destino, todas as configurações terão sido bem-sucedidas.

###Atualização das informações de integração VNET V1 (clássica)###

A atualização ou nova sincronização das informações exige que você repita as etapas executadas durante a criação da integração. Essas etapas são:

1. definir as informações de Configuração
1. declarar a Rede Virtual para o aplicativo
1. obter o pacote de ponto a site
1. carregar o pacote de ponto a site em seu aplicativo

###Desconectar seu aplicativo de uma Rede Virtual V1 (clássica)###

Para desconectar você precisa as informações de Configuração definidas durante a integração VNET. Com essas informações, há um comando necessário para desconectar seu aplicativo de sua Rede Virtual.

	$vnet = Remove-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-07-01

## Redes virtuais V2 (Resource Manager) ##

As Redes Virtuais V2 ou do Resource Manager têm APIs do ARM, e há algumas coisas que podemos fazer para facilitar ainda mais do que com as Redes Virtuais V1. Temos um script que permitirá a você:

- Criar uma Rede Virtual V2 e integrar seu aplicativo a ela
- Criar um gateway e configurar o ponto a site em uma Rede Virtual V2 pré-existente e, em seguida, integrar seu aplicativo a ela
- Integrar seu aplicativo a uma Rede Virtual V2 pré-existente que tenha um gateway e ponto a site habilitados 
- Desconectar seu aplicativo de sua Rede Virtual V2

###Script de integração do Serviço de Aplicativo da Rede Virtual V2###

Copie o script abaixo e salve-o em um arquivo. Se você não quiser usar o script como ele está, sinta-se à vontade para aprender com ele e saber como configurar as coisas com uma Rede Virtual V2.


    function ReadHostWithDefault($message, $default)
    {
    	$result = Read-Host "$message [$default]"
    	if($result -eq "")
	    {
			$result = $default
	    }
		    return $result
    	}
    
	function PromptCustom($title, $optionValues, $optionDescriptions)
	{
	    Write-Host $title
	    Write-Host
	    $a = @()
	    for($i = 0; $i -lt $optionValues.Length; $i++){
		    Write-Host "$($i+1))" $optionDescriptions[$i]
	    }
	    Write-Host
    
	    while($true)
	    {
		    Write-Host "Choose an option: "
		    $option = Read-Host
		    $option = $option -as [int]
		    
		    if($option -ge 1 -and $option -le $optionValues.Length)
		    {
			    return $optionValues[$option-1]
		    }
	    }
    }
    
    function PromptYesNo($title, $message, $default = 0)
    {
	    $yes = New-Object System.Management.Automation.Host.ChoiceDescription "&Yes", ""
	    $no = New-Object System.Management.Automation.Host.ChoiceDescription "&No", ""
	    $options = [System.Management.Automation.Host.ChoiceDescription[]]($yes, $no)
	    $result = $host.ui.PromptForChoice($title, $message, $options, $default)
	    return $result
    }
    
    function CreateVnet($resourceGroupName, $vnetName, $vnetAddressSpace, $vnetGatewayAddressSpace, $location)
    {
	    Write-Host "Creating a new VNET"
	    $gatewaySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $vnetGatewayAddressSpace
	    New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location -AddressPrefix $vnetAddressSpace -Subnet $gatewaySubnet
    }
    
    function CreateVnetGateway($resourceGroupName, $vnetName, $vnetIpName, $location, $vnetIpConfigName, $vnetGatewayName, $certificateData, $vnetPointToSiteAddressSpace)
    {
	    $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName
	    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
	    
	    Write-Host "Creating a public IP address for this VNET"
	    $pip = New-AzureRmPublicIpAddress -Name $vnetIpName -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
	    $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $vnetIpConfigName -Subnet $subnet -PublicIpAddress $pip
	    
	    Write-Host "Adding a root certificate to this VNET"
	    $root = New-AzureRmVpnClientRootCertificate -Name "AppServiceCertificate.cer" -PublicCertData $certificateData
	    
	    Write-Host "Creating Azure VNET Gateway. This may take up to an hour."
	    New-AzureRmVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $resourceGroupName -Location $location -IpConfigurations $ipconf -GatewayType Vpn -VpnType RouteBased -EnableBgp $false -GatewaySku Basic -VpnClientAddressPool $vnetPointToSiteAddressSpace -VpnClientRootCertificates $root
    }
    
    function AddNewVnet($subscriptionId, $webAppResourceGroup, $webAppName)
    {
	    Write-Host "Adding a new Vnet"
	    Write-Host
	    $vnetName = Read-Host "Specify a name for this Virtual Network"
	    
	    $vnetGatewayName="$($vnetName)-gateway"
	    $vnetIpName="$($vnetName)-ip"
	    $vnetIpConfigName="$($vnetName)-ipconf"
	    
	    # Virtual Network settings
	    $vnetAddressSpace="10.0.0.0/8"
	    $vnetGatewayAddressSpace="10.5.0.0/16"
	    $vnetPointToSiteAddressSpace="172.16.0.0/16"
	    
	    $changeRequested = 0
	    $resourceGroupName = $webAppResourceGroup
	    
	    while($changeRequested -eq 0)
	    {
		    Write-Host
		    Write-Host "Currently, I will create a VNET with the following settings:"
		    Write-Host
		    Write-Host "Virtual Network Name: $vnetName"
		    Write-Host "Resource Group Name:  $resourceGroupName"
		    Write-Host "Gateway Name: $vnetGatewayName"
		    Write-Host "Vnet IP name: $vnetIpName"
		    Write-Host "Vnet IP config name:  $vnetIpConfigName"
		    Write-Host "Address Space:$vnetAddressSpace"
		    Write-Host "Gateway Address Space:$vnetGatewayAddressSpace"
		    Write-Host "Point-To-Site Address Space:  $vnetPointToSiteAddressSpace"
		    Write-Host
		    $changeRequested = PromptYesNo "" "Do you wish to change these settings?" 1
		    
		    if($changeRequested -eq 0)
		    {
			    $vnetName = ReadHostWithDefault "Virtual Network Name" $vnetName
			    $resourceGroupName = ReadHostWithDefault "Resource Group Name" $resourceGroupName
			    $vnetGatewayName = ReadHostWithDefault "Vnet Gateway Name" $vnetGatewayName
			    $vnetIpName = ReadHostWithDefault "Vnet IP name" $vnetIpName
			    $vnetIpConfigName = ReadHostWithDefault "Vnet IP configuration name" $vnetIpConfigName
			    $vnetAddressSpace = ReadHostWithDefault "Vnet Address Space" $vnetAddressSpace
			    $vnetGatewayAddressSpace = ReadHostWithDefault "Vnet Gateway Address Space" $vnetGatewayAddressSpace
			    $vnetPointToSiteAddressSpace = ReadHostWithDefault "Vnet Point-to-site Address Space" $vnetPointToSiteAddressSpace
		    }
	    }
    
	    $ErrorActionPreference = "Stop";
	    
	    # We create the virtual network and add it here. The way this works is:
	    # 1) Add the VNET association to the App. This allows the App to generate certificates, etc. for the VNET.
	    # 2) Create the VNET and VNET gateway, add the certificates, create the public IP, etc., required for the gateway
	    # 3) Get the VPN package from the gateway and pass it back to the App.
	    
	    $webApp = Get-AzureRmResource -ResourceName $webAppName -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup
	    $location = $webApp.Location
	    
	    Write-Host "Creating App assocation to VNET"
	    $propertiesObject = @{
	     "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($resourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
	    }
	    $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force
	       
	    CreateVnet $resourceGroupName $vnetName $vnetAddressSpace $vnetGatewayAddressSpace $location
	    
	    CreateVnetGateway $resourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace
	    
	    Write-Host "Retreiving VPN Package and supplying to App"
	    $packageUri = Get-AzureRmVpnClientPackage -ResourceGroupName $resourceGroupName -VirtualNetworkGatewayName $vnetGatewayName -ProcessorArchitecture Amd64
	    
	    # Put the VPN client configuration package onto the App
	    $PropertiesObject = @{
	    "vnetName" = $VirtualNetworkName; "vpnPackageUri" = $packageUri
	    }
	    
	    New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)/primary" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force
	    
	    Write-Host "Finished!"
    }
    
    function AddExistingVnet($subscriptionId, $resourceGroupName, $webAppName)
    {
		$ErrorActionPreference = "Stop";
		
		# At this point, the gateway should be able to be joined to an App, but may require some minor tweaking. We will declare to the App now to use this VNET
		Write-Host "Getting App information"
		$webApp = Get-AzureRmResource -ResourceName $webAppName -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
		$location = $webApp.Location
		   
		$webAppConfig = Get-AzureRmResource -ResourceName "$($webAppName)/web" -ResourceType "Microsoft.Web/sites/config" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
		$currentVnet = $webAppConfig.Properties.VnetName
		if($currentVnet -ne $null -and $currentVnet -ne "")
		{
			Write-Host "Currently connected to VNET $currentVnet"
		}
		
		# Display existing vnets
		$vnets = Get-AzureRmVirtualNetwork
		$vnetNames = @()
		foreach($vnet in $vnets)
		{
			$vnetNames += $vnet.Name
	    }
	    
	    Write-Host
	    $vnet = PromptCustom "Select a VNET to integrate with" $vnets $vnetNames
	    
	    # We need to check if this VNET is able to be joined to a App, based on following criteria
		    # If there is no gateway, we can create one.
		    # If there is a gateway:
			    # It must be of type Vpn
			    # It must be of VpnType RouteBased
			    # If it doesn't have the right certificate, we will need to add it.
			    # If it doesn't have a point-to-site range, we will need to add it.
	       
	    $gatewaySubnet = $vnet.Subnets | Where-Object { $_.Name -eq "GatewaySubnet" }
	       
	    if($gatewaySubnet -eq $null -or $gatewaySubnet.IpConfigurations -eq $null -or $gatewaySubnet.IpConfigurations.Count -eq 0)
	    {
			$ErrorActionPreference = "Continue";
		    # There is no gateway. We need to create one.
		    Write-Host "This Virtual Network has no gateway. I will need to create one."
		    
		    $vnetName = $vnet.Name
		    $vnetGatewayName="$($vnetName)-gateway"
		    $vnetIpName="$($vnetName)-ip"
		    $vnetIpConfigName="$($vnetName)-ipconf"
		    
		    # Virtual Network settings
		    $vnetAddressSpace="10.0.0.0/8"
		    $vnetGatewayAddressSpace="10.5.0.0/16"
		    $vnetPointToSiteAddressSpace="172.16.0.0/16"
		    
		    $changeRequested = 0
		    
		    Write-Host "Your VNET is in the address space $($vnet.AddressSpace.AddressPrefixes), with the following Subnets:"
		    foreach($subnet in $vnet.Subnets)
		    {
			    Write-Host "$($subnet.Name): $($subnet.AddressPrefix)"
		    }
		    
		    $vnetGatewayAddressSpace = Read-Host "Please choose a GatewaySubnet address space"
		    
		    while($changeRequested -eq 0)
		    {
			    Write-Host
			    Write-Host "Currently, I will create a VNET gateway with the following settings:"
			    Write-Host
			    Write-Host "Virtual Network Name: $vnetName"
			    Write-Host "Resource Group Name:  $($vnet.ResourceGroupName)"
			    Write-Host "Gateway Name: $vnetGatewayName"
			    Write-Host "Vnet IP name: $vnetIpName"
			    Write-Host "Vnet IP config name:  $vnetIpConfigName"
			    Write-Host "Address Space:$($vnet.AddressSpace.AddressPrefixes)"
			    Write-Host "Gateway Address Space:$vnetGatewayAddressSpace"
			    Write-Host "Point-To-Site Address Space:  $vnetPointToSiteAddressSpace"
			    Write-Host
			    $changeRequested = PromptYesNo "" "Do you wish to change these settings?" 1
			    
			    if($changeRequested -eq 0)
			    {
				    $vnetGatewayName = ReadHostWithDefault "Vnet Gateway Name" $vnetGatewayName
				    $vnetIpName = ReadHostWithDefault "Vnet IP name" $vnetIpName
				    $vnetIpConfigName = ReadHostWithDefault "Vnet IP configuration name" $vnetIpConfigName
				    $vnetGatewayAddressSpace = ReadHostWithDefault "Vnet Gateway Address Space" $vnetGatewayAddressSpace
				    $vnetPointToSiteAddressSpace = ReadHostWithDefault "Vnet Point-to-site Address Space" $vnetPointToSiteAddressSpace
			    }
		    }
		    
		    $ErrorActionPreference = "Stop";
		    
		    Write-Host "Creating App assocation to VNET"
		    $propertiesObject = @{
		     "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
		    }
		    
		    $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force
		    
		    # If there is no gateway subnet, we need to create one.
		    if($gatewaySubnet -eq $null)
		    {
			    $gatewaySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $vnetGatewayAddressSpace
			    $vnet.Subnets.Add($gatewaySubnet);
			    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
		    }
		    
		    CreateVnetGateway $vnet.ResourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace
		    
		    $gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $vnet.ResourceGroupName -Name $vnetGatewayName
	    }
	    else
	    {
		    $uriParts = $gatewaySubnet.IpConfigurations[0].Id.Split('/')
		    $gatewayResourceGroup = $uriParts[4]
		    $gatewayName = $uriParts[8]
		       
		    $gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $vnet.ResourceGroupName -Name $gatewayName
		    
		    # validate gateway types, etc.
		    if($gateway.GatewayType -ne "Vpn")
		    {
			    Write-Error "This gateway is not of the Vpn type. It cannot be joined to an App."
			    return
		    }
		    
		    if($gateway.VpnType -ne "RouteBased")
		    {
			    Write-Error "This gateways Vpn type is not RouteBased. It cannot be joined to an App."
			    return
		    }
		    
		    if($gateway.VpnClientConfiguration -eq $null -or $gateway.VpnClientConfiguration.VpnClientAddressPool -eq $null)
		    {
			    Write-Host "This gateway does not have a Point-to-site Address Range. Please specify one in CIDR notation, e.g. 10.0.0.0/8"
			    $pointToSiteAddress = Read-Host "Point-To-Site Address Space"
			    Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $gateway.Name -VpnClientAddressPool $pointToSiteAddress
		    }
		    
		    Write-Host "Creating App assocation to VNET"
		    $propertiesObject = @{
		     "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
		    }
		    
		    $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force
		    
		    # We need to check if the certificate here exists in the gateway.
		    $certificates = $gateway.VpnClientConfiguration.VpnClientRootCertificates
		    
		    $certFound = $false
		    foreach($certificate in $certificates)
		    {
			    if($certificate.PublicCertData -eq $virtualNetwork.Properties.CertBlob)
			    {
				    $certFound = $true
				    break
			    }
		    }
		    
		    if(-not $certFound)
		    {
			    Write-Host "Adding certificate"
			    Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName "AppServiceCertificate.cer" -PublicCertData $virtualNetwork.Properties.CertBlob -VirtualNetworkGatewayName $gateway.Name
		    }
	    }
	    
	    # Now finish joining by getting the VPN package and giving it to the App
	    Write-Host "Retreiving VPN Package and supplying to App"
	    $packageUri = Get-AzureRmVpnClientPackage -ResourceGroupName $vnet.ResourceGroupName -VirtualNetworkGatewayName $gateway.Name -ProcessorArchitecture Amd64
	    
	    # Put the VPN client configuration package onto the App
	    $PropertiesObject = @{
	    "vnetName" = $vnet.Name; "vpnPackageUri" = $packageUri
	    }
	    
	    New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)/primary" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force
	    
	    Write-Host "Finished!"
    }
       
    function RemoveVnet($subscriptionId, $resourceGroupName, $webAppName)
    {
	    $webAppConfig = Get-AzureRmResource -ResourceName "$($webAppName)/web" -ResourceType "Microsoft.Web/sites/config" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
	    $currentVnet = $webAppConfig.Properties.VnetName
	    if($currentVnet -ne $null -and $currentVnet -ne "")
	    {
		    Write-Host "Currently connected to VNET $currentVnet"
		    
		    Remove-AzureRmResource -ResourceName "$($webAppName)/$($currentVnet)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
	    }
	  	  else
	    {
	  	  Write-Host "Not connected to a VNET."
	    }
    }
    
    Write-Host "Please Login"
    Login-AzureRmAccount
    
    # Choose subscription. If there's only one we will choose automatically
    
    $subs = Get-AzureRmSubscription
    $subscriptionId = ""
    
    if($subs.Length -eq 0)
    {
	    Write-Error "No subscriptions bound to this account."
	    return
    }
    
    if($subs.Length -eq 1)
    {
	    $subscriptionId = $subs[0].SubscriptionId
    }
    else
    {
	    $subscriptionChoices = @()
	    $subscriptionValues = @()
	       
	    foreach($subscription in $subs){
	    $subscriptionChoices += "$($subscription.SubscriptionName) ($($subscription.SubscriptionId))";
	    $subscriptionValues += ($subscription.SubscriptionId);
	    }
	    
	    $subscriptionId = PromptCustom "Choose a subscription" $subscriptionValues $subscriptionChoices
    }
    
    Select-AzureRmSubscription -SubscriptionId $subscriptionId
    
    $resourceGroup = Read-Host "Please enter the Resource Group of your App"
    
    $appName = Read-Host "Please enter the Name of your App"
    
    $options = @("Add a NEW Virtual Network to an App", "Add an EXISTING Virtual Network to an App", "Remove a Virtual Network from an App");
    $optionValues = @(0, 1, 2)
    $option = PromptCustom "What do you want to do?" $optionValues $options
    
    if($option -eq 0)
    {
		AddNewVnet $subscriptionId $resourceGroup $appName
    }
    if($option -eq 1)
    {
	    AddExistingVnet $subscriptionId $resourceGroup $appName
    }
	if($option -eq 2)
    {
	    RemoveVnet $subscriptionId $resourceGroup $appName
    }
    
Salve uma cópia do script. Neste documento, ele foi chamado de V2VnetAllinOne.ps1, mas você pode chamá-lo do que quiser. Não há nenhum argumento para esse script. Basta executá-lo. A primeira coisa que o script fará é solicitar sua entrada. Após sua entrada, o script obterá os detalhes sobre a sua conta e retornará uma lista de assinaturas para sua escolha. Sem contar o pop-up que solicita suas credenciais, a execução inicial do script tem esta aparência:

	PS C:\Users\ccompy\Documents\VNET> .\V2VnetAllInOne.ps1
	Please Login
	
	Environment           : AzureCloud
	Account               : ccompy@microsoft.com
	TenantId              : 722278f-fef1-499f-91ab-2323d011db47
	SubscriptionId        : af5358e1-acac-2c90-a9eb-722190abf47a
	CurrentStorageAccount :
	
	Choose a subscription
	
	1) Demo Subscription (af5358e1-acac-2c90-a9eb-722190abf47a)
	2) MS Test (a5350f55-dd5a-41ec-2ddb-ff7b911bb2ef)
	3) Purple Demo Subscription (2d4c99a4-57f9-4d5e-a0a1-0034c52db59d)
	
	Choose an option:
	3
	
	Account      : ccompy@microsoft.com
	Environment  : AzureCloud
	Subscription : 2d4c99a4-57f9-4d5e-a0a1-0034c52db59d
	Tenant       : 722278f-fef1-499f-91ab-2323d011db47
	
	Please enter the Resource Group of your App: hcdemo-rg
	Please enter the Name of your App: v2vnetpowershell
	What do you want to do?
	
	1) Add a NEW Virtual Network to an App
	2) Add an EXISTING Virtual Network to an App
	3) Remove a Virtual Network from an App

O restante deste documento detalha cada uma dessas três opções.

###Criar uma rede virtual V2 (Resource Manager) e integrá-la###

Para criar uma nova Rede Virtual V2 e integrá-la ao seu aplicativo, selecione 1) Adicionar uma NOVA Rede Virtual a um Aplicativo. Isso solicitará o nome da Rede Virtual. No meu caso abaixo, dei o nome de v2pshell.

O script fornece os detalhes sobre a Rede Virtual que está sendo criada. Se eu quiser, posso alterar qualquer um dos valores. Neste exemplo de execução, criei uma Rede Virtual com as seguintes configurações:

	Virtual Network Name:         v2pshell
	Resource Group Name:          hcdemo-rg
	Gateway Name:                 v2pshell-gateway
	Vnet IP name:                 v2pshell-ip
	Vnet IP config name:          v2pshell-ipconf
	Address Space:                10.0.0.0/8
	Gateway Address Space:        10.5.0.0/16
	Point-To-Site Address Space:  172.16.0.0/12
	
	Do you wish to change these settings?
	[Y] Yes  [N] No  [?] Help (default is "N"):

Se você quiser alterar algum dos valores, digite Y e faça as alterações desejadas. Quando estiver satisfeito com as configurações da Rede Virtual, digite N ou simplesmente pressione Enter quando receber a solicitação para alterar as configurações. A partir daí até a conclusão, o script informará um pouco do que está fazendo até criar o Gateway de Rede Virtual. Essa etapa pode demorar até uma hora. Não há qualquer indicador de progresso enquanto ele estiver nessa fase, mas ele avisará quando o gateway for criado.

Após a conclusão do script, ele informará **Concluído**. Neste ponto, você terá uma Rede Virtual V2 criada com o nome e as configurações que você selecionou, e essa nova Rede Virtual também estará integrada ao seu aplicativo.

###Integrar seu aplicativo a uma Rede Virtual V2 preexistente###

Ao realizar a integração a uma Rede Virtual pré-existente, se você fornecer uma Rede Virtual V2 que não tenha um gateway ou ponto a site, o script configurará isso. Se a Rede Virtual já tiver isso configurado, passará direto para a integração do aplicativo. Para iniciar esse processo, basta selecionar a opção 2) Adicionar uma Rede Virtual EXISTENTE a um Aplicativo.

Essa opção só funcionará se você tiver uma Rede Virtual V2 pré-existente que esteja na mesma assinatura que seu aplicativo. Após a seleção da opção 2, você receberá uma lista de suas Redes Virtuais V2.

	Select a VNET to integrate with
	
	1) v2demonetwork
	2) v2pshell
	3) v2vnetintdemo
	4) v2asenetwork
	5) v2pshell2
	
	Choose an option:
	5

Basta selecione a Rede Virtual a qual você deseja integrar. Se você já tiver um gateway com ponto a site habilitado, o script simplesmente integrará seu aplicativo à Rede Virtual. Se você não tiver um gateway, será necessário especificar a sub-rede do gateway. A sub-rede do gateway deve estar em seu espaço de endereço de Rede Virtual e não pode estar em qualquer outra sub-rede. Portanto, se você tiver uma rede virtual sem um gateway e executar esta etapa, verá o seguinte:

	This Virtual Network has no gateway. I will need to create one.
	Your VNET is in the address space 172.16.0.0/16, with the following Subnets:
	default: 172.16.0.0/24
	Please choose a GatewaySubnet address space: 172.16.1.0/26

Neste exemplo, eu criei um gateway de Rede Virtual com as seguintes configurações:

	Virtual Network Name:         v2pshell2
	Resource Group Name:          vnetdemo-rg
	Gateway Name:                 v2pshell2-gateway
	Vnet IP name:                 v2pshell2-ip
	Vnet IP config name:          v2pshell2-ipconf
	Address Space:                172.16.0.0/16
	Gateway Address Space:        172.16.1.0/26
	Point-To-Site Address Space:  172.16.0.0/12
	
	Do you wish to change these settings?
	[Y] Yes  [N] No  [?] Help (default is "N"):
	Creating App assocation to VNET

Se você quiser alterar qualquer uma dessas configurações, vá em frente ou pressione Enter e ele criará seu gateway e anexará o aplicativo à sua Rede Virtual. O tempo de criação do gateway ainda é de uma hora, lembre-se disso. Quando tudo estiver concluído, o script informará Concluído.

###Desconectar seu aplicativo de uma Rede Virtual V2###

A desconexão de seu aplicativo de sua Rede Virtual V2 não desativa o gateway ou desabilita o ponto a site. Afinal de contas, você pode usá-los para alguma outra coisa. Também não desconecta de outros aplicativos que não seja aquele que você forneceu. Para executar esta ação, selecione 3) Remover uma Rede Virtual de um Aplicativo. Quando você fizer isso, verá algo assim:

	Currently connected to VNET v2pshell
	
	Confirm
	Are you sure you want to delete the following resource:
	/subscriptions/edcc99a4-b7f9-4b5e-a9a1-3034c51db496/resourceGroups/hcdemo-rg/providers/Microsoft.Web/sites/v2vnetpowers
	hell/virtualNetworkConnections/v2pshell
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

O script diz delete, mas ele não está excluindo a Rede Virtual. Ele está simplesmente removendo a integração. Depois de confirmar que é isso o que você quer fazer, o comando será processado rapidamente e mostrará True quando estiver pronto.

<!--Links-->
[createvpngateway]: http://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/
[azureportal]: http://portal.azure.com

<!---HONumber=AcomDC_0406_2016-->