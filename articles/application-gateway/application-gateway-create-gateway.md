<properties
   pageTitle="Criar, iniciar ou excluir um gateway de aplicativo | Microsoft Azure"
   description="Esta página oferece instruções para criar, configurar, iniciar e excluir um gateway de aplicativo do Azure"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="jdial"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/09/2016"
   ms.author="gwallace"/>

# Criar, iniciar ou excluir um gateway de aplicativo

O Azure Application Gateway é um balanceador de carga de camada 7. Ele fornece o failover e solicitações HTTP de roteamento de desempenho entre diferentes servidores, estejam eles na nuvem ou no local. O Application Gateway tem os seguintes recursos de entrega de aplicativo: balanceamento de carga HTTP, afinidade de sessão baseada em cookie e descarregamento SSL.

> [AZURE.SELECTOR]
- [Portal do Azure](application-gateway-create-gateway-portal.md)
- [PowerShell do Azure Resource Manager](application-gateway-create-gateway-arm.md)
- [Azure Classic PowerShell](application-gateway-create-gateway.md)
- [Modelo do Azure Resource Manager ](application-gateway-create-gateway-arm-template.md)
- [CLI do Azure](application-gateway-create-gateway-cli.md)

<BR>

Este artigo orienta você pelas etapas para criar, configurar, iniciar e excluir um gateway de aplicativo.


## Antes de começar

1. Instale a versão mais recente dos cmdlets do Azure PowerShell usando o Web Platform Installer. Você pode baixar e instalar a versão mais recente na seção **Windows PowerShell** da [página Downloads](https://azure.microsoft.com/downloads/).
2. Se você tiver uma rede virtual existente, selecione uma sub-rede vazia existente ou crie uma nova sub-rede na rede virtual existente unicamente para uso pelo gateway de aplicativo. Não é possível implantar o gateway de aplicativo para uma rede virtual diferente dos recursos que você pretende implantar por trás do gateway de aplicativo.
3. Verifique se você tem uma rede virtual em funcionamento com uma sub-rede válida. Verifique se não há máquinas virtuais ou implantações em nuvem usando a sub-rede. O gateway de aplicativo deve estar sozinho em uma sub-rede de rede virtual.
3. Os servidores que você configura para usar o gateway de aplicativo devem existir ou ter seus pontos de extremidade criados na rede virtual ou com um IP/VIP público atribuído.

## O que é necessário para criar um gateway de aplicativo?


Quando você usa o comando **New-AzureApplicationGateway** para criar o gateway de aplicativo, nenhuma configuração é definida nesse ponto, e o recurso recém-criado é configurado usando um objeto de configuração ou XML.


Os valores são:

- **Pool de servidores back-end:** a lista de endereços IP dos servidores back-end. Os endereços IP listados devem pertencer à sub-rede da rede virtual ou devem ser um IP/VIP público.
- **Configurações do pool de servidores back-end:** cada pool tem configurações como porta, protocolo e afinidade baseada em cookie. Essas configurações são vinculadas a um pool e aplicadas a todos os servidores no pool.
- **Porta front-end:** essa porta é a porta pública aberta no gateway de aplicativo. O tráfego atinge essa porta e é redirecionado para um dos servidores back-end.
- **Ouvinte:** o ouvinte tem uma porta front-end, um protocolo (HTTP ou HTTPS, esses valores diferenciam maiúsculas de minúsculas) e o nome do certificado SSL (caso esteja configurando o descarregamento SSL).
- **Regra:** a regra vincula o ouvinte e o pool de servidores back-end e define à qual pool de servidores back-end o tráfego deve ser direcionado quando atinge um ouvinte específico.


## Criar um Application Gateway

Para criar um Application Gateway:

1. Criar um recurso de gateway de aplicativo.
2. Crie um arquivo XML de configuração ou um objeto de configuração.
3. Confirme a configuração do recurso de gateway de aplicativo recém-criado.

>[AZURE.NOTE] Se você precisar configurar uma investigação personalizada para o gateway de aplicativo, veja [Criar um gateway de aplicativo com investigações personalizadas usando o PowerShell](application-gateway-create-probe-classic-ps.md). Confira [investigações personalizadas e monitoramento de integridade](application-gateway-probe-overview.md) para saber mais.


### Criar um recurso de gateway de aplicativo

Para criar o gateway, use o cmdlet **New-AzureApplicationGateway**, substituindo os valores pelos seus próprios. A cobrança pelo gateway não se inicia neste momento. A cobrança é iniciada em uma etapa posterior, quando o gateway é iniciado com êxito.

O exemplo a seguir mostra como criar um novo Application Gateway usando uma rede virtual chamada "testvnet1" e uma sub-rede denominada "subnet-1".


	New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

	VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway
	VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399


 *Description*, *InstanceCount* e *GatewaySize* são parâmetros opcionais.


Para validar que esse gateway foi criado, você poderá usar o cmdlet **Get-AzureApplicationGateway**.




	Get-AzureApplicationGateway AppGwTest
	Name          : AppGwTest
	Description   :
	VnetName      : testvnet1
	Subnets       : {Subnet-1}
	InstanceCount : 2
	GatewaySize   : Medium
	State         : Stopped
	VirtualIPs    : {}
	DnsName       :

>[AZURE.NOTE]  O valor padrão para *InstanceCount* é 2, com um valor máximo de 10. O valor padrão para *GatewaySize* é Medium. Você pode escolher entre Small, Medium e Large.


 *VirtualIPs* e *DnsName* são mostrados em branco porque o gateway ainda não foi iniciado. Eles serão criados depois que o gateway estiver em estado de execução.

## Configurar o gateway de aplicativo

Você pode configurar o gateway de aplicativo usando XML ou um objeto de configuração.

## Configurar o gateway de aplicativo usando XML

No exemplo a seguir, você usará um arquivo XML para definir todas as configurações do Application Gateway e confirmá-las para o recurso do Application Gateway.

### Etapa 1  

Copie o seguinte texto no bloco de notas.

	<?xml version="1.0" encoding="utf-8"?>
	<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
	    <FrontendPorts>
	        <FrontendPort>
	            <Name>(name-of-your-frontend-port)</Name>
	            <Port>(port number)</Port>
	        </FrontendPort>
	    </FrontendPorts>
	    <BackendAddressPools>
	        <BackendAddressPool>
	            <Name>(name-of-your-backend-pool)</Name>
	            <IPAddresses>
	                <IPAddress>(your-IP-address-for-backend-pool)</IPAddress>
	                <IPAddress>(your-second-IP-address-for-backend-pool)</IPAddress>
	            </IPAddresses>
	        </BackendAddressPool>
	    </BackendAddressPools>
	    <BackendHttpSettingsList>
	        <BackendHttpSettings>
	            <Name>(backend-setting-name-to-configure-rule)</Name>
	            <Port>80</Port>
	            <Protocol>[Http|Https]</Protocol>
	            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
	        </BackendHttpSettings>
	    </BackendHttpSettingsList>
	    <HttpListeners>
	        <HttpListener>
	            <Name>(name-of-the-listener)</Name>
	            <FrontendPort>(name-of-your-frontend-port)</FrontendPort>
	            <Protocol>[Http|Https]</Protocol>
	        </HttpListener>
	    </HttpListeners>
	    <HttpLoadBalancingRules>
	        <HttpLoadBalancingRule>
	            <Name>(name-of-load-balancing-rule)</Name>
	            <Type>basic</Type>
	            <BackendHttpSettings>(backend-setting-name-to-configure-rule)</BackendHttpSettings>
	            <Listener>(name-of-the-listener)</Listener>
	            <BackendAddressPool>(name-of-your-backend-pool)</BackendAddressPool>
	        </HttpLoadBalancingRule>
	    </HttpLoadBalancingRules>
	</ApplicationGatewayConfiguration>

Edite os valores entre parênteses para os itens de configuração. Salve o arquivo com a extensão .xml.

>[AZURE.IMPORTANT] O item de protocolo Http ou Https diferencia maiúsculas de minúsculas.

O exemplo a seguir mostra como usar um arquivo de configuração para configurar o gateway de aplicativo. A carga de exemplo equilibra o tráfego HTTP na porta pública 80 e envia o tráfego de rede para a porta 80 do back-end entre dois endereços IP.

	<?xml version="1.0" encoding="utf-8"?>
	<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
	    <FrontendPorts>
	        <FrontendPort>
	            <Name>FrontendPort1</Name>
	            <Port>80</Port>
	        </FrontendPort>
	    </FrontendPorts>
	    <BackendAddressPools>
	        <BackendAddressPool>
	            <Name>BackendPool1</Name>
	            <IPAddresses>
	                <IPAddress>10.0.0.1</IPAddress>
	                <IPAddress>10.0.0.2</IPAddress>
	            </IPAddresses>
	        </BackendAddressPool>
	    </BackendAddressPools>
	    <BackendHttpSettingsList>
	        <BackendHttpSettings>
	            <Name>BackendSetting1</Name>
	            <Port>80</Port>
	            <Protocol>Http</Protocol>
	            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
	        </BackendHttpSettings>
	    </BackendHttpSettingsList>
	    <HttpListeners>
	        <HttpListener>
	            <Name>HTTPListener1</Name>
	            <FrontendPort>FrontendPort1</FrontendPort>
	            <Protocol>Http</Protocol>
	        </HttpListener>
	    </HttpListeners>
	    <HttpLoadBalancingRules>
	        <HttpLoadBalancingRule>
	            <Name>HttpLBRule1</Name>
	            <Type>basic</Type>
	            <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
	            <Listener>HTTPListener1</Listener>
	            <BackendAddressPool>BackendPool1</BackendAddressPool>
	        </HttpLoadBalancingRule>
	    </HttpLoadBalancingRules>
	</ApplicationGatewayConfiguration>


### Etapa 2

Em seguida, defina o gateway de aplicativo. Use o cmdlet **Set-AzureApplicationGatewayConfig** com um arquivo XML de configuração.


	Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile "D:\config.xml"

	VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig
	VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## Configurar o gateway de aplicativo usando um objeto de configuração

O exemplo a seguir mostra como configurar o gateway de aplicativo usando objetos de configuração. Todos os itens de configuração precisam ser configurado individualmente e, em seguida, adicionados a um objeto de configuração do gateway de aplicativo. Depois de criar o objeto de configuração, você usa o comando **Set-AzureApplicationGateway** para confirmar a configuração para o recurso do gateway de aplicativo criado anteriormente.

>[AZURE.NOTE] Antes de atribuir um valor a cada objeto de configuração, você precisa declarar que tipo de objeto do PowerShell vai armazená-lo. A primeira linha para criar os itens individuais define quais Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model(nome de objeto) são usados.

### Etapa 1

Crie todos os itens de configuração individuais.

Crie o IP front-end, como mostrado no exemplo a seguir.

	PS C:\> $fip = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration
	PS C:\> $fip.Name = "fip1"
	PS C:\> $fip.Type = "Private"
	PS C:\> $fip.StaticIPAddress = "10.0.0.5"

Crie a porta front-end, como mostrado no exemplo a seguir.

	PS C:\> $fep = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort
	PS C:\> $fep.Name = "fep1"
	PS C:\> $fep.Port = 80

Crie o pool de servidores back-end.

 Defina os endereços IP que são adicionados ao pool de servidores back-end conforme mostrado no exemplo a seguir.


	PS C:\> $servers = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendServerCollection
	PS C:\> $servers.Add("10.0.0.1")
	PS C:\> $servers.Add("10.0.0.2")

 Use o objeto $server para adicionar os valores ao objeto do pool de back-end ($pool).

	PS C:\> $pool = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool
	PS C:\> $pool.BackendServers = $servers
	PS C:\> $pool.Name = "pool1"

Crie a configuração do pool de servidores back-end.

	PS C:\> $setting = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings
	PS C:\> $setting.Name = "setting1"
	PS C:\> $setting.CookieBasedAffinity = "enabled"
	PS C:\> $setting.Port = 80
	PS C:\> $setting.Protocol = "http"

Crie o ouvinte.

	PS C:\> $listener = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener
	PS C:\> $listener.Name = "listener1"
	PS C:\> $listener.FrontendPort = "fep1"
	PS C:\> $listener.FrontendIP = "fip1"
	PS C:\> $listener.Protocol = "http"
	PS C:\> $listener.SslCert = ""

Crie a regra.

	PS C:\> $rule = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule
	PS C:\> $rule.Name = "rule1"
	PS C:\> $rule.Type = "basic"
	PS C:\> $rule.BackendHttpSettings = "setting1"
	PS C:\> $rule.Listener = "listener1"
	PS C:\> $rule.BackendAddressPool = "pool1"

### Etapa 2

Atribua todos os itens de configuração individuais a um objeto de configuração do gateway de aplicativo ($appgwconfig):

Adicione o IP front-end à configuração.

	PS C:\> $appgwconfig = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.ApplicationGatewayConfiguration
	PS C:\> $appgwconfig.FrontendIPConfigurations = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration]"
	PS C:\> $appgwconfig.FrontendIPConfigurations.Add($fip)

Adicione a porta front-end à configuração.

	PS C:\> $appgwconfig.FrontendPorts = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort]"
	PS C:\> $appgwconfig.FrontendPorts.Add($fep)

Adicione o pool de servidores back-end à configuração.

	PS C:\> $appgwconfig.BackendAddressPools = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool]"
	PS C:\> $appgwconfig.BackendAddressPools.Add($pool)  

Adicione a configuração do pool back-end à configuração.

	PS C:\> $appgwconfig.BackendHttpSettingsList = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings]"
	PS C:\> $appgwconfig.BackendHttpSettingsList.Add($setting)

Adicione o ouvinte à configuração.

	PS C:\> $appgwconfig.HttpListeners = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener]"
	PS C:\> $appgwconfig.HttpListeners.Add($listener)

Adicione a regra à configuração.

	PS C:\> $appgwconfig.HttpLoadBalancingRules = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule]"
	PS C:\> $appgwconfig.HttpLoadBalancingRules.Add($rule)

### Etapa 3

Confirme o objeto de configuração do recurso de gateway de aplicativo usando **Set-AzureApplicationGatewayConfig**.

	Set-AzureApplicationGatewayConfig -Name AppGwTest -Config $appgwconfig

## Iniciar o gateway

Depois que o gateway tiver sido configurado, use o cmdlet **Start-AzureApplicationGateway** para iniciá-lo. A cobrança por um gateway de aplicativo começa depois que o gateway tiver sido iniciado com êxito.


> [AZURE.NOTE] O cmdlet **Start-AzureApplicationGateway** pode demorar de 15 a 20 minutos para ser concluído.



	Start-AzureApplicationGateway AppGwTest

	VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway
	VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## Verificar o status do gateway

Use o cmdlet **Get-AzureApplicationGateway** para verificar o status do gateway. Se **Start-AzureApplicationGateway** tiver sido bem-sucedido na etapa anterior, o item *Estado* deverá ser Em execução e *Vip* e *DnsName* deverão ter entradas válidas.

Este exemplo mostra um gateway de aplicativo que está ativo, em execução e pronto para assumir o tráfego destinado a `http://<generated-dns-name>.cloudapp.net`.

	Get-AzureApplicationGateway AppGwTest

	VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway
	VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
	Name          : AppGwTest
	Description   :
	VnetName      : testvnet1
	Subnets       : {Subnet-1}
	InstanceCount : 2
	GatewaySize   : Medium
	State         : Running
	Vip           : 138.91.170.26
	DnsName       : appgw-1b8402e8-3e0d-428d-b661-289c16c82101.cloudapp.net


## Excluir um gateway de aplicativo

Para excluir um gateway de aplicativo:

1. Use o cmdlet **Stop-AzureApplicationGateway** para parar o gateway.
2. Use o cmdlet **Remove-AzureApplicationGateway** para remover o gateway.
3. Verifique se o gateway foi removido usando o cmdlet **Get-AzureApplicationGateway**.

O exemplo a seguir mostra o cmdlet **Stop-AzureApplicationGateway** na primeira linha, seguido pela saída.

	Stop-AzureApplicationGateway AppGwTest

	VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
	VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Depois que o gateway de aplicativo estiver em um estado parado, use o cmdlet **Remove-AzureApplicationGateway** para remover o serviço.


	Remove-AzureApplicationGateway AppGwTest

	VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
	VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

Para verificar se o serviço foi removido, você poderá usar o cmdlet **Get-AzureApplicationGateway**. Essa etapa não é necessária.


	Get-AzureApplicationGateway AppGwTest

	VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

	Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
	.....

## Próximas etapas

Se desejar configurar o descarregamento SSL, confira [Configurar um application gateway para descarregamento SSL](application-gateway-ssl.md).

Para configurar um gateway de aplicativo para usar com um balanceador de carga interno, confira [Criar um gateway de aplicativo com um ILB (balanceador de carga interno)](application-gateway-ilb.md).

Se deseja obter mais informações sobre as opções de balanceamento de carga no geral, consulte:

- [Balanceador de carga do Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gerenciador de Tráfego do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0831_2016-->