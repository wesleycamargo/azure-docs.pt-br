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
ms.translationtype: Human Translation
ms.sourcegitcommit: fc27849f3309f8a780925e3ceec12f318971872c
ms.openlocfilehash: 094ae187ec8ba5ff38f174ee4cf139d30db7e057
ms.contentlocale: pt-br
ms.lasthandoff: 06/14/2017


---

<a id="customize-web-application-firewall-rules-through-the-portal" class="xliff"></a>

# Personalizar as regras de firewall de aplicativo Web por meio do portal

O firewall de aplicativo Web de Gateway de Aplicativo fornece proteção para aplicativos Web. Essas proteções são fornecidas pelos conjuntos de regras OWASP CRS. Algumas regras podem causar falsos positivos e bloquear o tráfego real.  Por esse motivo, o gateway de aplicativo fornece a capacidade de personalizar grupo de regras e regras em um gateway de aplicativo com firewall do aplicativo Web habilitado. Para obter mais informações sobre os grupos de regras e regras específicas, visite [regras e grupos de regras CRS de firewall de aplicativo Web](application-gateway-crs-rulegroups-rules.md)

>[!NOTE]
> Se o gateway de aplicativo não estiver usando a camada de WAF, será exibida a opção para atualizar o gateway de aplicativo para a camada de WAF, conforme mostrado na imagem a seguir:

![habilitar waf][fig1]

<a id="view-rule-groups-and-rules" class="xliff"></a>

## Exibir grupos de regras e regras

Navegue para um gateway de aplicativo e selecione **Firewall do aplicativo Web**.  Clique em **Configuração de regra avançada**.  Isso mostra uma tabela na página de todos os grupos de regras fornecidos com o conjunto de regras escolhido.

![configurar regras desabilitadas][1]

<a id="search-for-rules-to-disable" class="xliff"></a>

## Pesquisar pelas regras a desabilitar

A folha de configurações de firewall do aplicativo Web fornece a capacidade de filtrar as regras por uma pesquisa de texto. O resultado exibe apenas grupos de regras e regras contendo o texto que está sendo pesquisado.

![pesquisar por regras][2]

<a id="disable-rule-groups-and-rules" class="xliff"></a>

## Desabilitar regras e grupos de regras

Ao desabilitar regras, você pode desabilitar um grupo de regras inteiro ou regras específicas em um ou mais grupos de regras.  Quando as regras que você deseja desabilitar estiverem desmarcadas, clique em **Salvar**.  Isso salva as alterações para o gateway de aplicativo.

![salvar alterações][3]

<a id="next-steps" class="xliff"></a>

## Próximas etapas

Depois que você configurar suas regras desabilitadas, saiba como exibir os logs de WAF visitando [Diagnóstico de Gateway de Aplicativo](application-gateway-diagnostics.md#diagnostic-logs)

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png

