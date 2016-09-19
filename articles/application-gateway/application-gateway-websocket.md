<properties
   pageTitle="Suporte a WebSocket do Gateway de Aplicativo | Microsoft Azure"
   description="Esta página oferece uma visão geral do suporte ao WebSocket do Application Gateway."
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
   ms.date="08/18/2016"
   ms.author="amsriva"/>

# Suporte ao WebSocket do Gateway de Aplicativo

O protocolo WebSocket padronizado na [RFC6455](https://tools.ietf.org/html/rfc6455) permite uma comunicação full-duplex entre servidor e cliente em uma conexão TCP de execução longa. Isso permite uma comunicação mais interativa entre o servidor Web e o cliente, que pode ser bidirecional sem a necessidade de sondagem como necessário nas implementações baseadas em HTTP. O WebSocket tem baixa sobrecarga, ao contrário do HTTP, e pode reutilizar a mesma conexão TCP para várias solicitações/respostas resultando em uma utilização mais eficiente de recursos. Os protocolos WebSocket são projetados para funcionar em portas HTTP tradicionais de 80 e 443.


O Gateway de Aplicativo fornece suporte nativo a WebSocket em todos os tamanhos de gateway. Não há nenhuma configuração configurada pelo usuário para habilitar ou desabilitar seletivamente o suporte ao WebSocket. Você pode continuar a usar um HTTPListener padrão na porta 80/443 para receber o tráfego WebSocket. O tráfego WebSocket será direcionado para o servidor de back-end habilitado para WebSocket usando o pool de back-end apropriado conforme especificado nas regras do gateway de aplicativo.

O servidor back-end deve responder a investigações de gateway de aplicativo descritas na seção [visão geral de investigação de integridade](application-gateway-probe-overview.md). As investigações de integridade de gateway de aplicativo são somente HTTP/HTTPS e isso significa que todos os servidores de back-end devem responder a investigações HTTP para que o gateway de aplicativo seja capaz de rotear o tráfego WebSocket para o servidor.


## Elemento de configuração do ouvinte

O HTTPListener existente pode ser usado para dar suporte ao WebSocket. A seguir, um trecho do elemento HttpListeners de um arquivo de modelo de exemplo. Você precisaria de ouvintes HTTP e HTTPS para oferecer suporte a tráfego WebSocket e WebSocket seguro. Da mesma forma, você pode usar [portal](application-gateway-create-gateway-portal.md) ou [powershell](application-gateway-create-gateway-arm.md) para criar um gateway de aplicativo com ouvintes na porta 80/443 para dar suporte ao tráfego WebSocket.


 		"httpListeners": [
                    {
                        "name": "appGatewayHttpsListener",
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
                        }
                    },
                    {
                        "name": "appGatewayHttpListener",
                        "properties": {
                            "FrontendIPConfiguration": {
                                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
                            },
                            "FrontendPort": {
                                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
                            },
                            "Protocol": "Http",
                        }
                    }
                ],

## Configuração de regra BackendAddressPool, BackendHttpSetting e Routing

BackendAddressPool deve ser usado para definir um pool de back-end com servidores habilitados para WebSocket. BackendHttpSetting deve ser definida somente com a porta 80/443 de back-end. As propriedades de afinidade baseada em cookie e requestTimeouts não são relevantes para o tráfego WebSocket. Não há nenhuma alteração necessária na regra de roteamento. A regra de roteamento 'Básica' deve continuar a ser usada para ligar o ouvinte apropriado ao pool de endereços de back-end correspondente.

	"requestRoutingRules": [{
		"name": "<ruleName1>",
		"properties": {
			"RuleType": "Basic",
			"httpListener": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener')]"
			},
			"backendAddressPool": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
			},
			"backendHttpSettings": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
			}
		}

	}, {
		"name": "<ruleName2>",
		"properties": {
			"RuleType": "Basic",
			"httpListener": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener')]"
			},
			"backendAddressPool": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
			},
			"backendHttpSettings": {
				"id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
			}

		}
	}]

## Back-end habilitado para WebSocket
O back-end deve ter um servidor Web HTTP/HTTPS em execução na porta configurada (normalmente 80/443) para que o WebSocket funcione. Isso ocorre porque o protocolo WebSocket exige que o handshake inicial deve ser HTTP com a atualização para o protocolo WebSocket como um campo de cabeçalho.

	GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: http://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13

Outro motivo é que investigação de integridade de back-end de gateway de aplicativo oferece suporte apenas aos protocolos HTTP/HTTPS. Se o servidor back-end não responder às investigações HTTP/HTTPS, ele será retirado do pool de back-end e nenhuma solicitação, incluindo as solicitações de WebSocket, seria capaz de acessar esse back-end.

	
## Próximas etapas 

Depois de aprender sobre o suporte ao WebSocket, vá para [criar um gateway de aplicativo](application-gateway-create-gateway.md) para começar a usar um aplicativo Web habilitado para WebSocket.

	    

<!---HONumber=AcomDC_0907_2016-->