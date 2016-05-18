<properties
	pageTitle="Conectar seu aplicativo à rede virtual usando o PowerShell"
	description="Instruções sobre como se conectar a e trabalhar com redes virtuais usando o PowerShell"
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

# Conectar seu aplicativo à rede virtual usando o PowerShell #

## Visão geral ##

No serviço de aplicativo do Azure, você pode conectar seu aplicativo (Web, móvel ou de API) a uma VNet (rede virtual) do Azure em sua assinatura. Esse recurso é chamado de Integração VNet. O recurso de Integração VNet não deve ser confundido com o recurso de Ambiente de Serviço de Aplicativo, que permite a execução de uma instância do Serviço de Aplicativo do Azure em sua rede virtual.

O recurso de Integração VNet tem uma IU (interface de usuário) no novo portal que você pode usar para a integração com redes virtuais que são implantadas usando o modelo de implantação clássico ou o modelo de implantação do Azure Resource Manager. Para saber mais sobre o recurso, confira [Integrar seu aplicativo a uma rede virtual do Azure](web-sites-integrate-with-vnet).

Este artigo não é sobre como usar a interface do usuário, mas sobre como habilitar a integração usando o PowerShell. Como os comandos de cada modelo de implantação são diferentes, este artigo tem uma seção para cada modelo de implantação.

Antes de continuar a ler este artigo, verifique se você tem:

- O SDK mais recente do Azure PowerShell instalado. Você pode instalá-lo usando o Web Platform Installer.
- Um aplicativo no Serviço de Aplicativo do Azure em execução em uma SKU Standard ou Premium.

## Redes virtuais clássicas ##

Esta seção explica as três tarefas para redes virtuais que usam o modelo de implantação clássico:

1. Conecte seu aplicativo a uma rede virtual pré-existente que tenha um gateway e esteja configurada para conectividade ponto a site.
1. Atualize as informações de integração de rede virtual para seu aplicativo.
1. Desconecte o aplicativo da rede virtual.

### Conectar um aplicativo a uma VNet clássica ###

Para conectar um aplicativo a uma rede virtual, siga estas três etapas:

1. Declare ao aplicativo Web que ele ingressará em uma rede virtual específica. O aplicativo gerará um certificado que será fornecido à rede virtual para a conectividade ponto a site.
1. Carregue o certificado do aplicativo Web na rede virtual e recupere o URI do pacote VPN ponto a site
1. Atualize a conexão de rede virtual de aplicativos Web com o URI do pacote ponto a site

A primeira e a terceira etapas são totalmente programáveis por scripts, mas a segunda etapa requer uma ação manual única por meio do portal ou acesso para executar ações **PUT** ou **PATCH** no ponto de extremidade do Azure Resource Manager da rede virtual. Entre em contato com o Suporte do Azure para habilitar esse recurso. Antes de começar, verifique se você tem uma rede virtual clássica que tenha a conectividade ponto a site já habilitada e um gateway implantado. Para criar o gateway e habilitar a conectividade ponto a site, você precisa usar o portal, conforme descrito em [Criação de um gateway de VPN][createvpngateway].

A rede virtual clássica precisa estar na mesma assinatura que o plano do Serviço de Aplicativo que contém o aplicativo ao qual você está fazendo a integração.

##### Configurar o SDK do Azure PowerShell #####

Abra uma janela do PowerShell e configure sua conta e assinatura do Azure usando:

	Login-AzureRmAccount

Esse comando abrirá um prompt para obter suas credenciais do Azure. Depois de entrar, use um dos comandos a seguir para escolher a assinatura que você deseja usar. Use a assinatura que contém sua rede virtual e o plano do Serviço de Aplicativo.

	Select-AzureRmSubscription –SubscriptionName [WebAppSubscriptionName]

ou o

	Select-AzureRmSubscription –SubscriptionId [WebAppSubscriptionId]

##### Variáveis usadas neste artigo #####

Para simplificar os comandos, definiremos uma variável **$Configuration** do PowerShell com a configuração específica.

Defina uma variável no PowerShell da seguinte forma com os parâmetros indicados:

	$Configuration = @{}
	$Configuration.WebAppResourceGroup = "[Your web app resource group]"
	$Configuration.WebAppName = "[Your web app name]"
	$Configuration.VnetSubscriptionId = "[Your vnet subscription id]"
	$Configuration.VnetResourceGroup = "[Your vnet resource group]"
	$Configuration.VnetName = "[Your vnet name]"

O local do aplicativo deve ser o local sem espaços. Por exemplo, Oeste dos EUA é westus.

	$Configuration.WebAppLocation = "[Your web app Location]"

O próximo item é onde o certificado deve ser gravado. Deve ser um caminho gravável no computador local. Inclua .cer no final.

	$Configuration.GeneratedCertificatePath = "[C:\Path\To\Certificate.cer]"

Para ver o que você definiu, digite **$Configuration**.

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

O restante desta seção pressupõe que você tenha criado uma variável como acabamos de descrever.

##### Declarar a rede virtual para o aplicativo #####

Use o comando a seguir para informar ao aplicativo que ele usará essa rede virtual específica. Isso fará com que o aplicativo gere os certificados necessários:

	$vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -PropertyObject @{"VnetResourceId" = "/subscriptions/$($Configuration.VnetSubscriptionId)/resourceGroups/$($Configuration.VnetResourceGroup)/providers/Microsoft.ClassicNetwork/virtualNetworks/$($Configuration.VnetName)"} -Location $Configuration.WebAppLocation -ApiVersion 2015-07-01

Se este comando for bem-sucedido, **$vnet** deverá conter uma variável **Properties**. A variável **Properties** deve conter uma impressão digital do certificado, bem como os dados do certificado.

##### Carregar o certificado do aplicativo Web para a rede virtual #####

Uma única etapa manual é necessária para cada combinação de assinatura e rede virtual. Ou seja, se estiver conectando aplicativos na Assinatura A à Rede Virtual A, você precisará executar essa etapa apenas uma vez, independentemente de quantos aplicativos configurar. Se estiver adicionando um novo aplicativo a outra rede virtual, isso precisará ser refeito. O motivo é que um conjunto de certificados é gerado em um nível de assinatura no Serviço de Aplicativo do Azure, e o conjunto é gerado uma vez para cada rede virtual à qual os aplicativos se conectarão.

Os certificados já terão sido definidos se você tiver seguido as etapas ou se tiver feito a integração com a mesma rede virtual usando o portal.

A primeira etapa é gerar o arquivo .cer. A segunda etapa é carregar o arquivo .cer para a rede virtual. Para gerar o arquivo .cer da chamada à API na etapa anterior, execute os seguintes comandos.

	$certBytes = [System.Convert]::FromBase64String($vnet.Properties.certBlob)
	[System.IO.File]::WriteAllBytes("$($Configuration.GeneratedCertificatePath)", $certBytes)

O certificado estará no local que **$Configuration.GeneratedCertificatePath** especifica.

Para carregar o certificado manualmente, use o [portal do Azure][azureportal] e **Procurar Rede Virtual (clássica)** > **Conexões VPN** > **Ponto a site** > **Gerenciar certificados**. Nesse local, carregue seu certificado.

##### Obter o pacote de ponto a site #####

A próxima etapa na configuração de uma conexão de rede virtual em um aplicativo Web é obter o pacote de ponto a site e fornecê-lo ao aplicativo Web.

Salve o modelo a seguir em um arquivo chamado GetNetworkPackageUri.json em algum lugar no computador; por exemplo, C:\\Azure\\Templates\\GetNetworkPackageUri.json.

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


Defina os parâmetros de entrada:

	$parameters = @{"certData" = $vnet.Properties.certBlob ;
	certThumbprint = $vnet.Properties.certThumbprint ;
	"networkName" = $Configuration.VnetName }

Chame o script:

	$output = New-AzureRmResourceGroupDeployment -Name unused -ResourceGroupName $Configuration.VnetResourceGroup -TemplateParameterObject $parameters -TemplateFile C:\PATH\TO\GetNetworkPackageUri.json


A variável **$output.Outputs.packageUri** conterá agora o URI do pacote a ser fornecido ao aplicativo Web.

##### Carregue o pacote de ponto a site em seu aplicativo #####

A etapa final é fornecer o aplicativo com este pacote. Basta executar o próximo comando:

	$vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)/primary" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-07-01 -PropertyObject @{"VnetName" = $Configuration.VnetName ; "VpnPackageUri" = $($output.Outputs.packageUri).Value } -Location $Configuration.WebAppLocation

Se uma mensagem solicitar a confirmação de que você está substituindo um recurso existente, permita essa ação.

Depois que o comando for bem-sucedido, o aplicativo deverá estar conectado à rede virtual. Para confirmar o êxito, vá para o console do aplicativo e digite o seguinte:

	SET WEBSITE_

Se houver uma variável de ambiente chamada WEBSITE\_VNETNAME com um valor que corresponda ao nome da rede virtual de destino, isso indicará que todas as configurações tiveram êxito.

### Atualizar informações de integração VNet clássica ###

Para atualizar ou ressincronizar suas informações, basta repetir as etapas seguidas ao criar a integração. Essas etapas são:

1. Defina as informações de configuração.
1. Declare a rede virtual para o aplicativo.
1. Obtenha o pacote de ponto a site.
1. Carregue o pacote de ponto a site no aplicativo

### Desconectar o aplicativo de uma VNet clássica ###

Para desconectar o aplicativo, você precisa das informações de configuração definidas durante a integração da rede virtual. Com essas informações, há um comando para desconectar o aplicativo da rede virtual.

	$vnet = Remove-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-07-01

## Redes virtuais do Gerenciador de Recursos ##

As redes virtuais do Resource Manager têm APIs do Azure Resource Manager que simplificam alguns processos se comparadas com redes virtuais clássicas. Há um script que o ajudará a concluir as seguintes tarefas:

- Criar uma rede virtual do Gerenciador de Recursos e integrar o aplicativo a ela.
- Criar um gateway, configurar a conectividade ponto a site em uma rede virtual do Gerenciador de Recursos pré-existente e integrar o aplicativo a ela.
- Integrar o aplicativo a uma rede virtual do Gerenciador de Recursos pré-existente que tem um gateway e habilitar a conectividade ponto a site.
- Desconecte o aplicativo da rede virtual.

### Script de integração do Serviço de Aplicativo de VNet do Gerenciador de Recursos ###

Copie o script a seguir e salve-o em um arquivo. Se não quiser usar o script, sinta-se à vontade para aprender com ele e saber como configurar itens com uma rede virtual do Gerenciador de Recursos.


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

	    Write-Host "Creating App association to VNET"
	    $propertiesObject = @{
	     "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($resourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
	    }
	    $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force

	    CreateVnet $resourceGroupName $vnetName $vnetAddressSpace $vnetGatewayAddressSpace $location

	    CreateVnetGateway $resourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace

	    Write-Host "Retrieving VPN Package and supplying to App"
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

		    Write-Host "Creating App association to VNET"
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

		    Write-Host "Creating App association to VNET"
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
	    Write-Host "Retrieving VPN Package and supplying to App"
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

Salve uma cópia do script. Neste artigo, ele é chamado de V2VnetAllinOne.ps1, mas você pode usar outro nome. Não há nenhum argumento para esse script. Basta executá-lo. A primeira coisa que o script fará é solicitar sua entrada. Após sua entrada, o script obterá os detalhes sobre sua conta e retornará uma lista de assinaturas. Sem contar a solicitação de suas credenciais, a execução inicial do script tem esta aparência:

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

O restante desta seção explica cada uma dessas três opções.

### Criar uma VNet do Gerenciador de Recursos e realizar a integração a ela ###

Para criar uma nova rede virtual que usa o modelo de implantação do Gerenciador de Recursos e integrá-la a seu aplicativo, escolha **1) Adicionar uma NOVA Rede Virtual a um Aplicativo**. Isso solicitará o nome da rede virtual. No meu caso, como você pode ver nas configurações a seguir, usei o nome v2pshell.

O script fornece os detalhes sobre a rede virtual que está sendo criada. Se eu quiser, posso alterar qualquer um dos valores. No exemplo em execução, criei uma rede virtual com as seguintes configurações:

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

Se quiser alterar algum dos valores, digite **Y** e faça as alterações. Quando estiver satisfeito com as configurações de rede virtual, digite **N** ou apenas pressione Enter quando receber a solicitação para alterar as configurações. Desse ponto até a conclusão, o script informará um pouco do que está fazendo até começar a criar o gateway de rede virtual. Essa etapa pode demorar até uma hora. Não há um indicador de progresso durante essa fase, mas o script informará quando o gateway for criado.

Após a conclusão do script, ele informará que foi **Concluído**. Neste ponto, você terá uma rede virtual do Gerenciador de Recursos que tem o nome e as configurações que você selecionou. Essa nova rede virtual também será integrada ao aplicativo.

### Integrar seu aplicativo a uma VNet pré-existente do Gerenciador de Recursos ###

Ao fazer a integração a uma rede virtual pré-existente, se você fornecer uma rede virtual do Gerenciador de Recursos que não tenha um gateway ou a conectividade ponto a site, o script fará a configuração. Se a VNET já tiver isso configurado, o script irá direto para a integração do aplicativo. Para iniciar esse processo, basta selecionar **2) Adicionar uma Rede Virtual EXISTENTE a um Aplicativo**.

Essa opção só funcionará se você tiver uma rede virtual pré-existente do Gerenciador de Recursos na mesma assinatura do aplicativo. Após escolher a opção, você verá uma lista de redes virtuais do Gerenciador de Recursos.

	Select a VNET to integrate with

	1) v2demonetwork
	2) v2pshell
	3) v2vnetintdemo
	4) v2asenetwork
	5) v2pshell2

	Choose an option:
	5

Basta selecionar a rede virtual com a qual você deseja fazer a integração. Se você já tiver um gateway com a conectividade ponto a site habilitada, o script simplesmente integrará seu aplicativo à rede virtual. Se não tiver um gateway, você precisará especificar a sub-rede de gateway. A sub-rede do gateway deve estar em seu espaço de endereço de Rede Virtual e não pode estar em outra sub-rede. se você tiver uma rede virtual sem um gateway e executar essa etapa, verá o seguinte:

	This Virtual Network has no gateway. I will need to create one.
	Your VNET is in the address space 172.16.0.0/16, with the following Subnets:
	default: 172.16.0.0/24
	Please choose a GatewaySubnet address space: 172.16.1.0/26

Neste exemplo, criei um gateway de rede virtual que tem as seguintes configurações:

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
	Creating App association to VNET

Se quiser, você pode alterar as configurações. Caso contrário, pressione Enter e o script criará o gateway e anexará o aplicativo à rede virtual. O tempo de criação do gateway ainda é de uma hora; portanto, lembre-se disso. Quando tudo estiver concluído, o script informará que foi **Concluído**.

### Desconectar o aplicativo de uma VNet do Gerenciador de Recursos ###

A desconexão de seu aplicativo da rede virtual não desativa o gateway nem desabilita a conectividade ponto a site. Afinal de contas, você poderia usá-los para alguma outra coisa. Também não o desconecta de outros aplicativos além daquele que você forneceu. Para executar essa ação, selecione **3) Remover uma Rede Virtual de um Aplicativo**. Quando você fizer isso, verá algo assim:

	Currently connected to VNET v2pshell

	Confirm
	Are you sure you want to delete the following resource:
	/subscriptions/edcc99a4-b7f9-4b5e-a9a1-3034c51db496/resourceGroups/hcdemo-rg/providers/Microsoft.Web/sites/v2vnetpowers
	hell/virtualNetworkConnections/v2pshell
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Embora o script indique a exclusão, ele não exclui a rede virtual. Ele está simplesmente removendo a integração. Após confirmar que é isso o que você quer fazer, o comando será processado rapidamente e mostrará **True** quando for concluído.

<!--Links-->
[createvpngateway]: http://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/
[azureportal]: http://portal.azure.com

<!---HONumber=AcomDC_0420_2016-->