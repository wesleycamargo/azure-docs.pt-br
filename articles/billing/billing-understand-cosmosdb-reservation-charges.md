---
title: Entenda como o desconto de reserva é aplicado ao Azure Cosmos DB | Microsoft Docs
description: Saiba como o desconto de reserva é aplicado à taxa de transferência provisionada (RU/s) no Azure Cosmos DB.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 8c5e9c8437c516a5e6d1d97494b21bfc8e90d88e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46960253"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-cosmos-db"></a>Entenda como o desconto de reserva é aplicado ao Azure Cosmos DB

Depois de adquirir uma capacidade reservada do Azure Cosmos DB, o desconto de reserva é aplicado automaticamente aos recursos do Banco de Dados do Azure Cosmos que correspondem aos atributos e à quantidade da reserva. Uma reserva abrange a taxa de transferência provisionada para recursos do DB do Azure Cosmos e não cobre custos de software, rede, armazenamento ou contêineres predefinidos.

## <a name="reservation-discount-applied-to-azure-cosmos-db-accounts"></a>Desconto de reserva aplicado às contas do Azure Cosmos DB

Um desconto de reserva é aplicado a [taxa de transferência provisionada](../cosmos-db/request-units.md) em termos de unidades de solicitação por segundo (RU / s) em uma base de hora a hora. Para recursos do BD do Azure Cosmos que não são executados durante toda a hora, o desconto de reserva é aplicado automaticamente a outros recursos do BD Cosmos que correspondem aos atributos de reserva. O desconto pode se aplicar aos recursos do BD do Azure Cosmos que estão sendo executados simultaneamente. Se você não tiver recursos do BD Cosmos que sejam executados durante a hora inteira e que correspondam aos atributos da reserva, você não terá o benefício total do desconto da reserva para essa hora.

* Os descontos são em camadas, o que significa que as reservas com unidades de solicitação mais altas fornecem descontos maiores.  
* A compra da reserva aplicará descontos a todas as regiões na proporção equivalente ao preço sob demanda regional. Para obter as taxas de desconto de reserva em cada região, consulte a seção [desconto de reserva por região](#reservation-discount-per-region) deste artigo.

## <a name="reservation-discount-per-region"></a>Desconto de reserva por região
O desconto de reserva é aplicado aos custos de taxa de transferência do banco de dados do Azure Cosmos em uma base de hora a hora na assinatura única ou no escopo de inscrição/conta. O desconto de reserva se aplica ao uso do medidor em diferentes regiões na seguinte proporção:

|Descrição de medidor  |Região |Proporção  |
|---------|---------|---------|
|Azure Cosmos DB - 100 RU / s / hora - AP Sudeste  |  Sudeste do Pacífico Asiático    |   1      |
|Azure Cosmos DB – 100 RU/s/hora – Leste do Pacífico Asiático |   Leste do Pacífico Asiático   |    1     |
|Centro-Norte da Europa de - 100 RU/s/hora – Azure Cosmos DB|  Norte da Europa       |    1     |
|Cos do Azure DB - 100 RU / s / Hora - KR Sul|    Sul da Coreia     |     1    |
|Azure Cosmos DB – 100 RU/s/hora – da Europa Ocidental|    Oeste da Europa     |      1   |
|Azure Cosmos DB – 100 RU/s/hora – Coreia Central|   Coreia Central    |       1  |
|Azure Cosmos DB – 100 RU/s/hora - UK Sul|   Sul do Reino Unido      |     1    |
|Oeste do Reino Unido 100 RU/s/hora - do BD Cosmos do Azure|   Oeste do Reino Unido      |    1     |
|Norte do Reino Unido 100 RU/s/hora - do BD Cosmos do Azure |   Norte do Reino Unido    |     1    |
|Azure Cosmos DB – 100 RU/s/hora - UK Sul 2|   Sul do Reino Unido 2      |     1    |
|Azure Cosmos DB – 100 RU/s/hora – Leste dos EUA 2|  Leste dos EUA 2     |     1    |
|Azure Cosmos DB – 100 RU/s/hora – Centro-Norte dos EUA|   Centro-Norte dos EUA      |     1    |
|Azure Cosmos DB – 100 RU/s/hora - Oeste dos EUA|   Oeste dos EUA      |     1    |
|Azure Cosmos DB – 100 RU/s/hora – centro dos EUA| Centro dos EUA        |     1    |
|Azure Cosmos DB – 100 RU/s/hora - Oeste dos EUA 2|   Oeste dos EUA 2      |      1   |
|Azure Cosmos DB – 100 RU/s/hora - Oeste dos EUA|   Centro-Oeste dos EUA      |       1  |
|Azure Cosmos DB – 100 RU/s/hora – Leste dos EUA|   Leste dos EUA      |  1       |
|Norte do Azure Cosmos DB – 100 RU/s/hora - SA|     Norte do SA    |   1      |
|Azure Cosmos DB – 100 RU/s/hora - SA Oeste |    Oeste do SA      |    1     |
|Azure Cosmos DB – 100 RU/s/hora – Sul da Índia|    Sul da Índia     |    1.0375    |
|Azure Cosmos DB – 100 RU/s/hora - AC Leste|   Leste do Canadá      |    1,1      |
|Cos do Azure DB - 100 RU / s / Hora - JA Leste|   Leste do Japão      |    1.125     |
|Azure Cosmos DB - 100 RU / s / Hora - JA Oeste|     Oeste do Japão    |   1.125       |
|Azure Cosmos DB – 100 RU/s/hora – Índia Ocidental|     Índia Ocidental    |    1.1375     |
|Azure Cosmos DB – 100 RU/s/hora – Índia Central|    Índia Central     |  1.1375       |
|Azure Cosmos DB – 100 RU/s/hora - AU Leste|     Leste da Austrália    |   1,15       |
|Azure Cosmos DB - 100 RU/s/Hour - CA Central|  CA Central       |   1.2       |
|Azure Cosmos DB – 100 RU/s/hora - França Central|   França Central      |    1,25      |
|Azure Cosmos DB – 100 RU/s/hora – Sul do Brasil|  Sul do Brasil       |   1.5      |
|Azure Cosmos DB – 100 RU/s/hora – Austrália Central|   Austrália Central      |   1.5      |
|Azure Cosmos DB – 100 RU/s/hora – Austrália Central 2| Austrália Central 2        |    1.5     |
|Sul da França de - 100 RU/s/hora – Azure Cosmos DB|    Sul da França     |    1.625     |

## <a name="scenarios-that-show-how-the-reservation-discount-is-applied"></a>Cenários que mostram como o desconto de reserva é aplicado

Considere os seguintes requisitos para uma reserva:

* Taxa de transferência necessária: 50.000 RU/s  
* Regiões usada: 2. 

Neste caso, o total de cobranças por demanda é de 500 unidades de 100 RU / s nessas duas regiões, para um consumo total de 100.000 RUs por hora. 

**Cenário 1**

Por exemplo, se você precisar de implantações do Azure Cosmos DB nas regiões "Norte dos EUA Central" e "Oeste dos EUA" e se cada região tiver um consumo de taxa de transferência de 50.000 RU/s. Uma compra de reserva de 100.000 RU/s equilibrará completamente os encargos de sob demanda.

O desconto coberto por uma reserva é calculado como (consumo de rendimento * reservation_discount_ratio_for_that_region). Para as regiões "Norte dos EUA" e "Oeste dos EUA", a taxa de desconto da reserva é "1". Assim, o total de RUs com desconto é de 100.000 RU / s (este valor é calculado como: 50.000 * 1 + 50.000 * 1 = 100.000 RU / s), e você não precisa pagar nenhum encargo adicional no pagamento regular. taxas de utilização. 

|Descrição de medidor | Região |Consumo de taxa de transferência (RU/s) |Desconto de reserva aplicado a RU/s |
|---------|---------|---------|---------|
|Azure Cosmos DB – 100 RU/s/hora – Centro-Norte dos EUA  |   Centro-Norte dos EUA  | 50.000  | 50.000  |
|Azure Cosmos DB – 100 RU/s/hora - Oeste dos EUA  |  Oeste dos EUA   |  50.000  |  50.000 |

**Cenário 2**

Por exemplo, se você precisar de implantações do Azure Cosmos DB nas regiões "AU Central 2" e "FR South", e se cada região tiver um consumo de taxa de transferência de 50.000 RU/s. Uma compra de reserva de 100.000 RU / s seria aplicável da seguinte forma (assumindo que o uso do AU Central 2 foi descontado em primeiro lugar):

|Descrição de medidor | Região |Consumo de taxa de transferência (RU/s) |Desconto de reserva aplicado a RU/s |
|---------|---------|---------|---------|
|Azure Cosmos DB – 100 RU/s/hora – Austrália Central 2  |  Austrália Central 2   |  50.000  |  50.000   |
|Sul da França de - 100 RU/s/hora – Azure Cosmos DB  |  Sul da França   |  50.000 |  15,384  |

50.000 unidades de uso na região "AU Central 2" correspondem a 75.000 RU / s de uso faturável (ou uso normalizado). Este valor é calculado como (consumo de rendimento * reservation_discount_ratio_for_that_region) que é igual a 75.000 RU / s (este valor é calculado como: 50.000 * 1.5 = 75.000 RU / s) de uso faturável ou normalizado. 

100.000 RU / s de compra de reserva compensariam as 75.000 RU / s em "AU Central 2" e deixaria 25.000 RU / s para a região "FR Sul". Das 25.000 RU / s restantes, um desconto de reserva de 15.384 RU / s (este valor é calculado como: 25.000 / 1.625 = 15.384 RU / s) é aplicado à região "FR Sul". As restantes 34.616 RU / s na região "FR Sul" são cobradas às taxas normais de pagamento conforme o uso. 

O sistema de faturamento do Azure atribuirá o benefício de faturamento de reserva à primeira instância processada que corresponda à configuração de reserva (por exemplo, AU Central 2 nesse caso).

Para entender e visualizar o aplicativo de suas reservas do Azure nos relatórios de uso de faturamento, consulte [Entender o uso de reserva do Azure](../billing/billing-understand-reserved-instance-usage-ea.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:

* [Quais são as reservas do Azure?](../billing/billing-save-compute-costs-reservations.md)  
* [Pagar antecipadamente por recursos do Azure Cosmos DB com capacidade reservada do Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)  
* [Pagar antecipadamente por recursos de computação de banco de dados SQL com capacidade reservada do Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)  
* [Gerenciar Reservas do Azure](../billing/billing-manage-reserved-vm-instance.md)  
* [Entender o uso de reserva para a sua assinatura paga conforme o uso](../billing/billing-understand-reserved-instance-usage.md)  
* [Entenda o uso de reservas para o seu registro Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md)  
* [Entender o uso de reserva para assinaturas de CSP](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

