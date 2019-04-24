---
title: Compreender os termos na sua encargos CSV e o uso do Azure para um contrato de cliente da Microsoft | Microsoft Docs
description: Saiba como ler e entender as seções do CSV de encargos para o perfil de cobrança e uso do Azure
services: ''
documentationcenter: ''
author: bandersmsft
manager: alherz
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: banders
ms.openlocfilehash: 8f71f42386ce49d4d7178cb03d28d74edacd7e39
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60371301"
---
# <a name="understand-terms-on-your-azure-usage-and-charges-csv-for-a-microsoft-customer-agreement"></a>Compreender os termos na sua encargos CSV e o uso do Azure para um contrato de cliente da Microsoft

Este artigo se aplicam a uma conta de cobrança para um contrato de cliente da Microsoft. [Verifique se você tem acesso a um contrato de cliente do Microsoft](#check-access-to-a-microsoft-customer-agreement).

O arquivo CSV de uso e encargos do Azure contém os encargos de nível de uso diário e medidor para o período de cobrança atual.

Para obter o arquivo de uso e os encargos do Azure, consulte [modo de exibição e download de uso do Azure e encargos para o contrato de cliente da Microsoft](billing-download-azure-daily-usage.md).
Ele está disponível em um formato de arquivo .csv (valores separados por vírgulas) que pode ser aberto em um aplicativo de planilhas.

Encargos de uso são o total de encargos **mensais** em uma assinatura. Os encargos de uso não levam em consideração nenhum crédito nem desconto.

## <a name="changes-in-the-enterprise-agreement-azure-usage-and-charges-csv"></a>Alterações no uso do Enterprise Agreement do Azure e os encargos de CSV

Se você fosse um cliente EA, você observará que os termos no perfil de cobrança arquivo CSV de uso do Azure são diferentes de termos no arquivo CSV de uso do EA Azure. Aqui está um mapeamento dos termos de uso EA para termos de uso do perfil de cobrança:

| CSV de uso do EA Azure | Uso do Microsoft Azure contrato de cliente e encargos de CSV |
| --- | --- |
| Data | date |
| Mês| date |
| Dia | date |
| Ano | date |
| Produto | product |
| MeterId | meterID |
| Categoria do Medidor | meterCategory |
| Sub-categoria do Medidor | meterSubCategory |
| Região do Medidor | meterRegion |
| Nome do Medidor | meterName |
| ConsumedQuantity | quantidade |
| Taxa de Recursos | effectivePrice | <!-- this was highlighted -->
| Custos Estendidos | cost |
| Localização do Recurso | resourceLocation |
| Serviço Consumido | consumedService |
| InstanceId | instanceId |
| Informações de Serviço 1 | serviceInfo1 |
| Informações de Serviço 2 | serviceInfo2 |
| Informações Adicionais | additionalInfo |
| Marcas | marcas |
| Identificador de Serviço da Loja | N/D |
| DepartmentName | invoiceSection | <!-- this was highlighted -->
| CostCenter | costCenter |
| Unidade de Medida | unitofMeasure |
| ResourceGroup | resourceGroup |
| ChargesBilledSeparately | isAzureCreditEligible | <!-- this was highlighted -->

<!-- TO DO: Marketplace CSV? -->

## <a name="detailed-terms-and-descriptions-in-your-azure-usage-and-charges-file"></a>Descrições em seu arquivo de uso e os encargos do Azure e termos detalhados

A seção a seguir descreve os termos importantes mostrados no arquivo de uso e os encargos do Azure.

Termo | DESCRIÇÃO
--- | ---
invoiceId | O ID exclusivo do documento listado na fatura em PDF
previousInvoiceId | Referência a uma fatura original se este item de linha é um reembolso
billingAccountName | Nome da conta de cobrança
billingAccountId | Identificador exclusivo para a conta de cobrança de raiz
billingProfileId | Nome do perfil cobrança acumulado os encargos são faturados
billingProfileName | Identificador exclusivo para o perfil de cobrança acumulado os encargos são faturados
invoiceSectionId | Identificador exclusivo para a seção de nota fiscal
invoiceSectionName | Nome da seção da nota fiscal
costCenter | O Centro de custo definido na assinatura para controlar custos (disponíveis somente em períodos de cobrança abertos)
billingPeriodStartDate | A data de início do período de cobrança para o qual a fatura é gerada
billingPeriodEndDate | A data de término do período de cobrança para o qual a fatura é gerada
servicePeriodStartDate | A data de início do período de classificação que definiu e bloqueada de preço para o serviço consumido ou comprado
servicePeriodEndDate | A data de término do período de classificação que definiu e bloqueada de preço para o serviço consumido ou comprado
date | Para os encargos com base no uso do Azure e o Marketplace, esta é a data de classificação. Para compras de uso únicas (reservas, Marketplace) ou encargos recorrentes fixos (ofertas de suporte), essa é a data de compra.
serviceFamily | Família de serviços que o serviço pertence
productOrderId | Identificador exclusivo para o pedido do produto
productOrderName | Nome exclusivo para a ordem de produto
consumedService | Nome do serviço consumido
meterId | O identificador exclusivo para o medidor
meterName | O nome do medidor
meterCategory | Nome da categoria de classificação para o medidor. Por exemplo, *serviços de nuvem*, *Networking*, etc.
meterSubCategory | Nome da categoria de classificação subpropriedades de medidor
meterRegion | Nome da região em que o medidor para o serviço está disponível. Identifica o local do data center para certos serviços que são cobrados com base no local do data center.
oferta | Nome da oferta adquirida
productId | Identificador exclusivo para o produto de acúmulo de encargos
product | Nome do produto acúmulo de encargos
ID da assinatura | Identificador exclusivo para a assinatura de acúmulo de encargos
subscriptionName | Nome da assinatura do acúmulo de encargos
reservationId | Identificador exclusivo para a instância de reserva adquirida
reservationName | Nome da instância de reserva adquirida
publisherType | Tipo de publicador (valores: firstParty, thirdPartyReseller, thirdPartyAgency)
publisherName | Publicador para os serviços do Marketplace
resourceGroupId | Identificador exclusivo para o grupo de recursos associado ao recurso
resourceGroupName | Nome do grupo de recursos associado ao recurso
resourceId | Identificador exclusivo para a instância do recurso
resourceType | Tipo de instância do recurso
resourceLocation | Identifica o local do data center onde o recurso está em execução.
location | Normalizado local do recurso se os locais de recursos diferentes são configurados para as regiões do mesmas
quantidade | O número de unidades comprados ou consumidos
unitOfMeasure | A unidade de medida de cobrança para o serviço. Por exemplo, os serviços de computação são cobrados por hora.
chargeType | O tipo de carga. Valores: <ul><li>AsCharged-Usage: Os encargos são acumulados com base no uso de um serviço do Azure. Isso inclui o uso em relação a máquinas virtuais que não são cobradas por causa de instâncias reservadas.</li><li>AsCharged-PurchaseMarketplace: Encargos recorrentes únicos ou fixos de compras do Marketplace</li><li>AsCharged-UsageMarketplace: Encargos para serviços do Marketplace que são cobrados com base em unidades de consumo</li></ul>
isAzureCreditEligible | Sinalizador que indica se o custo em relação ao serviço é elegível para ser pago para usar os créditos do Azure (valores: Verdadeiro, FALSO)
serviceInfo1 | Metadados específicos do serviço
serviceInfo2 | Campo herdado que captura os metadados específicos do serviço opcional
additionalInfo | Metadados adicionais de específicos do serviço.
marcas | Atribuir ao recurso de marcas

### <a name="how-do-i-make-sure-that-the-charges-in-my-azure-usage-and-charges-file-are-correct"></a>Como para ter certeza de que os encargos em meu arquivo de uso e os encargos do Azure estão corretos?

Se há um encargo em seu arquivo de uso detalhado que você deseja obter mais detalhes, consulte [entender os encargos na fatura do seu perfil de cobrança](billing-mca-understand-your-bill.md)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso a um contrato de cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de Ajuda, [criar uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximos passos

- [Exibir e baixar sua fatura do Microsoft Azure](billing-download-azure-invoice.md)
- [Exibir e baixar seus encargos e o uso do Microsoft Azure](billing-download-azure-daily-usage.md)
