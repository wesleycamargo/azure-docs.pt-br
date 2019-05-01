---
title: Configurar uma regra de restrição de IP com a regra de firewall de aplicativo web para frente do Azure
description: Saiba como configurar uma regra de WAF de restrição de endereço IP para um ponto de extremidade de porta de entrada existente.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: kumud;tyao
ms.openlocfilehash: 514c034c23eed3a87111331724f3a33104651a43
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/25/2019
ms.locfileid: "64514898"
---
# <a name="configure-an-ip-restriction-rule-with-web-application-firewall-for-azure-front-door-preview"></a>Configurar uma regra de restrição de IP com o firewall do aplicativo web para porta frontal do Azure (visualização)
 Este artigo mostra como configurar regras de restrição de IP no firewall do aplicativo web do Azure (WAF) para frente usando o modelo do Azure Resource Manager, Azure PowerShell ou CLI do Azure.

Uma regra de controle de acesso baseadas em endereço IP é uma regra personalizada do WAF que lhe permite controlar o acesso aos seus aplicativos web, especificando uma lista de endereços IP ou intervalos de endereços IP no formulário de Classless inter-Domain Routing (CIDR).

Por padrão, o aplicativo web é acessível pela internet. Se você quiser limitar o acesso aos aplicativos web somente para clientes de uma lista de endereços IP conhecidos ou intervalos de endereços IP, você precisa criar duas regras de correspondência de IP. Regra de correspondência de IP primeiro contém a lista de endereços IP como valores correspondentes e defina a ação para "Permitir". A segunda com prioridade mais baixa, é bloquear todos os outros endereços IP usando o operador "All" e defina a ação de "Bloco". Depois que uma regra de restrição de IP for aplicada, todas as solicitações provenientes de endereços fora dessa lista de permissão recebe uma resposta 403 (proibido).  

> [!IMPORTANT]
> O recurso de restrição de IP de WAF para frente do Azure está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="configure-waf-policy-with-azure-cli"></a>Configurar política de WAF com a CLI do Azure

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar a configurar uma diretiva de restrição de IP, configure seu ambiente da CLI e criar um perfil de porta da frente.

#### <a name="set-up-azure-cli-environment"></a>Configurar o ambiente de CLI do Azure
1. Instalar o [CLI do Azure](/cli/azure/install-azure-cli), ou usar o Azure Cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que podem ser executado diretamente no portal do Azure. Ele tem a CLI do Azure pré-instalada e configurada para usar com sua conta. Selecione o **Experimente** botão na CLI comandos a seguir. Selecionando **Experimente** invoca Cloud Shell que você pode entrar sua conta do Azure com o. Depois que uma sessão do cloud shell for iniciado, digite `az extension add --name front-door` para adicionar a extensão de front-door.
 2. Se usando a CLI localmente, no Bash, entre no Azure com `az login`.

#### <a name="create-front-door-profile"></a>Criar perfil de porta da frente
Crie um perfil de Front Door seguindo as instruções descritas no [Guia de Início Rápido: Criar um perfil de porta da frente](quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>Criar uma política de WAF

Crie uma política de WAF com o [waf-política de rede az criar](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-create) comando. No exemplo abaixo, substitua o nome da política *IPAllowPolicyExampleCLI* com um nome de política exclusivo.

```azurecli-interactive 
az network waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-custom-ip-access-control-rule"></a>Adicionar regra de controle de acesso IP personalizada

Adicionar uma regra de controle de acesso IP personalizada para a política de WAF criada na etapa anterior com o [personalizado-regra de política de waf de rede az criar](/cli/azure/ext/front-door/network/waf-policy/custom-rule?view=azure-cli-latest#ext-front-door-az-network-waf-policy-custom-rule-create) comando. 

No exemplo abaixo:
-  Substitua *IPAllowPolicyExampleCLI* com sua política exclusiva criada anteriormente.
-  Substitua *ip-endereço de-intervalo de-1*, *ip-endereço de-intervalo de-2* com seu próprio intervalo.

Primeiro, crie o IP permitem que a regra para os endereços especificados.

```azurecli
az network waf-policy custom-rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --match-condition RemoteAddr IPMatch ["<ip-address-range-1>","<ip-address-range-2>"] \
  --action Allow \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
Em seguida, crie um bloco de todas as regras IP com prioridade mais baixa do que o IP anterior de regra de permissão. Substitua os *IPAllowPolicyExampleCLI* com sua política exclusiva criada anteriormente.

```azurecli
az network waf-policy custom-rule create \
  --name IPDenyAllRule\
  --priority 2 \
  --rule-type MatchRule \
  --match-condition RemoteAddr Any
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
 ```

### <a name="find-waf-policy-id"></a>Localizar a ID de política de WAF
Localizar a ID de uma política de WAF com o [show do az rede waf política](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-show) comando. Substitua os *IPAllowPolicyExampleCLI* com sua política exclusiva criada anteriormente.

   ```azurecli
   az network waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-waf-policy-to-a-front-door-front-end-host"></a>Política de link WAF a um host de front-end de porta da frente

Definir a porta da frente *WebApplicationFirewallPolicyLink* ID para a ID de política com as [atualização do az rede front-door](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) comando. Substitua os *IPAllowPolicyExampleCLI* com sua política exclusiva criada anteriormente.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
Neste exemplo, a política de WAF é aplicada para FrontendEndpoints [0]. Você pode vincular a política de WAF para qualquer um dos seus front-ends.
> [!Note]
> Você só precisa definir as **WebApplicationFirewallPolicyLink** propriedade uma vez para vincular uma política de WAF a uma porta da frente front-end. Atualizações de política subsequentes são aplicadas automaticamente para o front-end.

## <a name="configure-waf-policy-with-azure-powershell"></a>Configurar política de WAF com o Azure PowerShell

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar a configurar uma diretiva de restrição de IP, configure o ambiente do PowerShell e criar um perfil de porta da frente.

#### <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell
O Azure PowerShell fornece um conjunto de cmdlets que usa o modelo do [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para gerenciar os recursos do Azure. 

Você pode instalar o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) no computador local e usá-lo em qualquer sessão do PowerShell. Siga as instruções na página para entrar com suas credenciais do Azure e instale o módulo do PowerShell do Az.

##### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Conectar-se ao Azure com uma caixa de diálogo interativa para entrar
```
Connect-AzAccount

```
Antes de instalar o módulo de porta da frente, verifique se que você tem a versão atual do PowerShellGet instalado. Execute o comando abaixo e reabra o PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

##### <a name="install-azfrontdoor-module"></a>Instalar o módulo Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Criar um perfil de Front Door
Crie um perfil de Front Door seguindo as instruções descritas no [Guia de Início Rápido: Criar um perfil de porta da frente](quickstart-create-front-door.md)

### <a name="define-ip-match-condition"></a>Definir condição de correspondência IP
Use o [New-AzFrontDoorMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoormatchconditionobject) comando para definir uma condição de correspondência IP. No exemplo abaixo, substitua *ip-endereço de-intervalo de-1*, *ip-endereço de-intervalo de-2* com seu próprio intervalo.

```powershell
  $IPMatchCondition = New-AzFrontDoorMatchConditionObject `
    -MatchVariable  RemoteAddr `
    -OperatorProperty IPMatch `
    -MatchValue ["ip-address-range-1", "ip-address-range-2"]
```
Criar uma correspondência IP em todas as regras de condição
```powershell
  $IPMatchALlCondition = New-AzFrontDoorMatchConditionObject `
    -MatchVariable  RemoteAddr `
    -OperatorProperty Any
    
```

### <a name="create-a-custom-ip-allow-rule"></a>Criar um personalizado a regra de permissão de IP
   Use o [New-AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-AzFrontDoorCustomRuleObject) comando para definir uma ação e definir uma prioridade. No exemplo a seguir, solicitações de cliente IPs que correspondem à lista serão permitidas. 

```powershell
  $IPAllowRule = New-AzFrontDoorCustomRuleObject `
    -Name "IPAllowRule" `
    -RuleType MatchRule `
    -MatchCondition $IPMatchCondition `
    -Action Allow -Priority 1
```
Crie um bloco de todas as regras IP com prioridade mais baixa do que o IP anterior de regra de permissão.

```powershell
  $IPBlockAll = New-AzFrontDoorCustomRuleObject `
    -Name "IPDenyAll" `
    -RuleType MatchRule `
    -MatchCondition $IPMatchALlCondition `
    -Action Block `
    -Priority 2
   ```

### <a name="configure-waf-policy"></a>Configurar a política de WAF
Encontre o nome do grupo de recursos que contém o perfil de Front Door usando `Get-AzResourceGroup`. Em seguida, configure uma política de WAF com a regra de bloco IP usando [New-AzFrontDoorFireWallPolicy](/powershell/module/Az.FrontDoor/New-AzFrontDoorFireWallPolicy).

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorFireWallPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule $IPBlockAll `
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-waf-policy-to-a-front-door-front-end-host"></a>Política de link WAF a um host de front-end de porta da frente

Vincule o objeto de diretiva de WAF a um host existente de front-end de porta da frente e atualizar propriedades de porta da frente. Primeiro, recupere o objeto de porta da frente usando [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Em seguida, defina o front-end *WebApplicationFirewallPolicyLink* propriedade como o resourceId do *$IPAllowPolicyExamplePS* criado na etapa anterior com o [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) comando.

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> Neste exemplo, a política de WAF é aplicada para FrontendEndpoints [0]. Você pode vincular a política de WAF para qualquer um dos seus front-ends. Você só precisa definir *WebApplicationFirewallPolicyLink* propriedade uma vez para vincular uma política de WAF a uma porta da frente front-end. Atualizações de política subsequentes são aplicadas automaticamente para o front-end.


## <a name="configure-waf-policy-with-resource-manager-template"></a>Configurar política de WAF com o modelo do Resource Manager
Exibir o modelo que cria uma porta da frente e uma política de WAF com regras de restrição de IP personalizadas [aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um perfil de porta da frente](quickstart-create-front-door.md).
