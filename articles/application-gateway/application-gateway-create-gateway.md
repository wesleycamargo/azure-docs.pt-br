<properties 
   pageTitle="Criar, iniciar ou excluir um Application Gateway | Microsoft Azure"
   description="Esta página fornece instruções para criar, configurar, iniciar e excluir um Application Gateway do Azure"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="07/29/2015"
   ms.author="joaoma"/>

# Criar, iniciar ou excluir um Application Gateway

Nesta versão, você pode criar um Application Gateway usando o PowerShell ou chamadas à API REST. O suporte ao portal e à CLI será fornecido em uma versão futura. Este artigo orienta você pelas etapas para criar, configurar, iniciar e excluir um gateway de aplicativo.

## Antes de começar

1. Instale a versão mais recente dos cmdlets do Azure PowerShell usando o Web Platform Installer. Você pode baixar e instalar a versão mais recente na seção **Windows PowerShell** da [página de download](http://azure.microsoft.com/downloads/).
2. Verifique se você tem uma rede virtual em funcionamento com uma sub-rede válida. Verifique se não há máquinas virtuais ou implantações em nuvem usando a sub-rede. O Application Gateway deve estar sozinho em uma sub-rede de rede virtual.
3. Os servidores que você configurará para usar o Application Gateway devem existir ou tem seus pontos de extremidade criados na rede virtual ou com um IP/VIP público atribuído.

## O que é necessário para criar um Application Gateway?
 

Quando você usa o comando New-AzureApplicationGateway para criar o Application Gateway, nenhuma configuração é definida nesse ponto e o recurso recém-criado precisará ser configurado usando um objeto de configuração ou XML.


Os valores são:

- **Pool de servidores back-end:** a lista de endereços IP dos servidores back-end. Os endereços IP listados devem pertencer à sub-rede da rede virtual ou devem ser um IP/VIP público. 
- **Configurações do pool de servidores back-end:** cada pool tem configurações como porta, protocolo e afinidade baseada em cookie. Essas configurações são vinculadas a um pool e aplicadas a todos os servidores no pool.
- **Porta front-end:** essa porta é a porta pública aberta no Application Gateway. O tráfego atinge essa porta e é redirecionado para um dos servidores back-end.
- **Ouvinte:** o ouvinte tem uma porta front-end, um protocolo (Http ou Https, que diferencia maiúsculas de minúsculas) e o nome do certificado SSL (se estiver configurando o descarregamento SSL). 
- **Regra:** a regra vincula o ouvinte e o pool de servidores back-end e define à qual pool de servidores back-end o tráfego deve ser direcionado quando atinge um ouvinte específico. Atualmente, há suporte apenas para a regra *basic*. A regra *basic* é a distribuição de carga round robin.


 
## Criar um novo Application Gateway

Existe uma ordem de etapas que você precisará seguir para criar um Application Gateway:

1. Crie um recurso do Application Gateway
2. Crie o arquivo XML de configuração ou o objeto de configuração
3. Confirme a configuração do recurso do Application Gateway recém-criado

### Criar o recurso do Application Gateway

**Para criar o gateway**, use o cmdlet `New-AzureApplicationGateway`, substituindo os valores pelos seus próprios. Observe que a cobrança pelo gateway não se inicia neste momento. A cobrança é iniciada em uma etapa posterior, quando o gateway é iniciado com êxito.

O exemplo a seguir mostra como criar um novo Application Gateway usando uma rede virtual chamada "testvnet1" e uma sub-rede denominada "subnet-1":

    
	PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

	VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway 
	VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399


 *Description*, *InstanceCount* e *GatewaySize* são parâmetros opcionais.


**Para validar** esse gateway que foi criado, você pode usar o cmdlet `Get-AzureApplicationGateway`.




	PS C:\> Get-AzureApplicationGateway AppGwTest
	Name          : AppGwTest
	Description   : 
	VnetName      : testvnet1
	Subnets       : {Subnet-1}
	InstanceCount : 2
	GatewaySize   : Medium
	State         : Stopped
	VirtualIPs    : {}
	DnsName       :

>[AZURE.NOTE]O valor padrão para *InstanceCount* é 2, com um valor máximo de 10. O valor padrão para *GatewaySize* é Medium. Você pode escolher entre Small, Medium e Large.


 *Vip* e *DnsName* são mostrados em branco porque o gateway ainda não foi iniciado. Eles serão criados depois que o gateway estiver em estado de execução.

## Configurar o Application Gateway

Você pode configurar o Application Gateway usando os seguintes métodos abaixo: objeto de configuração ou XML.

## Configurar o Application Gateway usando XML 

No exemplo a seguir, você usará um arquivo XML para definir todas as configurações do Application Gateway e confirmá-las para o recurso do Application Gateway.

### Etapa 1  

Copie o texto abaixo e cole-o no bloco de notas:

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

Edite os valores entre parênteses para os itens de configuração. Salve o arquivo com a extensão. XML

>[AZURE.IMPORTANT]O item de protocolo Http ou Https diferencia maiúsculas de minúsculas.

O exemplo a seguir mostra como usar um arquivo de configuração configurando o Application Gateway para balancear a carga do tráfego Http na porta pública 80 e enviando o tráfego de rede para a porta 80 do back-end entre dois endereços IP:

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

Em seguida, você vai configurar o gateway de aplicativo. Você usará o cmdlet `Set-AzureApplicationGatewayConfig` com um arquivo XML de configuração.


	PS C:\> Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile "D:\config.xml"

	VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig 
	VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## Configurar o Application Gateway usando o objeto de configuração

O exemplo a seguir mostra como configurar o Application Gateway usando objetos de configuração. Todos os itens de configuração precisam ser configurado individualmente e, em seguida, adicionados a um objeto de configuração do Application Gateway. Depois de criar o objeto de configuração, você usará o comando `Set-AzureApplicationGateway` para confirmar a configuração para o recurso do Application Gateway criado anteriormente.

>[AZURE.NOTE]Antes de atribuir um valor a cada objeto de configuração, você precisa declarar que tipo de objeto do PowerShell vai armazená-lo. A primeira linha para criar os itens individuais define quais Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model(nome de objeto) serão usados.

### Etapa 1

Crie todos os itens de configuração individuais:

Crie o IP front-end:

	PS C:\> $fip = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration 
	PS C:\> $fip.Name = "fip1" 
	PS C:\> $fip.Type = "Private" 
	PS C:\> $fip.StaticIPAddress = "10.0.0.5" 

Crie a porta front-end:
	
	PS C:\> $fep = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort 
	PS C:\> $fep.Name = "fep1" 
	PS C:\> $fep.Port = 80
	
Crie o pool de servidores back-end:

 Defina os endereços IP que serão adicionados ao pool de servidores back-end:


	PS C:\> $servers = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendServerCollection 
	PS C:\> $servers.Add("10.0.0.1") 
	PS C:\> $servers.Add("10.0.0.2")

 Usando o objeto $server, adicione os valores ao objeto do pool de back-end ($pool)

	PS C:\> $pool = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool 
	PS C:\> $pool.BackendServers = $servers 
	PS C:\> $pool.Name = "pool1"

Crie a configuração do pool de servidores back-end

	PS C:\> $setting = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings 
	PS C:\> $setting.Name = "setting1" 
	PS C:\> $setting.CookieBasedAffinity = "enabled" 
	PS C:\> $setting.Port = 80 
	PS C:\> $setting.Protocol = "http"

Crie o ouvinte

	PS C:\> $listener = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener 
	PS C:\> $listener.Name = "listener1" 
	PS C:\> $listener.FrontendPort = "fep1" 
	PS C:\> $listener.FrontendIP = "fip1" 
	PS C:\> $listener.Protocol = "http" 
	PS C:\> $listener.SslCert = ""

Crie a regra

	PS C:\> $rule = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule 
	PS C:\> $rule.Name = "rule1" 
	PS C:\> $rule.Type = "basic" 
	PS C:\> $rule.BackendHttpSettings = "setting1" 
	PS C:\> $rule.Listener = "listener1" 
	PS C:\> $rule.BackendAddressPool = "pool1"
 
### Etapa 2

Atribua todos os itens de configuração individuais a um objeto de configuração do Application Gateway ($appgwconfig):

Adicione o IP front-end à configuração

	PS C:\> $appgwconfig = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.ApplicationGatewayConfiguration
	PS C:\> $appgwconfig.FrontendIPConfigurations = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration]" 
	PS C:\> $appgwconfig.FrontendIPConfigurations.Add($fip)
 
Adicione a porta front-end à configuração

	PS C:\> $appgwconfig.FrontendPorts = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort]" 
	PS C:\> $appgwconfig.FrontendPorts.Add($fep)

Adicione o pool de servidores back-end à configuração

	PS C:\> $appgwconfig.BackendAddressPools = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool]" 
	PS C:\> $appgwconfig.BackendAddressPools.Add($pool)  

Adicione a configuração do pool back-end à configuração

	PS C:\> $appgwconfig.BackendHttpSettingsList = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings]" 
	PS C:\> $appgwconfig.BackendHttpSettingsList.Add($setting) 

Adicione o ouvinte à configuração

	PS C:\> $appgwconfig.HttpListeners = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener]" 
	PS C:\> $appgwconfig.HttpListeners.Add($listener)

Adicione a regra à configuração

	PS C:\> $appgwconfig.HttpLoadBalancingRules = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule]" 
	PS C:\> $appgwconfig.HttpLoadBalancingRules.Add($rule) 

### Etapa 3

Confirme o objeto de configuração do recurso do Application Gateway usando `Set-AzureApplicationGatewayConfig`:
 
	Set-AzureApplicationGatewayConfig -Name AppGwTest -Config $appgwconfig

## Iniciar o gateway

Depois que o gateway tiver sido configurado, use o cmdlet `Start-AzureApplicationGateway` para iniciá-lo. A cobrança por um gateway de aplicativo começa depois que o gateway tiver sido iniciado com êxito.


**Observação:** o cmdlet `Start-AzureApplicationGateway` pode levar até 15 a 20 minutos para ser concluído.



	PS C:\> Start-AzureApplicationGateway AppGwTest 

	VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
	VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## Verificar o status do gateway

Use o cmdlet `Get-AzureApplicationGateway` para verificar o status do gateway. Se *Start-AzureApplicationGateway* foi bem-sucedido na etapa anterior, o item State deverá ser *Running*, e Vip e DnsName deverão ter entradas válidas.

Este exemplo mostra um Application Gateway que está ativo, em execução e pronto para assumir o tráfego destinado a `http://<generated-dns-name>.cloudapp.net`.

	PS C:\> Get-AzureApplicationGateway AppGwTest 

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

Para excluir um gateway de aplicativo, você precisará seguir este procedimento na ordem:

1. Use o cmdlet `Stop-AzureApplicationGateway` para interromper o gateway. 
2. Use o cmdlet `Remove-AzureApplicationGateway` para remover o gateway.
3. Verifique se o gateway foi removido usando o cmdlet `Get-AzureApplicationGateway`.

Este exemplo mostra o cmdlet `Stop-AzureApplicationGateway` na primeira linha, seguido pela saída.

	PS C:\> Stop-AzureApplicationGateway AppGwTest 

	VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway 
	VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Depois que o Application Gateway estiver em um estado Stopped, use o cmdlet `Remove-AzureApplicationGateway` para remover o serviço.


	PS C:\> Remove-AzureApplicationGateway AppGwTest 

	VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway 
	VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

Para verificar se o serviço foi removido, você pode usar o cmdlet `Get-AzureApplicationGateway`. Essa etapa não é necessária.


	PS C:\> Get-AzureApplicationGateway AppGwTest 

	VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway 

	Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist. 
	.....

## Próximas etapas

Se desejar configurar o descarregamento SSL, consulte [Configurar um Application Gateway para descarregamento SSL](application-gateway-ssl.md).

Se desejar configurar um Application Gateway para usar com o ILB, consulte [Criar um Application Gateway com um ILB (Balanceador de Carga Interno)](application-gateway-ilb.md).

Se deseja obter mais informações sobre as opções de balanceamento de carga no geral, consulte:

- [Balanceador de Carga do Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gerenciador de Tráfego do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=August15_HO6-->