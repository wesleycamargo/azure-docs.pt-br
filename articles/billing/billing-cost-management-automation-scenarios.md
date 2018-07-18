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
ms.date: 6/07/2018
ms.author: erikre
ms.openlocfilehash: f84071577e9636e40d621093e2c57e3e9adf4913
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247659"
---
# <a name="billing-and-cost-management-automation-scenarios"></a>Cenários de automação do gerenciamento de custos e de cobrança

Cenários comuns para o espaço de gerenciamento de cobrança e de custo estão identificados abaixo e são mapeados para APIs diferentes que podem ser usadas nesses cenários. Um resumo de todas as APIs disponíveis e a funcionalidade que oferecem pode ser encontrado sob o cenário para o mapeamento de API. 

## <a name="common-scenarios"></a>Cenários comuns 

Você pode APIs de gerenciamento de custos e de cobrança em uma variedade de cenários para responder a perguntas relacionadas a uso e a custo.  Uma estrutura de tópicos dos cenários mais comuns é fornecida abaixo.

- **Reconciliação de fatura** – a Microsoft me cobrou o valor correto?  Qual é minha fatura e posso calculá-la por conta própria?

- **Cobranças entre setores** – agora que eu sei o quanto estou sendo cobrado, quem em minha organização precisa pagar?

- **Otimização de custos** – já sei quanto me foi cobrado, no entanto, como posso aproveitar melhor o dinheiro que estou gastando com o Azure?

- **Controle de custos** – quero ver o quanto estou gastando com o Azure e usando dele ao longo do tempo. Quais são as tendências? Em que eu poderia melhorar?

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
| Resumos da reserva       |                           |                  |           X          |         X        |                    |           |
| Detalhes de uso               |             X             |         X        |           X          |         X        |          X         |     X     |
| Períodos de Cobrança             |             X             |         X        |           X          |         X        |                    |           |
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
-   **Tabela de preços** (*SOMENTE clientes Enterprise*): clientes Enterprise podem usar a [API de tabela de preços](https://docs.microsoft.com/rest/api/consumption/pricesheet) para recuperar os respectivos preços personalizados para todos os medidores. As empresas podem usar esses dados em combinação com detalhes de uso e informações de uso do Marketplace para executar cálculos de custo usando dados de uso e do Marketplace. 
-   **Recomendações de reserva**: use a [API de recomendações de reserva](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations) para obter recomendações para compra de instâncias de VM reservadas. As recomendações são projetadas para permitir que os clientes analisem a economia de custos esperada e os valores de compra.
-   **Detalhes da reserva**: use a [API de detalhes de reserva](https://docs.microsoft.com/rest/api/consumption/reservationsdetails) para ver informações sobre reservas de VM adquiridas anteriormente, tais como a quantidade de consumo reservada versus a quantidade realmente sendo usada. Você pode ver os dados em um nível de detalhe por VM.
-   **Resumos de reserva**: use a [API de resumos de reserva](https://docs.microsoft.com/rest/api/consumption/reservationssummaries) para ver informações agregadas sobre reservas de VM adquiridas anteriormente, tais como a quantidade de consumo reservada versus a quantidade realmente sendo usada na agregação. 
-   **Detalhes de uso**: use a [API de detalhes de uso](https://docs.microsoft.com/rest/api/consumption/usagedetails) para obter dados de uso e cobrança do Azure para todos os recursos de primárias. As informações estão na forma de registros de detalhes de uso que atualmente são emitidos uma vez por metro por dia. As informações podem ser usadas para somar os custos de todos os recursos ou investigar os custos ou o uso de recursos específicos.
-   **RateCard**: os clientes Web Direct podem usar a [API RateCard](https://msdn.microsoft.com/library/azure/mt219005.aspx) para obter suas taxas de medidor. Eles podem então usar as informações retornadas com suas informações de uso de recursos para calcular manualmente a fatura esperada. 
-   **Uso não classificado**: você pode usar a [API de uso não classificado](https://msdn.microsoft.com/library/azure/mt219003.aspx) para obter informações de uso bruto, antes de qualquer medição/carregamento feito pelo Azure.

### <a name="billing"></a>Cobrança
-   **Períodos de cobrança**: use a [API de períodos de cobrança](https://docs.microsoft.com/rest/api/billing/billingperiods) para determinar um período de cobrança para analisar, juntamente com as IDs da fatura referentes àquele período. As IDs da fatura podem ser usadas com a API de fatura abaixo. 
-   **Faturas**: use a [API de faturas](https://docs.microsoft.com/rest/api/billing/invoices) para obter a URL de download para uma fatura referente a um determinado período de cobrança em formato PDF.

### <a name="enterprise-consumption"></a>Consumo da Enterprise
*(Todas as APIs Enterprise SOMENTE)*

-   **Resumo de saldo**: use a [API de resumo de saldo](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) para obter um resumo mensal de informações sobre saldos, novas compras, encargos de serviço do Azure Marketplace, ajustes e encargos de excedente. Você pode obter essas informações para o período de faturamento atual ou para qualquer período no passado. As empresas podem usar esses dados para realizar uma comparação com o resumo de encargos calculado manualmente. Essa API não fornece informações específicas do recurso nem uma exibição agregada de custos.
-   **Detalhes de uso**: Use a [API de detalhes de uso](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) para obter informações de detalhes de uso do Azure de primárias para o mês atual, um período de cobrança específico ou um período de datas personalizadas. As empresas podem usar esses dados para calcular a cobrança manualmente com base no consumo e de taxa de cobrança, e podem também usar as informações de departamento/organização presentes para atribuir custos pelas organizações. Os dados fornecem uma exibição do uso e do custo específica a um recurso.
-   **Cobrança da loja do Marketplace**: use a [API de cobrança da loja do Marketplace](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) para obter informações de detalhes de uso do Azure de terceiros para o mês atual, um período de cobrança específico ou um período de datas personalizadas. As empresas podem usar esses dados para calcular a cobrança manualmente com base no consumo e de taxa de cobrança, e podem também usar as informações de departamento/organização presentes para atribuir custos pelas organizações. A API de cobrança da loja do Marketplace fornece uma exibição de uso e de custo específica a um recurso.
-   **Tabela de Preços**: a [API Tabela de Preços](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) fornece a taxa aplicável de cada Medidor para o Registro e o Período de Cobrança determinados. Essas informações de taxa podem ser usadas em combinação com detalhes de uso e informações de uso de Marketplaces para calcular manualmente a fatura esperada.
-   **Períodos de cobrança**: use a [API de períodos de cobrança](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) para obter uma lista de períodos junto com uma propriedade que aponta para a rota de API para os quatro conjuntos de dados de API Enterprise que pertencem a esse período de cobrança – BalanceSummary, UsageDetails, Cobranças do Marketplace e PriceSheet.
-   **Recomendações de instância reservada**: a [API de recomendações de instância reservada](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) analisa 7, 30 ou 60 dias de uso da máquina virtual pelo cliente e oferece recomendações de compra individual e compartilhada. A API de instância reservada permite que os clientes analisem a economia de custos esperada e os valores de compra recomendados.

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre como usar APIs de cobrança do Azure para obter insights sobre o uso do Azure programaticamente, veja [Visão geral da API de cobrança do Azure](billing-usage-rate-card-overview.md).
