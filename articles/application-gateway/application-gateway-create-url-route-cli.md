---
title: "Criar um Gateway de Aplicativo usando as regras de roteamento de URL – CLI do Azure 2.0 | Microsoft Docs"
description: "Esta página oferece instruções para criar, configurar um gateway de aplicativo do Azure usando as regras de roteamento de URL"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 958049830d6753ec26635f18f8f8b2fabdec0733
ms.contentlocale: pt-br
ms.lasthandoff: 08/04/2017

---
# <a name="create-an-application-gateway-using-path-based-routing-with-azure-cli-20"></a>Criar um Gateway de Aplicativo usando roteamento com base em caminho com a CLI do Azure 2.0

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-url-route-portal.md)
> * [PowerShell do Azure Resource Manager](application-gateway-create-url-route-arm-ps.md)
> * [CLI 2.0 do Azure](application-gateway-create-url-route-cli.md)

O roteamento com base em caminho de URL permite que você associe rotas com base no caminho de URL de uma solicitação Http. Ele verifica se há uma rota para um pool de back-end configurado para a URL apresentada no Gateway de Aplicativo e envia o tráfego de rede para o pool de back-end definido. Um uso comum para o roteamento com base em URL é balancear a carga das solicitações para tipos de conteúdo diferentes para pools de servidores back-end diferentes.

O roteamento com base em URL apresenta um novo tipo de regra ao gateway de aplicativo. O gateway de aplicativo tem dois tipos de regra: básica e PathBasedRouting. O tipo de regra básica fornece o serviço de round robin para os pools de back-end, enquanto o PathBasedRouting também leva em consideração, além de distribuição round robin, o padrão de caminho da URL da solicitação ao escolher o pool de back-end.

## <a name="scenario"></a>Cenário

No exemplo a seguir, o Gateway de Aplicativo está fornecendo o tráfego para contoso.com com dois pools de servidor back-end: o pool de servidores padrão e o pool de servidores de imagem.

Solicitações de http://contoso.com/image* serão roteadas para o pool de servidores de imagem (imagesBackendPool) e, se o padrão de caminho não corresponder, um pool de servidores padrão (appGatewayBackendPool) será selecionado.

![roteamento de url](./media/application-gateway-create-url-route-cli/scenario.png)

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Abra o **Prompt de comando do Microsoft Azure**e faça logon. 

```azurecli
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

## <a name="add-a-path-based-rule-to-an-existing-application-gateway"></a>Adicionar uma regra com base em caminho a um Gateway de Aplicativo existente

Criar um Gateway de Aplicativo com uma regra com base em caminho definida

### <a name="create-a-new-back-end-pool"></a>Criar um novo pool de back-end

Defina a configuração de Gateway de Aplicativo **imagesBackendPool** para o tráfego de rede com balanceamento de carga no pool de back-end. Neste exemplo, defina as diferentes configurações de pool de back-end para o novo pool de back-end. Cada pool de back-end pode ter sua própria configuração de pool de back-end.  As configurações HTTP de back-end são usadas pelas regras para rotear o tráfego para os membros do pool de back-end corretos. Isso determina o protocolo e a porta usados ao enviar tráfego para os membros do pool de back-end. As sessões baseadas em cookies também são determinadas pelas configurações HTTP de back-end.  Se habilitada, a afinidade de sessão baseada em cookies envia o tráfego para o mesmo back-end das solicitações anteriores para cada pacote.

```azurecli-interactive
az network application-gateway address-pool create \
--gateway-name AdatumAppGateway \
--name imagesBackendPool  \
--resource-group myresourcegroup \
--servers 10.0.0.6 10.0.0.7
```

### <a name="create-a-new-front-end-port"></a>Criar uma nova porta de front-end

Configure a porta front-end para um gateway de aplicativo. O objeto de configuração de porta front-end é usado por um ouvinte para definir a porta cujo tráfego deve ser ouvido pelo Gateway de Aplicativo.

```azurecli-interactive
az network application-gateway frontend-port create --port 82 --gateway-name AdatumAppGateway --resource-group myresourcegroup --name port82
```

### <a name="create-a-new-listener"></a>Criar um novo ouvinte

Crie o ouvinte. Esta etapa configura o ouvinte para o endereço IP público, e a porta usada para receber o tráfego de rede. O exemplo a seguir usa a configuração de IP de front-end definida anteriormente, uma configuração de porta front-end e um protocolo (http ou https) e configura o ouvinte. Neste exemplo, o ouvinte ouve o tráfego HTTP na porta 82 no endereço IP público que foi criado anteriormente.

```azurecli-interactive
az network application-gateway http-listener create --name imageListener --frontend-ip appGatewayFrontendIP  --frontend-port port82 --resource-group myresourcegroup --gateway-name AdatumAppGateway
```

### <a name="create-the-url-path-map"></a>Criar o mapa de caminho de URL

Configure os caminhos de regra de URL para os pools de back-end. Esta etapa configura o caminho relativo usado pelo gateway de aplicativo para definir o mapeamento entre o caminho da URL e qual pool de back-end será atribuído para lidar com o tráfego de entrada.

> [!IMPORTANT]
> Cada caminho deve começar com /, sendo que o único lugar no qual um "\*" é permitido é no final. Exemplos válidos são /xyz, /xyz* ou /xyz/*. A cadeia de caracteres inserida no correspondente de caminho não inclui qualquer texto após o primeiro “?” ou “#”, e esses caracteres não são permitidos. 

O exemplo a seguir cria uma regra simples para o tráfego de roteamento do caminho "/images/*" para o "imagesBackendPool" do back-end. Essa regra garante que o tráfego para cada conjunto de URLs seja roteado para o back-end. Por exemplo, http://adatum.com/images/figure1.jpg vai para "imagesBackendPool". A configuração de mapa do caminho de regra também configurará um pool de endereços de back-end padrão se o caminho não corresponder a nenhuma das regras de caminho predefinidas. Por exemplo, http://adatum.com/shoppingcart/test.html vai para o pool1, já que ele está definido como o pool padrão para tráfego sem correspondência.

```azurecli-interactive
az network application-gateway url-path-map create \
--gateway-name AdatumAppGateway \
--name imagespathmap \
--paths /images/* \
--resource-group myresourcegroup2 \
--address-pool imagesBackendPool \
--default-address-pool appGatewayBackendPool \
--default-http-settings appGatewayBackendHttpSettings \
--http-settings appGatewayBackendHttpSettings \
--rule-name images
```

## <a name="next-steps"></a>Próximas etapas

Se você quiser aprender sobre o descarregamento de protocolo SSL, consulte [Configurar um Gateway de Aplicativo para descarregamento SSL](application-gateway-ssl-cli.md).


[scenario]: ./media/application-gateway-create-url-route-cli/scenario.png
[1]: ./media/application-gateway-create-url-route-cli/figure1.png
[2]: ./media/application-gateway-create-url-route-cli/figure2.png
[3]: ./media/application-gateway-create-url-route-cli/figure3.png

