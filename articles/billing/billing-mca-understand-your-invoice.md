---
title: Entenda sua fatura de contrato de cliente da Microsoft | Microsoft Docs
description: Saiba como ler e entender sua fatura MCA
services: ''
documentationcenter: ''
author: jureid
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: e5dc8ac0716e194dd0949e2e270346ee338a792a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57839722"
---
# <a name="understand-terms-on-your-microsoft-customer-agreement-invoice"></a>Compreender os termos na sua fatura de contrato de cliente da Microsoft

Este artigo se aplicam a uma conta de cobrança para um contrato de cliente da Microsoft. [Verifique se você tem acesso a um contrato de cliente do Microsoft](#check-access-to-a-microsoft-customer-agreement).

Sua fatura fornece um resumo dos seus encargos e instruções para pagamento. Ela está disponível para download no formato .pdf (Portable Document Format) no [Portal do Azure](https://portal.azure.com/) ou pode ser enviada via email. Para obter mais informações, consulte [modo de exibição e download de fatura do Microsoft Azure](billing-download-azure-invoice.md).

<!-- ## When am I billed?

You are invoiced on a monthly basis. You can find out which day of the month you receive invoices by checking *invoice date* under billing profile properties in the [Azure portal](https://portal.azure.com/). Charges that occur between the end of the billing period and the invoice date are included in the next month's invoice, since they are in the next billing period. The billing period start and end dates for each invoice are listed in the invoice PDF above **Billing Summary**. -->

## <a name="invoice-terms-and-descriptions"></a>Descrições e termos de nota fiscal

As seções a seguir listam os termos importantes que você vê em sua fatura e descrições para cada termo.

### <a name="invoice-summary"></a>Resumo da fatura

O **Resumo da fatura** está na parte superior da primeira página e mostra informações sobre o perfil de cobrança e como você paga.

![Seção Resumo da fatura](./media/billing-understand-your-invoice-mca/invoicesummary.png)

| Prazo | Descrição |
| --- | --- |
| Vendido para |Endereço da entidade legal, encontrado em Propriedades da conta de cobrança|
| Endereço de Cobrança |Endereço do perfil de cobrança do recebimento da fatura de cobrança, encontrado em Propriedades de perfil de cobrança|
| Perfil de Cobrança |O nome do perfil de cobrança do recebimento da fatura |
| P.O. número |Um número de ordem de compra opcional, atribuído por você para acompanhamento |
| Número da nota fiscal |Um número de fatura exclusivo gerado pelo Microsoft usado para fins de acompanhamento |
| Data da fatura |Data em que a fatura é gerada, normalmente 5 a 12 dias após o fim do ciclo de cobrança. Você pode verificar a data da nota fiscal nas propriedades de perfil de cobrança.|
| Termos de pagamento |Como você paga por sua fatura da Microsoft. *Líquido 30 dias* significa que você paga pela verificação ou transferência de transmissão de 30 dias da data da nota fiscal. |

### <a name="billing-summary"></a>Resumo de cobrança

O **resumo de cobrança** mostra os encargos em relação ao perfil de cobrança desde o período de cobrança anterior, os créditos que foram aplicados, imposto e o valor total devido.

![Seção de resumo de cobrança](./media/billing-understand-your-invoice-mca/billingsummary.png)

| Prazo | Descrição |
| --- | --- |
| Encargos|Número total de encargos da Microsoft para este perfil de cobrança desde o último período de cobrança |
| Créditos |Créditos que você recebeu da retorna |
| Créditos do Azure aplicados | Créditos do Azure que são aplicados automaticamente para o Azure cobra cada período de cobrança |
| Subtotal |O valor antes do imposto devido |
| Imposto |O tipo e o valor do imposto que você paga, dependendo do país do seu perfil de cobrança. Se você não precisa pagar impostos, você não verá o imposto sobre sua fatura. |
| Economia total estimada |A quantidade total estimada que você salvou de descontos em vigor. Se aplicável, taxas de desconto em vigor serão listadas abaixo os itens de linha de compra em detalhes pela seção de nota fiscal. |

### <a name="invoice-sections"></a>Seções de fatura

Para cada seção de nota fiscal em seu perfil de cobrança, você verá os encargos, a quantidade de créditos aplicados, imposto e o valor total devido.

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>Detalhes pela seção de nota fiscal

Os detalhes mostram o custo para cada seção de nota fiscal dividido pela ordem de produto. Dentro de cada pedido de produto, o custo é dividido pelo tipo de serviço. Você pode encontrar os encargos de diário para seus produtos e serviços no portal do Azure e uso do Azure e encargos de CSV. Para saber mais, consulte [entender os encargos na sua fatura para um contrato de cliente do Microsoft](billing-mca-understand-your-bill.md).

O valor total devido para cada família de serviços é calculada subtraindo *créditos do Azure* de *créditos/encargos* e adicionando *imposto*:

<!-- `Total = Charges/Credits - Azure Credit + Tax` -->

![Detalhes pela seção de nota fiscal](./media/billing-understand-your-invoice-mca/invoicesectiondetails.png)

| Prazo |Descrição |
| --- | --- |
| Preço unitário | O preço de unidade efetivo do serviço (no preço moeda) que é usado para a taxa de uso. Isso é exclusivo para um produto, família de serviços, medidor e oferta. |
| Qtd. | Quantidade adquiridos ou consumido durante o período de cobrança |
| Encargos/créditos | Valor líquido de encargos após a aplicação de créditos/reembolsos |
| Crédito do Azure | A quantidade de créditos aplicados aos encargos/créditos|
| Taxa de imposto | Impostos dependendo do país |
| Valor do imposto | Valor do imposto aplicado para compra com base na taxa de imposto |
| Total | O valor total devido para a compra |

### <a name="how-to-pay"></a>Como pagar

Na parte inferior da fatura, há instruções para pagar sua fatura. Você pode pagar por verificação de transmissão, ou online. Se você pagar online, você pode usar um cartão de crédito/débito ou créditos do Azure, se aplicável.

### <a name="publisher-information"></a>Informações do editor

Se você tiver serviços de terceiros em sua fatura, o nome e endereço de cada publicador é listado na parte inferior da fatura.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso a um contrato de cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contate-nos

Se você tiver dúvidas ou precisar de Ajuda, [criar uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

- [Entender os encargos na fatura do seu perfil de cobrança](billing-mca-understand-your-bill.md)
- [Como obter sua fatura de cobrança e dados de uso diário do Azure](billing-download-azure-invoice-daily-usage-date.md)
- [Exiba os preços do Azure da sua organização](billing-ea-pricing.md)
- [Exibir documentos de imposto para o perfil de cobrança](billing-mca-download-tax-document.md)
