---
title: Como obter suporte técnico da equipe de desenvolvimento do Application Insights | Microsoft Docs
description: Quando você tiver um caso que exija suporte especial da equipe de desenvolvimento do Application Insights, esse é o processo para enviar os detalhes para obter ajuda.
services: application-insights
documentationcenter: ''
author: alexbulankou
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/01/2016
ms.author: albulank

---
# Como obter suporte técnico da equipe de desenvolvimento do Application Insights
Veja aqui as opções para obter ajuda quando você tiver um problema técnico com o [Visual Studio Application Insights](app-insights-overview.md):

## 1\. Verificar os documentos
* Dados ausentes? Confira: [amostragem](app-insights-sampling.md), [cotas e limitação](app-insights-pricing.md).
* Solução de problemas: [ASP.NET](app-insights-troubleshoot-faq.md) | [Java](app-insights-java-troubleshoot.md)

## 2\. Pesquisar os fóruns
* [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)
* [StackOverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)

## 3\. Plano de suporte do Azure?
Há situações em que você gostaria que os desenvolvedores investigassem seu caso específico.

Se você tiver um [plano de suporte com o Microsoft Azure](https://azure.microsoft.com/support/plans/), é possível [abrir um tíquete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## 4\. Contatar a equipe do Application Insights
Se você não tiver um plano de suporte, nossa equipe de desenvolvimento tem a satisfação de oferecer o melhor suporte aos clientes do Application Insights, enquanto preparamos a versão de Disponibilidade Geral. Estamos apresentando **uma nova opção de suporte**: descreva seu caso enviando um formulário de comentários no portal do Azure e um desenvolvedor da equipe do Application Insights entrará em contato com você para ajudar a resolver o problema.

1. No [portal do Application Insights](https://portal.azure.com), clique no smiley no canto superior direito:  
   
    ![Botão de comentários](./media/app-insights-get-dev-support/01.png)
2. Na caixa de comentários, não se esqueça de especificar **AppInsights** como a primeira linha e inclua as seguintes informações:
   
    ```
   
    AppInsights   
    ikey: <instrumentation key>   
    sdk: <SDK that you are using, including name and version>  
    issue: <please describe the problem you are having>
   
    ```   
   
    ![Caixa de diálogo Comentários](./media/app-insights-get-dev-support/02.png)
3. Marque "Sim, aceito receber emails".
   
    ![Enviar seção](./media/app-insights-get-dev-support/03.png)

Um engenheiro da equipe do Application Insights entrará em contato com você em breve. Como estamos fornecendo esse serviço com base nos melhores esforços, nenhum SLA formal será fornecido neste momento.

<!---HONumber=AcomDC_0615_2016-->