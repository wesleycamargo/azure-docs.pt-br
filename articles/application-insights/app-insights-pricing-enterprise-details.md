---
title: Detalhes do plano de preços Legacy Enterprise do Azure Application Insights | Microsoft Docs
description: Gerencie volumes de telemetria e monitore custos no Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/21/2018
ms.author: mbullwin
ms.openlocfilehash: 65307eab0bf1b5f502f11c14c369826cd12e0966
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36311080"
---
# <a name="enterprise-plan-details"></a>Detalhes do plano Enterprise

O Application Insights do Azure oferece dois planos de preços: Básico e Enterprise. O plano de preços [Básico](app-insights-pricing.md) é o plano padrão. Ele inclui todos os recursos do plano Enterprise, sem nenhum custo adicional. O plano Básico cobra principalmente o volume de dados ingeridos. 

O plano Enterprise tem uma encargo por nó e cada nó recebe uma permissão diária de dados. No plano de preços Enterprise, você é cobrado pelos dados ingeridos acima da permissão incluída. Se você estiver usando o Operations Management Suite, deverá escolher o plano Enterprise. 

Para preços atuais em sua moeda e região, consulte [Preços do Application Insights](http://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> Em abril de 2018, [introduzimos](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) um novo modelo de preços para monitoramento do Azure. Esse modelo adota um modelo de "pagamento conforme o uso" simples no portfólio completo de serviços de monitoramento. Saiba mais sobre o [novo modelo de preços](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), como a [avaliar o impacto de migrar para esse modelo](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) com base nos seus padrões de uso e [como aceitar o novo modelo](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model)

## <a name="how-the-enterprise-plan-works"></a>Como o plano Enterprise funciona

* Você paga por cada nó que envia telemetria para qualquer aplicativo no plano Enterprise.
 * Um *nó* é um computador de servidor físico ou virtual ou uma instância de função de plataforma como serviço que hospeda o aplicativo.
 * Computadores de desenvolvimento, navegadores do cliente e dispositivos móveis não contam como nós.
 * Se o aplicativo tiver vários componentes que enviam telemetria, como um serviço Web e um trabalhado de back-end, os componentes serão contados separadamente.
 * Os dados de [Live Metrics Stream](app-insights-live-stream.md) não são contatos para fins de preços. Em uma assinatura, seus encargos são por nó, não por aplicativo. Se você tiver cinco nós que enviam telemetria para 12 aplicativos, o encargo será de cinco nós.
* Embora as cobrança sejam cotadas por mês, você é cobrado apenas por aquelas horas em que um nó envia telemetria de um aplicativo. O encargo por hora é a cobrança mensal cotada dividida por 744 (o número de horas em um mês de 31 dias).
* Uma alocação de volume de dados de 200 MB por dia é fornecida para cada nó detectado (com granularidade por hora). A alocação de dados não utilizada não é transportada de um dia para o outro.
 * Se você escolher o plano de preços Enterprise, cada assinatura receberá uma permissão diária de dados com base no número de nós que envia a telemetria aos recursos do Application Insights nessa assinatura. Portanto, se você tiver cinco nós que enviam dados todos os dias, você terá uma permissão em pool de 1 GB aplicada a todos os recursos do Application Insights nessa assinatura. Não importa se determinados nós enviam mais dados que outros nós, porque os dados incluídos são compartilhados entre todos os nós. Se em um determinado dia os recursos do Application Insights recebem mais dados do que está incluído na alocação de dados diária para esta assinatura, as cobranças por dados excedentes por GB se aplicam. 
 * A permissão de dados diária é calculada como o número de horas por dia (usando o UTC) que cada nó envia telemetria dividido por 24, multiplicado por 200 MB. Portanto, se você tiver quatro nós que enviam telemetria durante 15 das 24 horas do dia, os dados incluídos para esse dia serão ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB. Pelo preço de US$ 2,30 por GB de dados excedentes, o valor a cobrar seria de US$ 1,15 se os nós enviassem 1 GB de dados naquele dia.
 * A permissão diária do plano Enterprise não é compartilhada com aplicativos para os quais você escolheu o plano Básico. A permissão não utilizada não é herdada do dia a dia. 

## <a name="examples-of-how-to-determine-distinct-node-count"></a>Exemplos de como determinar a contagem de nós distinta

| Cenário                               | Contagem de nós diária total |
|:---------------------------------------|:----------------:|
| 1 aplicativo usando 3 instâncias do Serviço de Aplicativo do Azure e 1 servidor virtual | 4 |
| 3 aplicativos em execução em 2 VMs; os recursos do Application Insights para esses aplicativos estão na mesma assinatura e no plano Enterprise | 2 | 
| 4 aplicativos cujos recursos do Applications Insights estão na mesma assinatura; cada aplicativo executando 2 instâncias durante 16 horas fora de pico e 4 instâncias durante 8 horas de pico | 13.33 | 
| Serviços de nuvem com uma função de trabalho e uma função web, cada uma executando duas instâncias | 4 | 
| Um cluster do Azure Service Fabric de 5 nós que executa 50 microsserviços; cada microsserviço executando 3 instâncias | 5|

* A contagem de nós exata depende de qual SDK do Application Insights seu aplicativo está usando. 
  * No SDK versões 2.2 e posterior, o [SDK Core](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) do Application Insights e o [SDK Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) relatam cada host de aplicativo como um nó. Exemplos são o nome do computador para servidores físicos e hosts de VM ou o nome da instância para serviços de nuvem.  A única exceção é um aplicativo que usa apenas o [.NET Core](https://dotnet.github.io/) e o Application Insights o SDK Core. Nesse caso, apenas um nó é relatado para todos os hosts porque o nome do host não está disponível. 
  * Para versões anteriores do SDK, o [SDK Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) se comporta como as versões do SDK mais recentes, mas o [SDK Core](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) relata apenas um nó, independentemente do número de hosts do aplicativo. 
  * Se o aplicativo usar o SDK para definir **roleInstance** como um valor personalizado, por padrão, esse mesmo valor será usado para determinar a contagem de nós. 
  * Se você estiver usando uma nova versão do SDK com um aplicativo que executa a partir de computadores clientes ou dispositivos móveis, a contagem de nós poderá retornar um número muito grande (devido ao grande número de computadores clientes ou dispositivos móveis). 
