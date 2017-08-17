---
title: "Personalizar regras de firewall do aplicativo Web no Gateway de Aplicativo do Azure – PowerShell | Microsoft Docs"
description: "Esta página fornece informações sobre como personalizar regras de firewall de aplicativo Web no Gateway de Aplicativo com o PowerShell."
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 7ed79af5654939c86ca9449be2a59aa0be1136dc
ms.contentlocale: pt-br
ms.lasthandoff: 08/04/2017

---

# <a name="customize-web-application-firewall-rules-through-powershell"></a>Personalizar as regras de firewall de aplicativo Web por meio do PowerShell

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [CLI 2.0 do Azure](application-gateway-customize-waf-rules-cli.md)

O firewall de aplicativo Web de Gateway de Aplicativo fornece proteção para aplicativos Web. Essas proteções são fornecidas pelos conjuntos de regras OWASP CRS. Algumas regras podem causar falsos positivos e bloquear o tráfego real.  Por esse motivo, o Gateway de Aplicativo fornece a capacidade de personalizar regras e grupos de regras em um Gateway de Aplicativo com firewall do aplicativo Web habilitado. Para obter mais informações sobre os grupos de regras e regras específicas, visite [regras e grupos de regras CRS de firewall de aplicativo Web](application-gateway-crs-rulegroups-rules.md)

## <a name="view-rule-groups-and-rules"></a>Exibir grupos de regras e regras

A seguir estão exemplos que mostram como exibir regras e grupos de regras que podem ser configurados em um Gateway de Aplicativo com WAF habilitado.

### <a name="view-rule-groups"></a>Exibir grupos de regras

```powershell
Get-AzureRmApplicationGatewayAvailableWafRuleSets
```

A seguir está uma resposta truncada do exemplo anterior.

```
OWASP (Ver. 3.0):

    REQUEST-910-IP-REPUTATION:
        Description:
            
        Rules:
            RuleId     Description
            ------     -----------
            910011     Rule 910011
            910012     Rule 910012
            ...        ...

    REQUEST-911-METHOD-ENFORCEMENT:
        Description:
            
        Rules:
            RuleId     Description
            ------     -----------
            911011     Rule 911011
            ...        ...

OWASP (Ver. 2.2.9):

    crs_20_protocol_violations:
        Description:
            
        Rules:
            RuleId     Description
            ------     -----------
            960911     Invalid HTTP Request Line
            981227     Apache Error: Invalid URI in Request.
            960000     Attempted multipart/form-data bypass
            ...        ...
```

## <a name="disable-rules"></a>Desabilitar regras

O exemplo a seguir desabilita as regras `910018` e `910017` em um Gateway de Aplicativo.

```azurecli
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
```

## <a name="next-steps"></a>Próximas etapas

Depois que você configurar suas regras desabilitadas, saiba como exibir os logs de WAF visitando [Diagnóstico de Gateway de Aplicativo](application-gateway-diagnostics.md#diagnostic-logging)

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
