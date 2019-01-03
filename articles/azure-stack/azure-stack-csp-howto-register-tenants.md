---
title: Adicionar locatários para uso e cobrança para o Azure Stack | Microsoft Docs
description: As etapas necessárias adicionam um usuário final para o Azure Stack gerenciados por um provedor de serviços de nuvem (CSP).
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2018
ms.author: sethm
ms.reviewer: alfredo
ms.openlocfilehash: e319c803b610005bc130e9c5d314d23c48c1e9a3
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53717171"
---
# <a name="add-tenant-for-usage-and-billing-to-azure-stack"></a>Adicione o locatário para uso e cobrança para o Azure Stack

*Aplica-se a: Sistemas integrados do Azure Stack*

Este artigo descreve as etapas necessárias adicionar um usuário final para o Azure Stack gerenciados por um provedor de serviços de nuvem (CSP). Quando o novo locatário usa recursos, o Azure Stack relatará uso para suas assinaturas do CSP.

CSPs geralmente oferecem serviços a vários clientes finais (locatários) em sua implantação do Azure Stack. A adição de locatários para o registro de pilha do Azure garante que o uso de cada locatário será relatado e cobrado para a assinatura de CSP correspondente. Se você não concluir as etapas neste artigo, uso de locatário é cobrado para a assinatura usada no registro inicial do Azure Stack. Antes de adicionar um cliente final para o Azure Stack para acompanhamento de uso e para gerenciar seu locatário, você precisará configurar o Azure Stack como um CSP. Para obter etapas e recursos, consulte [gerenciar o uso e cobrança para o Azure Stack como um provedor de serviços de nuvem](azure-stack-add-manage-billing-as-a-csp.md).

O diagrama a seguir mostra as etapas que um CSP precisa seguir para habilitar um novo cliente para usar o Azure Stack e para configurar o rastreamento para o cliente de uso. Adicionando o cliente final, também será gerenciar recursos no Azure Stack. Você tem duas opções para gerenciar seus recursos:

1. Você pode manter o cliente final e forneça credenciais para a assinatura do Azure Stack local para o cliente final.  
2. Ou o cliente final pode trabalhar com sua assinatura localmente e adicionar o CSP como uma convidada com permissões de proprietário.  

**Etapas para adicionar um cliente final**

![Configurar o provedor de serviços de nuvem para acompanhamento de uso e para gerenciar a conta do cliente final](media/azure-stack-csp-enable-billing-usage-tracking/process-csp-enable-billing.png)

## <a name="create-a-new-customer-in-partner-center"></a>Criar um novo cliente no Partner Center

No Centro de parceiros, crie uma nova assinatura do Azure para o cliente. Para obter instruções, consulte [adicionar um novo cliente](https://msdn.microsoft.com/partner-center/add-a-new-customer).


##  <a name="create-an-azure-subscription-for-the-end-customer"></a>Criar uma assinatura do Azure para o cliente final

Depois de criar um registro de seu cliente no Partner Center, você pode vendê-los assinaturas de produtos no catálogo. Para obter instruções, consulte [Create, suspender ou Cancelar assinaturas de cliente](https://msdn.microsoft.com/partner-center/create-a-new-subscription).

## <a name="create-a-guest-user-in-the-end-customer-directory"></a>Criar um usuário convidado no diretório do cliente final

Se o cliente final será gerenciar sua própria conta, criar um usuário convidado em seu diretório e envie as informações. O usuário final será, em seguida, adicionar o convidado e elevar a permissão de convidado para **proprietário** para a conta do Azure Stack CSP.
 
## <a name="update-the-registration-with-the-end-customer-subscription"></a>Atualizar o registro com a assinatura do cliente final

Atualize seu registro com a nova assinatura do cliente. Azure informa o uso do cliente usando a identidade do cliente do Partner Center. Essa etapa garante que o uso de cada cliente é reportado na assinatura de CSP individual do cliente. Isso facilita acompanhar o uso de usuário e cobrança.

> [!Note]  
> Para executar esta etapa, você deve ter [registrado do Azure Stack](azure-stack-register.md).

1. Abra o Windows PowerShell com um prompt com privilégios elevados e execute:  
    `Add-AzureRmAccount`
2. Digite suas credenciais do Azure.
3. Na sessão do PowerShell, execute:

```powershell
    New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties <PSObject>
```
### <a name="new-azurermresource-powershell-parameters"></a>Parâmetros do PowerShell New-AzureRmResource
| Parâmetro | DESCRIÇÃO |
| --- | --- | 
|registrationSubscriptionID | A assinatura do Azure que foi usada para o registro inicial do Azure Stack.|
| customerSubscriptionID | A assinatura do Azure (não o Azure Stack) que pertencem ao cliente a ser registrado. Deve ser criado na oferta de CSP; Na prática, isso significa, por meio do Partner Center. Se um cliente tiver mais de um locatário do Azure Active Directory, essa assinatura deve ser criada no locatário que será usado para fazer logon no Azure Stack. A ID de assinatura de cliente deve usar letras minúsculas. |
| resourceGroup | O grupo de recursos no Azure em que o registro é armazenado. |
| registrationName | O nome do registro do seu Azure Stack. Ele é um objeto armazenado no Azure. | 
| propriedades | Especifica as propriedades do recurso. Use esse parâmetro para especificar os valores das propriedades que são específicas ao tipo de recurso.


> [!Note]  
> Os locatários precisam ser registrados com cada Azure Stack que eles usam. Se você tiver duas implantações do Azure Stack, e um locatário vai usar ambos, você precisa atualizar os registros iniciais de cada implantação com a assinatura de locatário.

## <a name="onboard-tenant-to-azure-stack"></a>Locatário integrado ao Azure Stack

Configure o Azure Stack para oferecer suporte a usuários de vários locatários do AD do Azure para usar os serviços no Azure Stack. Para obter instruções, consulte [habilitar a multilocação no Azure Stack](azure-stack-enable-multitenancy.md).


## <a name="create-a-local-resource-in-the-end-customer-tenant-in-azure-stack"></a>Criar um recurso local no locatário do cliente final no Azure Stack

Depois que você adicionou o novo cliente para o Azure Stack ou Locatário do cliente final tiver habilitado sua conta de convidado com privilégios de proprietário, verifique se que você pode criar um recurso em seu locatário. Por exemplo, eles podem [criar uma máquina virtual do Windows com o portal do Azure Stack](user/azure-stack-quick-windows-portal.md).

## <a name="next-steps"></a>Próximas etapas

 - Para examinar as mensagens de erro se eles são disparados em seu processo de registro, consulte [mensagens de erro de registro de inquilinos](azure-stack-csp-ref-infrastructure.md#usage-and-billing-error-codes).
 - Para saber mais sobre como recuperar informações de uso de recursos do Azure Stack, consulte [uso e cobrança do Azure Stack](azure-stack-billing-and-chargeback.md).
 - Para examinar como um cliente final pode adicioná-lo, como o CSP, como o gerente de seus Azure Stack, locatário, consulte [permitem que um provedor de serviço de nuvem gerenciar sua assinatura do Azure Stack](user/azure-stack-csp-enable-billing-usage-tracking.md).
