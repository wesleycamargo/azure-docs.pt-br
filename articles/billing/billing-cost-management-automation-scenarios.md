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
ms.openlocfilehash: c5445eb5e936fe73d51a41936b052f6495b1bcc6
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39631886"
---
# <a name="billing-and-cost-management-automation-scenarios"></a>Cenários de automação do gerenciamento de custos e de cobrança

Cenários comuns para o espaço de gerenciamento de cobrança e de custo estão identificados abaixo e são mapeados para APIs diferentes que podem ser usadas nesses cenários. Um resumo de todas as APIs disponíveis e a funcionalidade que oferecem pode ser encontrado sob o cenário para o mapeamento de API. 

## <a name="common-scenarios"></a>Cenários comuns

Você pode APIs de gerenciamento de custos e de cobrança em uma variedade de cenários para responder a perguntas relacionadas a uso e a custo.  Uma estrutura de tópicos dos cenários mais comuns é fornecida abaixo.

- **Reconciliação de faturas** - A Microsoft me cobrou o valor correto?  Qual é minha fatura e posso calculá-la por conta própria?

- **Cross encargos** – agora que já sabe quanto eu estou sendo cobrado pela, quem na minha organização precisa pagar?

- **Otimização de custos** -saber quanto eu já foi cobrado, no entanto, como pode posso aproveitar mais o dinheiro que estou gastando no Azure?

- **Controle de custos** – que desejo ver o quanto Estou gastando e usando o Azure ao longo do tempo. Quais são as tendências? Em que eu poderia melhorar?

- **Gastos do Azure durante o mês** – quanto é o meu gasto mensal atual até a data de hoje? É necessário fazer quaisquer ajustes no meu gastos e/ou no uso do Azure? Em que período do mês estou consumindo mais do Azure?

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
> Os mapeamentos de cenário para API abaixo não incluem as APIs de consumo Enterprise. Sempre que possível, utilize as APIs de consumo gerais para lidar com novos cenários de desenvolvimento líquidos no futuro.

## <a name="api-summaries"></a>Resumos de API

### <a name="consumption"></a>Consumo
(*clientes Web Direct + Enterprise para todas as APIs exceto as listadas abaixo*)

-   **Orçamentos** (*SOMENTE clientes Enterprise*): use a [API de orçamentos](https://docs.microsoft.com/rest/api/consumption/budgets) para criar os orçamentos de custo ou de uso de recursos, grupos de recursos ou medidores de cobrança.  Depois que você tiver criado os orçamentos, o alerta pode ser configurado para notificar quando os limites de orçamento definidos pelo usuário são excedidos. Ações também poderão ser configuradas para que ocorram quando os valores de orçamento forem atingidos.
-   **Marketplaces**: use a [API de encargos do Marketplace](https://docs.microsoft.com/rest/api/consumption/marketplaces) para obter dados de uso e de cobrança em todos os recursos do Marketplace (ofertas de terceiros do Azure). Esses dados podem ser usados para somar os custos de todos os recursos do Marketplace ou investigar os custos ou o uso de recursos específicos.
-   **Folha de preços** (*Clientes empresariais SOMENTE*): os clientes corporativos podem usar a [API Folha de preços](https://docs.microsoft.com/rest/api/consumption/pricesheet) para recuperar seus preços personalizados para todos os medidores. As empresas podem usar esses dados em combinação com detalhes de uso e informações de uso do Marketplace para executar cálculos de custo usando dados de uso e do Marketplace. 
-   **Recomendações de reserva**: Use o [API de recomendações de reserva](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations) obter recomendações para a compra de instâncias de VM reservadas. As recomendações são projetadas para permitir que os clientes analisem a economia de custos esperada e os valores de compra.
-   **Detalhes da reserva**: use a [Reservation Details](https://docs.microsoft.com/rest/api/consumption/reservationsdetails) para ver informações sobre reservas de VM compradas anteriormente, como quanto consumo foi reservado versus quanto está sendo usado. Você pode ver os dados em um nível de detalhe por VM.
-   **Resumos de reserva**: use a [Reservation Summaries API](https://docs.microsoft.com/rest/api/consumption/reservationssummaries) para ver informações agregadas sobre reservas de VM compradas anteriormente, como quanto consumo foi reservado versus quanto está sendo realmente usado no agregado. 
-   **Detalhes de uso**: Use o [API de detalhes de uso](https://docs.microsoft.com/rest/api/consumption/usagedetails) para obter o custo e uso em todo o Azure 1º recursos de terceiros. As informações estão na forma de registros de detalhes de uso que atualmente são emitidos uma vez por metro por dia. As informações podem ser usadas para somar os custos de todos os recursos ou investigar os custos ou o uso de recursos específicos.
-   **RateCard**: os clientes Web Direct podem usar a [API RateCard](https://msdn.microsoft.com/library/azure/mt219005.aspx) para obter suas taxas de medidor. Eles podem então usar as informações retornadas com suas informações de uso de recursos para calcular manualmente a fatura esperada. 
-   **Não classificado de uso**: você pode usar o [API de uso não classificado](https://msdn.microsoft.com/library/azure/mt219003.aspx) para obter informações de uso bruto, antes de qualquer medição/cobrar realizada pelo Azure.

### <a name="billing"></a>Cobrança
-   **Períodos de cobrança**: Use o [API períodos de cobrança](https://docs.microsoft.com/rest/api/billing/billingperiods) para determinar um período de cobrança para analisar, juntamente com a nota fiscal IDs para aquele período. As IDs da fatura podem ser usadas com a API de fatura abaixo. 
-   **Faturas**: use a [API de faturas](https://docs.microsoft.com/rest/api/billing/invoices) para obter a URL de download para uma fatura referente a um determinado período de cobrança em formato PDF.

### <a name="enterprise-consumption"></a>Consumo de Enterprise
*(Todas as APIs Enterprise SOMENTE)*

-   **Resumo de saldo**: Use o [API de resumo de saldo](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) para obter um mensal resumo de informações sobre saldos, novas compras, encargos de serviço do Azure Marketplace, ajustes e encargos de excedente. Você pode obter essas informações para o período de faturamento atual ou para qualquer período no passado. As empresas podem usar esses dados para realizar uma comparação com o resumo de encargos calculado manualmente. Essa API não fornece informações específicas do recurso nem uma exibição agregada de custos.
-   **Detalhes de uso**: Use o [API de detalhes de uso](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) para obter informações de detalhes de uso do Azure para o mês atual, um período de cobrança específico ou um período de datas personalizado de 1º de terceiros. As empresas podem usar esses dados para calcular a cobrança manualmente com base no consumo e de taxa de cobrança, e podem também usar as informações de departamento/organização presentes para atribuir custos pelas organizações. Os dados fornecem uma exibição do uso e do custo específica a um recurso.
-   **Custos de armazenamento do Marketplace**: Use o [API de cobrança do Marketplace Store](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) obter 3ª parte informações de detalhes de uso do Azure para o mês atual, um período de cobrança específico ou um período de datas personalizado. As empresas podem usar esses dados para calcular a cobrança manualmente com base no consumo e de taxa de cobrança, e podem também usar as informações de departamento/organização presentes para atribuir custos pelas organizações. A API de cobrança da loja do Marketplace fornece uma exibição de uso e de custo específica a um recurso.
-   **Folha de preços**: O [API tabela de preços](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) fornece a taxa aplicável de cada medidor para o registro e o período de cobrança determinados. Essas informações de taxa podem ser usadas em combinação com detalhes de uso e informações de uso de Marketplaces para calcular manualmente a fatura esperada.
-   **Períodos de cobrança**: Use o [API períodos de cobrança](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) para obter uma lista de períodos, juntamente com uma propriedade que aponta para a rota da API dos quatro conjuntos de dados da API do Enterprise que pertencem ao período de cobrança -: BalanceSummary, de cobrança UsageDetails, encargos do Marketplace e PriceSheet.
-   **Recomendações de reserva do Azure** : A [API de recomendações de instâncias reservadas](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) analisa os 7 dias, 30 dias ou 60 dias de uso de máquina virtual do Cliente e oferece recomendações de compra única e compartilhada. A API de instância reservada permite que os clientes analisem a economia de custos esperada e os valores de compra recomendados.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="what-is-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Qual é a diferença entre as APIs de relatórios corporativos e as APIs de consumo? Quando devo usar cada um?
Essas APIs têm um conjunto semelhante de funcionalidades e podem responder ao mesmo conjunto amplo de perguntas no espaço de faturamento e gerenciamento de custos. No entanto, cada API segmenta diferentes públicos: 

- **APIs de relatórios corporativos**: essas APIs estão disponíveis para clientes que assinaram um Enterprise Agreement com a Microsoft que concede a eles acesso negociados investimentos e preços personalizados. As APIs requerem uma chave para uso que pode ser obtida através do [Portal Empresarial](https://ea.azure.com). Para obter uma descrição dessas APIs, consulte [visão geral de APIs de relatórios para clientes corporativos](billing-enterprise-api.md).

- **Consumo de APIs**: essas APIs estão disponíveis para todos os clientes, com algumas exceções. Para obter mais informações, consulte [Visão geral da API de consumo do Azure](billing-consumption-api-overview.md) e a [referência da API de consumo do Azure](https://docs.microsoft.com/rest/api/consumption/). As APIs fornecidas são a solução recomendada para os cenários de desenvolvimento mais recentes. 

### <a name="what-is-the-difference-between-the-usage-details-api-and-the-usage-api"></a>Qual é a diferença entre a API de detalhes de uso e a API de uso?
Essas APIs fornecem dados fundamentalmente diferentes:

- **Detalhes do uso**: A [API Detalhes do uso](https://docs.microsoft.com/rest/api/consumption/usagedetails) fornece informações de uso e custo do Azure por instância do medidor. Os dados fornecidos já passaram pelo sistema de medição de custos do Azure e tiveram seu custo aplicado junto com outras possíveis alterações:

    - Alterações para contabilizar o uso de compromissos monetários pré-pagos
    - Alterações para considerar discrepâncias de uso descobertas pelo Azure

- **Uso**: O [API de uso](https://msdn.microsoft.com/library/Mt219003.aspx) fornece informações de uso do Azure bruto antes de transmiti-la por meio do Azure custo do sistema de medição. Esses dados podem não ter nenhuma correlação com a quantidade de uso e/ou custos visto depois que o Azure cobra um sistema de medição.

### <a name="what-is-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Qual é a diferença entre a API de fatura e a API de detalhes de uso?
Essas APIs fornecem uma visão diferente dos mesmos dados. A [Fatura Invoice API](https://docs.microsoft.com/rest/api/billing/invoices) é apenas para clientes Web Direct e fornece um roll up mensal da sua fatura com base nas cobranças agregadas para cada tipo de medidor. O [API detalhes de uso](https://docs.microsoft.com/rest/api/consumption/usagedetails) fornece uma exibição detalhada dos registros de uso/custo para cada dia e pode ser usado por clientes Enterprise e Web Direct.

### <a name="what-is-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>O que é a diferença entre a API de tabela de preços e a API RateCard?
Essas APIs fornecem conjuntos semelhantes de dados, mas têm diferentes públicos. Informações a seguir.

- Preço da API da folha: Os [API tabela de preços](https://docs.microsoft.com/rest/api/consumption/pricesheet) fornece personalizado de preços que terá sido negociado para um cliente empresarial.
- API RateCard: O [API RateCard](https://msdn.microsoft.com/library/mt219005.aspx) fornece a voltadas para o pública de preços que se aplica a clientes diretos da Web.

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre como usar as APIs do Azure para obter insights de forma programática sobre seu uso do Azure, consulte [Visão geral da API de consumo do Azure](billing-consumption-api-overview.md) e [Visão geral da Azure Billing API](billing-usage-rate-card-overview.md).
- Para comparar sua fatura com o arquivo detalhado de uso diário e os relatórios de gerenciamento de custos no Portal do Azure, consulte [Entenda sua fatura do Microsoft Azure](billing-understand-your-bill.md)
- Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
