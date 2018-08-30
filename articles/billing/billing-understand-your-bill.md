---
title: Entenda sua fatura do Azure | Microsoft Docs
description: Saiba como ler e entender o uso e a fatura da sua assinatura do Azure
services: ''
documentationcenter: ''
author: tonguyen10
manager: tonguyen
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/14/2018
ms.author: tonguyen
ms.openlocfilehash: c782cadadb0250e6c3ca4912dbf8f81e19cb88c5
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42919047"
---
# <a name="understand-your-bill-for-microsoft-azure"></a>Entenda sua fatura do Microsoft Azure
Para entender sua fatura do Azure, compare sua fatura com o arquivo de uso diário detalhado e os relatórios de gerenciamento de custo no Portal do Azure.

>[!NOTE]
>Este artigo não se aplica aos clientes do EA (Enterprise Agreement). Se você for um cliente do EA, [você pode encontrar a documentação de fatura no Enterprise Portal.](https://ea.azure.com/helpdocs/understandingYourInvoice)  

Para obter um PDF de sua fatura e uma cópia do download do arquivo de uso diário detalhado em CSV, consulte [Como obter sua fatura de cobrança do Azure e dados de uso diário](billing-download-azure-invoice-daily-usage-date.md). 

Para termos e descrições detalhados de sua fatura e arquivo de uso diário detalhado, consulte [Compreender os termos em sua fatura do Microsoft Azure](billing-understand-your-invoice.md) e [Compreender os termos em seu uso detalhado do Microsoft Azure](billing-understand-your-usage.md). 

Para obter detalhes sobre os relatórios de gerenciamento de custo, consulte [Gerenciamento de custos do portal do Azure](https://docs.microsoft.com/azure/billing/billing-getting-started).

> [!div class="nextstepaction"]
> [Ajudar a melhorar os documentos de cobrança do Azure](https://go.microsoft.com/fwlink/p/?linkid=2010091)

## <a name="charges"></a>Como fazer para ter certeza de que os encargos na minha fatura estão corretos?

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

Se há um encargo na fatura sobre o qual você deseja obter mais detalhes, há duas opções.

### <a name="option-1-review-your-invoice-and-compare-the-usage-and-costs-with-the-detailed-usage-csv-file"></a>Opção 1: examine a fatura e compare o uso e os custos com o arquivo CSV de uso detalhado

O arquivo CSV de uso detalhado mostra os encargos por período de cobrança e de uso diário. Para obter o arquivo CSV de uso detalhado, veja [How to get your Azure billing invoice and daily usage data](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date) (Como obter sua fatura de cobrança e os dados de uso diário do Azure).

Os encargos de uso são exibidos no nível do medidor. Os termos na lista a seguir têm o mesmo significado na fatura e no arquivo de uso detalhado. Por exemplo, o ciclo de cobrança na fatura é equivalente ao período de cobrança mostrado no arquivo de uso detalhado.

 | Fatura (PDF) | Uso detalhado (CSV)|
 | --- | --- |
|Ciclo de cobrança | Período de Cobrança |
 |NOME |Categoria de medidor |
 |Tipo |Subcategoria de medidor |
 |Recurso |Nome do medidor |
 |Região |Região do medidor |
 |Consumido |Quantidade consumida |
 |Incluso |Quantidade incluída |
 |Faturável |Quantidade de excesso |

A seção de **Encargos de Uso** da sua fatura tem o valor total de cada medidor que foi consumido durante o período de cobrança. Por exemplo, a captura de tela a seguir mostra um encargo de uso para o serviço Agendador do Azure.

![Encargos de uso da fatura](./media/billing-understand-your-bill/1.png)

O mesmo encargo é mostrado na seção **Instrução** de seu CSV de uso detalhado. A quantidade em *Consumido* e o *Valor* correspondem à fatura.

![Encargos de uso de CSV](./media/billing-understand-your-bill/2.png)

Para ver uma divisão desse encargo dia a dia, vá para a seção **Uso Diário** do CSV. Filtre por "Agendador" em *Categoria de Medidor* e você poderá ver em quais dias o medidor foi usado e quanto foi consumido. As informações *Recurso* e *Grupo de Recursos* também são listadas para comparação. A soma dos valores de *Consumido* devem equivaler ao que é mostrado na fatura.

![Seção Uso Diário no CSV](./media/billing-understand-your-bill/3.png)

Para obter o custo por dia, multiplique os valores de *Consumido* com o valor de *Taxa* da seção **Instrução**.

Para saber mais sobre a fatura, consulte [Entenda sua fatura do Azure](billing-understand-your-invoice.md).

Para saber mais sobre cada uma das colunas no CSV, consulte [Entenda seu uso detalhado do Azure](billing-understand-your-invoice.md).

### <a name="option-2-review-your-invoice-and-compare-with-the-usage-and-costs-in-the-azure-portal"></a>Opção 2: examine a fatura e compare com o uso e os custos no Portal do Azure

O portal do Azure também pode ajudar a verificar seus encargos. O portal do Azure fornece gráficos de gerenciamento de custos para uma visão geral rápida de seu uso e dos encargos na sua fatura.

Para continuar com o exemplo acima, visite a [página Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), selecione sua assinatura e, em seguida, escolha **Análise de custo**. Daí em diante, você pode especificar o período de tempo e ver os encargos de uso para o serviço Agendador do Azure.

![Exibição de análise de custo no portal do Azure](./media/billing-understand-your-bill/4.png)

Para ver a análise de custo diário em **Histórico de custo**, clique na linha.

![Exibição de histórico de custo no Portal do Azure](./media/billing-understand-your-bill/5.png)

Para saber mais, veja [Evitar custos inesperados com o gerenciamento de custos e cobrança do Azure](billing-getting-started.md#costs).

## <a name="external"></a>E quanto a cobranças de serviço externo?
Serviços externos (também conhecidos como pedidos do Azure Marketplace) são fornecidos por fornecedores de serviços independentes e são cobrados separadamente. Os encargos não aparecem na fatura do Azure. Para saber mais, confira [Entender os encargos dos serviços externos do Azure](billing-understand-your-azure-marketplace-charges.md).

## <a name="payment"></a>Como fazer um pagamento?

Se você tiver configurado um cartão de crédito ou débito como método de pagamento, o pagamento será cobrado automaticamente em até dez dias após o término do período de cobrança. Na sua declaração de cartão de crédito, o item de linha diria **MSFT Azure**.

Se você [pagar por fatura](billing-how-to-pay-by-invoice.md), envie seu pagamento para o local indicado na parte inferior da fatura. Para obter mais ajuda, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="how-do-i-check-the-status-of-a-payment-made-by-credit-card"></a>Como faço para verificar o status de um pagamento feito com cartão de crédito?

[Crie um tíquete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para solicitar o status do pagamento. 

## <a name="are-there-different-azure-customer-types-how-do-i-know-what-customer-type-i-am"></a>Existem diferentes tipos de clientes do Azure? Como eu sei o tipo de cliente que eu sou?
Existem diferentes tipos de clientes do Azure. Para entender melhor seu preço e faturamento, consulte as seguintes descrições de tipo de cliente.

- **Enterprise**: clientes corporativos assinaram um Enterprise Agreement com o Azure para fazer compromissos financeiros negociados e obter acesso a preços personalizados para recursos do Azure.
- **Web Direct**: os clientes do Web Direct não assinaram nenhum contrato personalizado com o Azure. Esses clientes se inscreveram no Azure pelo azure.com e receberam preços públicos para todos os recursos do Azure.
- **Provedor de serviços em nuvem**: os provedores de serviços em nuvem normalmente são empresas que foram contratadas por um cliente final para criar soluções em cima do Azure.

## <a name="why-dont-i-see-the-cost-the-resource-i-have-created-in-my-bill"></a>Por que não vejo o custo do recurso que criei na minha fatura?
O Azure não fatura diretamente com base no custo do recurso. O faturamento é feito com base em um ou mais medidores usados para rastrear o uso de um recurso durante toda a sua vida útil. Esses medidores são usados para calcular a conta. Veja mais sobre a medição do Azure abaixo.

## <a name="how-does-azure-charge-metering-work"></a>Como funciona a medição do Azure?
Quando você ativa um único recurso do Azure, como uma máquina virtual, ele também terá uma ou várias instâncias de medidores. Esses medidores são usados para rastrear o uso do recurso ao longo do tempo. Cada medidor emite registros de uso que são usados pelo Azure em nosso sistema de medição de custos para calcular a fatura. 

Por exemplo, uma única máquina virtual criada no Azure pode ter os seguintes medidores criados para rastrear seu uso:

- Calcular horas
- Horas de endereço IP
- Transferência de dados em
- Transferência de dados
- Disco gerenciado padrão
- Operações de disco gerenciado padrão
- Disco IO padrão
- Leitura de blob padrão do bloco IO
- Escrita de Blob Padrão de Bloqueio de E/S
- Bloqueio de blobs padrão IO-Block

Depois que a VM for criada, cada um dos medidores acima começará a emitir registros de uso. Esse uso será usado no sistema de medição do Azure junto com o preço do medidor para determinar quanto um cliente é cobrado.

> [!Note]
> Os medidores de exemplo acima podem ser apenas um subconjunto dos medidores criados em uma VM criada.

## <a name="what-is-the-difference-between-azure-1st-party-charges-and-azure-marketplace-charges"></a>Qual é a diferença entre as cobranças da primeira parte do Azure e as cobranças do Azure Marketplace?
As cobranças da primeira parte do Azure são para recursos desenvolvidos diretamente e oferecidos pelo Azure. As cobranças do Azure Marketplace são para recursos que foram criados por fornecedores de software de terceiros que estão disponíveis para uso no mercado do Azure. Por exemplo, um Barracuda Firewall é um recurso do mercado do Azure oferecido por terceiros. Todos os encargos para o firewall e seus medidores correspondentes aparecerão como cobranças de marketplace. 

## <a name="tips-for-cost-management"></a>Dicas para gerenciamento de custos
- Estimar os custos usando a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) e a [calculadora de custo total de propriedade](https://aka.ms/azure-tco-calculator) e obtenha as [informações de preços detalhadas para cada serviço](https://azure.microsoft.com/pricing/).
- [Configurar alertas de cobrança](billing-set-up-alerts.md).
- [Examine o uso e os custos regularmente no Portal do Azure](billing-getting-started.md#costs).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
