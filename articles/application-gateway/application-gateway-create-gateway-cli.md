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
ms.date: 05/03/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 14dab2197ff7c1eaff012066e321ef1b99f05bb3
ms.contentlocale: pt-br
ms.lasthandoff: 05/02/2017


---
<a id="create-an-application-gateway-by-using-the-azure-cli-20" class="xliff"></a>
# Criar um gateway de aplicativo usando a CLI do Azure 2.0

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-gateway-portal.md)
> * [PowerShell do Azure Resource Manager](application-gateway-create-gateway-arm.md)
> * [Azure Classic PowerShell](application-gateway-create-gateway.md)
> * [Modelo do Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
> * [CLI 1.0 do Azure](application-gateway-create-gateway-cli.md)
> * [CLI 2.0 do Azure](application-gateway-create-gateway-cli.md)

O Azure Application Gateway é um balanceador de carga de camada 7. Ele fornece o failover e solicitações HTTP de roteamento de desempenho entre diferentes servidores, estejam eles na nuvem ou no local. O Application Gateway tem os seguintes recursos de entrega de aplicativo: balanceamento de carga HTTP, afinidade de sessão baseada em cookie e descarregamento SSL (protocolo SSL), investigações de integridade personalizadas e suporte para vários sites.

<a id="cli-versions-to-complete-the-task" class="xliff"></a>
## Versões da CLI para concluir a tarefa

Você pode concluir a tarefa usando uma das seguintes versões da CLI:

* [CLI do Azure 1.0](application-gateway-create-gateway-cli-nodejs.md) – nossa CLI para os modelos de implantação clássico e do resource manager.
* [CLI do Azure 2.0](application-gateway-create-gateway-cli.md) – nossa próxima geração de CLI para o modelo de implantação do resource manager

<a id="prerequisite-install-the-azure-cli-20" class="xliff"></a>
## Pré-requisito: instalar a CLI do Azure 2.0

Para executar as etapas deste artigo, será necessário [instalar a Interface de Linha de Comando do Azure para Mac, Linux e Windows (CLI do Azure)](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

> [!NOTE]
> Se você não tiver uma conta do Azure, crie uma. Inscreva-se em uma [avaliação gratuita aqui](../active-directory/sign-up-organization.md).

<a id="scenario" class="xliff"></a>
## Cenário

Nesse cenário, você aprenderá como criar um Application Gateway usando o portal do Azure.

Este cenário:

* Criará um Application Gateway com duas instâncias.
* Criará uma rede virtual chamada AdatumAppGatewayVNET, com um bloco CIDR 10.0.0.0/16 reservado.
* Criará uma sub-rede chamada Appgatewaysubnet que usa 10.0.0.0/28 como seu bloco CIDR.
* Configurará um certificado para descarregamento SSL.

![Cenário de exemplo][scenario]

> [!NOTE]
> A configuração adicional do Application Gateway, incluindo investigações de integridade personalizadas, endereços de pool de back-end e regras adicionais são configuradas após o Application Gateway ser configurado e não durante a implantação inicial.

<a id="before-you-begin" class="xliff"></a>
## Antes de começar

O Azure Application Gateway requer sua própria sub-rede. Ao criar uma rede virtual, certifique-se de deixar espaço de endereço suficiente para ter várias sub-redes. Depois de implantar um gateway de aplicativo a uma sub-rede, apenas gateway de aplicativos adicionais poderão ser adicionados à sub-rede.

<a id="log-in-to-azure" class="xliff"></a>
## Fazer logon no Azure

Abra o **Prompt de comando do Microsoft Azure**e faça logon. 

```azurecli
az login -u "username"
```

> [!NOTE]
> Você também pode usar `az login` sem a opção de logon de dispositivo que exija a inserção de um código em aka.ms/devicelogin.

Depois de digitar o exemplo anterior, um código será fornecido. Navegue até https://aka.ms/devicelogin em um navegador para continuar o processo de logon.

![cmd mostrando logon de dispositivo][1]

No navegador, digite o código recebido. Você será redirecionado para uma página de entrada.

![navegador para inserir código][2]

Depois que o código foi inserido, você estará conectado. Feche o navegador para continuar com o cenário.

![conectado com êxito][3]

<a id="create-the-resource-group" class="xliff"></a>
## Criar o grupo de recursos

Antes de criar o gateway de aplicativo, um grupo de recursos é criado para conter o gateway de aplicativo. O código a seguir mostra o comando.

```azurecli
az resource group create --name myresourcegroup --location "West US"
```

<a id="create-a-virtual-network-and-subnet" class="xliff"></a>
## Criar a rede virtual e a sub-rede

Após a criação do grupo de recursos, uma rede virtual é criada para o Gateway de Aplicativo.  No exemplo a seguir, o espaço de endereço foi 10.0.0.0/16 é definida para a rede virtual e 10.0.0.0/28 é usado para a sub-rede conforme mostrado nas notas para o cenário anterior.

```azurecli
az network vnet create \
--name AdatumAppGatewayVNET \
--address-prefix 10.0.0.0/16 \
--subnet-name Appgatewaysubnet \
--subnet-prefix 10.0.0.0/28 \
--resource-group AdatumAppGateway \
--location eastus
```

<a id="create-the-application-gateway" class="xliff"></a>
## Criar o gateway de aplicativo

Depois que a rede virtual e a sub-rede forem criadas, os pré-requisitos para o gateway de aplicativo estarão completos. Além disso, um certificado .pfx exportado anteriormente e a senha do certificado são necessários para a etapa seguinte: os endereços IP usados para o back-end são os endereços IP para seu servidor de back-end. Esses valores podem ser IPs privados na rede virtual, ips públicos ou nomes de domínio totalmente qualificados para seus servidores de back-end.

```azurecli
az network application-gateway create \
--name AdatumAppGateway \
--location eastus \
--resource-group AdatumAppGatewayRG \
--vnet-name AdatumAppGatewayVNET \
---vnet-address-prefix 10.0.0.0/16 \
--subnet Appgatewaysubnet \
---subnet-address-prefix 10.0.0.0/28 \
--servers 10.0.0.4 10.0.0.5 \
--cert-file /mnt/c/Users/username/Desktop/application-gateway/fabrikam.pfx \
--cert-password P@ssw0rd \
--capacity 2 \
--sku Standard_Small \
--http-settings-cookie-based-affinity Enabled \
--http-settings-protocol Http \
--public-ip-address AdatumAppGatewayPIP \
--frontend-port 443 \
--routing-rule-type Basic \
--http-settings-port 80

```

> [!NOTE]
> Para obter uma lista de parâmetros que podem ser fornecidos durante a criação, execute o seguinte comando: **az network application-gateway create --help**.

Este exemplo cria um Application Gateway básico com configurações padrão para o ouvinte, pool de back-end, configurações de http de back-end e regras. Ele também configura o descarregamento de SSL. Você pode modificar essas configurações de acordo com sua implantação quando o provisionamento for bem-sucedido.
Se você já tiver seu aplicativo Web definido com o pool de back-end nas etapas anteriores, o balanceamento de carga começará depois que ele for criado.

<a id="delete-all-resources" class="xliff"></a>
## Excluir todos os recursos

Para excluir todos os recursos criados neste artigo, conclua as seguintes etapas:

```azurecli
az group delete --name AdatumAppGatewayRG
```
 
<a id="next-steps" class="xliff"></a>
## Próximas etapas

Saiba como criar investigações de integridade personalizados visitando [Criar uma investigação de integridade personalizada](application-gateway-create-probe-portal.md)

Saiba como configurar o Descarregamento de SSL e levar a descriptografia SSL cara longe dos seus servidores Web visitando [Configurar Descarregamento de SSL](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png

