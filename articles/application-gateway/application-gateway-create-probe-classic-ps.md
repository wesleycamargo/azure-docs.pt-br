<properties 
   pageTitle="Criar uma investigação personalizada para o Application Gateway usando o PowerShell no modelo de implantação clássico | Microsoft Azure"
   description="Saiba como criar uma investigação personalizada para o Application Gateway usando o PowerShell no modelo de implantação clássico"
   services="application-gateway"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/17/2015"
   ms.author="joaoma" />

# Criar investigação personalizada para o Application Gateway (clássico) usando o PowerShell


[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)].

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](application-gateway-create-probe-ps.md).


[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


## Criar um novo gateway de aplicativo 

Existe uma ordem de etapas que você precisará seguir para criar um Application Gateway:

1. Crie um recurso do Application Gateway.
2. Crie o arquivo XML de configuração ou o objeto de configuração.
3. Confirme a configuração do recurso do Application Gateway recém-criado.

### Criar um recurso do Application Gateway

Para criar o gateway, use o cmdlet `New-AzureApplicationGateway`, substituindo os valores pelos seus próprios. Observe que a cobrança pelo gateway não se inicia neste momento. A cobrança é iniciada em uma etapa posterior, quando o gateway é iniciado com êxito.

O exemplo a seguir mostra como criar um novo Application Gateway usando uma rede virtual chamada "testvnet1" e uma sub-rede denominada "subnet-1".


	PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

	VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway
	VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399


 *Description*, *InstanceCount* e *GatewaySize* são parâmetros opcionais.


**Para validar** que o gateway foi criado, você pode usar o cmdlet `Get-AzureApplicationGateway`.


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

## Configurar um Application Gateway

Você pode configurar o Application Gateway usando XML ou um objeto de configuração.

## Configurar um Application Gateway usando XML

No exemplo a seguir, você usará um arquivo XML para definir todas as configurações do Application Gateway e confirmá-las para o recurso do Application Gateway.

### Etapa 1  

Copie o seguinte texto no bloco de notas.


	<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations>
        <FrontendIPConfiguration>
            <Name>fip1</Name>
            <Type>Private</Type>
        </FrontendIPConfiguration>
    </FrontendIPConfigurations>    
	<FrontendPorts>
        <FrontendPort>
            <Name>port1</Name>
            <Port>80</Port>
        </FrontendPort>
    </FrontendPorts>
    <Probes>
        <Probe>
            <Name>Probe01</Name>
            <Protocol>Http</Protocol>
            <Host>contoso.com</Host>
            <Path>/path/custompath.htm</Path>
            <Interval>15</Interval>
            <Timeout>15</Timeout>
            <UnhealthyThreshold>5</UnhealthyThreshold>
        </Probe>
    <BackendAddressPools>
        <BackendAddressPool>
            <Name>pool1</Name>
            <IPAddresses>
                <IPAddress>1.1.1.1</IPAddress>
				<IPAddress>2.2.2.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>listener1</Name>
            <FrontendIP>fip1</FrontendIP>
	    <FrontendPort>port1</FrontendPort>
            <Protocol>Http</Protocol>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>lbrule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>setting1</BackendHttpSettings>
            <Listener>listener1</Listener>
            <BackendAddressPool>pool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
	</ApplicationGatewayConfiguration>


Edite os valores entre parênteses para os itens de configuração. Salve o arquivo com a extensão. XML

O exemplo a seguir mostra como usar um arquivo de configuração configurando o Application Gateway para balancear a carga do tráfego Http na porta pública 80 e enviando o tráfego de rede para a porta 80 do back-end entre dois endereços IP usando a investigação personalizada.

>[AZURE.IMPORTANT]O item de protocolo Http ou Https diferencia maiúsculas de minúsculas.


Um novo item de configuração <Probe> é adicionado ao configurar investigações personalizadas.

Os parâmetros de configuração são:

- **Name** - nome de referência da investigação personalizada
- **Protocol** -protocolo usado (os valores possíveis são Http ou Https)
- **Host** e **Path** - caminho de URL completo que é invocado pelo Application Gateway para determinar a integridade da instância. Por exemplo: você tem um site http://contoso.com/ e a investigação personalizada pode ser configurada para "http://contoso.com/path/custompath.htm" para verificações de investigação com resposta HTTP bem-sucedida. 
- **Interval** - configura as verificações de intervalo de investigação em segundos 
- **Timeout** - define o tempo limite da investigação para uma verificação de resposta HTTP
- **UnhealthyThreshold** - o número de respostas HTTP com falha; ele é necessário para sinalizar a instância de back-end como *não íntegra*

O nome da investigação é referenciado na configuração <BackendHttpSettings> para atribuir qual pool de back-end usará as configurações da investigação personalizada.

## Adicionar configuração de investigação personalizada a um Application Gateway existente

Alterar a configuração atual de um Application Gateway exige três etapas: obter o arquivo de configuração XML atual, modificar para ter a investigação personalizada e configurar o Application Gateway com as novas configurações de XML.

### Etapa 1

Obtenha o arquivo xml usando get-AzureApplicationGatewayConfig. Isso exportará o XML de configuração a ser modificado para adicionar uma configuração de investigação
	
	get-AzureApplicationGatewayConfig -Name <application gateway name> -Exporttofile "<path to file>"


### Etapa 2 

Abra o arquivo XML em um editor de texto. Adicione a seção `<probe>` após `<frontendport>`
	
	<Probes>
        <Probe>
            <Name>Probe01</Name>
            <Protocol>Http</Protocol>
            <Host>contoso.com</Host>
            <Path>/path/custompath.htm</Path>
            <Interval>15</Interval>
            <Timeout>15</Timeout>
            <UnhealthyThreshold>5</UnhealthyThreshold>
        </Probe>

Na seção backendHttpSettings do XML, adicione o nome da investigação como mostrado no exemplo a seguir:
    
        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>

Salve o arquivo XML


### Etapa 3 

Atualize a configuração do Application Gateway de aplicativo usando o novo arquivo XML usando `Set-AzureApplicationGatewayConfig`. Isso atualizará seu Application Gateway com a nova configuração.

	set-AzureApplicationGatewayConfig -Name <application gateway name> -Configfile "<path to file>"


## Próximas etapas

Se desejar configurar o descarregamento SSL, confira [Configurar um Application Gateway para descarregamento SSL](application-gateway-ssl.md).

Se desejar configurar um Application Gateway para usar com o ILB, consulte [Criar um Application Gateway com um ILB (Balanceador de Carga Interno)](application-gateway-ilb.md).

<!---HONumber=AcomDC_0107_2016-->