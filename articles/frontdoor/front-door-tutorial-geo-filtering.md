---
title: Tutorial – Configurar filtragem geográfica em um domínio para o Azure Front Door Service | Microsoft Docs
description: Neste tutorial, você aprenderá a criar uma política de filtragem geográfica simples e associar a política ao host de front-end Front Door existente
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/20/2018
ms.author: sharadag
ms.openlocfilehash: c4032f7c33cec7b7a7864ccff07a05b87c945949
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988572"
---
# <a name="how-to-set-up-a-geo-filtering-policy-for-your-front-door"></a>Como configurar uma política de filtragem geográfica para o Front Door
Este tutorial mostra como usar o Azure PowerShell para criar uma política de filtragem geográfica de exemplo e associar a política a seu host de front-end Front Door existente. Essa política de filtragem geográfica de exemplo bloqueará as solicitações de todos os outros países, exceto dos Estados Unidos.

## <a name="1-set-up-your-powershell-environment"></a>1. Configurar o ambiente do PowerShell
O Azure PowerShell fornece um conjunto de cmdlets que usa o modelo do [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para gerenciar os recursos do Azure. 

Você pode instalar o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) no computador local e usá-lo em qualquer sessão do PowerShell. Siga as instruções na página para entrar com suas credenciais do Azure e instale o AzureRM.
```
# Connect to Azure with an interactive dialog for sign-in
Connect-AzureRmAccount
Install-Module -Name AzureRM
```
> [!NOTE]
>  O suporte ao [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) estará disponível em breve.

Antes de instalar o módulo Front Door, verifique se você tem a versão atual do PowerShellGet instalada. Execute o comando abaixo e reabra o PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

Módulo AzureRM.FrontDoor. 

```
Install-Module -Name AzureRM.FrontDoor -AllowPrerelease
```

## <a name="2-define-geo-filtering-match-conditions"></a>2. Definir as condições de correspondência da filtragem geográfica
Primeiro, crie uma condição de correspondência de exemplo que seleciona as solicitações não provenientes de "US". Consulte o [guia](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoormatchconditionobject) do PowerShell sobre parâmetros durante a criação de uma condição de correspondência. O código do país de duas letras para o mapeamento de país é fornecido [aqui](/Protection/GeoFiltering).

```
$nonUSGeoMatchCondition = New-AzureRmFrontDoorMatchConditionObject -MatchVariable RemoteAddr -OperatorProperty GeoMatch -NegateCondition $true -MatchValue "US"
```
 
## <a name="3-add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>3. Adicionar condição de correspondência de filtragem geográfica a uma regra com Ação e Prioridade

Em seguida, crie um objeto CustomRule `nonUSBlockRule` com base em uma Prioridade, em uma Ação e na condição de correspondência.  Uma CustomRule pode ter várias MatchCondition.  Neste exemplo, a Ação está definida como Bloquear e a Prioridade como 1, a prioridade mais alta.

```
$nonUSBlockRule = New-AzureRmFrontDoorCustomRuleObject -Name "geoFilterRule" -RuleType MatchRule -MatchCondition $nonUSGeoMatchCondition -Action Block -Priority 1
```

Consulte o [guia](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoorcustomruleobject) do PowerShell sobre parâmetros durante a criação de um CustomRuleObject.

## <a name="4-add-rules-to-a-policy"></a>4. Adicionar regras a uma política
Esta etapa cria um objeto de política `geoPolicy` que contém `nonUSBlockRule` da etapa anterior no grupo de recursos especificado. Use `Get-AzureRmResourceGroup` para localizar seu ResourceGroupName $resourceGroup.

```
$geoPolicy = New-AzureRmFrontDoorFireWallPolicy -Name "geoPolicyAllowUSOnly" -resourceGroupName $resourceGroup -Customrule $nonUSBlockRule  -Mode Prevention -EnabledState Enabled
```

Consulte o [guia](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoorfirewallpolicy) do PowerShell sobre parâmetros durante a criação de uma política.

## <a name="5-link-policy-to-a-front-door-frontend-host"></a>5. Vincular a política a um host de front-end Front Door
As últimas etapas são para vincular o objeto de política de proteção a um host de front-end Front Door existente e atualizar as propriedades do Front Door. Você primeiro recupera o objeto Front Door usando [Get-AzureRmFrontDoor](https://docs.microsoft.com/azure/frontdoor/get-azurermfrontdoor) e, em seguida, define sua propriedade WebApplicationFirewallPolicyLink de front-end como o resourceId da `geoPolicy`.

```
$geoFrontDoorObjectExample = Get-AzureRmFrontDoor -ResourceGroupName $resourceGroup
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

Use o seguinte [comando](https://docs.microsoft.com/azure/frontdoor/set-azurermfrontdoor) para atualizar o objeto Front Door.

```
Set-AzureRmFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Você só precisará definir a propriedade WebApplicationFirewallPolicyLink uma vez para vincular uma política de proteção a um host de front-end Front Door. As atualizações subsequentes da política serão aplicadas automaticamente ao host de front-end.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [segurança da camada de aplicativo com o Front Door](front-door-application-security.md).
- Saiba como [criar um Front Door](quickstart-create-front-door.md).
