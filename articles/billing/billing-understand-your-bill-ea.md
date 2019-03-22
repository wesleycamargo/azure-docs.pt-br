---
title: Entenda sua fatura do Azure Enterprise | Microsoft Docs
description: Saiba como ler e entender o uso e a fatura para clientes do Azure com um Enterprise Agreement
services: ''
documentationcenter: ''
author: adpick
manager: dougeby
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2018
ms.author: banders
ms.openlocfilehash: 16feb41707e5afa07054d20b620f5822eedad773
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57858905"
---
# <a name="understand-your-bill-for-azure-customers-with-an-enterprise-agreement"></a>Entenda sua fatura para clientes do Azure com um Enterprise Agreement

Os clientes do Azure com um Enterprise Agreement recebem uma fatura quando excedem o crédito da organização ou usam os serviços que não são cobertos pelo crédito. 

O crédito da sua organização inclui o seu compromisso monetário. O compromisso monetário é a quantidade que sua organização paga antecipadamente por uso de serviços do Azure. Você pode adicionar fundos de compromisso monetário para o Enterprise Agreement ao entrar em contato com seu gerente de conta da Microsoft ou o revendedor.  

## <a name="when-credit-exceeded-or-doesnt-apply"></a>Quando o crédito for excedido ou não se aplicar

Você obtém uma ou mais faturas quando ocorre o seguinte:

- **Excedente de serviço**: os encargos de uso da sua organização excederam o saldo de crédito.
- **Encargos cobrados separadamente**: os serviços que a sua organização usou não são cobertos pelo crédito. Você foi faturado pelos seguintes serviços, independentemente do seu saldo de crédito:
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
- **Encargos do Marketplace**: as compras do Microsoft Azure Marketplace e o uso não são cobertos pelo crédito de sua organização e são cobrados separadamente. O Administrador Corporativo tem a capacidade de habilitar e desabilitar as compras do Marketplace para sua organização no Portal Empresarial. 

Quando você tem encargos de vencimento para o excedente de serviço e encargos cobrados separadamente durante o período de cobrança, você obtém uma fatura que inclui ambos os tipos de encargos. Encargos de marketplaces sempre são faturados separadamente.

## <a name="review-charges"></a>Revisar encargos

Para examinar e verificar os encargos na sua fatura, você deve ser um Administrador Corporativo. Para obter mais informações, consulte [Entender as funções administrativas do Azure Enterprise Agreement no Azure](billing-understand-ea-roles.md). Se você não souber quem é o Administrador Corporativo da sua organização [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="review-service-overage-invoice"></a>Revisão da fatura excedente do serviço

Comparar o valor do seu uso total do portal empresarial no **Relatórios** > **Resumo de uso** com sua fatura excedente de serviço. A fatura excedente de serviço inclui o uso que excede o crédito de sua organização e/ou serviços que não são cobertos pelo crédito. Os valores do **Resumo de Uso** não incluem impostos. 

1. Entre no [Portal empresarial](https://ea.azure.com).
1. Selecione **Relatórios**.
1. No canto superior direito da guia, alterne a exibição de **M** a **C** e corresponda o período da fatura.
 
   ![Captura de tela que mostra a opção M + C no resumo de uso.](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)

1. A quantidade de **Uso Total** deve corresponder com a **Quantidade Total Estendida** na sua fatura excedente de serviço. A tabela a seguir lista os termos e as descrições mostradas na fatura e, no **Resumo de uso** no Portal corporativo:

   |Termo de fatura|Termo do resumo de uso|DESCRIÇÃO|
   |---|---|---|
   |Valor total estendido|Uso total|O encargo total de uso antes do imposto para o período específico antes do crédito é aplicado.|
   |Uso de compromisso|Uso de compromisso|Crédito aplicado durante o período específico.|
   |Venda Total|Excedente Total|O encargo total de uso que excede a quantidade de crédito. Esse valor não inclui impostos.|
   |Valor do imposto|Não aplicável|Imposto que se aplica à quantidade total das vendas para o período específico.|
   |Valor total|Não aplicável|O valor de vencimento da nota fiscal depois que o crédito é aplicado e o imposto é adicionado.|
1. Para obter mais informações sobre seus encargos, vá para **Baixar Uso** > **Download de Relatório Avançado**. Este relatório não inclui os impostos ou encargos do marketplace ou encargos para reservas.

      ![Captura de tela que o Download do relatório Avançado na guia de uso de Download.](./media/billing-understand-your-bill-ea/ea-portal-download-usage-advanced.png)

### <a name="review-marketplace-invoice"></a>Revisão da fatura do marketplace

Compare seu total no Azure Marketplace em **Relatórios** > **Resumo de uso** no Portal empresarial com sua fatura do marketplace. A fatura do marketplace é destinada apenas para uso e compras do Azure Marketplace. Os valores do **Resumo de Uso** não incluem impostos. 

1. Entre no [Portal empresarial](https://ea.azure.com).
1. Selecione **Relatórios**.
1. No canto superior direito da guia, alterne a exibição de **M** a **C** e corresponda o período da fatura.

     ![Captura de tela que mostra a opção M + C no resumo de uso.](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)

1. O total **Azure Marketplace** deve corresponder à **Venda Total** na sua fatura do marketplace.
1. Para obter mais informações sobre seus encargos, vá para **Baixar Uso**. Em **Encargos do Marketplace**, selecione **Download**. Este relatório não inclui os impostos ou mostra as compras de uso único.

     ![Captura de tela que mostra a opção de download nos encargos do Marketplace.](./media/billing-understand-your-bill-ea/ea-portal-download-usage-marketplace.png)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de Ajuda, [criar uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
