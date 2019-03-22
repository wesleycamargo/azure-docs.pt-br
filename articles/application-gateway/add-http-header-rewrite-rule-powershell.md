---
title: Reescrever cabeçalhos HTTP em um Gateway de Aplicativo do Azure
description: Este artigo fornece informações sobre como reescrever cabeçalhos HTTP em um Gateway de Aplicativo do Azure existente usando Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: f3bda2cf680fa1ad17b5cbb3b9ba5911c78a1e4e
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2019
ms.locfileid: "57316499"
---
# <a name="rewrite-http-headers-in-an-existing-application-gateway"></a>Reescrever cabeçalhos HTTP em um Gateway de Aplicativo existente

É possível usar o Azure PowerShell para configurar [regras para reescrever cabeçalhos de solicitação e resposta HTTP](rewrite-http-headers.md) em um [SKU de gateway de aplicativo com redundância de zona e dimensionamento automático](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) existente

> [!IMPORTANT]
> O dimensionamento automático e o gateway de aplicativo com redundância de zona SKU está atualmente em visualização pública. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Recuperar configuração de um gateway de aplicativo existente
> * Especificar a configuração da regra de reescrita do cabeçalho HTTP
> * Atualizar o gateway de aplicativo com a configuração acima para reescrever cabeçalhos HTTP

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial requer que você execute o Azure PowerShell localmente. É necessário ter instalado o módulo Az versão 1.0.0 ou posterior. Execute `Import-Module Az` e, em seguida, `Get-Module Az` para localizar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Depois de verificar a versão do PowerShell, execute `Login-AzAccount` para criar uma conexão com o Azure.

## <a name="sign-in-to-azure"></a>Entrar no Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>**Especificar a configuração da regra de reescrita do cabeçalho HTTP**

Configure os novos objetos necessários para reescrever os cabeçalhos HTTP:

- **RequestHeaderConfiguration**: este objeto é usado para especificar os campos de cabeçalho de solicitação que você pretende reescrever e o novo valor que deve ser reescrito nos cabeçalhos originais.
- **ResponseHeaderConfiguration**: este objeto é usado para especificar os campos de cabeçalho de resposta que você pretende reescrever e o novo valor que deve ser reescrito nos cabeçalhos originais.
- **ActionSet**: este objeto contém as configurações dos cabeçalhos de solicitação e de resposta especificados acima.
- **RewriteRule**: este objeto contém todos os *actionSets* especificados acima.
- **RewriteRuleSet**- este objeto contém todos os *rewriteRules* e deverá ser anexado a uma regra de roteamento de solicitação - básica ou com base no caminho.

```azurepowershell
$requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
```

## <a name="retrieve-configuration-of-your-existing-application-gateway"></a>Recuperar configuração de um gateway de aplicativo existente

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-configuration-of-your-existing-request-routing-rule"></a>Recuperar configuração da regra de roteamento de solicitação existente

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name Rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Atualizar o gateway de aplicativo com a configuração para reescrever cabeçalhos HTTP

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name rewriteRuleSet1 -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>Excluir uma regra de regravação

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "rewriteRuleSet1" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um gateway de aplicativo com regras de roteamento baseadas em caminhos de URL](./tutorial-url-route-powershell.md)
