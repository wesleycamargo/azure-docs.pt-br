<properties
   pageTitle="Visão geral de roteamento de conteúdo baseado em URL | Microsoft Azure"
   description="Esta página fornece uma visão geral do roteamento de conteúdo baseado em URL do Application Gateway, configuração de UrlPathMap e regra de PathBasedRouting."
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/21/2016"
   ms.author="joaoma"/>

# Visão geral do Roteamento Baseado em Caminho de URL

O Roteamento Baseado em Caminho de URL permite rotear o tráfego para pools do servidor de back-end com base nos Caminhos de URL da solicitação. Um cenário possível é rotear as solicitações para tipos de conteúdo diferentes para pools de servidores de back-end diferentes. No exemplo a seguir, o Application Gateway está atendendo o tráfego de contoso.com de três pools de servidor de back-end, como por exemplo: VideoServerPool, ImageServerPool e DefaultServerPool.

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

Solicitações de http://contoso.com/video* serão roteadas para VideoServerPool e http://contoso.com/images* para ImageServerPool. O DefaultServerPool será selecionado se nenhum dos padrões de caminho forem compatíveis.

## Elemento de configuração UrlPathMap

O elemento UrlPathMap é usado para especificar padrões de Caminho para mapeamentos de pool do servidor de back-end. Esse é o trecho de código do elemento urlPathMap do arquivo de modelo.

	"urlPathMaps": [
	{
    "name": "<urlPathMapName>",
    "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/ urlPathMaps/<urlPathMapName>",
    "properties": {
        "defaultBackendAddressPool": {
            "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendAddressPools/<poolName>"
        },
        "defaultBackendHttpSettings": {
            "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendHttpSettingsList/<settingsName>"
        },
        "pathRules": [
            {
                "paths": [
                    <pathPattern>
                ],
                "backendAddressPool": {
                    "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendAddressPools/<poolName2>"
                },
                "backendHttpsettings": {
                    "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendHttpsettingsList/<settingsName2>"
                },

            },

        ],

    }
	}
	

>[AZURE.NOTE] PathPattern: está é uma lista de padrões de caminho para correspondência. Cada um deve começar com / e o único lugar no qual um * é permitido é no final após um “/”. A cadeia de caracteres inserida no correspondente de caminho não inclui nenhum texto após o primeiro ? ou #, e esses caracteres não são permitidos aqui.

Você pode conferir um [modelo de ARM usando o roteamento baseado em URL](https://azure.microsoft.com/documentation/templates/201-application-gateway-url-path-based-routing) para obter mais informações.

## Regra de PathBasedRouting

RequestRoutingRule do tipo PathBasedRouting é usada para associar um ouvinte a um urlPathMap. Todas as solicitações que são recebidas por este ouvinte são roteadas com base na política especificada no urlPathMap. Trecho de código da regra de PathBasedRouting:

	"requestRoutingRules": [
  	{

    "name": "<ruleName>",
    "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/requestRoutingRules/<ruleName>",
    "properties": {
        "ruleType": "PathBasedRouting",
        "httpListener": {
            "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/httpListeners/<listenerName>"
        },
        "urlPathMap": {
            "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/ urlPathMaps/<urlPathMapName>"
        },

    }
	
## Próximas etapas 

Depois de conhecer o roteamento de conteúdo baseado em URL, acesse [criar um application gateway usando o roteamento baseado em URL](application-gateway-create-url-route-arm-ps.md) para criar um application gateway com as regras de roteamento de URL.

<!---HONumber=AcomDC_0224_2016-->