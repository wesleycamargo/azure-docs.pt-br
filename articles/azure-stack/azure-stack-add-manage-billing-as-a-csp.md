---
title: "Gerenciar o uso e cobrança para a pilha do Azure como um provedor de serviços de nuvem | Microsoft Docs"
description: Um passo a passo Registrando a pilha do Azure como um provedor de nuvem e adicionando os clientes.
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
ms.date: 02/27/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: 23e3a675e6a464c92d26df220c8126c970f590a0
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="manage-usage-and-billing-for-azure-stack-as-a-cloud-service-provider"></a>Gerenciar o uso e cobrança para a pilha do Azure como um provedor de serviços de nuvem 

*Aplica-se a: sistemas integrados de pilha do Azure*

Este artigo orienta você por registrar pilha do Azure como um provedor de nuvem (CSP) e adição de clientes.

Como um CSP, você é provável que muitos clientes diferentes que usam a pilha do Azure. Cada cliente tem uma assinatura do CSP no Azure, e você precisará direcionar o uso da pilha do Azure, a assinatura de cada usuário.

O diagrama a seguir mostra as etapas que você precisa escolher a conta de serviços compartilhados e registrar a conta do azure com a conta. Quando isso for feito, você pode integrar seus clientes finais.

**Etapas para adicionar o uso de rastreamento como um CSP**

![Processo para habilitar o uso e gerenciamento como um provedor de serviços de nuvem.](media\azure-stack-add-manage-billing-as-a-csp\process-add-useage-as-a-csp.png)

## <a name="create-a-csp-or-cspss-subscription"></a>Criar uma assinatura de CSP ou CSPSS

### <a name="cloud-service-provider-subscription-types"></a>Tipos de assinatura do provedor de serviços de nuvem

Você precisará escolher o tipo de conta de serviços compartilhados que você usa para a pilha do Azure. Os tipos de assinaturas que podem ser usados para o registro de um multilocatário pilha do Azure são:

 - Provedor de serviços de nuvem 
 - Assinatura de parceiro de serviços compartilhados 

#### <a name="csp-shared-services"></a>Serviços compartilhados do CSP

Assinaturas de serviços de compartilhado de provedor de serviço (CSPSS) de nuvem são a opção preferencial para o registro quando um CSP direta ou um distribuidor de CSP opera pilha do Azure.

Assinaturas de CSPSS são associadas um locatário de serviços compartilhados. Quando você registra a pilha do Azure, você precisa fornecer credenciais para uma conta que é proprietário da assinatura. A conta usada para registrar a pilha do Azure pode ser diferente da conta de administrador que você usa para a implantação; fazer os dois *não* devem pertencer ao mesmo domínio. Em outras palavras, você pode implantar usando o locatário que você já usa. Por exemplo você pode usar ContosoCSP.onmicrosoft.com e registrar usando um locatário diferente, por exemplo IURContosoCSP.onmicrosoft.com. Você precisará Lembre-se de que você faça logon usando ContosoCSP.onmicrosoft.com quando você fizer a administração do dia para fazer pilha do Azure. Quando você entrar no Azure usando IURContosoCSP.onmicrosoft.com quando você precisa realizar as operações de registro.

Consulte o seguinte para obter uma descrição de assinaturas CSPSS e instruções sobre como criar assinatura [adicionar parceiro do Azure Shared Services](https://msdn.microsoft.com/partner-center/shared-services).

#### <a name="csp-subscriptions"></a>Assinaturas de CSP

Assinaturas de serviço CSP (provedor) de nuvem são a opção preferencial para o registro quando um revendedor CSP ou cliente final opera pilha do Azure.

## <a name="register-azure-stack"></a>Registrar a pilha do Azure

Para registrar-se com a pilha do Azure, consulte [registrar pilha do Azure com sua assinatura do Azure](azure-stack-registration.md).

## <a name="add-end-customer"></a>Adicionar cliente final

Para configurar a pilha do Azure para que quando um novo locatário usa recursos de seu uso será relatado à sua assinatura do provedor de serviços de nuvem (CSP), consulte [adicionar locatário para uso e cobrança para pilha Azure](azure-stack-csp-howto-register-tenants.md).

## <a name="charge-the-right-subscriptions"></a>Cobrar as assinaturas à direita

A pilha do Azure usa um recurso chamado de registro. Um registro é um objeto, armazenado no Azure, que documenta quais assinaturas do Azure para usar a cobrar por uma pilha do Azure determinada. Esta seção aborda a importância de registro.

Usando o registro de pilha do Azure pode:
 - Encaminhar dados de uso da pilha do Azure para comércio do Azure e cobrar por uma assinatura do Azure.
 - Relatório de uso de cada cliente em uma assinatura diferente com uma implantação de multilocatário de pilha do Azure. Multilocação permite que a pilha do Azure dar suporte a diferentes organizações na mesma instância de pilha do Azure.

Para cada pilha do Azure, há uma assinatura padrão e como muitas assinaturas de locatários, conforme necessário. A assinatura padrão é uma assinatura do Azure que será cobrada se não houver nenhuma assinatura de locatário específico. Ele deve ser o primeiro a ser registrado. Para o uso de vários locatários reporting para trabalhar, a assinatura deve ser um CSP ou CSPSS uma assinatura.

Em seguida, o registro é atualizado com uma assinatura do Azure para cada locatário que irá usar a pilha do Azure. Assinaturas de locatários devem ser do tipo de CSP e reverta para o parceiro que possui a assinatura padrão. Em outras palavras, você não pode registrar os clientes de outra pessoa.

Quando o Azure pilha encaminha informações de uso para o Azure global, um serviço no Azure consulta o registro e o uso de cada locatário é mapeado para a assinatura de locatário apropriado. Se um locatário não tiver sido registrado, o que o uso vai para a assinatura padrão para a instância de pilha do Azure do qual ele foi originado.

Como assinaturas de locatários são assinaturas de CSP, sua fatura é enviada ao parceiro de CSP e informações de uso não são visíveis para o cliente final.



## <a name="next-steps"></a>Próximas etapas

 - Para saber mais sobre o programa CSP, consulte [programa do provedor de soluções de nuvem](https://partnercenter.microsoft.com/en-us/partner/programs).
 - Para saber mais sobre como recuperar informações de uso do recurso da pilha do Azure, consulte [uso e cobrança no Azure pilha](/azure-stack-billing-and-chargeback.md).
