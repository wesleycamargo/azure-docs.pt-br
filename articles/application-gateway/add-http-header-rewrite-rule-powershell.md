---
title: Reescrever cabeçalhos HTTP no Gateway de Aplicativo do Azure
description: Este artigo fornece informações sobre como reescreva cabeçalhos HTTP no Gateway de aplicativo do Azure usando o Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 47fe6a5247622e3ad3b3720955068580e0329913
ms.sourcegitcommit: ed66a704d8e2990df8aa160921b9b69d65c1d887
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64947200"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Reescreva cabeçalhos de solicitação e resposta HTTP com o Gateway de aplicativo do Azure - Azure PowerShell

Este artigo descreve como usar o Azure PowerShell para configurar uma [SKU do Gateway de aplicativo v2](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) instância reescreva os cabeçalhos HTTP em solicitações e respostas.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

- Você precisa executar o PowerShell do Azure localmente para concluir as etapas neste artigo. Você também precisará ter Az módulo versão 1.0.0 ou posterior instalado. Execute `Import-Module Az` e, em seguida, `Get-Module Az` para determinar a versão que você instalou. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Depois de verificar a versão do PowerShell, execute `Login-AzAccount` para criar uma conexão com o Azure.
- Você precisa ter uma instância SKU do Gateway de aplicativo v2. Regravação de cabeçalhos não tem suporte no SKU do v1. Se você não tiver o SKU do v2, crie uma [SKU do Gateway de aplicativo v2](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) instância antes de começar.

## <a name="create-required-objects"></a>Criar objetos necessários

Para configurar a reconfiguração do cabeçalho HTTP, você precisa concluir estas etapas.

1. Crie os objetos que são necessários para a reconfiguração do cabeçalho HTTP:

   - **RequestHeaderConfiguration**: Usado para especificar os campos de cabeçalho de solicitação que você pretende reescrever e o novo valor para os cabeçalhos.

   - **ResponseHeaderConfiguration**: Usado para especificar os campos de cabeçalho de resposta que você pretende reescrever e o novo valor para os cabeçalhos.

   - **ActionSet**: Contém as configurações dos cabeçalhos de solicitação e resposta especificados anteriormente.

   - **Condição**: Uma configuração opcional. Condições de regravação avaliar o conteúdo do HTTP (S) de solicitações e respostas. A ação de regravação ocorrerá se a resposta ou solicitação de HTTP (S) corresponde à condição de regravação.

     Se você associar mais de uma condição com uma ação, a ação ocorre somente quando todas as condições são atendidas. Em outras palavras, a operação é uma operação AND lógica.

   - **RewriteRule**: Contém várias ação de regravação / reescrever combinações de condição.

   - **RuleSequence**: Executar uma configuração opcional que ajuda a determinar a ordem na qual as regras de reconfiguração. Essa configuração é útil quando você tiver várias regras de reescrita em um conjunto de regravação. Uma regra de regravação que tem um valor de sequência de regra mais baixo será executado primeira. Se você atribuir o mesmo valor de sequência de regra a duas regras de reescrita, a ordem de execução é não determinística.

     Se você não especificar explicitamente o RuleSequence, um valor padrão de 100 é definido.

   - **RewriteRuleSet**: Contém várias regras de reconfiguração que serão associadas a uma regra de roteamento de solicitação.

2. Anexe o RewriteRuleSet a uma regra de roteamento. A configuração de regravação é anexada ao ouvinte de origem por meio da regra de roteamento. Quando você usa uma regra de roteamento básica, a configuração de regravação de cabeçalho está associada um ouvinte de origem e é uma reformulação de cabeçalho global. Quando você usa uma regra de roteamento com base no caminho, a configuração de regravação de cabeçalho é definida no mapa de caminho de URL. Nesse caso, ele se aplica somente à área de caminho específico de um site.

Você pode criar vários conjuntos de reconfiguração do cabeçalho HTTP e aplicar cada reescrita definida para vários ouvintes. Mas você pode aplicar somente um reescrever definido como um ouvinte específico.

## <a name="sign-in-to-azure"></a>Entrar no Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>Especifique a configuração da regra de reconfiguração de cabeçalho HTTP

Neste exemplo, modificaremos a uma URL de redirecionamento reescrevendo o cabeçalho location na resposta HTTP sempre que o cabeçalho location contém uma referência a azurewebsites.net. Para fazer isso, adicionaremos uma condição para avaliar se o cabeçalho location na resposta contém azurewebsites.net. Vamos usar o padrão de `(https?):\/\/.*azurewebsites\.net(.*)$`. E vamos usar `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` como o valor do cabeçalho. Esse valor substituirá *azurewebsites.net* com *contoso.com* no cabeçalho location.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>Recuperar a configuração do seu gateway de aplicativo

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>Recuperar a configuração da sua regra de roteamento de solicitação

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Atualize o gateway de aplicativo com a configuração para a reconfiguração de cabeçalhos HTTP

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

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como configurar alguns casos de uso comuns, consulte [cenários de reconfiguração de cabeçalho comum](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).