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
ms.topic: conceptual
ms.reviewer: Dale.Koetke
ms.date: 06/21/2018
ms.author: mbullwin
ms.openlocfilehash: a401bc3ed68dc9273ac14cfd357b9c05111dd54c
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36311100"
---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Gerenciar preços e volume de dados no Application Insights

Os preços do [Azure Application Insights][start] baseiam-se no volume de dados por aplicativo. Cada recurso do Application Insights é cobrado como um serviço separado e contribui para a cobrança da sua assinatura do Azure.

O Application Insights oferece dois planos de preços: Básico e Enterprise. O plano de preços Básico é o padrão. Ele inclui todos os recursos do plano Enterprise, sem nenhum custo adicional. O plano Básico cobra principalmente o volume de dados ingeridos. 

O plano Enterprise tem uma encargo por nó e cada nó recebe uma permissão diária de dados. No plano de preços Enterprise, você é cobrado pelos dados ingeridos acima do limite incluído. Se você usar o Operations Management Suite, você deve escolher o plano Enterprise. 

Se tiver dúvidas sobre como os preços são aplicados ao Application Insights, você poderá postar uma pergunta em nosso [fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights).

## <a name="pricing-plans"></a>Planos de preços

Para ver os preços atuais em sua moeda e região, consulte [Preços do Application Insights][pricing].

> [!NOTE]
> Em abril de 2018, [introduzimos](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) um novo modelo de preços para monitoramento do Azure. Esse modelo adota um modelo de "pagamento conforme o uso" simples no portfólio completo de serviços de monitoramento. Saiba mais sobre o [novo modelo de preços](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), como a [avaliar o impacto de migrar para esse modelo](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) com base nos seus padrões de uso e [como aceitar o novo modelo](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model).

### <a name="basic-plan"></a>Plano Básico

O plano Básico é o plano de preços padrão quando um novo recurso do Application Insights é criado. O plano Básico é ideal para todos os clientes, exceto aqueles que têm uma assinatura do Operations Management Suite.

* No plano Básico, você é cobrado por volume de dados. O volume de dados é o número de bytes de telemetria recebidos pelo Application Insights. O volume de dados é medido como o tamanho do pacote de dados JSON descompactado recebido pelo Application Insights do seu aplicativo. Para [dados tabulares importados para o Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), o volume de dados é medido como o tamanho descompactado de arquivos enviados ao Application Insights.
* Seus encargos de volume de dados do seu aplicativo agora são relatados em um novo medidor de cobrança denominado **Ingestão de Dados** a partir de abril de 2018. Este novo medidor é compartilhado entre tecnologias de monitoramento, como Applications Insights e Log Analytics e está atualmente no nome do serviço **Serviços de Aplicativos** (e em breve será alterado para **Log Analytics**). 
* Os dados de [Live Metrics Stream](app-insights-live-stream.md) não são contatos para fins de preços.
* [Exportação contínua](app-insights-export-telemetry.md) e [conector do Azure Log Analytics](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409) estarão disponíveis sem nenhum custo adicional no plano Básico a partir de abril de 2018.

### <a name="enterprise-plan-and-operations-management-suite-subscription-entitlements"></a>Direitos de assinatura do plano Empresarial e do Operations Management Suite

Os clientes que compram o Operations Management Suite E1 e E2 podem obter o Application Insights Enterprise como um componente adicional sem custo adicional conforme [anunciado anteriormente](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/). Especificamente, cada unidade do Operations Management Suite E1 e E2 dá direito a um nó do plano Application Insights Enterprise. Cada nó do Application Insights inclui até 200 MB de dados ingeridos por dia (separado de ingestão de dados do Log Analytics), com a retenção de dados de 90 dias sem nenhum custo adicional. O plano será descrito mais detalhados posteriormente neste artigo. 

Como esse plano é aplicável somente para clientes com uma assinatura do Operations Management Suite, os clientes que não tiverem uma assinatura do Operations Management Suite não verão uma opção para selecionar esse plano.

> [!NOTE]
> Para garantir que você obtenha este direito, os recursos do Application Insights deverão estar no plano de preços Enterprise. Esse direito se aplica apenas como nós. Os recursos do Application Insights no plano Básico não têm nenhum benefício. Esse direito não será visível nos custos estimados mostrados no painel **Uso e custo estimado**. Além disso, se você mover uma assinatura par o novo modelo de preços de monitoramento do Azure em abril de 2018, o plano Básico será o único disponível. Migrar uma assinatura para o novo modelo de preços de monitoramento do Azure não será recomendado se você tiver uma assinatura do Operations Management Suite.

Para obter mais informações sobre o plano Enterprise, consulte [Detalhes de preços do Enterprise](app-insights-pricing-enterprise-details.md).

### <a name="multi-step-web-tests"></a>Testes na Web com diversas etapas

Há uma cobrança adicional para [testes na Web de várias etapas](app-insights-monitor-web-app-availability.md#multi-step-web-tests). Testes na Web de várias etapas se referem a testes na Web que executam uma sequência de ações.

Não há nenhuma cobrança separada para *testes de ping* de uma única página. A telemetria de testes de ping e de testes de várias etapas é cobrada da mesma forma que outras telemetrias do seu aplicativo.

## <a name="review-pricing-plans-and-estimate-costs"></a>Examine os planos de preços e estime os custos

O Application Insights facilita a compreensão dos planos de preços disponíveis e quais custos podem provavelmente ser baseados em padrões de uso recentes. Para começar, no Portal do Azure, para o recurso do Application Insights, vá para o painel **Uso e custos estimados**:

![Escolher preços](./media/app-insights-pricing/pricing-001.png)

a. Examine o volume de dados do mês. Isso inclui todos os dados recebidos e mantidos (após qualquer [amostragem](app-insights-sampling.md)) de seu servidor e aplicativos cliente e dos testes de disponibilidade.  
B. Uma cobrança separada é feita pelos [testes na Web de várias etapas](app-insights-monitor-web-app-availability.md#multi-step-web-tests). (Isso não inclui testes de disponibilidade simples, que são incluídos na cobrança de volume de dados).  
C. Visualize as tendências do volume de dados do último mês.  
D. Habilite a [amostragem](app-insights-sampling.md) de ingestão de dados.   
E. Configure o limite de volume de dados diários.  

Encargos do Application Insights são adicionados à sua conta do Azure. Você pode ver os detalhes da sua conta do Azure na seção de **Cobrança** do Portal do Azure ou no [portal de cobrança do Azure](https://account.windowsazure.com/Subscriptions). 

![No menu à esquerda, selecione Cobrança](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>Taxa de dados
O volume de dados enviados é limitado de três formas:

* **Amostragem:** você pode usar a amostragem para reduzir o volume de telemetria enviado do seu servidor e de aplicativos cliente, com mínima distorção de métricas. Amostragem é a ferramenta principal que você pode usar para ajustar a quantidade de dados enviados. Saiba mais sobre [recursos de amostragem](app-insights-sampling.md). 
* **Limite diário:** quando você cria um recurso do Application Insights no Portal do Azure, o limite diário é definido como 100 GB/dia. Quando você cria um recurso do Application Insights no Visual Studio, o padrão é pequeno (somente 32,3 MB/dia). O padrão de limite diário é definido para facilitar o teste. O propósito dele é que o usuário irá gerar o limite diário antes de colocar o aplicativo em produção. 

    O limite máximo é 1.000 GB/dia, a menos que você solicite um máximo maior para um aplicativo de alto tráfego. 

    Tome cuidado ao definir o limite diário. A intenção deve ser *nunca atingir o limite diário*. Se atingir o limite diário, você perderá os dados para o restante do dia e não poderá monitorar seu aplicativo. Para alterar o limite diário, use a opção **Limite de volume diário**. Você pode acessar essa opção no painel **Uso e custos estimados** (isso está descrito em mais detalhes mais adiante neste artigo).
    Removemos a restrição de alguns tipos de assinatura com crédito que não pôde ser usado no Application Insights. Anteriormente, se a assinatura tivesse um limite de gastos, a caixa de diálogo de limite diário teria instruções sobre como remover esse limite e permitir que ele fosse aumentado para mais de 32,3 MB/dia.
* **Limitação:** esta opção limita a taxa de dados para 32.000 eventos por segundo, com média de 1 minuto por chave de instrumentação.

*O que acontece se o aplicativo exceder a taxa de limitação?*

* O volume de dados que seu aplicativo envia é avaliado a cada minuto. Se ele exceder a taxa por segundo média por minuto, o servidor recusa algumas solicitações. O SDK armazena em buffer os dados e, em seguida, tenta enviá-los novamente. Ele espalha um surto por vários minutos. Se o seu aplicativo enviar dados acima da taxa de limitação constantemente, alguns dados serão descartados. (Os SDKs do ASP.NET, Java e JavaScript tentam reenviar dados dessa maneira; outros SDKs poderão simplesmente descartar dados limitados.) Caso ocorra uma limitação, um aviso de notificação o alertará de que isso ocorreu.

*Como posso saber quantos dados meu aplicativo está enviando?*

Você pode usar uma das opções a seguir para ver a quantidade de dados sendo enviada pelo seu aplicativo:

* Vá para o painel **Uso e custos estimados** para ver o gráfico de volume de dados diário. 
* No Metrics Explorer, adicione um novo gráfico. Para a métrica do gráfico, selecione **Volume de ponto de dados**. Ative o **Agrupamento** e agrupe por **Tipo de dados**.

## <a name="reduce-your-data-rate"></a>Reduza sua taxa de dados
Veja abaixo o que é possível fazer para reduzir o volume de dados:

* Use a [Amostragem](app-insights-sampling.md). Essa tecnologia reduz a taxa de dados sem distorcer sua métrica. Você não perderá a capacidade de navegar entre os itens relacionados em Pesquisar. Em aplicativos de servidor, a amostragem funciona automaticamente.
* [Limite o número de chamadas do Ajax que podem ser informadas](app-insights-javascript.md#detailed-configuration) em cada modo de exibição de página ou desative o relatório de Ajax.
* [Edite Applicationinsights](app-insights-configuration-with-applicationinsights-config.md) para desativar os módulos de coleção desnecessários. Por exemplo, você pode decidir que os contadores de desempenho ou dados de dependência não são essenciais.
* Divida a telemetria em chaves de instrumentação separadas. 
* Métricas de pré-agregação. Se tiver feito chamadas para o TrackMetric no seu aplicativo, você poderá reduzir o tráfego usando a sobrecarga que aceita o cálculo do desvio médio e padrão de um lote de medições. Ou você pode usar um [pacote de pré-agregação](https://www.myget.org/gallery/applicationinsights-sdk-labs).

## <a name="manage-the-maximum-daily-data-volume"></a>Gerenciar o volume de dados diário máximo

Você pode usar o limite de volume diário para limitar os dados coletados. No entanto, se o limite for atingido, ocorrerá uma perda de toda a telemetria enviada do seu aplicativo no restante do dia. *Não é aconselhável* deixar o aplicativo atingir o limite diário. Não será possível rastrear a integridade e o desempenho do seu aplicativo após ele atingir o limite diário.

Em vez de usar o limite de volume diário, use a [amostragem](app-insights-sampling.md) para ajustar o volume de dados para o nível desejado. Em seguida, use o limite diário apenas como um "último recurso", no caso de seu aplicativo inesperadamente começar a enviar volumes muito mais altos de telemetria.

Para alterar o limite diário, na seção **Configurar** do recurso Application Insights, no painel **Uso e custos estimados**, selecione **Limite Diário**.

![Ajustar o limite de volume de telemetria diário](./media/app-insights-pricing/pricing-003.png)

## <a name="sampling"></a>amostragem
[Amostragem](app-insights-sampling.md) é um método de redução da taxa em que a telemetria é enviada para o seu aplicativo, enquanto mantém a capacidade de encontrar eventos relacionados durante as pesquisas de diagnóstico. Você também mantém contagens de eventos corretas.

A amostragem é uma maneira eficiente de reduzir encargos e permanecer dentro de sua cota mensal. O algoritmo de amostragem mantém itens de telemetria relacionados, para que, por exemplo, quando Pesquisar for utilizado, você possa encontrar a solicitação relacionada a uma exceção específica. O algoritmo também mantém contagens corretas, para que você veja os valores corretos no Metrics Explorer referentes a taxas de solicitação, taxas de exceção e outras contagens.

Há várias formas de amostragem.

* [Amostragem adaptável](app-insights-sampling.md) é o padrão para o SDK do ASP.NET. A amostragem adaptável se ajusta automaticamente ao volume de telemetria enviado por seu aplicativo. Ela opera automaticamente no SDK em seu aplicativo Web, para que o tráfego de telemetria na rede seja reduzido. 
* *amostragem de ingestão* é uma alternativa que opera no ponto em que a telemetria de seu aplicativo entra no serviço do Application Insights. A amostragem de ingestão não afeta o volume de telemetria enviado do seu aplicativo, mas reduz o volume retido pelo serviço. Você pode usar a amostragem de ingestão para reduzir a cota usada pela telemetria de navegadores e de outros SDKs.

Para definir a amostragem de ingestão, vá para o painel **Preços**:

![No painel Cota e preços, clique no bloco Amostras e selecione uma fração de amostragem](./media/app-insights-pricing/pricing-004.png)

> [!WARNING]
> O painel **Amostragem de dados** controla somente o valor de amostragem de ingestão. Ele não reflete a taxa de amostragem aplicada pelo SDK do Application Insights no seu aplicativo. Se a telemetria de entrada já tiver sido obtida como amostra no SDK, a amostragem de ingestão não será aplicada.
>

Para descobrir a taxa de amostragem real, independentemente de onde ela tiver sido aplicada, use uma [consulta do Analytics](app-insights-analytics.md). A consulta tem esta aparência:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

Em cada registro mantido, `itemCount` indica o número de registros originais que ele representa. É igual a 1 + o número de registros descartados anteriormente. 

## <a name="automation"></a>Automação

É possível escrever um script para definir o plano de preço usando o Gerenciamento de Recursos do Azure. [Saiba como](app-insights-powershell.md#price).

## <a name="limits-summary"></a>Resumo de limites

[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="next-steps"></a>Próximas etapas

* [Amostragem](app-insights-sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/