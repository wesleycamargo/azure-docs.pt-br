---
title: Gerenciar o uso e cobrança para o Azure Stack como um provedor de serviços de nuvem | Microsoft Docs
description: Um passo a passo de registro do Azure Stack como um provedor de nuvem (CSP) e adicionando os clientes para cobrança.
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
ms.date: 02/13/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 1ba3697b5c683ed2e892396db71328e0ed41fdce
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56266910"
---
# <a name="manage-usage-and-billing-for-azure-stack-as-a-cloud-service-provider"></a>Gerenciar o uso e cobrança para o Azure Stack como um provedor de serviços de nuvem

*Aplica-se a: Sistemas integrados do Azure Stack*

Este artigo orienta você por meio do registro do Azure Stack como um provedor de nuvem (CSP) e adicionando os clientes.

Como um CSP, você trabalha com diversos clientes usando o Azure Stack. Cada cliente tem uma assinatura do CSP no Azure. Você deve direcionar o uso do Azure Stack para cada assinatura do usuário.

A figura a seguir mostra as etapas necessárias para escolher sua conta de serviços compartilhados e registrar a conta do Azure com a conta do Azure Stack. Depois de registrado, você pode integrar seus clientes finais:

[![Processo para habilitar o uso e gerenciamento como um provedor de serviços de nuvem](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png "processo para habilitar o uso e gerenciamento como um provedor de serviços de nuvem")](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png#lightbox)

## <a name="create-a-csp-or-apss-subscription"></a>Criar uma assinatura de CSP ou APSS

### <a name="cloud-service-provider-subscription-types"></a>Tipos de assinatura do provedor de serviços de nuvem

Escolha o tipo de conta de serviços compartilhados que você usa para o Azure Stack. Os tipos de assinaturas que podem ser usados para o registro de um multilocatário do Azure Stack são:

- Provedor de serviços de nuvem
- Assinatura de parceiro de serviços compartilhados

#### <a name="azure-partner-shared-services"></a>Serviços compartilhados do parceiro do Azure

Assinaturas de parceiro Shared Services (APSS) do Azure são a opção preferencial para o registro quando um CSP direto ou um distribuidor CSP opera o Azure Stack.

Assinaturas de APSS são associadas um locatário de serviços compartilhados. Quando você registra o Azure Stack, você forneça credenciais para uma conta que seja um proprietário da assinatura. A conta usada para registrar o Azure Stack pode ser diferente da conta de administrador que você pode usar para a implantação. Além disso, as duas contas não precisam pertencer ao mesmo domínio; Você pode implantar usando o locatário que você já usa. Por exemplo, você pode usar `ContosoCSP.onmicrosoft.com`, em seguida, registre-se usando um locatário diferente; por exemplo, `IURContosoCSP.onmicrosoft.com`. Você deve se lembrar de entrar usando `ContosoCSP.onmicrosoft.com` quando você executa a administração diária do Azure Stack. Entre Azure usando `IURContosoCSP.onmicrosoft.com` quando você precisa fazer operações de registro.

Para obter uma descrição das assinaturas de APSS e como criá-los, consulte [adicionar parceiro do Azure Shared Services](https://msdn.microsoft.com/partner-center/shared-services).

#### <a name="csp-subscriptions"></a>Assinaturas de CSP

Assinaturas de nuvem do provedor de serviços (CSP) são a opção preferencial para o registro quando um revendedor CSP ou um cliente final opera o Azure Stack.

## <a name="register-azure-stack"></a>Registrar o Azure Stack

Use a assinatura APSS criada usando as informações na seção anterior para registrar o Azure Stack com o Azure. Para obter mais informações, consulte [registrar o Azure Stack com sua assinatura do Azure](azure-stack-registration.md).

## <a name="add-end-customer"></a>Adicionar cliente final

Para configurar o Azure Stack, de modo que quando um novo locatário usa recursos, seu uso é relatado à sua assinatura do provedor de serviços de nuvem (CSP), consulte [adicionar o locatário para uso e cobrança para o Azure Stack](azure-stack-csp-howto-register-tenants.md).

## <a name="charge-the-right-subscriptions"></a>Cobramos as assinaturas à direita

O Azure Stack usa um recurso chamado de registro. Um registro é um objeto armazenado no Azure. O objeto de registro documenta quais assinaturas do Azure para usar a cobrança para um determinado Azure Stack. Esta seção aborda a importância de registro.

Usando o registro do Azure Stack pode:

- Encaminhar dados de uso do Azure Stack para comércio do Azure e cobraremos uma assinatura do Azure.
- Cada cliente de relatório ' uso de s em uma assinatura diferente com uma implantação de multilocatário do Azure Stack. A multilocação permite que o Azure Stack oferecer suporte a organizações diferentes na mesma instância do Azure Stack.

Para cada pilha do Azure, há uma assinatura padrão e muitas assinaturas de locatários. A assinatura padrão é uma assinatura do Azure que é cobrada se não houver nenhuma assinatura específica do locatário. Ele deve ser a primeira assinatura a ser registrado. Para o uso de vários locatário relatório seja executado, a assinatura deve ser uma assinatura de CSP ou APSS.

Em seguida, o registro é atualizado com uma assinatura do Azure para cada locatário que usa o Azure Stack. As assinaturas de locatários devem ser do tipo CSP e reverta para o parceiro que possui a assinatura padrão. Você não pode registrar os clientes de outra pessoa.

Quando o Azure Stack encaminha as informações de uso para o Azure global, um serviço no Azure consulta o registro e mapeia o uso de cada locatário para a assinatura de locatário apropriada. Se um locatário não foi registrado, o que o uso de vai para a assinatura padrão para a instância do Azure Stack do qual ele foi originado.

Como as assinaturas de locatários são assinaturas de CSP, sua fatura é enviada para o parceiro CSP e informações de uso não são visíveis para o cliente final.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre o programa CSP, consulte [programa Cloud Solution Provider](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
- Para saber mais sobre como recuperar informações de uso de recursos do Azure Stack, consulte [uso e cobrança do Azure Stack](azure-stack-billing-and-chargeback.md).
