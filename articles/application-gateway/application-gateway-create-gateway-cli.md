---
title: Criar um Gateway de Aplicativo do Azure - CLI do Azure 2.0 | Microsoft Docs
description: Saiba como criar um Gateway de Aplicativo usando a CLI do Azure 2.0 no Resource Manager
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c2f6516e-3805-49ac-826e-776b909a9104
ms.service: application-gateway
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 052410db8c7619c7990dc319951a55663f2c2ba1
ms.contentlocale: pt-br
ms.lasthandoff: 08/04/2017

---
# <a name="create-an-application-gateway-by-using-the-azure-cli-20"></a>Criar um gateway de aplicativo usando a CLI do Azure 2.0

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-gateway-portal.md)
> * [PowerShell do Azure Resource Manager](application-gateway-create-gateway-arm.md)
> * [Azure Classic PowerShell](application-gateway-create-gateway.md)
> * [Modelo do Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
> * [CLI 1.0 do Azure](application-gateway-create-gateway-cli.md)
> * [CLI 2.0 do Azure](application-gateway-create-gateway-cli.md)

O Gateway de Aplicativo é uma solução de virtualização dedicada que fornece o ADC (controlador de entrega de aplicativos) como serviço, oferecendo várias funcionalidades de balanceamento de carga de camada 7 para o aplicativo.

## <a name="cli-versions-to-complete-the-task"></a>Versões da CLI para concluir a tarefa

Você pode concluir a tarefa usando uma das seguintes versões da CLI:

* [CLI do Azure 1.0](application-gateway-create-gateway-cli-nodejs.md) – nossa CLI para os modelos de implantação clássico e do resource manager.
* [CLI do Azure 2.0](application-gateway-create-gateway-cli.md) – nossa próxima geração de CLI para o modelo de implantação do resource manager

## <a name="prerequisite-install-the-azure-cli-20"></a>Pré-requisito: instalar a CLI do Azure 2.0

Para executar as etapas deste artigo, será necessário [instalar a Interface de Linha de Comando do Azure para Mac, Linux e Windows (CLI do Azure)](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

> [!NOTE]
> Se você não tiver uma conta do Azure, crie uma. Inscreva-se em uma [avaliação gratuita aqui](../active-directory/sign-up-organization.md).

## <a name="scenario"></a>Cenário

Nesse cenário, você aprenderá como criar um Gateway de Aplicativo usando o portal do Azure.

Este cenário:

* Criará um Gateway de Aplicativo com duas instâncias.
* Criará uma rede virtual chamada AdatumAppGatewayVNET, com um bloco CIDR 10.0.0.0/16 reservado.
* Criará uma sub-rede chamada Appgatewaysubnet que usa 10.0.0.0/28 como seu bloco CIDR.

> [!NOTE]
> A configuração adicional do Gateway de Aplicativo, incluindo investigações de integridade personalizadas, endereços de pool de back-end e regras adicionais são configuradas após o Gateway de Aplicativo ser configurado e não durante a implantação inicial.

## <a name="before-you-begin"></a>Antes de começar

O Gateway de Aplicativo do Azure requer sua própria sub-rede. Ao criar uma rede virtual, certifique-se de deixar espaço de endereço suficiente para ter várias sub-redes. Depois de implantar um Gateway de Aplicativo a uma sub-rede, apenas Gateways de Aplicativo adicionais podem ser adicionados à sub-rede.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Abra o **Prompt de comando do Microsoft Azure**e faça logon. 

```azurecli-interactive
az login -u "username"
```

> [!NOTE]
> Você também pode usar `az login` sem a opção de logon de dispositivo que exige a inserção de um código em aka.ms/devicelogin.

Depois de digitar o exemplo anterior, um código será fornecido. Navegue até https://aka.ms/devicelogin em um navegador para continuar o processo de logon.

![cmd mostrando logon de dispositivo][1]

No navegador, digite o código recebido. Você será redirecionado para uma página de entrada.

![navegador para inserir código][2]

Depois que o código foi inserido, você estará conectado. Feche o navegador para continuar com o cenário.

![conectado com êxito][3]

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Antes de criar o gateway de aplicativo, um grupo de recursos é criado para conter o gateway de aplicativo. O código a seguir mostra o comando.

```azurecli-interactive
az group create --name myresourcegroup --location "eastus"
```

## <a name="create-the-application-gateway"></a>Criar o gateway de aplicativo

Os endereços IP usados para o back-end são os endereços IP para o servidor de back-end. Esses valores podem ser IPs privados na rede virtual, ips públicos ou nomes de domínio totalmente qualificados para seus servidores de back-end. O exemplo a seguir cria um Gateway de Aplicativo com definições de configuração adicionais para as configurações HTTP, de portas e de regras.

```azurecli-interactive
az network application-gateway create \
--name "AdatumAppGateway" \
--location "eastus" \
--resource-group "myresourcegroup" \
--vnet-name "AdatumAppGatewayVNET" \
--vnet-address-prefix "10.0.0.0/16" \
--subnet "Appgatewaysubnet" \
--subnet-address-prefix "10.0.0.0/28" \
--servers 10.0.0.4 10.0.0.5 \
--capacity 2 \
--sku Standard_Small \
--http-settings-cookie-based-affinity Enabled \
--http-settings-protocol Http \
--frontend-port 80 \
--routing-rule-type Basic \
--http-settings-port 80 \
--public-ip-address "pip2" \
--public-ip-address-allocation "dynamic" \

```

O exemplo anterior mostra várias propriedades que não são necessárias durante a criação de um Gateway de Aplicativo. O exemplo de código a seguir cria um Gateway de Aplicativo com as informações necessárias.

```azurecli-interactive
az network application-gateway create \
--name "AdatumAppGateway" \
--location "eastus" \
--resource-group "myresourcegroup" \
--vnet-name "AdatumAppGatewayVNET" \
--vnet-address-prefix "10.0.0.0/16" \
--subnet "Appgatewaysubnet \
--subnet-address-prefix "10.0.0.0/28" \
--servers "10.0.0.5"  \
--public-ip-address pip
```
 
> [!NOTE]
> Para obter uma lista de parâmetros que podem ser fornecidos durante a criação, execute o seguinte comando: `az network application-gateway create --help`.

Este exemplo cria um Gateway de Aplicativo básico com configurações padrão para o ouvinte, pool de back-end, configurações de http de back-end e regras. Você pode modificar essas configurações de acordo com sua implantação quando o provisionamento for bem-sucedido.
Se você já tiver seu aplicativo Web definido com o pool de back-end nas etapas anteriores, o balanceamento de carga começará depois que ele for criado.

## <a name="get-application-gateway-dns-name"></a>Obter um nome DNS de Gateway de Aplicativo

Depois de criar o gateway, a próxima etapa será configurar o front-end para comunicação. Ao usar um IP público, o gateway de aplicativo requer um nome DNS atribuído dinamicamente, o que não é amigável. Para garantir que os usuários finais possam alcançar o gateway de aplicativo, um registro CNAME pode ser usado para apontar para o ponto de extremidade público do gateway de aplicativo. [Configurando um nome de domínio personalizado no Azure](../dns/dns-custom-domain.md). Para configurar um alias, recupere os detalhes do Gateway de Aplicativo e seu nome DNS/IP associado usando o elemento PublicIPAddress anexado ao Gateway de Aplicativo. O nome DNS do Gateway de Aplicativo deve ser usado para criar um registro CNAME que aponta os dois aplicativos Web para esse nome DNS. O uso de registros A não é recomendável, pois o VIP pode mudar na reinicialização do gateway de aplicativo.


```azurecli-interactive
az network public-ip show --name "pip" --resource-group "AdatumAppGatewayRG"
```

```
{
  "dnsSettings": {
    "domainNameLabel": null,
    "fqdn": "8c786058-96d4-4f3e-bb41-660860ceae4c.cloudapp.net",
    "reverseFqdn": null
  },
  "etag": "W/\"3b0ac031-01f0-4860-b572-e3c25e0c57ad\"",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/publicIPAddresses/pip2",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "40.121.167.250",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway2/frontendIPConfigurations/appGatewayFrontendIP",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "AdatumAppGatewayRG",
    "subnet": null
  },
  "location": "eastus",
  "name": "pip2",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "AdatumAppGatewayRG",
  "resourceGuid": "3c30d310-c543-4e9d-9c72-bbacd7fe9b05",
  "tags": {
    "cli[2] owner[administrator]": ""
  },
  "type": "Microsoft.Network/publicIPAddresses"
}
```

## <a name="delete-all-resources"></a>Excluir todos os recursos

Para excluir todos os recursos criados neste artigo, conclua as seguintes etapas:

```azurecli-interactive
az group delete --name AdatumAppGatewayRG
```
 
## <a name="next-steps"></a>Próximas etapas

Saiba como criar investigações de integridade personalizados visitando [Criar uma investigação de integridade personalizada](application-gateway-create-probe-portal.md)

Saiba como configurar o Descarregamento de SSL e levar a descriptografia SSL cara longe dos seus servidores Web visitando [Configurar Descarregamento de SSL](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png

