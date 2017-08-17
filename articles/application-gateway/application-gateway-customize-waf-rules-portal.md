---
title: "Personalizar regras de firewall do aplicativo Web no Gateway de Aplicativo do Azure – Portal | Microsoft Docs"
description: "Esta página fornece informações sobre como personalizar regras de firewall de aplicativo Web no Gateway de Aplicativo com o portal."
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 1159500b-17ba-41e7-88d6-b96986795084
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 03/28/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: c3e93614f012eecff0e88f5f2ad13db199406f4a
ms.contentlocale: pt-br
ms.lasthandoff: 08/04/2017

---

# <a name="customize-web-application-firewall-rules-through-the-portal"></a>Personalizar as regras de firewall de aplicativo Web por meio do portal

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [CLI 2.0 do Azure](application-gateway-customize-waf-rules-cli.md)

O firewall de aplicativo Web de Gateway de Aplicativo fornece proteção para aplicativos Web. Essas proteções são fornecidas pelos conjuntos de regras OWASP CRS. Algumas regras podem causar falsos positivos e bloquear o tráfego real.  Por esse motivo, o Gateway de Aplicativo fornece a capacidade de personalizar regras e grupos de regras em um Gateway de Aplicativo com firewall do aplicativo Web habilitado. Para obter mais informações sobre os grupos de regras e regras específicas, visite [regras e grupos de regras CRS de firewall de aplicativo Web](application-gateway-crs-rulegroups-rules.md)

>[!NOTE]
> Se o gateway de aplicativo não estiver usando a camada de WAF, será exibida a opção para atualizar o gateway de aplicativo para a camada de WAF, conforme mostrado na imagem a seguir:

![habilitar WAF][fig1]

## <a name="view-rule-groups-and-rules"></a>Exibir grupos de regras e regras

Navegue para um gateway de aplicativo e selecione **Firewall do aplicativo Web**.  Clique em **Configuração de regra avançada**.  Essa exibição mostra uma tabela na página de todos os grupos de regras fornecidos com o conjunto de regras escolhido.

![configurar regras desabilitadas][1]

## <a name="search-for-rules-to-disable"></a>Pesquisar pelas regras a desabilitar

A folha de configurações de firewall do aplicativo Web fornece a capacidade de filtrar as regras por uma pesquisa de texto. O resultado exibe apenas grupos de regras e regras contendo o texto que está sendo pesquisado.

![pesquisar por regras][2]

## <a name="disable-rule-groups-and-rules"></a>Desabilitar regras e grupos de regras

Ao desabilitar regras, você pode desabilitar um grupo de regras inteiro ou regras específicas em um ou mais grupos de regras.  Quando as regras que você deseja desabilitar estiverem desmarcadas, clique em **Salvar**.  Essa etapa salva as alterações para o Gateway de Aplicativo.

![salvar alterações][3]

## <a name="next-steps"></a>Próximas etapas

Depois que você configurar suas regras desabilitadas, saiba como exibir os logs de WAF visitando [Diagnóstico de Gateway de Aplicativo](application-gateway-diagnostics.md#diagnostic-logging)

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png

