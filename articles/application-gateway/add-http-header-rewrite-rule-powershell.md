---
title: Reescrever cabeçalhos HTTP no Gateway de Aplicativo do Azure
description: Este artigo fornece informações sobre como reescreva cabeçalhos HTTP no Gateway de aplicativo do Azure usando o Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: bfafc74cbcb97f28cc085196a2cbaf4e9bf2e871
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548312"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Reescreva cabeçalhos de solicitação e resposta HTTP com o Gateway de aplicativo do Azure - Azure PowerShell

Este artigo mostra como usar o Azure PowerShell para configurar uma [SKU do Gateway de aplicativo v2](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) reescreva os cabeçalhos HTTP em solicitações e respostas.

> [!IMPORTANT]
> O dimensionamento automático e o gateway de aplicativo com redundância de zona SKU está atualmente em visualização pública. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Este tutorial requer que você execute o Azure PowerShell localmente. É necessário ter instalado o módulo Az versão 1.0.0 ou posterior. Execute `Import-Module Az` e, em seguida, `Get-Module Az` para localizar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Depois de verificar a versão do PowerShell, execute `Login-AzAccount` para criar uma conexão com o Azure.
- Você precisa ter um v2 de Gateway de aplicativo, pois o cabeçalho de funcionalidade e reconfiguração de SKU não há suporte para a SKU de v1. Se você não tiver o SKU do v2, crie uma [SKU do Gateway de aplicativo v2](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps>) antes de começar.

## <a name="what-is-required-to-rewrite-a-header"></a>O que é necessário para reescrever um cabeçalho

Para configurar a reconfiguração do cabeçalho HTTP, você precisará:

1. Criar os novos objetos necessários para reescrever os cabeçalhos http:

   - **RequestHeaderConfiguration**: este objeto é usado para especificar os campos de cabeçalho de solicitação que você pretende reescrever e o novo valor que deve ser reescrito nos cabeçalhos originais.

   - **ResponseHeaderConfiguration**: este objeto é usado para especificar os campos de cabeçalho de resposta que você pretende reescrever e o novo valor que deve ser reescrito nos cabeçalhos originais.

   - **ActionSet**: este objeto contém as configurações dos cabeçalhos de solicitação e de resposta especificados acima.

   - **Condição**: É uma configuração opcional. Se uma condição de regravação é adicionada, ela avaliará o conteúdo das solicitações de HTTP (S) e as respostas. A decisão de executar a ação de regravação associada com a condição de regravação se baseará se a resposta ou solicitação de HTTP (S) de correspondência com a condição de regravação. 

     Se mais de um condições são associados com uma ação e, em seguida, a ação será executada somente quando todas as condições forem atendidas, ou seja, será executada uma operação AND lógica.

   - **RewriteRule**: contém vários ação de regravação - combinações de condição de regravação.

   - **RuleSequence**: Esta é uma configuração opcional. Ele ajuda a determinar a ordem na qual as regras de reescrita diferentes são executadas. Isso é útil quando há várias regras de reescrita em um conjunto de regravação. A regra de reconfiguração com o menor valor de sequência de regra é executada pela primeira vez. Se você fornecer a mesma sequência de regra para duas regras de regravação a ordem de execução será não determinística.

     Se você não especificar explicitamente o RuleSequence, um valor padrão de 100 será definido.

   - **RewriteRuleSet**: este objeto contém várias regras de reconfiguração que serão associadas a uma regra de roteamento de solicitação.

2. Você precisará anexar o rewriteRuleSet com uma regra de roteamento. Isso ocorre porque a configuração de regravação é anexada ao ouvinte de origem por meio da regra de roteamento. Ao usar uma regra de roteamento básica, a configuração de redirecionamento é associada a um ouvinte de origem e será uma reescrita de cabeçalho global. Quando uma regra de roteamento com base em caminho é usada, a configuração de redirecionamento será definida no mapa de caminho de URL. Então, ela se aplica somente a área de caminho específico de um site.

Você pode criar vários conjuntos de regravação de cabeçalho http e cada conjunto de reconfiguração pode ser aplicado a vários ouvintes. No entanto, você pode aplicar somente um reescrever definido como um ouvinte específico.

## <a name="sign-in-to-azure"></a>Entrar no Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>**Especificar a configuração da regra de reescrita do cabeçalho HTTP**

Neste exemplo, vamos modificar a URL de redirecionamento reescrevendo o cabeçalho location na resposta http sempre que o cabeçalho location contém uma referência a "azurewebsites.net". Para fazer isso, vamos adicionar uma condição para avaliar se o cabeçalho location na resposta contém azurewebsites.net, usando o padrão de `(https?):\/\/.*azurewebsites\.net(.*)$`. Nós usaremos `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` como o valor do cabeçalho. Isso substituirá *azurewebsites.net* com *contoso.com* no cabeçalho location.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-configuration-of-your-existing-application-gateway"></a>Recuperar configuração de um gateway de aplicativo existente

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-configuration-of-your-existing-request-routing-rule"></a>Recuperar configuração da regra de roteamento de solicitação existente

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Atualizar o gateway de aplicativo com a configuração para reescrever cabeçalhos HTTP

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name LocationHeaderRewrite -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>Excluir uma regra de regravação

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "LocationHeaderRewrite" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre a configuração necessária para realizar algumas das comum casos de uso, consulte [cenários de reconfiguração de cabeçalho comum](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).