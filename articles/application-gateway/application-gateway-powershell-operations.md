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
   ms.date="06/30/2015"
   ms.author="joaoma"/>

# Criar, iniciar ou excluir um Application Gateway

Nesta versão, você pode criar um Application Gateway usando o PowerShell ou chamadas à API REST. O suporte ao portal e à CLI será fornecido em uma versão futura. Este artigo orienta você pelas etapas para criar, configurar, iniciar e excluir um gateway de aplicativo.

## Antes de começar

1. Instale a versão mais recente dos cmdlets do Azure PowerShell usando o Web Platform Installer. Você pode baixar e instalar a versão mais recente na seção **Windows PowerShell** da [página de download](http://azure.microsoft.com/downloads/).
2. Verifique se você tem uma rede virtual em funcionamento com uma sub-rede válida.
3. Verifique se você tem servidores back-end na rede virtual ou com um IP/VIP público atribuído.


Para criar um novo gateway de aplicativo, execute as etapas a seguir na ordem listada.

1. [Criar um novo gateway de aplicativo](#create-a-new-application-gateway)
2. [Configurar o gateway](#configure-the-gateway)
3. [Definir a configuração do gateway](#set-the-gateway-configuration)
4. [Iniciar o gateway](#start-the-gateway)
4. [Verificar o status do gateway](#verify-the-gateway-status)

Se desejar excluir um gateway de aplicativo, vá para [Excluir um gateway de aplicativo](#delete-an-application-gateway).

## Criar um novo gateway de aplicativo

**Para criar o gateway**, use o cmdlet `New-AzureApplicationGateway`, substituindo os valores pelos seus próprios. Observe que a cobrança pelo gateway não se inicia neste momento. A cobrança é iniciada em uma etapa posterior, quando o gateway é iniciado com êxito.

Este exemplo mostra o cmdlet na primeira linha, seguido pela saída.
    
	PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

	VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway 
	VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399

**Para validar** esse gateway que foi criado, você pode usar o cmdlet `Get-AzureApplicationGateway`.

No exemplo, *Description*, *InstanceCount* e *GatewaySize* são parâmetros opcionais. O valor padrão para *InstanceCount* é 2, com um valor máximo de 10. O valor padrão para *GatewaySize* é Medium. Small e Large são outros valore disponíveis. *Vip* e *DnsName* são mostrados em branco porque o gateway ainda não foi iniciado. Eles serão criados depois que o gateway estiver em estado de execução.




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


## Configurar o gateway

Uma configuração de gateway de aplicativo consiste em vários valores. Os valores podem ser vinculados para construir a configuração.

Os valores são:

- **Pool de servidores de back-end:** a lista de endereços IP dos servidores back-end. Os endereços IP listados ou devem pertencer à sub-rede da VNet, ou devem ser um IP/VIP público. 
- **Configurações do pool de servidores back-end:** cada pool tem configurações como porta, protocolo e afinidade baseada em cookie. Essas configurações são vinculadas a um pool e aplicadas a todos os servidores no pool.
- **Porta front-end:** essa porta é a porta pública aberta no gateway de aplicativo. O tráfego atinge essa porta e é redirecionado para um dos servidores back-end.
- **Ouvinte:** o ouvinte tem uma porta front-end, um protocolo (Http ou Https, que diferencia maiúsculas de minúsculas) e o nome do certificado SSL (se estiver configurando o descarregamento SSL). 
- **Regra:** a regra vincula o ouvinte e o pool de servidores back-end e define à qual pool de servidores back-end o tráfego deve ser direcionado quando atinge um ouvinte específico. Atualmente, há suporte apenas para a regra *basic*. A regra *basic* é a distribuição de carga round robin.

Você pode construir sua configuração criando um objeto de configuração ou usando um arquivo XML de configuração. Para construir a configuração usando um arquivo XML de configuração, use o exemplo abaixo.

 **Exemplo de XML de configuração**

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

## Definir a configuração do gateway

Em seguida, você vai configurar o gateway de aplicativo. É possível usar o cmdlet `Set-AzureApplicationGatewayConfig` com um objeto de configuração ou com um arquivo XML de configuração.


	PS C:\> Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml

	VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig 
	VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## Iniciar o gateway

Depois que o gateway tiver sido configurado, use o cmdlet `Start-AzureApplicationGateway` para iniciá-lo. A cobrança por um gateway de aplicativo começa depois que o gateway tiver sido iniciado com êxito.


**Observação:** o cmdlet `Start-AzureApplicationGateway` pode levar de 15 a 20 minutos para ser concluído.



	PS C:\> Start-AzureApplicationGateway AppGwTest 

	VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
	VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## Verificar o status do gateway

Use o cmdlet `Get-AzureApplicationGateway` para verificar o status do gateway. Se *Start-AzureApplicationGateway* foi bem-sucedido na etapa anterior, o Estado deverá ser *Running*, e Vip e DnsName devem ter entradas válidas.

Este exemplo mostra um gateway de aplicativo que está ativo, em execução e pronto para assumir o tráfego destinado a `http://<generated-dns-name>.cloudapp.net`.

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

Depois que o gateway de aplicativo estiver em um estado Parado, use o cmdlet `Remove-AzureApplicationGateway` para remover o serviço.


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

Se desejar configurar o descarregamento SSL, consulte [Configurar o Application Gateway para descarregamento SSL](application-gateway-ssl.md).

Se desejar configurar um gateway de aplicativo para usar com o ILB, consulte [Criar um Application Gateway com um ILB (Balanceador de Carga Interno)](application-gateway-ilb.md).

Se deseja obter mais informações sobre as opções de balanceamento de carga no geral, consulte:

- [Balanceador de carga do Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gerenciador de Tráfego do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=August15_HO6-->