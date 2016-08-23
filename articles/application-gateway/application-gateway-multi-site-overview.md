<properties
   pageTitle="Hospedando vários sites no Application Gateway | Microsoft Azure"
   description="Esta página fornece uma visão geral do suporte a vários sites do Application Gateway."
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/07/2016"
   ms.author="amsriva"/>

# Hospedagem de vários sites do Application Gateway

A hospedagem de vários sites permite que você configure mais de um aplicativo Web na mesma instância do Application Gateway. Isso permite que você configure a topologia mais eficiente para suas implantações empacotando até 20 sites para um Application Gateway. Cada site pode ser direcionado para seu próprio pool de back-end. No exemplo a seguir, o Application Gateway está fornecendo o tráfego para contoso.com e fabrikam.com de dois pools de servidores de back-end chamados ContosoServerPool e FabrikamServerPool.

![imageURLroute](./media/application-gateway-multi-site-overview/multisite.png)

Solicitações de http://contoso.com serão roteadas para ContosoServerPool e http://fabrikam.com para FabrikamServerPool.

Da mesma forma, dois subdomínios do mesmo domínio pai podem ser hospedados na mesma implantação do Application Gateway. Exemplos disso incluem http://blog.contoso.com e http://app.contoso.com hospedados em uma implantação do Application Gateway.


##Cabeçalhos de host e SNI (Indicação de Nome de Servidor)
Existem três mecanismos comuns para habilitar a hospedagem de vários sites na mesma infraestrutura.

1. Hospede vários aplicativos Web, cada um em um endereço IP exclusivo.
2. Use o nome de host para hospedar vários aplicativos Web no mesmo endereço IP.
3. Use portas diferentes para hospedar vários aplicativos Web no mesmo endereço IP.

No momento, um Application Gateway obtém um único endereço IP público que escuta o tráfego. Portanto, o suporte a vários aplicativos, cada um com seu próprio endereço IP, não tem suporte no momento. O Application Gateway dá suporte à hospedagem de vários aplicativos, cada um ouvindo em portas diferentes, mas isso exige que os aplicativos aceitem o tráfego em portas não padrão e normalmente não é uma configuração desejada. O Application Gateway depende de cabeçalhos de host HTTP 1.1 para hospedar mais de um site na mesma porta e endereço IP público. Os sites hospedados no Application Gateway também podem dar suporte ao descarregamento SSL com a extensão TLS de SNI (Indicação de Nome de Servidor). Isso significa que o farm da Web de back-end e o navegador cliente devem dar suporte à extensão TLS e HTTP/1.1 conforme definido no RFC 6066.

  

## Elemento de configuração do ouvinte

O elemento de configuração HTTPListener existente é aprimorado para dar suporte aos elementos de indicação de nome de servidor e nome de host que são usados pelo Application Gateway para rotear o tráfego para o pool de back-end adequado. Esse é o trecho de código do elemento HttpListeners do arquivo de modelo.

 		"httpListeners": [
                    {
                        "name": "appGatewayHttpsListener1",
                        "properties": {
                            "FrontendIPConfiguration": {
                                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/DefaultFrontendPublicIP"
                            },
                            "FrontendPort": {
                                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort443'"
                            },
                            "Protocol": "Https",
                            "SslCertificate": {
                                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/sslCertificates/appGatewaySslCert1'"
                            },
                            "HostName": "contoso.com",
                            "RequireServerNameIndication": "true"
                        }
                    },
                    {
                        "name": "appGatewayHttpListener2",
                        "properties": {
                            "FrontendIPConfiguration": {
                                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
                            },
                            "FrontendPort": {
                                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
                            },
                            "Protocol": "Http",
                            "HostName": "fabrikam.com",
                            "RequireServerNameIndication": "false"
                        }
                    }
                ],

	


Você pode consultar o [modelo ARM usando a hospedagem de vários sites](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting) para encontrar um modelo de ponta a ponta com base em implantação.

## Regra de roteamento

Não há nenhuma alteração necessária na regra de roteamento. A regra de roteamento 'Básica' deve continuar a ser escolhido para ligar o ouvinte do site apropriado ao pool de endereços de back-end correspondente.

	"requestRoutingRules": [
	{
		"name": "<ruleName1>",
		"properties": {
			"RuleType": "Basic",
			"httpListener": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener1')]"
			},
			"backendAddressPool": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
			},
			"backendHttpSettings": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
			}
		}

	},
	{
		"name": "<ruleName2>",
		"properties": {
			"RuleType": "Basic",
			"httpListener": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener2')]"
			},
			"backendAddressPool": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/FabrikamServerPool')]"
			},
			"backendHttpSettings": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
			}
		}

	}
	]
	
## Próximas etapas 

Depois de conhecer várias opções de hospedagem de site, vá para [criar um Application Gateway usando a hospedagem de vários sites](application-gateway-create-multisite-azureresourcemanager-powershell.md) para criar um Application Gateway com capacidade de dar suporte a mais de um aplicativo Web.

<!---HONumber=AcomDC_0810_2016-->