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
ms.topic: article
ms.date: 04/02/2018
ms.author: mbullwin
ms.openlocfilehash: 6e7591ccf0f21099474a08dda088422c377135f6
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2018
---
# <a name="enterprise-plan-details"></a>Detalhes do plano Enterprise

Application Insights tem dois planos de preços. O plano padrão é chamado [Básico](app-insights-pricing.md), que inclui todos os mesmos recursos que o plano Enterprise sem nenhum custo adicional e com cobrança principalmente relacionada ao volume de dados ingeridos. Se você estiver usando o Operations Management Suite, deve optar pelo plano Enterprise, que tem cobrança por nó com permissões de dados diárias e cobrará pelos dados ingeridos acima da permissão inclusa.

Consulte a [página de preços do Application Insights](http://azure.microsoft.com/pricing/details/application-insights/) para preços atuais em sua moeda e região.

## <a name="heres-how-the-enterprise-plan-works"></a>Assim funciona o plano Enterprise

* Você paga por nó que está enviando telemetria para quaisquer aplicativos no plano Enterprise.
 * Um *nó* é um computador de servidor físico ou virtual ou então uma instância de função de Plataforma como Serviço que hospeda seu aplicativo.
 * Computadores de desenvolvimento, navegadores do cliente e dispositivos móveis não são contados como nós.
 * Se seu aplicativo tem vários componentes que enviam telemetria, assim como um serviço Web e um trabalho de back-end, eles são contadas separadamente.
 * Os dados de [Live Metrics Stream](app-insights-live-stream.md) não são contados para fins de preço.* Em uma assinatura, as cobranças são por nó, não por aplicativo. Se você tiver cinco nós enviando telemetria para 12 aplicativos, a cobrança será para cinco nós.
* Embora as cobrança sejam cotadas por mês, você é cobrado apenas por aquelas horas em que um nó envia telemetria de um aplicativo. A cobrança por hora é a cobrança mensal cotada / 744 (o número de horas em um mês de 31 dias).
* Uma alocação de volume de dados de 200 MB por dia é fornecida para cada nó detectado (com granularidade por hora). A alocação de dados não utilizados não é transferida de um dia para o seguinte.
 * Se você escolher a opção de preço Enterprise, cada assinatura receberá uma provisão diária de dados com base no número de nós enviando telemetria para os recursos do Application Insights nessa assinatura. Então, se você tiver 5 nós enviando dados todos os dias, uma provisão em pool de 1 GB será aplicada a todos os recursos do Application Insights nessa assinatura. Não importa se determinados nós estão enviando mais dados que outros, porque os dados incluídos são compartilhados entre todos os nós. Se em um determinado dia os recursos do Application Insights recebem mais dados do que está incluído na alocação de dados diária para esta assinatura, as cobranças por dados excedentes por GB se aplicam. 
 * A provisão diária de dados é calculada como o número de horas por dia (usando UTC) que cada nó envia telemetria, dividido por 24, vezes 200 MB. Então, se você tem quatro nós enviando telemetria durante 15 das 24 horas do dia, os dados incluídos para aquele dia seriam ((4 x 15) / 24) X 200 MB = 500 MB. Pelo preço de US$ 2,30 por GB de dados excedentes, o valor a cobrar seria de US$ 1,15 se os nós enviassem 1 GB de dados naquele dia.
 * A provisão diária do plano Enterprise não é compartilhado com aplicativos para os quais você escolheu a opção Básico e o subsídio bonificação não utilizado não é transferido de um dia para o outro. 

## <a name="here-are-some-examples-of-determining-distinct-node-count"></a>Aqui estão alguns exemplos de determinar a contagem de nós distintos

| Cenário                               | Contagem de nós diária total |
|:---------------------------------------|:----------------:|
| Um aplicativo está usando três instâncias do Serviço de Aplicativo do Azure e um servidor virtual | 4 |
| Três aplicativos em execução em duas VMs, sendo que os recursos do Application Insights para esses aplicativos estão na mesma assinatura e no plano Enterprise | 2 | 
| Quatro aplicativos cujos recursos do Application Insights estão na mesma assinatura. Cada aplicativo executa duas instâncias durante 16 horas fora do horário de pico e quatro instâncias durante oito horas no horário de pico. | 13.33 | 
| Serviços de nuvem com uma função de trabalho e uma função web, cada uma executando duas instâncias | 4 | 
| Cluster do Service Fabric de cinco nós executando 50 microsserviços, cada microsserviço executando três instâncias | 5|

* O comportamento preciso da contagem de nós depende de qual SDK do Application Insights seu aplicativo está usando. 
  * No SDK versões 2.2 e posteriores, tanto o Application Insights [SDK do Core](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) quanto o [SDK Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) relatarão cada host de aplicativo como um nó, por exemplo, o nome do computador para o servidor físico e hosts de VM ou o nome da instância no caso de serviços de nuvem.  A única exceção é o caso de aplicativos usando apenas o [.NET Core](https://dotnet.github.io/) e o SDK do Core do Application Insights; nesse caso, somente um nó será relatado para todos os hosts porque o nome do host não está disponível. 
  * Para versões anteriores do SDK, o [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) se comportará exatamente como as versões mais recentes do SDK, no entanto, o [SDK do Core](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) reportará apenas um nó, independentemente do número de hosts de aplicativos reais. 
  * Se seu aplicativo estiver usando o SDK para definir roleInstance para um valor personalizado, esse mesmo valor será, por padrão, usado para determinar a contagem de nós. 
  * Se você está usando uma nova versão do SDK com um aplicativo que é executado em dispositivos móveis ou computadores cliente, é possível que a contagem de nós retorne um número muito grande (do grande número de dispositivos móveis ou computadores cliente). 
