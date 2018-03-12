---
title: "Adicionar locatários pelo uso e cobrança para a pilha do Azure | Microsoft Docs"
description: "As etapas necessárias adicionam um usuário final a pilha do Azure gerenciados por um provedor de serviço de nuvem."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: 67e5a67d7cd5caf6bd4d2625969b139411d62696
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="add-tenant-for-usage-and-billing-to-azure-stack"></a>Adicionar locatário para uso e cobrança para a pilha do Azure

*Aplica-se a: sistemas integrados de pilha do Azure*

Este artigo descreve as etapas necessárias adicionar um usuário final a pilha do Azure gerenciados por um provedor de serviços de nuvem (CSP). Quando o novo locatário usa recursos, o Azure pilha relatará uso à sua assinatura do CSP.

CSPs geralmente oferecem serviços a vários clientes (locatários) em sua implantação da pilha do Azure. Adicionando locatários para o registro de pilha do Azure garante que o uso de cada locatário será relatado e cobrado para a assinatura correspondente do CSP. Se você não concluir as etapas neste artigo, o uso de locatário é cobrado para a assinatura usada no registro inicial da pilha do Azure. Antes de adicionar um cliente final para a pilha do Azure para rastreamento de uso e gerenciar seu locatário, você precisará configurar a pilha do Azure como um CSP. Para etapas e recursos, consulte [gerenciar o uso e cobrança para a pilha do Azure como um provedor de serviços de nuvem](azure-stack-add-manage-billing-as-a-csp.md).

O diagrama a seguir mostra as etapas que precisará de um CSP a seguir para habilitar um novo cliente para usar a pilha do Azure e configurar o uso de rastreamento para o cliente. Adicionando o cliente final, também será gerenciar recursos na pilha do Azure. Você tem duas opções para gerenciar seus recursos:

1. Você pode manter o locatário do cliente final e fornecer credenciais para a assinatura do Azure pilha local para o cliente final.  
2. Ou o cliente final pode trabalhar com sua assinatura localmente e adicione o CSP como convidado com permissões de proprietário.  

**Etapas para adicionar um cliente final**

![Configurar o provedor de serviços de nuvem para rastreamento de uso e para gerenciar a conta do cliente final](media\azure-stack-csp-enable-billing-usage-tracking\process-csp-enable-billing.png)

## <a name="create-a-new-customer-in-partner-center"></a>Criar um novo cliente no Centro de parceiros

No Centro de parceiros, crie uma nova assinatura do Azure para o cliente. Para obter instruções, consulte [adicionar um novo cliente](https://msdn.microsoft.com/partner-center/add-a-new-customer).


##  <a name="create-an-azure-subscription-for-the-end-customer"></a>Criar uma assinatura do Azure para o cliente final

Depois de criar um registro de cliente no Centro de parceiros, você pode vendem assinaturas para produtos do catálogo. Para obter instruções, consulte [criar, suspender ou Cancelar assinaturas de cliente](https://msdn.microsoft.com/partner-center/create-a-new-subscription).

## <a name="create-a-guest-user-in-the-end-customer-directory"></a>Criar um usuário convidado no diretório de clientes finais

Se o cliente final gerenciar sua própria conta, crie um usuário convidado em seu diretório e enviar as informações elas. O usuário final será, em seguida, adicione o convidado e elevar as permissões de convidado para **proprietário** para a conta do Azure pilha CSP.
 
## <a name="update-the-registration-with-the-end-customer-subscription"></a>Atualizar o registro com a assinatura do cliente final

Atualize o registro com a nova assinatura do cliente. Azure relatórios de uso do cliente usando a identidade do cliente do parceiro Central. Essa etapa garante que o uso de cada cliente é relatado na assinatura de CSP individual do cliente. Acompanhamento de uso do usuário e a cobrança isso torna muito mais fácil.

> [!Note]  
> Para executar essa etapa, você deve ter [registrado Azure pilha](azure-stack-register.md).

1. Abra o Windows PowerShell com um prompt com privilégios elevados e execute:  
    `Login-AzureRmAccount`
2. Digite suas credenciais do Azure.
3. Na sessão do PowerShell, execute:

```powershell
    New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```
### <a name="new-azurermresource-powershell-parameters"></a>Parâmetros do PowerShell New-AzureRmResource
| Parâmetro | DESCRIÇÃO |
| --- | --- | 
|registrationSubscriptionID | A assinatura do Azure que foi usada para o registro inicial da pilha do Azure. |
| customerSubscriptionID | A assinatura do Azure (não pilha do Azure) que pertencem ao cliente a ser registrado. Deve ser criado na oferta do CSP; Na prática, isso significa, por meio do Centro de parceiros. Se um cliente tiver mais de um locatário do Active Directory do Azure, essa assinatura deve ser criada no locatário que será usado para fazer logon na pilha do Azure.
| resourceGroup | O grupo de recursos no Azure no qual o registro está armazenado. 
| registrationName | O nome do registro de pilha do Azure. É um objeto armazenado no Azure. | 


> [!Note]  
> Os locatários precisam ser registrados com cada pilha do Azure que eles usam. Se você tem duas implantações de pilha do Azure e um locatário irá usar ambos os parâmetros, você precisa atualizar os registros inicias de cada implantação com a assinatura de locatário.

## <a name="onboard-tenant-to-azure-stack"></a>Locatário integrado à pilha do Azure

Configure a pilha do Azure para oferecer suporte a usuários de vários locatários do AD do Azure para usar os serviços na pilha do Azure. Para obter instruções, consulte [habilitar multilocação na pilha do Azure](azure-stack-enable-multitenancy.md).


## <a name="create-a-local-resource-in-the-end-customer-tenant-in-azure-stack"></a>Criar um recurso local no locatário do cliente final na pilha do Azure

Depois que você adicionou o novo cliente para a pilha do Azure ou o locatário do cliente final tiver habilitado a conta com privilégios de proprietário, verifique se que você pode criar um recurso em seu locatário. Por exemplo, eles podem [criar uma máquina virtual do Windows com o portal do Azure pilha](user\azure-stack-quick-windows-portal.md).

## <a name="next-steps"></a>Próximas etapas

 - Para examinar as mensagens de erro se eles são acionados no processo de registro, consulte [mensagens de erro de registro de inquilinos](azure-stack-csp-ref-infrastructure.md#usage-and-billing-error-codes).
 - Para saber mais sobre como recuperar informações de uso do recurso da pilha do Azure, consulte [uso e cobrança no Azure pilha](/azure-stack-billing-and-chargeback.md).
 - Para examinar como um cliente final pode adicioná-lo, como o CSP, como o Gerenciador de sua pilha do Azure, de locatário, consulte [habilitar um provedor de serviços de nuvem gerenciar sua assinatura do Azure pilha](user\azure-stack-csp-enable-billing-usage-tracking.md).