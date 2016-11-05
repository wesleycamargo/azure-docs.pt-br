---
title: Configurar a política de SSL e o SSL de ponta a ponta com o Gateway de Aplicativo | Microsoft Docs
description: Este artigo descreve como configurar o SSL de ponta a ponta com o Gateway de Aplicativo usando o Azure PowerShell Resource Manager
services: application-gateway
documentationcenter: na
author: georgewallace
manager: carmonmills
editor: tysonn

ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2016
ms.author: gwallace

---
# <a name="configure-ssl-policy-and-end-to-end-ssl-with-application-gateway-using-powershell"></a>Configurar a política de SSL e o SSL de ponta a ponta com o Gateway de Aplicativo usando o PowerShell
## <a name="overview"></a>Visão geral
O Gateway de Aplicativo oferece suporte à criptografia de tráfego de ponta a ponta. O Gateway de Aplicativo faz isso ao encerrar a conexão SSL no gateway de aplicativo. O gateway, em seguida, aplica as regras de roteamento ao tráfego, criptografa o pacote novamente e encaminha o pacote para o back-end apropriado com base nas regras de roteamento definidas. Qualquer resposta do servidor Web passa pelo mesmo processo de volta para o usuário final.

Outro recurso a que esse gateway de aplicativo oferece suporte está desabilitando determinadas versões do protocolo SSL. O Gateway de Aplicativo oferece suporte a desabilitar a versão do protocolo a seguir; TLSv1.0, TLSv1.1 e TLSv1.2.

![imagem de cenário][scenario]

## <a name="scenario"></a>Cenário
Neste cenário, você aprenderá a criar um gateway de aplicativo usando o SSL de ponta a ponta usando o PowerShell.

Este cenário:

* Criar um grupo de recursos denominado "appgw-rg"
* Criará uma rede virtual chamada "appgwvnet", com um bloco CIDR 10.0.0.0/16 reservado.
* Crie duas sub-redes chamadas "appgwsubnet" e "appsubnet".
* Crie um pequeno gateway de aplicativo que ofereça suporte à criptografia SSL de ponta a ponta que desabilita determinados protocolos SSL.

## <a name="before-you-begin"></a>Antes de começar
Para configurar o SSL de ponta a ponta com um gateway de aplicativo, um certificado é necessário para o gateway e são necessários certificados para os servidores de back-end. O certificado de gateway é usado para criptografar e descriptografar o tráfego enviado para ele via SSL. O certificado do gateway precisa estar no formato pfx (Troca de Informações Pessoais). Esse formato de arquivo permite que a chave privada seja exportada, o que é exigido pelo Application Gateway para executar criptografia e descriptografia de tráfego. 

Para criptografia ssl de ponta a ponta, o back-end deve estar na lista branca do gateway de aplicativo. Isso é feito por meio do upload do certificado público dos back-ends para o gateway de aplicativo. Isso garante que o gateway de aplicativo se comunique somente com instâncias de back-end conhecidas, protegendo a comunicação de ponta a ponta. Cada atributo é descrito nas etapas a seguir:

## <a name="create-the-resource-group"></a>Criar o Grupo de recursos
Esta seção orienta você pela criação de um grupo de recursos que contenha o gateway de aplicativo.

### <a name="step-1"></a>Etapa 1
Faça logon na sua Conta do Azure.

    Login-AzureRmAccount

### <a name="step-2"></a>Etapa 2
Selecione a assinatura a ser usada nesse cenário.

    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"

### <a name="step-3"></a>Etapa 3
Crie um grupo de recursos (ignore esta etapa se você estiver usando um grupo de recursos existente).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Criar uma rede virtual e uma sub-rede para o gateway de aplicativo
O exemplo a seguir cria uma rede virtual e duas sub-redes. Uma sub-rede é usada para hospedar o gateway de aplicativo. A outra sub-rede é usada para os servidores de back-end que hospedam o aplicativo Web.

### <a name="step-1"></a>Etapa 1
Atribua um intervalo de endereços para que a sub-rede seja usada para o próprio Gateway de Aplicativo.

    $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

### <a name="step-2"></a>Etapa 2
Atribua um intervalo de endereços a ser usado para o pool de endereços do Back-end.

    $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

### <a name="step-3"></a>Etapa 3
Crie uma rede virtual com as sub-redes definidas nas etapas anteriores.

    $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

### <a name="step-4"></a>Etapa 4
Recupere o recurso de rede virtual e os recursos de sub-rede a serem usados nestas etapas:

    $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
    $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
    $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end
Crie um recurso IP público a ser usado para o gateway de aplicativo. Este endereço IP público será usado em uma etapa seguinte.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name 'appgwpip' -Location "West US" -AllocationMethod Dynamic

> [!IMPORTANT]
> O Gateway de Aplicativo não dá suporte ao uso de um endereço IP público criado com um rótulo de domínio definido. Há suporte para apenas para um endereço IP público com um rótulo de domínio criado dinamicamente. Se você precisar de um nome amigável de dns para o gateway de aplicativo, é recomendável usar um registro cname como alias.
> 
> 

## <a name="create-an-application-gateway-configuration-object"></a>Criar um objeto de configuração do gateway do aplicativo
Você deve configurar todos os itens de configuração antes de criar o gateway de aplicativo. As etapas a seguir criam os itens de configuração necessários para um recurso de gateway de aplicativo.

### <a name="step-1"></a>Etapa 1
Crie uma configuração de IP do gateway de aplicativo para configurar qual sub-rede será usada pelo gateway de aplicativo. Quando o Gateway de Aplicativo é iniciado, ele escolhe um endereço IP na sub-rede configurada e encaminha o tráfego de rede para os endereços IP no pool de IPs de back-end. Tenha em mente que cada instância usa um endereço IP.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

### <a name="step-2"></a>Etapa 2
Crie uma configuração de IP de front-end para mapear um endereço ip público ou privado para o front-end do gateway de aplicativo. A etapa a seguir associa o endereço IP público da etapa anterior à configuração de IP do front-end.

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

### <a name="step-3"></a>Etapa 3
Configure o pool de endereços IP do back-end com os endereços IP dos servidores Web de back-end. Esses endereços IP são os que receberão o tráfego de rede proveniente do ponto de extremidade do IP de front-end. Você substitui os endereços IP a seguir para adicionar seus próprios pontos de extremidade de endereço IP do aplicativo.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

> [!NOTE]
> Um nome de domínio totalmente qualificado (FQDN) também é um valor válido no lugar de um endereço ip para os servidores de back-end.
> 
> 

### <a name="step-4"></a>Etapa 4
Configure a porta IP front-end para o ponto de extremidade IP público. Essa porta é a porta à qual os usuários finais se conectam.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

### <a name="step-5"></a>Etapa 5
Configure o certificado para o gateway de aplicativo. Esse certificado é usado para descriptografar e criptografar novamente o tráfego no gateway de aplicativo.

    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

> [!NOTE]
> Esta amostra configura o certificado usado para a conexão SSL. O certificado deve estar no formato .pfx e a senha deve ter entre 4 e 12 caracteres.
> 
> 

### <a name="step-6"></a>Etapa 6
Crie o ouvinte HTTP para o gateway de aplicativo. Atribua a configuração do ip, a porta e o certificado ssl do front-end a ser usado.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

### <a name="step-7"></a>Etapa 7
Carregue o certificado a ser usado nos recursos do pool de back-end habilitado para ssl.

    $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile C:\users\gwallace\Desktop\cert.cer

> [!NOTE]
> O certificado fornecido nesta etapa deve ser a chave pública do certificado pfx presente no back-end. Esta etapa coloca o back-end na lista branca do gateway de aplicativo. 
> 
> 

### <a name="step-8"></a>Etapa 8
Defina as configurações de http de back-end do gateway de aplicativo. Atribua o certificado carregado na etapa anterior às configurações de http.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

### <a name="step-9"></a>Etapa 9
Crie uma regra de roteamento do balanceador de carga que configure o comportamento do balanceador de carga. Neste exemplo, é criada uma regra básica de round robin.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### <a name="step-10"></a>Etapa 10
Configure o tamanho da instância do gateway de aplicativo.  Os tamanhos disponíveis são **Standard\_Small**, **Standard\_Medium** e **Standard\_Large**.  Para a capacidade e os valores disponíveis são 1 a 10.

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

> [!NOTE]
> É possível escolher uma contagem de instâncias de 1 para fins de teste. É importante saber que qualquer contagem de instâncias em duas instâncias não é coberta por um SLA e, portanto, não são recomendadas. Gateways pequenos devem ser usados para teste de desenvolvimento e não para fins de produção.
> 
> 

### <a name="step-11"></a>Etapa 11
Configure a política SSL a ser usada no Gateway de Aplicativo. O Gateway de Aplicativo oferece suporte à capacidade de desabilitar determinadas versões do protocolo SSL.

Os valores a seguir são uma lista de versões do protocolo que podem ser desabilitadas.

* **TLSv1_0**
* **TLSv1_1**
* **TLSv1_2**

O exemplo a seguir desabilita a TLSv1\_0 e a TLSv1\_1

    $sslPolicy = New-AzureRmApplicationGatewaySslPolicy -DisabledSslProtocols TLSv1_0, TLSv1_1

## <a name="create-the-application-gateway"></a>Criar o Application Gateway
Usando todas as etapas anteriores, crie o Gateway de Aplicativo. A criação do gateway é um processo de execução demorada.

    $appgw = New-AzureRmApplicationGateway -Name appgateway -SslCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslPolicy $sslPolicy -AuthenticationCertificates $authcert -Verbose

## <a name="disable-ssl-protocol-versions-on-an-existing-application-gateway"></a>Desabilitar as versões de protocolo SSL em um Gateway de Aplicativo existente
As etapas anteriores levam você a criar um aplicativo com ssl de ponta a ponta e a desabilitar determinadas versões do protocolo SSL. O exemplo a seguir desabilita determinadas políticas de SSL em um gateway de aplicativo existente.

### <a name="step-1"></a>Etapa 1
Recupere o gateway de aplicativo para atualizar.

    $gw = Get-AzureRmApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG

### <a name="step-2"></a>Etapa 2
Defina uma política de SSL. No exemplo a seguir, o TLSv1.0 está desabilitado.

    Set-AzureRmApplicationGatewaySslPolicy -DisabledSslProtocols TLSv1_0 -ApplicationGateway $gw

### <a name="step-3"></a>Etapa 3
Por fim, atualize o gateway. É importante observar que essa última etapa é uma tarefa de execução demorada. Quando estiver pronto, o ssl de ponta a ponta será configurado no gateway de aplicativo.

    $gw | Set-AzureRmApplicationGateway

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como otimizar a segurança dos seus aplicativos Web com o Firewall do Aplicativo Web por meio do Gateway de Aplicativo ao visitar [Visão geral do Firewall de Aplicativo Web](application-gateway-webapplicationfirewall-overview.md)

[scenario]: ./media/application-gateway-end-to-end-ssl-powershell/scenario.png


<!--HONumber=Oct16_HO2-->


