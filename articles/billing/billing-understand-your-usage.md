---
title: Entenda seu uso detalhado e encargos | Microsoft Docs
description: Saiba como ler e entender seu uso detalhado e encargos
author: bandersmsft
manager: micflan
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/24/2019
ms.author: banders
ms.openlocfilehash: 9ff9b6b5313026d2102b98659183fa97c6a5ef84
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683998"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Compreender os termos em seu arquivo de uso e os encargos do Azure

O arquivo de uso e encargos detalhado contém classificado uso diário, com base em taxas negociadas, compras (por exemplo, reservas, taxas do Marketplace) e reembolsos durante o período especificado.
Taxas não incluem créditos, impostos, ou outras cobranças ou descontos.
A seguinte tabela cobre quais encargos serão incluídos para cada tipo de conta.

Tipo de conta | Uso do Azure | Uso do Marketplace | Compras | Reembolsos
--- | --- | --- | --- | ---
EA (Enterprise Agreement) | Sim | sim | sim | Não 
MCA (Contrato de Cliente da Microsoft) | Sim | sim | sim | Sim
Pagamento conforme o uso (PAYG) | Sim | Não | Não | Não 

Para saber mais sobre os pedidos do Marketplace (também conhecidos como serviços externos), consulte [entenda os encargos de serviço externos do Azure](billing-understand-your-azure-marketplace-charges.md).

Ver [como obter sua fatura e o diário de dados de uso de cobrança do Azure](billing-download-azure-invoice-daily-usage-date.md) para obter instruções de download.
O arquivo de uso e encargos está disponível em um formato de arquivo de valores separados por vírgulas (. csv), que pode ser aberto em um aplicativo de planilha.

## <a name="list-of-terms-and-descriptions"></a>Lista de termos e descrições

A tabela a seguir descreve os termos importantes usados na versão mais recente do arquivo de uso e os encargos do Azure.
A lista aborda pré-pagas (PAYG), Enterprise Agreement (EA) e contas de contrato de cliente da Microsoft (MCA).

Termo | Tipo de conta | DESCRIÇÃO
--- | --- | ---
AccountName | EA | Nome de exibição da conta do registro.
Id de Proprietário de Conta | EA | Identificador exclusivo para a conta de registro.
Informações Adicionais | Todos | Metadados específicos ao serviço. Por exemplo, um tipo de imagem para uma máquina virtual.
BillingAccountId | EA, MCA | Identificador exclusivo para a conta de cobrança de raiz.
BillingAccountName | EA, MCA | Nome da conta de cobrança.
BillingCurrency | EA, MCA | Moeda associada com a conta de cobrança.
BillingPeriod | EA | O período de cobrança da cobrança.
BillingPeriodEndDate | EA, MCA | A data de término do período de cobrança.
billingPeriodStartDate | EA, MCA | A data de início do período de cobrança.
BillingProfileId | EA, MCA | Identificador exclusivo do registro de EA ou MCA perfil de cobrança.
BillingProfileName | EA, MCA | Nome do registro de EA ou MCA perfil de cobrança.
ChargeType | EA, MCA | Indica se o encargo representa uso (**uso**), uma compra (**comprar**), ou o reembolso (**reembolso**).
ConsumedQuantity | PAYG | Consulte Quantity.
Serviço Consumido | Todos | Nome do serviço o encargo está associado.
Custo | EA | Consulte CostInBillingCurrency.
CostCenter | EA, MCA | O Centro de custo definido para a assinatura para controlar custos (disponíveis somente em períodos de cobrança abertos para contas MCA).
CostInBillingCurrency | MCA | Custo dos encargos na moeda de cobrança antes de créditos ou outros impostos.
CostInPricingCurrency | MCA | Custo dos encargos na moeda do preço antes de créditos ou outros impostos.
Moeda | PAYG | Consulte BillingCurrency.
Data | EA, MCA | A data de compra ou de uso da cobrança.
ExchangeRateDate | MCA | Data em que a taxa de câmbio foi estabelecida.
ExchangeRatePricingToBilling | MCA | Taxa de câmbio usada para converter o custo na moeda do preço para a moeda de cobrança.
Frequência | EA, MCA | Indica se um encargo espera-se repita. Os encargos de pode a acontecer uma vez (**OneTime**), repetir em uma base mensal ou anual (**recorrente**), ou ser com base no uso (**UsageBased**).
IncludedQuantity | PAYG | A quantidade do medidor que está incluído sem custo adicional no período de cobrança atual.
InstanceId | PAGY | Consulte ResourceId.
InvoiceId | EA, MCA | A ID do documento exclusivo listada na fatura em PDF.
InvoiceSection | MCA | Consulte InvoiceSectionName.
InvoiceSectionId | EA, MCA | Identificador exclusivo para o departamento de EA ou a seção de nota fiscal MCA.
InvoiceSectionName | EA, MCA | Nome do departamento de EA ou seção de nota fiscal MCA.
IsAzureCreditEligible | EA, MCA | Indica se o custo é elegível para ser pago para usar os créditos do Azure (valores: True, False).
Local padrão | EA, MCA | Local do Datacenter onde o recurso está em execução.
Categoria do Medidor | Todos | Nome da categoria de classificação para o medidor. Por exemplo, *serviços de nuvem* e *Networking*.
MeterId | Todos | O identificador exclusivo para o medidor.
Nome do Medidor | Todos | O nome do medidor.
Região do Medidor | Todos | Nome do data center local para serviços com preços estipulados com base no local. Veja a localização.
Sub-categoria do Medidor | Todos | Nome da categoria subclassificação medidor.
OfferId | EA, MCA | Nome da oferta adquirida.
PartNumber | EA | Identificador usado para obter preços específico medidor.
PlanName | EA | Nome do plano do Marketplace.
PreviousInvoiceId | MCA | Referência a uma fatura original se este item de linha é um reembolso.
pricingCurrency | MCA | Moeda usada quando a classificação com base nos preços negociados.
Produto | MCA | Consulte ProductName.
ProductId | EA, MCA | Identificador exclusivo para o produto.
ProductName | EA | O nome do produto.
ProductOrderId | EA, MCA | Identificador exclusivo para a ordem de produto.
ProductOrderName | EA, MCA | Nome exclusivo para a ordem de produto.
PublisherName | EA, MCA | Publicador para os serviços do Marketplace.
PublisherType | EA, MCA | Tipo de publicador (valores: firstParty, thirdPartyReseller, thirdPartyAgency).
Quantidade | EA, MCA | O número de unidades comprados ou consumido.
Tarifa | PAYG | Consulte UnitPrice.
ReservationId | EA, MCA | Identificador exclusivo para a instância de reserva adquirida.
ReservationName | EA, MCA | Nome da instância de reserva adquirida.
ResourceGroupId | EA, MCA | Identificador exclusivo para o [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) o recurso está em.
ResourceGroupName | EA, MCA | Nome da [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) o recurso está em.
ResourceId | EA, MCA | Identificador exclusivo do [do Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources) recursos.
Localização do Recurso | EA, MCA | Local do Datacenter onde o recurso está em execução. Veja a localização.
ResourceName | EA | Nome do recurso.
ResourceType | MCA | Tipo de instância do recurso.
ServiceFamily | EA, MCA | Família de serviços que o serviço pertence.
Informações de Serviço 1 | Todos | Metadados específicos ao serviço.
Informações de Serviço 2 | Todos | Campo herdado com metadados específicos do serviço opcional.
ServicePeriodEndDate | MCA | A data de término do período de classificação que definidos e bloqueada de preço para o serviço consumido ou comprado.
ServicePeriodStartDate | MCA | A data de início do período de classificação que definidos e bloqueada de preço para o serviço consumido ou comprado.
SubscriptionId | Todos | Identificador exclusivo para a assinatura.
SubscriptionName | Todos | Nome da assinatura.
Marcas | Todos | Marcas atribuídas ao recurso. Não inclui as marcas do grupo de recursos. Pode ser usado para agrupar ou distribuir os custos de estorno interno. Para saber mais, confira [Organizar os recursos do Azure com marcas](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/).
Unidade | PAYG | Consulte UnitOfMeasure.
Unidade de Medida | Todos | A unidade de medida de cobrança para o serviço. Por exemplo, os serviços de computação são cobrados por hora.
UnitPrice | EA | O preço unitário do encargo.
UsageDate | PAYG | Consulte Data.

Observe que alguns campos podem diferir em maiusculas e minúsculas e o espaçamento entre os tipos de conta.
Versões mais antigas de arquivos de uso pago conforme o uso tem seções separadas para a instrução e o uso diário.

## <a name="ensure-that-your-charges-are-correct"></a>Certifique-se de que seus encargos estão corretos

Para saber mais sobre o uso detalhado e encargos, leia sobre como entender seus [pré-pagas](./billing-understand-your-bill.md) ou [contrato de cliente do Microsoft](billing-mca-understand-your-bill.md) nota fiscal.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

- [Exibir e baixar sua fatura do Microsoft Azure](billing-download-azure-invoice.md)
- [Exibir e baixar seus encargos e o uso do Microsoft Azure](billing-download-azure-daily-usage.md)
