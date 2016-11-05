---
title: Criar uma investigação personalizada para o Application Gateway usando o PowerShell no modelo de implantação clássico | Microsoft Docs
description: Saiba como criar uma investigação personalizada para o Application Gateway usando o PowerShell no modelo de implantação clássico
services: application-gateway
documentationcenter: na
author: georgewallace
manager: carmonm
editor: ''
tags: azure-service-management

ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/09/2016
ms.author: gwallace

---
# Criar uma investigação personalizada para o Application Gateway (clássico) pelo uso do PowerShell
> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-probe-portal.md)
> * [PowerShell do Azure Resource Manager](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)
> 
> 

<BR>

[!INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]

Saiba como [executar estas etapas usando o modelo do Resource Manager](application-gateway-create-probe-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Criar um Application Gateway
Para criar um Application Gateway:

1. Criar um recurso de gateway de aplicativo.
2. Crie um arquivo XML de configuração ou um objeto de configuração.
3. Confirme a configuração do recurso de Application Gateway recém-criado.

### Criar um recurso de Application Gateway
Para criar o gateway, use o cmdlet **New-AzureApplicationGateway**, substituindo os valores pelos seus próprios. A cobrança pelo gateway não se inicia neste momento. A cobrança é iniciada em uma etapa posterior, quando o gateway é iniciado com êxito.

O exemplo a seguir cria um novo Application Gateway usando uma rede virtual chamada "testvnet1" e uma sub-rede chamada "subnet-1".

    New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

Para validar que esse gateway foi criado, você poderá usar o cmdlet **Get-AzureApplicationGateway**.

    Get-AzureApplicationGateway AppGwTest

> [!NOTE]
> O valor padrão para *InstanceCount* é 2, com um valor máximo de 10. O valor padrão para *GatewaySize* é Medium. Você pode escolher entre Small, Medium e Large.
> 
> 

 *VirtualIPs* e *DnsName* são mostrados em branco porque o gateway ainda não foi iniciado. Eles serão criados depois que o gateway estiver em estado de execução.

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
      </Probes>
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


Edite os valores entre parênteses para os itens de configuração. Salve o arquivo com a extensão .xml.

O exemplo a seguir mostra como usar um arquivo de configuração configurando o Application Gateway para balancear a carga do tráfego HTTP na porta pública 80 e enviando o tráfego de rede para a porta 80 do back-end entre dois endereços IP pelo uso de uma investigação personalizada.

> [!IMPORTANT]
> O item de protocolo Http ou Https diferencia maiúsculas de minúsculas.
> 
> 

Um novo item de configuração <Probe> é adicionado para configurar investigações personalizadas.

Os parâmetros de configuração são:

* **Name**: nome de referência da investigação personalizada.
* **Protocol**: protocolo usado (os valores possíveis são HTTP ou HTTPS).
* **Host** e **Path**: caminho de URL completo que é invocado pelo Application Gateway para determinar a integridade da instância. Por exemplo, se você tiver um site http://contoso.com/, a investigação personalizada poderá ser configurada para "http://contoso.com/path/custompath.htm" para verificações de investigação com uma resposta HTTP bem-sucedida.
* **Interval**: configura as verificações de intervalo de investigação em segundos.
* **Timeout**: define o tempo limite da investigação para uma verificação de resposta HTTP.
* **UnhealthyThreshold**: o número de respostas HTTP com falha necessárias para sinalizar a instância de back-end como *unhealthy*.

O nome da investigação é referenciado na configuração <BackendHttpSettings> para atribuir qual pool de back-end usa as configurações da investigação personalizada.

## Adicionar uma configuração de investigação personalizada a um Application Gateway existente
Alterar a configuração atual de um Application Gateway exige três etapas: obter o arquivo de configuração XML atual, modificar para ter uma investigação personalizada e configurar o Application Gateway com as novas configurações de XML.

### Etapa 1
Obtenha o arquivo XML usando get-AzureApplicationGatewayConfig. Isso exporta o XML de configuração a ser modificada para adicionar uma configuração de investigação.

    Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"


### Etapa 2
Abra o arquivo XML em um editor de texto. Adicione uma seção `<probe>` após `<frontendport>`.

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
    </Probes>

Na seção backendHttpSettings do XML, adicione o nome da investigação como mostrado no exemplo a seguir:

        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>

Salve o arquivo XML.

### Etapa 3
Atualize a configuração do Application Gateway com o novo arquivo XML usando **Set-AzureApplicationGatewayConfig**. Isso atualiza seu Application Gateway com a nova configuração.

    Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"


## Próximas etapas
Se você quiser configurar o descarregamento de protocolo SSL, consulte [Configurar um Application Gateway para o descarregamento SSL](application-gateway-ssl.md).

Para configurar um gateway de aplicativo para usar com um balanceador de carga interno, confira [Criar um gateway de aplicativo com um ILB (balanceador de carga interno)](application-gateway-ilb.md).

<!---HONumber=AcomDC_0907_2016-->