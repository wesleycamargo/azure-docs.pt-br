---
title: Acompanhar o saldo de crédito do Azure para o acordo de cliente da Microsoft | Microsoft Docs
description: Saiba como verificar o saldo de crédito do Azure para o acordo de cliente da Microsoft.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: amberb
ms.openlocfilehash: 1e8c3e6863b9cd8f2f5ced18a57918c32c865e75
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57544589"
---
# <a name="track-azure-credit-balance-for-microsoft-customer-agreement"></a>Acompanhar o saldo de crédito do Azure para o acordo de cliente da Microsoft

Você pode verificar o saldo de crédito do Azure para o acordo de cliente da Microsoft no portal do Azure. Você pode usar créditos para pagar produtos que são cobertos pelos créditos.

Você é cobrado quando você usa os produtos que não são cobertos pelos créditos ou seu uso excede o saldo de crédito. Para obter mais informações, consulte [produtos que não são cobertos pelo créditos do Azure.](#products-that-arent-covered-by-azure-credits)

Este artigo se aplicam a uma conta de cobrança para um contrato de cliente da Microsoft. [Verifique se você tem acesso a um contrato de cliente do Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-credit-balance-in-the-azure-portal"></a>Verificar saldo de crédito no portal do Azure

1. Entre no [Portal do Azure]( https://portal.azure.com).

2. Pesquise **Gerenciamento de Custos do Azure + Cobrança**.

   ![Captura de tela que mostra a pesquisa no portal de gerenciamento de custos + cobrança](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3. Vá para o perfil de cobrança. Dependendo do seu acesso, você precisa selecionar uma conta de cobrança. Na conta de cobrança, selecione **perfis de cobrança** e, em seguida, um perfil de cobrança.

4. Selecione **créditos do Azure**.

5. A página de créditos do Azure exibe as seguintes informações:

   ![Captura de tela de saldo de crédito e transações para um perfil de cobrança](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | Termo               | Definição                           |
   |--------------------|--------------------------------------------------------|
   | Saldo estimado  | A quantidade estimada de créditos que você tem depois de considerar todos os cobrado e as transações pendentes |
   | Saldo atual    | Valor dos créditos a partir de sua última fatura. Ele não inclui as transações pendentes |
   | Transações       | Todas as transações de cobrança que afetava o saldo de crédito do Azure |

   Quando o saldo a estimados cai a 0, você é cobrado para todo o seu uso, incluindo para os produtos que são cobertos pelos créditos.

6. Selecione **lista de créditos** para exibir uma lista de créditos para o perfil de cobrança. A lista de créditos fornece as seguintes informações:

   ![Captura de tela de listas de créditos para um perfil de cobrança](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | Termo                 | Definição                           |
   |----------------------|--------------------------------------------------------|
   | Fonte               | A fonte de aquisição do crédito |
   | Data inicial           | A data quando você tiver adquirido o crédito |
   | Data de validade      | A data de vencimento do crédito |
   | Saldo              | O saldo a partir de sua última fatura |
   | Valor original      | A quantidade original de crédito |
   | Status               | O status atual de crédito. Status pode estar ativo, usado, expirado ou expirando |

## <a name="how-credits-are-used-in-microsoft-customer-agreement"></a>Como os créditos são usados no contrato de cliente da Microsoft

Em uma conta de cobrança para um contrato de cliente da Microsoft, você pode usar perfis de cobrança para gerenciar seus métodos de pagamento e notas fiscais. Uma fatura mensal é gerada para cada perfil de cobrança e usar os métodos de pagamento para pagar a nota fiscal.

Os créditos do Azure são um dos métodos de pagamento. Você obtém crédito da Microsoft, como o crédito promocional e o crédito de nível de serviço. Esses créditos são atribuídos a um perfil de cobrança. Quando uma fatura é gerada para o perfil de cobrança, os créditos são aplicados automaticamente para a quantidade total cobrada para calcular o valor que você precisa pagar. Pagar o valor restante com outro método de pagamento, como seleção ou transferência de transmissão.

## <a name="products-that-arent-covered-by-azure-credits"></a>Produtos que não são cobertos pelo créditos do Azure

 Os produtos a seguir não são cobertos por seus créditos do Azure. Você será cobrado para usar esses produtos, independentemente de seu saldo de crédito:

- Canônico
- Citrix XenApp Essentials
- Citrix XenDesktop 
- Usuário registrado
- OpenLogic
- Direitos de Acesso Remoto XenApp Essentials
Usuário Registrado
- Ubuntu Advantage
- Visual Studio Enterprise (mensal)
- Visual Studio Enterprise (anual)
- Visual Studio Professional (mensal)
- Visual Studio Professional (anual)
- Produtos do Azure Marketplace
- Planos de Suporte do Azure

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso a um contrato de cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se precisar de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

## <a name="next-steps"></a>Próximas etapas

- [Entenda a conta de cobrança do contrato de cliente da Microsoft](billing-mca-overview.md)
- [Compreender os termos na sua fatura de contrato de cliente da Microsoft](billing-mca-understand-your-invoice.md)