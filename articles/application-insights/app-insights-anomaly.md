<properties 
	pageTitle="Application Insights: Detecção de anomalias pró-ativa" 
	description="O Application Insights executa uma análise profunda de telemetria do seu aplicativo e o avisará sobre possíveis problemas de desempenho." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/18/2015" 
	ms.author="awills"/>

#  Application Insights: Detecção de anomalias pró-ativa

*O Application Insights está em modo de visualização.*


O Application Insights executa uma análise profunda de telemetria do seu aplicativo e o avisará sobre possíveis problemas de desempenho. Provavelmente você está lendo este artigo porque recebeu um dos nossas alertas de anomalias por email.

## Sobre o alerta de anomalia

* *Por que eu recebei esse alerta?*
 * O Application Insights analisa periodicamente os dados com regras de reconhecimento do padrão. Ele procura por anomalias que possam indicar problemas de desempenho em seu aplicativo.
* *A notificação significa que, definitivamente, tenho um problema?*
 * Não. É simplesmente uma sugestão sobre algo que talvez você queira examinar mais detalhadamente. 
* *O que devo fazer?*
 * [Examine os dados apresentados](#responding-to-an-alert) e considere se os mesmos podem representar um problema. Caso contrário, está tudo bem.
* *Então, vocês examinam os meus dados?*
 * Não. O serviço é totalmente automático. Somente você recebe as notificações. Os dados são [privados](app-insights-data-retention-privacy.md).


## O processo de detecção

* *Quais tipos de anomalias são detectadas?*
 * Padrões que você acharia demorado verificar por conta própria. Por exemplo, um baixo desempenho em uma combinação específica de local, hora do dia e plataforma.
* *Posso criar minhas próprias regras de detecção de anomalias?*
 * Não para esse tipo de análise detalhada. (Mas você pode [configurar alertas](app-insights-alerts.md) que informam quando uma métrica exceder um limite.)
* *Com que frequência a análise é executada?*
 * Não fazemos análise em um recurso de aplicativo que não possui muita telemetria. É pouco provável que você receba avisos sobre sessões de depuração.


## Respondendo a um alerta

Abra o relatório de anomalias no email ou na lista de anomalias.

![](./media/app-insights-anomaly/02.png)

Aviso:

* A descrição
* A declaração de impacto, que informa quantos ou com que frequência os usuários são afetados.

Clique em um gráfico para abrir uma folha com mais detalhes.

Modifique o intervalo de tempo e os filtros para explorar a telemetria.




## Emails de notificação

* *É necessário assinar este serviço para receber notificações?*
 * Não. Nosso bot periodicamente pesquisa os dados de todos os usuários do Application Insights e envia notificações se detectar problemas.
* *Posso cancelar a assinatura ou ter as notificações enviadas para meus colegas em vez disso?*
 * Clique no link que está no alerta ou no email. Abra as configurações de anomalias.
 
    ![](./media/app-insights-anomaly/01.png)

    No momento, elas são enviadas para aqueles que têm [acesso de gravação ao recurso Application Insights](app-insights-resources-roles-access-control.md).
* *Não quero ser inundado com essas mensagens.*
 * Elas são limitados a três por dia. Você não receberá nenhuma mensagem repetida.
* *Se eu não fizer nada, posso receber um lembrete?*
 * Não, você receberá uma mensagem sobre cada problema apenas uma vez.
* *Perdi o email. Onde posso encontrar as notificações no portal?*
 * Na visão geral do Application Insights do seu aplicativo, clique no bloco **Anomalias**. 






 

<!---HONumber=Oct15_HO3-->