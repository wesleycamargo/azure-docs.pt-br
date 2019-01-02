---
title: Cenários de automação do gerenciamento de custos e de cobrança do Azure |Microsoft Docs
description: Saiba como cenários comuns de gerenciamento de cobrança e de custos são mapeados para diferentes APIs.
services: billing
documentationcenter: ''
author: Erikre
manager: dougeby
editor: ''
tags: billing
ms.assetid: 204b15b2-6667-4b6c-8ea4-f32c06f287fd
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 6/13/2018
ms.author: erikre
ms.openlocfilehash: 064f141680e62d7102d7c3332e4d93efd6c28037
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53258739"
---
# <a name="billing-and-cost-management-automation-scenarios"></a>Cenários de automação do gerenciamento de custos e de cobrança

Cenários comuns para gerenciamento de custos e de cobrança estão identificados abaixo e são mapeados para APIs diferentes que podem ser usadas nesses cenários. Um resumo de todas as APIs disponíveis e a funcionalidade que oferecem pode ser encontrado sob o cenário para o mapeamento de API.

## <a name="common-scenarios"></a>Cenários comuns

Você pode APIs de gerenciamento de custos e de cobrança em vários cenários para responder a perguntas relacionadas a uso e a custo.  Uma estrutura de tópicos dos cenários mais comuns é fornecida abaixo.

- **Reconciliação de faturas** - A Microsoft me cobrou o valor correto?  Qual é minha fatura e posso calculá-la por conta própria?

- **Cross encargos** – agora que já sabe quanto eu estou sendo cobrado pela, quem na minha organização precisa pagar?

- **Otimização de custos** – eu sei quanto me foi cobrado. Porém, como posso obter mais do dinheiro que estou gastando no Azure?

- **Controle de custos** – desejo ver o quanto estou gastando e usando o Azure ao longo do tempo. Quais são as tendências? Em que eu poderia melhorar?

- **Gastos do Azure durante o mês** – quanto é o meu gasto mensal atual até a data de hoje? É necessário fazer alguma alteração ao meus gastos e/ou uso do Azure? Em que período do mês estou consumindo mais do Azure?

- **Configurar alertas** – gostaria de configurar alertas de consumo com base em recursos ou alertas com base monetária.

## <a name="scenario-to-api-mappings"></a>Mapeamentos de cenário para API

|         API/cenário        | Reconciliação de fatura    | Cobranças entre setores    | Otimização de custos    | Controle de custos    | Gastos quinzenais    | Alertas    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Orçamentos                     |                           |                  |           X          |                  |                    |     X     |
| Marketplaces                |             X             |         X        |           X          |         X        |          X         |     X     |
| Tabela de preços                 |             X             |         X        |           X          |         X        |          X         |           |
| Recomendações de reserva |                           |                  |           X          |                  |                    |           |
| Detalhes da reserva         |                           |                  |           X          |         X        |                    |           |
| Resumos de reserva       |                           |                  |           X          |         X        |                    |           |
| Detalhes de uso               |             X             |         X        |           X          |         X        |          X         |     X     |
| Períodos de cobrança             |             X             |         X        |           X          |         X        |                    |           |
| Faturas                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Uso sem classificação               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> Os mapeamentos de cenário para API abaixo não incluem as APIs de consumo Enterprise. Sempre que possível, use as APIs de consumo geral para novos cenários de desenvolvimento.

## <a name="api-summaries"></a>Resumos de API

### <a name="consumption"></a>Consumo
(*clientes Web Direct + Enterprise para todas as APIs exceto as listadas abaixo*)

-   **Orçamentos** (*SOMENTE clientes Enterprise*): use a [API de Orçamentos](https://docs.microsoft.com/rest/api/consumption/budgets) para criar os orçamentos de custo ou de uso de recursos, grupos de recursos ou medidores de cobrança. Quando você tiver criado os orçamentos, o alerta poderá ser configurado para notificar quando os limites de orçamento definidos pelo usuário são excedidos. Ações também poderão ser configuradas para que ocorram quando os valores de orçamento forem atingidos.
-   **Marketplaces**: use a [API de Cobranças do Marketplace](https://docs.microsoft.com/rest/api/consumption/marketplaces) para obter dados de uso e de cobrança em todos os recursos do Marketplace (ofertas de terceiros do Azure). Esses dados podem ser usados para somar os custos de todos os recursos do Marketplace ou investigar os custos ou o uso de recursos específicos.
-   **Tabela de preços** (*SOMENTE para clientes Enterprise*): clientes Enterprise podem usar a [API de Tabela de Preços](https://docs.microsoft.com/rest/api/consumption/pricesheet) para recuperar seus preços personalizados para todos os medidores. As empresas podem usar esses dados em combinação com detalhes de uso e informações de uso do Marketplace para realizar cálculos de custo usando dados de uso e do Marketplace. 
-   **Recomendações de reserva**: use a [API de Recomendações de Reserva](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations) obter recomendações para a compra de instâncias de VM reservadas. As recomendações são projetadas para permitir que os clientes analisem a economia de custos esperada e os valores de compra. Para obter mais informações, confira [APIs para automação de reserva do Azure](billing-reservation-apis.md).
-   **Detalhes da reserva**: use a [API de Detalhes de Reserva](https://docs.microsoft.com/rest/api/consumption/reservationsdetails) para ver informações sobre reservas de VM adquiridas anteriormente, como a quantidade de consumo reservada versus quantidade usada. Você pode ver os dados em um nível de detalhe por VM. Para obter mais informações, confira [APIs para automação de reserva do Azure](billing-reservation-apis.md).
-   **Resumos de reserva**: use o [API de Resumos de Reserva](https://docs.microsoft.com/rest/api/consumption/reservationssummaries) para ver informações agregadas sobre reservas de VM que sua organização comprou, como quanto consumo está reservado versus a quantidade usada na agregação. Para obter mais informações, confira [APIs para automação de reserva do Azure](billing-reservation-apis.md).
-   **Detalhes de uso**: use a [API de Detalhes de Uso](https://docs.microsoft.com/rest/api/consumption/usagedetails) para obter o custo e uso em todos os recursos de primeira parte do Azure. As informações estão na forma de registros de detalhes de uso que atualmente são emitidos uma vez por metro por dia. As informações podem ser usadas para somar os custos de todos os recursos ou investigar os custos ou o uso de recursos específicos.
-   **RateCard**: os clientes Web Direct podem usar a [API RateCard](https://msdn.microsoft.com/library/azure/mt219005.aspx) para obter suas taxas de medidor. Eles podem então usar as informações retornadas com suas informações de uso de recursos para calcular manualmente a fatura esperada. 
-   **Uso sem classificação**: Você pode usar a [API de Uso Não Classificado](https://msdn.microsoft.com/library/azure/mt219003.aspx) para obter informações de uso bruto antes de qualquer medição/cobrança ser realizada pelo Azure.

### <a name="billing"></a>Cobrança
-   **Períodos de cobrança**: use a [API de Períodos de Cobrança](https://docs.microsoft.com/rest/api/billing/billingperiods) para determinar um período de cobrança para analisar, juntamente com as IDs da fatura para aquele período. As IDs da fatura podem ser usadas com a API de fatura abaixo. 
-   **Faturas**: use a [API de faturas](https://docs.microsoft.com/rest/api/billing/invoices) para obter a URL de download para uma fatura referente a um determinado período de cobrança em formato PDF.

### <a name="enterprise-consumption"></a>Consumo de Enterprise
*(Todas as APIs Enterprise SOMENTE)*

-   **Resumo de saldo**: use a [API de Resumo de Saldo](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) para obter um resumo mensal de informações sobre saldos, novas compras, encargos de serviço do Azure Marketplace, ajustes e encargos de excedente. Você pode obter essas informações para o período de faturamento atual ou para qualquer período no passado. As empresas podem usar esses dados para comparação com o resumo de encargos calculados manualmente. Essa API não fornece informações específicas do recurso nem uma exibição agregada de custos.
-   **Detalhes de uso**: use a [API de Detalhes de Uso](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) para obter informações de detalhes de uso do Azure de primeira parte para o mês atual, um período de cobrança específico ou um período de datas personalizado. As empresas podem usar esses dados para calcular a cobrança manualmente com base no consumo e de taxa de cobrança, e podem também usar as informações de departamento/organização presentes para atribuir custos pelas organizações. Os dados fornecem uma exibição do uso e do custo específica a um recurso.
-   **Cobrança de armazenamento do Marketplace**: use a [API de Cobrança do Marketplace Store](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) para obter informações de detalhes de uso de terceiros do Azure para o mês atual, um período de cobrança específico ou um período de datas personalizado. As empresas podem usar esses dados para calcular a cobrança manualmente com base no consumo e de taxa de cobrança, e podem também usar as informações de departamento/organização presentes para atribuir custos pelas organizações. A API de cobrança da loja do Marketplace fornece uma exibição de uso e de custo específica a um recurso.
-   **Tabela de preços**: a [API de Tabela de Preços](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) fornece a taxa aplicável de cada Medidor para o Registro e o Período de Cobrança determinados. Essas informações de taxa podem ser usadas em combinação com detalhes de uso e informações de uso de Marketplaces para calcular manualmente a fatura esperada.
-   **Períodos de cobrança**: Use o [API períodos de cobrança](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) para obter uma lista de períodos, juntamente com uma propriedade que aponta para a rota da API dos quatro conjuntos de dados da API do Enterprise que pertencem ao período de cobrança – BalanceSummary, de cobrança UsageDetails, encargos do Marketplace e PriceSheet.
-   **Recomendações de reserva do Azure**: a [API de Recomendações de Instância Reservada](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) analisa os 7 dias, 30 dias ou 60 dias de uso de máquina virtual do Cliente e oferece recomendações de compra única e compartilhada. A API de instância reservada permite que os clientes analisem a economia de custos esperada e os valores de compra recomendados. Para obter mais informações, confira [APIs para automação de reserva do Azure](billing-reservation-apis.md).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="what-is-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Qual é a diferença entre as APIs de relatórios corporativos e as APIs de consumo? Quando devo usar cada um?
Essas APIs têm um conjunto semelhante de funcionalidades e podem responder ao mesmo conjunto amplo de perguntas no espaço de faturamento e gerenciamento de custos. No entanto, cada API segmenta diferentes públicos: 

- **APIs de Relatórios Enterprise**: essas APIs estão disponíveis para clientes que assinaram um Contrato Enterprise com a Microsoft que concede a eles acesso negociados investimentos e preços personalizados. As APIs exigem uma chave para uso que você pode obter do [Enterprise Portal](https://ea.azure.com). Para obter uma descrição dessas APIs, confira [visão geral de APIs de relatórios para clientes empresariais](billing-enterprise-api.md).

- **Consumo de APIs**: essas APIs estão disponíveis para todos os clientes, com algumas exceções. Para obter mais informações, consulte [Visão geral da API de consumo do Azure](billing-consumption-api-overview.md) e a [referência da API de consumo do Azure](https://docs.microsoft.com/rest/api/consumption/). As APIs fornecidas são a solução recomendada para os cenários de desenvolvimento mais recentes. 

### <a name="what-is-the-difference-between-the-usage-details-api-and-the-usage-api"></a>Qual é a diferença entre a API de detalhes de uso e a API de uso?
Essas APIs fornecem dados fundamentalmente diferentes:

- **Detalhes de uso**: a [API Detalhes do uso](https://docs.microsoft.com/rest/api/consumption/usagedetails) fornece informações de uso e custo do Azure por instância do medidor. Os dados fornecidos já passaram pelo sistema de medição de custos do Azure e tiveram seu custo aplicado junto com outras possíveis alterações:

    - Alterações para contabilizar o uso de compromissos monetários pré-pagos
    - Alterações para considerar discrepâncias de uso descobertas pelo Azure

- **Uso**: a [API de uso](https://msdn.microsoft.com/library/Mt219003.aspx) fornece informações de uso do Azure bruto antes de transmiti-la por meio do Azure custo do sistema de medição. Esses dados podem não ter nenhuma correlação com a quantidade de uso ou custos visto depois que o Azure cobra um sistema de medição.

### <a name="what-is-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Qual é a diferença entre a API de fatura e a API de detalhes de uso?
Essas APIs fornecem uma visão diferente dos mesmos dados. A [API de Fatura](https://docs.microsoft.com/rest/api/billing/invoices) é apenas para clientes Web Direct e fornece um roll up mensal da sua fatura com base nas cobranças agregadas para cada tipo de medidor. O [API detalhes de uso](https://docs.microsoft.com/rest/api/consumption/usagedetails) fornece uma exibição detalhada dos registros de uso/custo para cada dia e pode ser usado por clientes Enterprise e Web Direct.

### <a name="what-is-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>O que é a diferença entre a API de tabela de preços e a API RateCard?
Essas APIs fornecem conjuntos semelhantes de dados, mas têm diferentes públicos. Informações a seguir.

- API de folha de preço: a [API de folha de preço](https://docs.microsoft.com/rest/api/consumption/pricesheet) fornece preço personalizado negociado para um cliente Enterprise.
- API RateCard: a [API RateCard](https://msdn.microsoft.com/library/mt219005.aspx) fornece a voltadas para o pública de preços que se aplica a clientes diretos da Web.

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre como usar as APIs do Azure para obter insights de forma programática sobre seu uso do Azure, consulte [Visão geral da API de consumo do Azure](billing-consumption-api-overview.md) e [Visão geral da Azure Billing API](billing-usage-rate-card-overview.md).
- Para comparar sua fatura com o arquivo detalhado de uso diário e os relatórios de gerenciamento de custos no Portal do Azure, consulte [Entenda sua fatura do Microsoft Azure](billing-understand-your-bill.md)
- Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
