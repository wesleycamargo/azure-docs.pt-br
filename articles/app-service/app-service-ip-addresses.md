---
title: Endereços IP no Serviço de Aplicativo do Azure | Microsoft Docs
description: Descreve como os endereços IP de entrada e saída são usados no Serviço de Aplicativo e como encontrar informações sobre esses endereços no aplicativo.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: cephalin
ms.openlocfilehash: 906a5d511615c57b6ff807ac240a838c63917e66
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2018
ms.locfileid: "31788072"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Endereços IP de entrada e saída no Serviço de Aplicativo do Azure

O [Serviço de Aplicativo do Azure](app-service-web-overview.md) é um serviço multilocatário, exceto para [Ambientes de Serviço de Aplicativo](environment/intro.md). Aplicativos que não estão em um ambiente de Serviço de Aplicativo (não na [Camada isolada](https://azure.microsoft.com/pricing/details/app-service/)) compartilham a infraestrutura de rede com outros aplicativos. Como resultado, os endereços IP de entrada e saída de um aplicativo podem ser diferentes e até alterar em determinadas situações. 

[Ambientes de Serviço de Aplicativo](environment/intro.md) usam infraestruturas de rede dedicadas para que aplicativos executados em um ambiente de Serviço de Aplicativo obtenham endereços IP dedicados e estáticos para conexões de entrada e saída.

## <a name="when-inbound-ip-changes"></a>Quando ocorrem alterações do IP de entrada

Independentemente do número de instâncias dimensionadas, cada aplicativo tem um único endereço IP de entrada. O endereço IP de entrada pode alterar ao executar uma das ações a seguir:

- Excluir um aplicativo e recriá-lo em um grupo de recursos diferente.
- Excluir o último aplicativo em uma combinação de grupo de recursos _e_ região e recriá-lo.
- Excluir uma associação SSL existente, como durante a renovação do certificado (consulte [Renovar Certificados](app-service-web-tutorial-custom-ssl.md#renew-certificates)).

## <a name="get-static-inbound-ip"></a>Obter IP de entrada estático

Às vezes, é possível querer um endereço IP dedicado e estático para o aplicativo. Para obter um endereço IP de entrada estático, é necessário configurar uma [associação SSL baseada em IP](app-service-web-tutorial-custom-ssl.md#bind-your-ssl-certificate). Se você realmente não precisar da funcionalidade SSL para proteger o aplicativo, poderá até carregar um certificado autoassinado para essa associação. Em uma associação SSL baseada em IP, o certificado é vinculado ao próprio endereço IP, portanto, o Serviço de Aplicativo fornece um endereço IP estático para que isso aconteça. 

## <a name="when-outbound-ips-change"></a>Quando ocorrem alterações dos IPs de saída

Independentemente do número de instâncias dimensionadas, cada aplicativo tem um número definido de endereços IP de saída a qualquer momento. Qualquer conexão de saída do aplicativo do Serviço de Aplicativo, como um banco de dados back-end, usa um dos endereços IP de saída como o endereço IP de origem. Não é possível saber antecipadamente o endereço IP que uma determinada instância de aplicativo usará para estabelecer a conexão de saída, portanto, o serviço de back-end deverá abrir o firewall para todos os endereços IP de saída do aplicativo.

O conjunto de endereços IP de saída para o aplicativo é alterado quando você dimensiona o aplicativo entre as camadas inferiores (**Básico**, **Standard** e **Premium**) e a camada **Premium V2**.

É possível localizar o conjunto de todos os endereços IP de saída possíveis que o aplicativo pode usar, independentemente dos tipos de preços, procurando pela propriedade `possibleOutboundIPAddresses`. Consulte [Localizar IPs de saída](#find-outbound-ips).

## <a name="find-outbound-ips"></a>Localizar IPs de saída

Para localizar os endereços IP de saída atualmente usados pelo aplicativo no Portal do Azure, clique em **Propriedades** na navegação esquerda do aplicativo. 

É possível localizar as mesmas informações, executando o comando a seguir no [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

Para localizar todos os endereços IP de saída possíveis para o aplicativo, independentemente dos tipos de preços, execute o comando a seguir no [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

## <a name="next-steps"></a>Próximas etapas

Saiba como restringir o tráfego de entrada por endereços IP de origem.

> [!div class="nextstepaction"]
> [Restrições de IP estático](app-service-ip-addresses.md)
