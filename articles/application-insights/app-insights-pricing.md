---
title: "Gerenciar preços e volume de dados do Application Insights | Microsoft Docs"
description: Gerencie volumes de telemetria e monitore custos no Application Insights.
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 01/13/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 9b26cfed4102bc09512f5ef1270aa0275a7f8f69
ms.openlocfilehash: 58e1f5862cd147d7664248cf393f77a5e9af10d3


---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Gerenciar preços e volume de dados no Application Insights


Os preços do [Azure Application Insights][start] baseiam-se no volume de dados por aplicativo. A baixa utilização durante o desenvolvimento ou para um aplicativo pequeno provavelmente será gratuita, pois há uma provisão mensal de 1 GB para dados de telemetria.

Cada recurso Application Insights é cobrado como um serviço separado e contribui para a conta da sua assinatura do Azure.

Há dois planos de preço. O plano padrão é o Básico. Você pode optar pelo plano Enterprise, que tem uma cobrança diária, mas habilita determinados recursos adicionais, como [exportação contínua](app-insights-export-telemetry.md).

Se tiver dúvidas sobre como os preços são aplicados ao Application Insights, fique à vontade para postar uma pergunta em nosso [fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights). 

## <a name="the-pricing-plans"></a>Os planos de preços

Consulte a [página de preços do Application Insights][pricing] para preços atuais em sua moeda.

### <a name="basic-plan"></a>Plano Básico

O plano Básico é o padrão quando um novo recurso do Application Insights é criado e será suficiente para a maioria dos clientes.

* No plano Básico, você é cobrado pelo volume de dados: o número de bytes de telemetria recebidos pelo Application Insights. O volume de dados é medido como o tamanho do pacote de dados JSON descompactado recebido pelo Application Insights do seu aplicativo.
* O primeiro GB para cada aplicativo é gratuito, portanto, se você estiver apenas testando ou desenvolvendo, é pouco provável que precise pagar.
* [Exportação contínua](app-insights-export-telemetry.md) está disponível para um encargo de GB de extra no plano básico, embora ele é gratuito até de 2017 início de março.

### <a name="enterprise-plan"></a>Plano Enterprise

* No plano Enterprise, seu aplicativo pode usar todos os recursos do Application Insights. A [Exportação Contínua](app-insights-export-telemetry.md) e o [Conector do Log Analytics](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409) estão disponíveis sem nenhuma cobrança adicional no plano Enterprise.
* Você paga por nó que está enviando telemetria para quaisquer aplicativos no plano Enterprise. 
 * Um *nó* é um computador de servidor físico ou virtual ou então uma instância de função de Plataforma como Serviço que hospeda seu aplicativo.
 * Computadores de desenvolvimento, navegadores do cliente e dispositivos móveis não são contados como nós.
 * Se seu aplicativo tem vários componentes que enviam telemetria, assim como um serviço Web e um trabalho de back-end, eles são contadas separadamente.
* Em uma assinatura, as cobranças são por nó, não por aplicativo. Se você tiver cinco nós enviando telemetria para 12 aplicativos, a cobrança será para cinco nós.
* Embora as cobrança sejam cotadas por mês, você é cobrado apenas por aquelas horas em que um nó envia telemetria de um aplicativo. A cobrança por hora é a cobrança mensal cotada / 744 (o número de horas em um mês de 31 dias).
* Uma alocação de volume de dados de 200 MB por dia é fornecida para cada nó detectado (com granularidade por hora). A alocação de dados não utilizados não é transferida de um dia para o seguinte.
 * Se você escolher a opção de preço Enterprise, cada assinatura receberá uma provisão diária de dados com base no número de nós enviando telemetria para os recursos do Application Insights nessa assinatura. Então, se você tiver 5 nós enviando dados todos os dias, uma provisão em pool de 1 GB será aplicada a todos os recursos do Application Insights nessa assinatura. Não importa se determinados nós estão enviando mais dados que outros, porque os dados incluídos são compartilhados entre todos os nós. Se em um determinado dia os recursos do Application Insights recebem mais dados do que está incluído na alocação de dados diária para esta assinatura, as cobranças por dados excedentes por GB se aplicam. 
 * A provisão diária de dados é calculada como o número de horas por dia (usando UTC) que cada nó envia telemetria, dividido por 24, vezes 200 MB. Então, se você tem quatro nós enviando telemetria durante 15 das 24 horas do dia, os dados incluídos para aquele dia seriam ((4 x 15) / 24) X 200 MB = 500 MB. Pelo preço de US$ 2,30 por GB de dados excedentes, o valor a cobrar seria de US$ 1,15 se os nós enviassem 1 GB de dados naquele dia.
 * Observe que a provisão diária do plano Enterprise não é compartilhado com aplicativos para os quais você escolheu a opção Básico e o subsídio bonificação não utilizado não é transferido de um dia para o outro. 
* Aqui estão alguns exemplos de determinar a contagem de nós distintos:
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
  * Observe que se seu aplicativo estiver usando o SDK para definir roleInstance para um valor personalizado, esse mesmo valor será, por padrão, usado para determinar a contagem de nós. 
  * Se você está usando uma nova versão do SDK com um aplicativo que é executado em dispositivos móveis ou computadores cliente, é possível que a contagem de nós retorne um número muito grande (do grande número de dispositivos móveis ou computadores cliente). 

### <a name="multi-step-web-tests"></a>Testes na Web com diversas etapas

Há uma cobrança adicional para [testes na Web de várias etapas](app-insights-monitor-web-app-availability.md#multi-step-web-tests). Isso se refere a testes na Web que executam uma sequência de ações. 

Não há nenhuma cobrança separada para 'testes de ping' de uma única página. A telemetria de testes de ping e de testes de várias etapas é cobrada juntamente com outras telemetrias do seu aplicativo.

## <a name="review-pricing-plan-and-estimate-costs-for-your-application-insights-resource"></a>Examinar o plano de preços e estimar custos para seu recurso do Application Insights
Abra a folha Recursos + Preço no recurso do Application Insights para seu aplicativo.

![Escolha Preço.](./media/app-insights-pricing/01-pricing.png)

**a.** Examine o volume de dados do mês. Isso inclui todos os dados recebidos e mantidos (após qualquer [Amostragem](app-insights-sampling.md) de seu servidor e aplicativos cliente e dos testes de disponibilidade.

**b.** Uma cobrança separada é feita pelos [testes na Web de várias etapas](app-insights-monitor-web-app-availability.md#multi-step-web-tests). (Isso não inclui testes de disponibilidade simples, que são incluídos na cobrança de volume de dados).

**c.** Habilite o plano Enterprise.

**d.** Clique nas opções de gerenciamento de dados para exibir o volume de dados do último mês, definir uma capacidade diária ou definir a amostragem de ingestão.

Encargos do Application Insights são adicionados à sua conta do Azure. Você pode ver os detalhes da sua conta do Azure cobrar na seção de Cobrança do Portal do Azure ou no [Portal de Cobrança do Azure](https://account.windowsazure.com/Subscriptions). 

![No menu lateral, escolha Cobrança.](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>Taxa de dados
Há três maneiras de limitar o volume de dados enviados:

* **Capacidade diária.** Por padrão, é definida em 500 GB/dia. Quando o aplicativo atinge a capacidade, enviamos um email e descartamos dados até o fim do dia. Para alterá-la, use a folha Gerenciamento de Volume de Dados.
* **[Amostragem](app-insights-sampling.md).** Esse mecanismo pode reduzir o volume de telemetria enviado do seu servidor e de aplicativos cliente, com mínima distorção de métricas.
* **Limitação** limita a taxa de dados para 16 mil eventos por segundo, medidos ao longo de um minuto. 


*O que acontece se o aplicativo exceder a taxa de limitação?*

* O volume de dados que seu aplicativo envia é avaliado a cada minuto. Se ele exceder a taxa por segundo média por minuto, o servidor recusa algumas solicitações. O SDK armazena os dados em buffer e tenta reenviá-los, espalhando o aumento por vários minutos. Se seu aplicativo envia dados em acima da taxa de limitação consistentemente, alguns dados serão descartados. (Os SDKs do ASP.NET, Java e JavaScript tentam reenviar dessa maneira; outros SDKs poderão simplesmente descartar dados limitados.)

Caso ocorra uma limitação, será exibido um aviso de notificação que isso aconteceu.

*Como posso saber quantos pontos de dados meu aplicativo está enviando?*

* Abra a folha Preço para ver o gráfico Volume de Dados.
* Ou, no Metrics Explorer, adicione um novo gráfico e selecione **Volume de pontos de dados** como a métrica. Ative o Agrupamento e agrupe por **Tipo de dados**.

## <a name="to-reduce-your-data-rate"></a>Para reduzir a taxa de dados
Veja abaixo o que é possível fazer para reduzir o volume de dados:

* Reduza a capacidade de volume diária. O padrão é 500 GB/dia.
* Use a [Amostragem](app-insights-sampling.md). Essa tecnologia reduz a taxa de dados sem distorcer suas métricas e sem afetar a capacidade de navegar entre itens relacionados na Pesquisa. Em aplicativos de servidor, ela funciona automaticamente.
* [Limite o número de chamadas do Ajax que podem ser informadas](app-insights-javascript.md#detailed-configuration) em cada modo de exibição de página ou desative o relatório de Ajax.
* Desative os módulos de coleção que você não precisa [editando o ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Por exemplo, você pode decidir que os contadores de desempenho ou dados de dependência não são essenciais.
* Divida a telemetria para separar as chaves de instrumentação. 
* Métricas de pré-agregação. Se você colocou chamadas ao TrackMetric no seu aplicativo, você pode reduzir o tráfego usando a sobrecarga que aceita o cálculo da média e o desvio padrão de um lote de medições. Ou então, você pode usar um [pacote de pré-agregação](https://www.myget.org/gallery/applicationinsights-sdk-labs). 

## <a name="sampling"></a>amostragem
[Sampling](app-insights-sampling.md) é um método de redução da taxa na qual a telemetria é enviada para seu aplicativo, ao mesmo tempo que ainda retém a capacidade de encontrar eventos relacionados durante as pesquisas de diagnóstico, retendo também as contagens de eventos corretas. 

A amostragem é uma maneira eficiente de reduzir encargos e permanecer dentro de sua cota mensal. O algoritmo de amostragem retém itens de telemetria relacionados, para que, por exemplo, quando a Pesquisa for utilizada, seja possível encontrar a solicitação relacionada a uma exceção específica. O algoritmo também retém contagens corretas, para que você veja os valores corretos no Gerenciador de Métricas referentes a taxas de solicitação, taxas de exceção e outras contagens.

Há várias formas de amostragem.

* [Adaptive sampling](app-insights-sampling.md) é o padrão para o SDK do ASP.NET, que se ajusta automaticamente ao volume de telemetria enviado por seu aplicativo. Ela opera automaticamente no SDK em seu aplicativo Web, para que o tráfego de telemetria na rede seja reduzido. 
* *amostragem de ingestão* é uma alternativa que opera no ponto em que a telemetria de seu aplicativo entra no serviço do Application Insights. Ela não afeta o volume de telemetria enviado de seu aplicativo, mas reduz o volume retido pelo serviço. Você pode usá-la para reduzir a cota usada pela telemetria de navegadores e de outros SDKs.

Para definir a amostragem de ingestão, defina o controle na folha Preço:

![Na folha Cota e preço, clique no bloco Exemplos e selecione uma fração de amostragem.](./media/app-insights-pricing/04.png)

> [!WARNING]
> O valor mostrado no bloco Exemplos Retidos indica somente o valor definido para amostragem de ingestão. Ele não mostra a taxa de amostragem operando com o SDK em seu aplicativo. 
> 
> Se a telemetria de entrada já tiver sido obtida como amostra no SDK, a amostragem de ingestão não será aplicada.
> 
> 

Para descobrir a taxa de amostragem real, independentemente de onde ela tiver sido aplicada, use uma [consulta do Analytics](app-insights-analytics.md) como esta:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h) 
    | render areachart 

Em cada registro retido, o `itemCount` indica o número de registros originais que ele representa, igual a 1 + o número de registros descartados anteriormente. 


## <a name="transition-from-the-old-pricing-tiers"></a>Transição dos tipos de preço antigos

Aplicativos existentes podem continuar a usar os tipos de preço antigos até de fevereiro de 2017. Chegado esse momento, a maioria dos aplicativos serão movidos automaticamente para o plano Básico. Aqueles que usarem a exportação contínua ou o conector para o OMS Log Analytics serão movidos para o plano Enterprise.


## <a name="limits-summary"></a>Resumo de limites
[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="next-steps"></a>Próximas etapas

* [Amostragem](app-insights-sampling.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/




<!--HONumber=Feb17_HO1-->


