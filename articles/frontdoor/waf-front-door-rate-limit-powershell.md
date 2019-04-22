---
title: Configurar uma regra de limite de taxa de firewall de aplicativo web para frente - Azure PowerShell
description: Saiba como configurar uma regra de limite de taxa para um ponto de extremidade de porta de entrada existente.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2019
ms.author: kumud;tyao
ms.openlocfilehash: e0ad1e85a4cd47de823bc4f224b5a8834b1068b9
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59685690"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Configurar uma regra na web application firewall taxa limite usando o Azure PowerShell
Regra de limite de taxa de WAF (firewall) do aplicativo web do Azure para frente do Azure controla o número de solicitações permitidas de um único cliente IP durante uma duração de um minuto.
Este artigo mostra como configurar uma regra de limite de taxa de WAF que controla o número de solicitações de permissão de um único cliente para um aplicativo web que contém */promo* na URL usando o Azure PowerShell.

> [!IMPORTANT]
> O recurso de regra de limite de taxa de WAF para frente do Azure está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar a configurar uma política de limite de taxa, configure o ambiente do PowerShell e criar um perfil de porta da frente.
### <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell
O Azure PowerShell fornece um conjunto de cmdlets que usa o modelo do [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para gerenciar os recursos do Azure. 

Você pode instalar o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) no computador local e usá-lo em qualquer sessão do PowerShell. Siga as instruções na página para entrar com suas credenciais do Azure e instale o módulo do PowerShell do Az.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Conecte-se ao Azure com um diálogo interativo para entrar
```
Connect-AzAccount

```
Antes de instalar o módulo Front Door, verifique se você tem a versão atual do PowerShellGet instalada. Execute o comando abaixo e reabra o PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Instalar o módulo Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Criar um perfil de Front Door
Crie um perfil de Front Door seguindo as instruções descritas no [Guia de Início Rápido: Criar um perfil de porta da frente](quickstart-create-front-door.md)

## <a name="define-url-match-conditions"></a>Definir condições de correspondência de url
Definir uma condição de correspondência de URL (URL contém /promo) usando [New-AzFrontDoorMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoormatchconditionobject).
O exemplo a seguir corresponde */promo* como o valor da *RequestUri* variável:

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>Criar uma regra de limite de taxa personalizada
Definir um limite de taxa usando [New-AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-AzFrontDoorCustomRuleObject). No exemplo a seguir, o limite é definido como 1000. Solicitações de qualquer cliente para a página de promoção exceder 1000 durante um minuto são bloqueadas até que o próximo minuto é iniciado.

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>Configurar uma política de segurança

Encontre o nome do grupo de recursos que contém o perfil de Front Door usando `Get-AzureRmResourceGroup`. Em seguida, configure uma política de segurança com uma regra de limite de taxa personalizada usando [New-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/new-azfrontdoorfirewallPolicy) no grupo de recursos especificado que contém o perfil de porta da frente.

O exemplo abaixo usa o nome do Grupo de Recursos *myResourceGroupFD1* supondo que você criou o perfil de Front Door usando as instruções fornecidas no [Guia de Início Rápido: Criar um artigo de Front Door](quickstart-create-front-door.md).

 usando o [New-AzFrontDoorFireWallPolicy](/powershell/module/Az.FrontDoor/New-AzFrontDoorFireWallPolicy).

```powershell-interactive
   $ratePolicy = New-AzFrontDoorFireWallPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>Política de link para um host de front-end de porta da frente
Vincular o objeto de diretiva de segurança em um host de front-end existente da frente e atualizar propriedades de porta da frente. Primeiro, recupere o objeto de porta da frente usando [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) comando.
Em seguida, defina o front-end *WebApplicationFirewallPolicyLink* propriedade para o *resourceId* de "$ratePolicy" criado na etapa anterior usando [conjunto AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) comando. 

O exemplo abaixo usa o nome do Grupo de Recursos *myResourceGroupFD1* supondo que você criou o perfil de Front Door usando as instruções fornecidas no [Guia de Início Rápido: Criar um artigo de Front Door](quickstart-create-front-door.md). Além disso, no exemplo abaixo, substitua $frontDoorName pelo nome do seu perfil de porta da frente. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Você só precisa definir *WebApplicationFirewallPolicyLink* propriedade uma vez para vincular uma política de segurança a uma porta da frente front-end. Atualizações de política subsequentes são aplicadas automaticamente para o front-end.

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre [da frente](front-door-overview.md) 


