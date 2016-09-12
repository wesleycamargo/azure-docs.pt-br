<properties
   pageTitle="Criar um gateway de aplicativo usando a CLI do Azure no Resource Manager | Microsoft Azure"
   description="Saiba como criar um Gateway de Aplicativo usando a CLI do Azure no Resource Manager"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/22/2016"
   ms.author="gwallace" />

# Criar um gateway de aplicativo usando a CLI do Azure

O Azure Application Gateway é um balanceador de carga de camada 7. Ele fornece o failover e solicitações HTTP de roteamento de desempenho entre diferentes servidores, estejam eles na nuvem ou no local. O Application Gateway tem os seguintes recursos de entrega de aplicativo: balanceamento de carga HTTP, afinidade de sessão baseada em cookie e descarregamento SSL (protocolo SSL), investigações de integridade personalizadas e suporte para vários sites.

> [AZURE.SELECTOR]
- [Portal do Azure](application-gateway-create-gateway-portal.md)
- [PowerShell do Azure Resource Manager](application-gateway-create-gateway-arm.md)
- [Azure Classic PowerShell](application-gateway-create-gateway.md)
- [Modelo do Azure Resource Manager ](application-gateway-create-gateway-arm-template.md)
- [CLI do Azure](application-gateway-create-gateway-cli.md)

<BR>

## Pré-requisito: instalar a CLI do Azure

Para executar as etapas deste artigo, será necessário [instalar a Interface de Linha de Comando do Azure para Mac, Linux e Windows (CLI do Azure)](../xplat-cli-install.md) e você precisará [fazer logon no Azure](../xplat-cli-connect.md).

> [AZURE.NOTE] Se você não tiver uma conta do Azure, crie uma. Inscreva-se em uma [avaliação gratuita aqui](../active-directory/sign-up-organization.md).

## Cenário

Nesse cenário, você aprenderá como criar um Application Gateway usando o portal do Azure.

Este cenário:

- Criará um Application Gateway com duas instâncias.
- Criará uma rede virtual chamada AdatumAppGatewayVNET, com um bloco CIDR 10.0.0.0/16 reservado.
- Criará uma sub-rede chamada Appgatewaysubnet que usa 10.0.0.0/28 como seu bloco CIDR.
- Configurará um certificado para descarregamento SSL.

![Cenário de exemplo][scenario]

>[AZURE.NOTE] A configuração adicional do Application Gateway, incluindo investigações de integridade personalizadas, endereços de pool de back-end e regras adicionais são configuradas após o Application Gateway ser configurado e não durante a implantação inicial.

## Antes de começar

O Azure Application Gateway requer sua própria sub-rede. Ao criar uma rede virtual, certifique-se de deixar espaço de endereço suficiente para ter várias sub-redes. Depois de implantar um gateway de aplicativo a uma sub-rede, apenas gateway de aplicativos adicionais poderão ser adicionados à sub-rede.

## Criar o grupo de recursos

Antes de criar o gateway de aplicativo, um grupo de recursos é criado para conter o gateway de aplicativo. O código a seguir mostra o comando e a saída esperada.

    azure group create -n AdatumAppGatewayRG -l eastus

    info:    Executing command group create
    + Getting resource group AdatumAppGatewayRG
    + Creating resource group AdatumAppGatewayRG
    info:    Created resource group AdatumAppGatewayRG
    data:    Id:                  /subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG
    data:    Name:                AdatumAppGatewayRG
    data:    Location:            eastus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

## Criar uma rede virtual

Após a criação do grupo de recursos, uma rede virtual é criada para o gateway de aplicativo. No exemplo a seguir, o espaço de endereço foi 10.0.0.0/16, conforme definido nas anotações do cenário anterior.

    azure network vnet create -n AdatumAppGatewayVNET -a 10.0.0.0/16 -g AdatumAppGatewayRG -l eastus

    info:    Executing command network vnet create
    + Looking up the virtual network "AdatumAppGatewayVNET"
    + Creating virtual network "AdatumAppGatewayVNET"
    data:    Id                              : /subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/virtualNetworks/AdatumAppGatewayVNET
    data:    Name                            : AdatumAppGatewayVNET
    data:    Type                            : Microsoft.Network/virtualNetworks
    data:    Location                        : eastus
    data:    Provisioning state              : Succeeded
    data:    Address prefixes:
    data:      10.0.0.0/16
    info:    network vnet create command OK

## Criar uma sub-rede

Após a criação da rede virtual, uma sub-rede é adicionada ao gateway de aplicativo. Se você planeja usar o gateway de aplicativo com um aplicativo Web hospedado na mesma rede virtual do que o gateway de aplicativo, não se esqueça de deixar espaço suficiente para outra sub-rede.

    azure network vnet subnet create -g AdatumAppGatewayRG -n Appgatewaysubnet -v AdatumAppGatewayVNET -a 10.0.0.0/28 

    info:    Executing command network vnet subnet create
    verbose: Looking up the virtual network "AdatumAppGatewayVNET"
    verbose: Looking up the subnet "Appgatewaysubnet"
    verbose: Creating subnet "Appgatewaysubnet"
    data:    Id                              : /subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/virtualNetworks/AdatumAppGatewayVNET/subnets/Appgatewaysubnet
    data:    Name                            : Appgatewaysubnet
    data:    Provisioning state              : Succeeded
    data:    Address prefix                  : 10.0.0.0/28
    info:    network vnet subnet create command OK

## Criar o gateway de aplicativo

Depois que a rede virtual e a sub-rede forem criadas, os pré-requisitos para o gateway de aplicativo estarão completos. Adicionalmente, um certificado .pfx exportado anteriormente e a senha do certificado são necessários para a etapa a seguir. Os endereços IP usados para o back-end são os endereços IP para o servidor de back-end. Eles podem ser IPs privados na rede virtual, ips públicos ou nomes de domínio totalmente qualificados para seus servidores de back-end.

    azure network application-gateway create -n AdatumAppGateway -l eastus -g AdatumAppGatewayRG -e AdatumAppGatewayVNET -m Appgatewaysubnet -r 134.170.185.46,134.170.188.221,134.170.185.50 -y c:\AdatumAppGateway\adatumcert.pfx -x P@ssw0rd

    info:    Executing command network application-gateway create
    + Looking up an application gateway "AdatumAppGateway"
    + Looking up the subnet "Appgatewaysubnet"
    warn:    Using default http listener protocol: https
    warn:    Using default gateway ip name: ipConfig01
    warn:    Using default sku name: Standard_Medium
    warn:    Using default sku tier: Standard
    warn:    Using default sku capacity: 2
    warn:    Using default frontend ip name: frontendIp01
    warn:    Using default frontend port name: frontendPort01
    warn:    Using default frontend port: 443
    warn:    Using default address pool name: pool01
    warn:    Using default http settings name: httpSettings01
    warn:    Using default http settings protocol: http
    warn:    Using default http settings port: 80
    warn:    Using default http settings cookie based affinity: Disabled
    warn:    Using default http listener name: listener01
    warn:    Using default request routing rule name: rule01
    warn:    Using default request routing rule type: Basic
    + Looking up the subnet "Appgatewaysubnet"
    + Creating configuration for an application gateway "AdatumAppGateway"
    data:    Id                              : /subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway
    data:    Name                            : AdatumAppGateway
    data:    Location                        : eastus
    data:    Provisioning state              : Succeeded
    data:    Sku                             : Standard_Medium
    data:    Resource Group                  : AdatumAppGatewayRG
    data:    Gateway IP configations         : [ipConfig01]
    data:    SSL cerificates                 : [cert01]
    data:    Frontend ip configurations      : [frontendIp01]
    data:    Frontend ports                  : [frontendPort01]
    data:    Backend address pools           : [pool01]
    data:    Backend http settings           : [httpSettings01]
    data:    Http listeners                  : [listener01]
    data:    Request routing rules           : [rule01]
    data:    Probes                          : []
    data:    Url Path Maps                   : []
    data:    GatewayIpConfigurationText      : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/gatewayIPConfigurations/ipConfig01",
            "subnet": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/virtualNetworks/AdatumAppGatewayVNET/subnets/Appgatewaysubnet"
            },
            "provisioningState": "Succeeded",
            "name": "ipConfig01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    SslCertificateText              : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/sslCertificates/cert01",
            "publicCertData": "MIIDHjCCAgagAwIBAgIQE8ZZuidyb59IscYNJPYTvDANBgkqhkiG9w0BAQsFADA4MTYwNAYDVQQDEy1NU0ZULUdXQUxMU1A0Lm5vcnRoYW1lcmljYS5jb3JwLm1pY3Jvc29mdC5jb20wHhcNMTYwNzI4MTUyNzIwWhcNMTcwNzI4MDAwMDAwWjA4MTYwNAYDVQQDEy1NU0ZULUdXQUxMU1A0Lm5vcnRoYW1lcmljYS5jb3JwLm1pY3Jvc29mdC5jb20wggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDC+pkArA1y9C10AOPWISHEThbNqvuRA+MaVDFUOu15NB9M7+0PUx5pyGlbW+1MuXb9acia/KXF9WxpYUMbQt8t8p1S5HsV4oKGdEOpdR0d7dozyPrkNgBgYvqRzDQ6R5VuK/uLq9oWpjPkqNoQeYR1wr7f/SNsIA4YsaDqqAi62ET6cvg1wN/VRXbWyi9wLeon7g6fZiCrFZspTUiSyqrRQx7sO0e/bqV7nKgSWmaqo4jLoUAqJBBCUJryDaTNfkFO4VEdnsQLN+PSGO8HwSZPJOzG1V6+MynmaGCKaTJE2UCxtLIJQHBhmES+X/BoinrIsjNVxsKqWFMv/mV7M2GBAgMBAAGjJDAiMAsGA1UdDwQEAwIEMDATBgNVHSUEDDAKBggrBgEFBQcDATANBgkqhkiG9w0BAQsFAAOCAQEAa2XLrwQJwX2ZmVN0MR+/+jWTED134wgoIKw6Ni30ukF9U936FsuvFcEjPr4vBp82cjnz76BjLNhyw/MxAHP7tTaguxzHgUHP9X9fmtcsLEUD74/D5BPmnpl+4cJ/BZMdyzIsuyyPSsDxkVN/W70ykOVTJeAb1ycwfJCllgLgkZcLVgTcMMAJYSttfWn9e1dhTUIlTIYKzD669emFvdBHi+sdTT1HGrZenpkT5oK+H6/5wIV7/DW+C+pqvXCsK0XSeYWW7KuBk5MpD8829HeCvV0rBSf538nYLwUUVUUMNHuTp5QXzouAtHOWyvo00/xRi+aDeq0NfUvTv2iS2BS/Ow==",
            "provisioningState": "Succeeded",
            "name": "cert01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    FrontendIpConfigurationText     : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/frontendIPConfigurations/frontendIp01",
            "privateIPAddress": "10.0.0.6",
            "privateIPAllocationMethod": "Dynamic",
            "subnet": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/virtualNetworks/AdatumAppGatewayVNET/subnets/Appgatewaysubnet"
            },
            "provisioningState": "Succeeded",
            "name": "frontendIp01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    FrontendPortText                : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/frontendPorts/frontendPort01",
            "port": 443,
            "provisioningState": "Succeeded",
            "name": "frontendPort01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    BackendAddressPoolText          : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/backendAddressPools/pool01",
            "backendAddresses": [
                {
                    "ipAddress": "134.170.185.46"
                },
                {
                    "ipAddress": "134.170.188.221"
                },
                {
                    "ipAddress": "134.170.185.50"
                }
            ],
            "provisioningState": "Succeeded",
            "name": "pool01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    BackendHttpSettingsText         : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/backendHttpSettingsCollection/httpSettings01",
            "port": 80,
            "protocol": "Http",
            "cookieBasedAffinity": "Disabled",
            "requestTimeout": 30,
            "provisioningState": "Succeeded",
            "name": "httpSettings01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    HttpListenersText               : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/httpListeners/listener01",
            "frontendIPConfiguration": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/frontendIPConfigurations/frontendIp01"
            },
            "frontendPort": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/frontendPorts/frontendPort01"
            },
            "protocol": "Https",
            "sslCertificate": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/sslCertificates/cert01"
            },
            "requireServerNameIndication": false,
            "provisioningState": "Succeeded",
            "name": "listener01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    RequestRoutingRulesText         : [
        {
            "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/requestRoutingRules/rule01",
            "ruleType": "Basic",
            "backendAddressPool": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/backendAddressPools/pool01"
            },
            "backendHttpSettings": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/backendHttpSettingsCollection/httpSettings01"
            },
            "httpListener": {
                "id": "/subscriptions/<subscription id>/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway/httpListeners/listener01"
            },
            "provisioningState": "Succeeded",
            "name": "rule01",
            "etag": "W/"836418e5-6728-43d7-8dcb-2ae131414bcc""
        }
    ]
    data:    SkuText                         : {
        "name": "Standard_Medium",
        "tier": "Standard",
        "capacity": 2
    }
    data:    ProbesText                      : []
    data:    UrlPathMapsText                 : []
    info:    network application-gateway create command OK

Isso cria um Application Gateway básico com configurações padrão para o ouvinte, pool de back-end, configurações de http de back-end e regras. Ele também configura o descarregamento de SSL. Você pode modificar essas configurações de acordo com sua implantação quando o provisionamento for bem-sucedido. Se você já tiver o seu aplicativo Web definido com os endereços IP para o pool de back-end definido nas etapas anteriores, quando o gateway de aplicativo for provisionado e iniciado, o balanceamento de carga será iniciado.

## Próximas etapas

Saiba como criar investigações de integridade personalizados visitando [Criar uma investigação de integridade personalizada](application-gateway-create-probe-portal.md)

Saiba como configurar o Descarregamento de SSL e levar a descriptografia SSL cara longe dos seus servidores Web visitando [Configurar Descarregamento de SSL](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png

<!---HONumber=AcomDC_0831_2016-->