---
title: Gerenciar preços e volume de dados do Azure Application Insights | Microsoft Docs
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
ms.openlocfilehash: 42c54509cedf48e9ebabb5b50865ed56e54bee05
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2018
---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Gerenciar preços e volume de dados no Application Insights

Os preços do [Azure Application Insights][start] baseiam-se no volume de dados por aplicativo. Cada recurso Application Insights é cobrado como um serviço separado e contribui para a conta da sua assinatura do Azure.

Há dois planos de preço. O plano padrão é chamado Básico, que inclui todos os mesmos recursos que o plano Enterprise sem nenhum custo adicional e com cobrança principalmente relacionada ao volume de dados ingeridos. Se você estiver usando o Operations Management Suite, deve optar pelo plano Enterprise, que tem cobrança por nó com permissões de dados diárias e cobrará pelos dados ingeridos acima da permissão inclusa.

Se tiver dúvidas sobre como os preços são aplicados ao Application Insights, fique à vontade para postar uma pergunta em nosso [fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights).

## <a name="the-price-plans"></a>Os planos de preço

Consulte a [página de preços do Application Insights][pricing] para preços atuais em sua moeda e região.

### <a name="basic-plan"></a>Plano Básico

O plano Básico é o padrão quando um novo recurso do Application Insights é criado e é ideal para todos os clientes, exceto aqueles com uma assinatura do Operations Management Suite.

* No plano Básico, você é cobrado pelo volume de dados: o número de bytes de telemetria recebidos pelo Application Insights. O volume de dados é medido como o tamanho do pacote de dados JSON descompactado recebido pelo Application Insights do seu aplicativo.
Para [dados tabulares importados para análise](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), o volume de dados é medido como o tamanho descompactado de arquivos enviados ao Application Insights.
* Os dados de [Live Metrics Stream](app-insights-live-stream.md) não são contatos para fins de preços.
* [Exportação Contínua](app-insights-export-telemetry.md) e o [conector do Log Analytics](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409) estarão disponíveis sem nenhum custo adicional no plano Básico a partir de abril de 2018.

### <a name="enterprise-plan-and-operations-management-suite-subscription-entitlements"></a>Direitos de assinatura do plano Empresarial e do Operations Management Suite

Os clientes que compram o Microsoft Operations Management Suite E1 e E2 podem obter o Application Insights Enterprise como um componente adicional sem custo adicional como [anunciado anteriormente](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/). Especificamente, cada unidade adquirida do Operations Management Suite E1 e E2 incluem uma autorização para o nó 1 do plano Enterprise de Application Insights. Conforme descrito abaixo com mais detalhes, cada nó do Application Insights inclui até 200 MB de dados ingeridos por dia (separados do processamento de dados do Log Analytics), com retenção de dados de 90 dias sem custo adicional. Como isso é aplicável apenas aos clientes com uma assinatura do Operations Management Suite, os clientes sem uma assinatura não verão uma opção para selecionar esse plano.

> [!NOTE]
> Para garantir que você obtenha este direito, você deve ter os recursos do Application Insights no plano de preços Enterprise. Esse direito se aplica apenas somente aos nós, portanto os recursos do Application Insights no plano Básico não terão nenhum benefício. Observe que esse direito não será visível nos custos estimados mostrados na página *Uso e custos estimados*. Além disso, se você mover uma assinatura para o novo modelo de sistema de preços de monitoramento do Azure de abril de 2018, o plano Básico será o único disponível, portanto, não é recomendável se você tiver uma assinatura do Operations Management Suite.

Para obter mais detalhes sobre o plano Empresarial, visite a [página de detalhes de preços do Enterprise.](app-insights-pricing-enterprise-details.md)

### <a name="multi-step-web-tests"></a>Testes na Web com diversas etapas

Há uma cobrança adicional para [testes na Web de várias etapas](app-insights-monitor-web-app-availability.md#multi-step-web-tests). Isso se refere a testes na Web que executam uma sequência de ações.

Não há nenhuma cobrança separada para 'testes de ping' de uma única página. A telemetria de testes de ping e de testes de várias etapas é cobrada juntamente com outras telemetrias do seu aplicativo.

## <a name="review-pricing-plans-and-estimate-costs"></a>Examine os planos de preços e estime os custos

O Application Insights facilita reconhecer os planos de preços disponíveis e quais custos podem ser baseados em padrões de uso recentes. Comece abrindo a página **Uso e custos estimados** no recurso do Application Insights no Portal do Azure:

![Escolha Preço.](./media/app-insights-pricing/pricing-001.png)

**a.** Examine o volume de dados do mês. Isso inclui todos os dados recebidos e mantidos (após qualquer [Amostragem](app-insights-sampling.md) de seu servidor e aplicativos cliente e dos testes de disponibilidade.

**b.** Uma cobrança separada é feita pelos [testes na Web de várias etapas](app-insights-monitor-web-app-availability.md#multi-step-web-tests). (Isso não inclui testes de disponibilidade simples, que são incluídos na cobrança de volume de dados).

**c.** Visualize as tendências do volume de dados do último mês.

**d.** Habilite a [amostragem](app-insights-sampling.md) de ingestão de dados. 

**e.** Configure o limite de volume de dados diários.

Encargos do Application Insights são adicionados à sua conta do Azure. Você pode ver os detalhes da sua conta do Azure cobrar na seção de Cobrança do Portal do Azure ou no [Portal de Cobrança do Azure](https://account.windowsazure.com/Subscriptions). 

![No menu lateral, escolha Cobrança.](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>Taxa de dados
Há três maneiras de limitar o volume de dados enviados:

* **Amostragem:** esse mecanismo pode ser usado para reduzir o volume de telemetria enviado do seu servidor e de aplicativos cliente, com mínima distorção de métricas. Essa é a ferramenta principal que você tem para ajustar a quantidade de dados. Saiba mais sobre [recursos de amostragem](app-insights-sampling.md). 
* **Limite diário:** ao criar um recurso Application Insights no Portal do Azure, isso é definido como 100 GB/dia. Ao criar um recurso Application Insights do Visual Studio, o padrão é pequeno (somente 32,3 MB/dia), algo que só se destina à facilitação dos testes. Nesse caso, o propósito dele é que o usuário irá gerar o limite diário antes de implantar o aplicativo em produção. O limite máximo é 1000 GB/dia, a menos que você tenha solicitado um máximo maior para um aplicativo de alto tráfego. Tome cuidado ao definir o limite diário, porque sua intenção deve ser **nunca atingir o limite diário**, pois você irá perder dados do restante do dia e não poderá monitorar seu aplicativo. Para alterá-lo, use a opção Limite diário de volume, vinculada à página Uso e custos estimados (veja abaixo). Removemos a restrição de alguns tipos de assinatura com crédito que não puderam ser usados no Application Insights. Anteriormente, se a assinatura tivesse um limite de gastos, o diálogo de limite diário teria instruções sobre como removê-lo e permitir que o limite diário fosse aumentado para mais de 32,3 MB/dia.
* **Limitação:** isso limita a taxa de dados para 32.000 eventos por segundo, com média de 1 minuto.

*O que acontece se o aplicativo exceder a taxa de limitação?*

* O volume de dados que seu aplicativo envia é avaliado a cada minuto. Se ele exceder a taxa por segundo média por minuto, o servidor recusa algumas solicitações. O SDK armazena os dados em buffer e tenta reenviá-los, espalhando o aumento por vários minutos. Se seu aplicativo envia dados em acima da taxa de limitação consistentemente, alguns dados serão descartados. (Os SDKs do ASP.NET, Java e JavaScript tentam reenviar dessa maneira; outros SDKs poderão simplesmente descartar dados limitados.) Caso ocorra uma limitação, será exibido um aviso de notificação que isso aconteceu.

*Como posso saber quantos dados meu aplicativo está enviando?*

* Abra a página **Uso e custos estimados** para ver o gráfico de volume de dados diário. 
* Ou, no Metrics Explorer, adicione um novo gráfico e selecione **Volume de pontos de dados** como a métrica. Ative o Agrupamento e agrupe por **Tipo de dados**.

## <a name="to-reduce-your-data-rate"></a>Para reduzir a taxa de dados
Veja abaixo o que é possível fazer para reduzir o volume de dados:

* Use a [Amostragem](app-insights-sampling.md). Essa tecnologia reduz a taxa de dados sem distorcer suas métricas e sem afetar a capacidade de navegar entre itens relacionados na Pesquisa. Em aplicativos de servidor, ela funciona automaticamente.
* [Limite o número de chamadas do Ajax que podem ser informadas](app-insights-javascript.md#detailed-configuration) em cada modo de exibição de página ou desative o relatório de Ajax.
* Desative os módulos de coleção que você não precisa [editando o ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Por exemplo, você pode decidir que os contadores de desempenho ou dados de dependência não são essenciais.
* Divida a telemetria para separar as chaves de instrumentação. 
* Métricas de pré-agregação. Se você colocou chamadas ao TrackMetric no seu aplicativo, você pode reduzir o tráfego usando a sobrecarga que aceita o cálculo da média e o desvio padrão de um lote de medições. Ou então, você pode usar um [pacote de pré-agregação](https://www.myget.org/gallery/applicationinsights-sdk-labs).

## <a name="managing-the-maximum-daily-data-volume"></a>Gerenciar o volume diário máximo de dados

Você pode usar o limite de volume diário para limitar os dados coletados, mas, se o limite for atingido, isso resultará em perda de toda a telemetria enviada de seu aplicativo durante o restante do dia. **Não é aconselhável** que o aplicativo atinja o limite diário, pois você não pode controlar a integridade e o desempenho do seu aplicativo após esse limite ser atingido.

Em vez disso, use [Amostragem](app-insights-sampling.md) para ajustar o volume de dados para o nível que você desejar e use o limite diário apenas como "último recurso" caso o aplicativo comece a enviar volumes de telemetria muito mais altos inesperadamente.

Para alterar o limite diário, na seção Configurar do recurso Application Insights, na página **Uso e custos estimados**, clique em **Limite Diário**.

![Ajustar o limite de volume de telemetria diária](./media/app-insights-pricing/pricing-003.png)

## <a name="sampling"></a>amostragem
[Sampling](app-insights-sampling.md) é um método de redução da taxa na qual a telemetria é enviada para seu aplicativo, ao mesmo tempo que ainda retém a capacidade de encontrar eventos relacionados durante as pesquisas de diagnóstico, retendo também as contagens de eventos corretas.

A amostragem é uma maneira eficiente de reduzir encargos e permanecer dentro de sua cota mensal. O algoritmo de amostragem retém itens de telemetria relacionados, para que, por exemplo, quando a Pesquisa for utilizada, seja possível encontrar a solicitação relacionada a uma exceção específica. O algoritmo também retém contagens corretas, para que você veja os valores corretos no Gerenciador de Métricas referentes a taxas de solicitação, taxas de exceção e outras contagens.

Há várias formas de amostragem.

* [Adaptive sampling](app-insights-sampling.md) é o padrão para o SDK do ASP.NET, que se ajusta automaticamente ao volume de telemetria enviado por seu aplicativo. Ela opera automaticamente no SDK em seu aplicativo Web, para que o tráfego de telemetria na rede seja reduzido. 
* *amostragem de ingestão* é uma alternativa que opera no ponto em que a telemetria de seu aplicativo entra no serviço do Application Insights. Ela não afeta o volume de telemetria enviado de seu aplicativo, mas reduz o volume retido pelo serviço. Você pode usá-la para reduzir a cota usada pela telemetria de navegadores e de outros SDKs.

Para definir a amostragem de ingestão, defina o controle na caixa de diálogo Preços:

![Na caixa de diálogo Cota e preços, clique no bloco Amostras e selecione uma fração de amostragem.](./media/app-insights-pricing/pricing-004.png)

> [!WARNING]
> A caixa de diálogo Amostragem de dados apenas controla o valor de amostragem de ingestão. Ele não reflete a taxa de amostragem aplicada pelo SDK do Application Insights em seu aplicativo. Se a telemetria de entrada já tiver sido obtida como amostra no SDK, a amostragem de ingestão não será aplicada.
>

Para descobrir a taxa de amostragem real, independentemente de onde ela tiver sido aplicada, use uma [consulta do Analytics](app-insights-analytics.md) como esta:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

Em cada registro retido, o `itemCount` indica o número de registros originais que ele representa, igual a 1 + o número de registros descartados anteriormente. 

## <a name="automation"></a>Automação

É possível escrever um script para definir o plano de preço, usando o Gerenciamento de Recursos do Azure. [Saiba como](app-insights-powershell.md#price).

## <a name="limits-summary"></a>Resumo de limites

[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="next-steps"></a>Próximas etapas

* [Amostragem](app-insights-sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
