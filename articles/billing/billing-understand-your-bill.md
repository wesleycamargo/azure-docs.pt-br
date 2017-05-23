---
title: Entendendo sua fatura do Azure | Microsoft Docs
description: Saiba como ler e entender o uso e a fatura da sua assinatura do Azure
services: 
documentationcenter: 
author: genlin
manager: ruchic
editor: 
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2017
ms.author: erihur;genli
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: b5268239fb4c50e6bdea77323267a42e267268db
ms.contentlocale: pt-br
ms.lasthandoff: 05/15/2017


---
# <a name="understand-your-bill-for-microsoft-azure"></a>Entenda sua fatura do Microsoft Azure
Para entender sua fatura do Azure, examine o resumo dos encargos e o arquivo separado de uso diário detalhado. Este artigo descreve a maioria dos termos mostrados na fatura e o arquivo de uso diário. Para obter esses arquivos, confira [How to get your Azure billing invoice and daily usage data](billing-download-azure-invoice-daily-usage-date.md) (Como obter sua fatura de cobrança e os dados de uso diário do Azure). Se você estiver usando uma assinatura de avaliação gratuita, poderá obter suas informações de uso diário, mas não terá uma fatura.

Os encargos das assinaturas do Microsoft Azure variam de acordo com o plano de tarifas. Alguns planos de tarifas, como as assinaturas do MPN (Visual Studio Enterprise), incluem créditos mensais que podem ser usados em qualquer serviço do Azure com base em suas necessidades.

Sua fatura atual pode apresentar até 24 horas de uso do final do período de cobrança anterior. Os encargos listados nos demonstrativos de cobrança para clientes internacionais servem somente para fins de estimativa. Os bancos têm custos diferentes para as taxas de conversão.

## <a name="pdf"></a>Entenda sua fatura (.pdf)
A fatura fornece um resumo dos seus encargos. Ele está disponível para download no formato .pdf (Portable Document Format) no [Portal do Azure](https://portal.azure.com). Para saber mais, confira [How to get your Azure billing invoice and daily usage data](billing-download-azure-invoice-daily-usage-date.md) (Como obter sua fatura de cobrança e os dados de uso diário do Azure). 

As seções a seguir listam a maioria dos termos existentes em sua fatura, junto com descrições para cada termo.

### <a name="account-information"></a>Informações da conta
A seção de informações da conta mostra informações sobre seu uso e perfil.

![cabeçalho](./media/billing-understand-your-bill/Header.png)

| Termo | Descrição |
| --- | --- |
| Nº da Fatura |Um identificador de fatura único para fins de acompanhamento |
| Ciclo de cobrança |Intervalo de datas coberto por esta nota fiscal |
| Data da fatura |A data em que a fatura foi gerada |
| Método de pagamento |Tipo de pagamento usado na conta (fatura ou cartão de crédito). Se você precisar atualizar seu cartão de crédito, confira [Como mudar o cartão de crédito usado para pagar por uma assinatura do Azure](billing-how-to-change-credit-card.md). Se você representa uma empresa, pode pagar sua assinatura do Azure com métodos de pagamento de fatura, como cheques ou transferências bancárias. Confira [Cobrança do Azure – Como faturar](https://azure.microsoft.com/pricing/invoicing/). |
| Enviar cobrança para |Endereço de pagamentos do Microsoft Azure. Saiba como atualizar suas informações de cobrança em [Alterar o cartão de crédito usado para pagar uma assinatura do Azure](billing-how-to-change-credit-card.md). |
| Oferta de assinatura |Tipo de oferta de assinatura que tiver sido adquirida (Pré-pago, BizSpark Plus, Azure Pass etc.). Se você quiser alterar de pré-pago para uma oferta de assinatura diferente, confira [Trocar sua assinatura do Azure por outra oferta](billing-how-to-switch-azure-offer.md). |
| Email do proprietário da conta |O endereço de email da conta no qual a conta do Microsoft Azure está registrada. Para alterar seu endereço de email, confira [Como alterar as informações de perfil de sua conta do Azure, como email de contato, endereço e número de telefone](billing-how-to-change-azure-account-profile.md). |

### <a name="understand-the-invoice-summary"></a>Entender o resumo da fatura
A seção **Resumo da Fatura** da fatura resume as transações desde sua última fatura e os encargos de uso atuais.

![Resumo da Fatura](./media/billing-understand-your-bill/InvoiceSummary.png)

A seção saldo anterior, pagamentos e saldo pendente da fatura resume as transações desde sua última fatura.

| Termo | Descrição |
| --- | --- |
| Saldo anterior |O valor total devido em sua última fatura |
| Pagamentos |Os pagamentos totais aplicados à sua última fatura |
| Saldo pendente (do ciclo de cobrança anterior) |Qualquer ajuste de fatura (créditos ou saldos) aplicado à sua conta desde sua última fatura |

### <a name="understand-the-current-charges"></a>Entender os encargos atuais
A seção Encargos Atuais da fatura mostra detalhes sobre seus encargos mensais. 

| Termo | Descrição |
| --- | --- |
| Encargos de uso |Encargos de uso são o total de encargos mensais em uma assinatura. Você é cobrado pelo uso do mês anterior. |
| Descontos |Descontos de serviço aplicados à sua fatura atual. |
| Ajustes |Créditos diversos ou encargos pendentes aplicados à sua cobrança atual. Por exemplo, se você tiver a oferta Visual Studio Enterprise com MSDN, verá um crédito mensal. Se você cancelar sua assinatura, verá quaisquer encargos mensais pelo uso que excedem o crédito mensal obtido com sua oferta de assinatura. Os encargos são do começo de seu período de cobrança atual até a data de cancelamento da assinatura. |


### <a name="sold-to-and-payment-instructions"></a>Instruções de venda e pagamento
A tabela a seguir descreve as instruções de venda e pagamento mostradas em sua fatura.

| Termo | Descrição |
| --- | --- |
| Vendido para | O endereço de perfil que está na conta. Se você precisar alterar o endereço, confira [Como alterar as informações de perfil de sua conta do Azure, como email de contato, endereço e número de telefone](billing-how-to-change-azure-account-profile.md). |
| Instruções de pagamento | Se você pagar por fatura, estas instruções lhe dirão para onde enviar cheques, transferências ou pagamentos em 24 horas. Para saber mais, confira [Cobrança do Azure – Como faturar](https://azure.microsoft.com/pricing/invoicing/) |

## <a name="csv"></a> Entender os encargos de uso detalhados (.csv)
O arquivo de uso mostra o quanto de cada recurso foi usado no período de faturamento atual. Ele está disponível em um formato de arquivo .csv (valores separados por vírgulas) que pode ser aberto em um aplicativo de planilhas. Se você vir duas versões disponíveis, baixe a versão 2. Esse é o formato de arquivo mais recente. Para saber mais, confira [How to get your Azure billing invoice and daily usage data](billing-download-azure-invoice-daily-usage-date.md) (Como obter sua fatura de cobrança e os dados de uso diário do Azure).

Encargos de uso são o total de encargos **mensais** em uma assinatura, menos créditos ou descontos. Você é cobrado pelo uso do mês anterior.  

As seções a seguir descrevem a maioria dos termos mostrados na versão 2 do arquivo de uso detalhado.

### <a name="statement"></a>Instrução 
A seção superior do arquivo mostra os serviços que você usou durante o ciclo de faturamento do mês anterior. A tabela a seguir lista os termos e as descrições mostradas nesta seção.

| Termo | Descrição |
| --- | --- |
|Período de Cobrança |O período de cobrança quando o recurso ou serviço foi usado. |
|Categoria de medidor |Identifica o serviço de nível superior ao qual esse uso pertence. |
|Subcategoria de medidor |Define o tipo de serviço do Azure e pode afetar a tarifa. |
|Nome do medidor |Identifica a unidade de medida para o recurso sendo consumido. |
|Região do medidor |Identifica o local do datacenter para determinados serviços que são cobrados com base no local do datacenter. |
|SKU |Identifica o identificador de sistema exclusivo para cada recurso do Azure. |
|Unidade |Identifica a unidade em que o serviço é cobrado. Por exemplo, GB, horas, 10.000 s. |
|Quantidade consumida |O valor do recurso usado durante o período de cobrança. |
|Quantidade incluída |A quantidade do recurso que está incluída gratuitamente em seu período de cobrança atual. |
|Quantidade de excesso |Mostra a diferença entre a Quantidade Consumida e a Quantidade Incluída. A cobrança é feita com base nessa quantidade. Para ofertas pré-pagas sem quantidade incluída na oferta, esse total será igual à Quantidade Consumida. |
|Dentro do Compromisso |Mostra os encargos do recurso que são subtraídos do valor de compromisso associado à sua oferta de seis ou 12 meses. Os encargos do recurso são subtraídos em ordem cronológica. |
|Moeda |A moeda usada em seu período de cobrança atual. |
|Excedente |Mostra os encargos de recurso que excedem o valor de compromisso associado à sua oferta de seis ou 12 meses. |
|Tarifa de Compromisso |Mostra a tarifa de compromisso com base no valor de compromisso total associado à sua oferta de seis ou 12 meses. |
|Tarifa |A tarifa cobrada por unidade faturável. |
|Valor |Mostra o resultado da multiplicação da coluna Quantidade Excedente pela coluna Tarifa. Se a Quantidade Consumida não exceder a Quantidade Incluída, nenhum encargo será exibido nessa coluna. |

### <a name="daily-usage"></a>Uso diário 

A seção de Uso diário do arquivo mostra os detalhes de uso que afetam as taxas de cobrança. A tabela a seguir lista os termos e as descrições mostradas nesta seção. 

| Termo| Descrição |
| --- | --- |
|Data de Uso |A data na qual o recurso foi usado. |
|Categoria de medidor |Identifica o serviço de nível superior ao qual esse uso pertence. |
|ID de medidor |O identificador de medidor cobrado que é usado para uso de cobrança de preços. |
|Subcategoria de medidor |Define o tipo de serviço do Azure e pode afetar a tarifa. |
|Nome do medidor |Identifica a unidade de medida para o recurso sendo consumido. |
|Região do medidor|Identifica o local do datacenter para determinados serviços que são cobrados com base no local do datacenter. |
|Unidade |Identifica a unidade em que o serviço é cobrado. Por exemplo, GB, horas, 10.000 s. |
|Quantidade consumida |A quantidade do recurso que foi consumida naquele dia. |
|Local do recurso |Identifica o datacenter onde o recurso está sendo executado. |
|Serviço consumido |O serviço da plataforma do Azure que você usou. |
|Grupo de recursos |O grupo de recursos no qual o recurso implantado está sendo executado. Para saber mais, consulte [Visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). |
|ID da instância |O identificador do recurso. O identificador contém o nome especificado para o recurso quando ele foi criado. É o nome do recurso ou a ID do Recurso totalmente qualificada. Para saber mais, confira [API do Azure Resource Manager](/rest/api/resources/resources). |
|Marcas |Marca atribuída ao recurso. Use marcas para agrupar registros de cobrança. Por exemplo, você pode usar marcas para distribuir os custos entre os departamentos que usam o recurso. Os serviços que dão suporte à emissão de marcas são as máquinas virtuais, armazenamento e serviços de rede provisionados com a [API do Azure Resource Manager](/rest/api/resources/resources). Para saber mais, confira [Organizar os recursos do Azure com marcas](http://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/). |
|Informações Adicionais |Metadados específicos ao serviço. Por exemplo, um tipo de imagem para uma máquina virtual. |
|Informações do Serviço 1 |O nome do projeto ao qual o serviço pertence em sua assinatura. |
|Informações do Serviço 2 |Um campo herdado que captura os metadados específicos do serviço opcional. |

## <a name="tips-for-cost-management"></a>Dicas para gerenciamento de custos
- Custos estimados usando a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/), o [custo total de calculadora de propriedade](https://aka.ms/azure-tco-calculator) e quando você adiciona um serviço
- [Configurar alertas de cobrança](billing-set-up-alerts.md)
- [Analise o uso e os custos regularmente no portal do Azure](billing-getting-started.md#costs)

Para saber mais, veja [Introdução ao gerenciamento de custos e de cobrança do Azure](billing-getting-started.md).

## <a name="how-do-i-make-a-payment"></a>Como faço um pagamento?
Se você tiver configurado um cartão de crédito ou débito como forma de pagamento, o pagamento será feito automaticamente. Na sua declaração de cartão de crédito, o item de linha diria **MSFT Azure**.

Se você usar [uma forma de pagamento de fatura](https://azure.microsoft.com/pricing/invoicing/), envie seu pagamento para o local indicado na parte inferior de sua fatura. Para obter mais ajuda, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="how-do-i-check-the-status-of-a-payment-made-by-credit-card"></a>Como faço para verificar o status de um pagamento feito com cartão de crédito?
[Crie um tíquete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para solicitar o status do pagamento. 

## <a name="what-about-marketplace-orders-or-external-service-charges"></a>E quanto a pedidos no Marketplace ou encargos de serviços externos?
Os serviços externos costumavam ser chamados de pedidos do Marketplace. Os serviços externos são fornecidos por prestadores de serviços independentes, mas estão integrados ao Azure. Para saber mais, confira [Entender os encargos dos serviços externos do Azure](billing-understand-your-azure-marketplace-charges.md).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte. 
Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
 




