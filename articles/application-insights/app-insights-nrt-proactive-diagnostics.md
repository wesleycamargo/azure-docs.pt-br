<properties 
	pageTitle="Diagnóstico Proativo Quase em Tempo Real no Application Insights" 
	description="O Diagnóstico Proativo NRT notificará você automaticamente se o tempo de resposta do servidor mostrar um comportamento incomum. Nenhuma configuração é necessária." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2016" 
	ms.author="awills"/>
 
# Diagnóstico proativo quase em tempo real

*Esse recurso está em teste inicial.*

*Envie seus comentários para:* [ainrtpd@microsoft.com](mailto:ainrtpd@microsoft.com)

O [Visual Studio Application Insights](app-insights-overview.md) envia uma notificação a você praticamente em tempo real se a taxa de solicitações com falha de seu aplicativo Web aumentar consideravelmente. Para ajudar você com a triagem e diagnóstico do problema, a notificação acompanha uma análise das características das solicitações com falha e a telemetria relacionada. Também há links para portal do Application Insights, onde você pode obter um diagnóstico mais detalhado. O recurso não precisa de qualquer configuração, pois usa algoritmos de aprendizado de máquina para prever a taxa normal de falhas de linha de base. Ele precisa de um certo volume mínimo de tráfego para funcionar.

Veja a seguir exemplo do alerta:

![Exemplo de alerta inteligente mostrando a análise de cluster sobre a falha](./media/app-insights-nrt-proactive-diagnostics/010.png)

Para obter alertas sobre a falha, você deve configurar o [Application Insights para o ASP.NET](app-insights-asp-net.md) ou [para seu aplicativo Web em Java](app-insights-java-get-started.md).

## Como ele funciona

O Diagnóstico Proativo Quase em Tempo Real monitora a telemetria recebida de seu aplicativo, especialmente a taxa de solicitações com falha. Normalmente, essa métrica indica o número de solicitações HTTP que retornaram um código de resposta 400 ou mais (a menos que você tenha escrito o código personalizado para [filtrar](app-insights-api-filtering-sampling.md#filtering) ou gerar suas próprias chamadas de [TrackRequest](app-insights-api-custom-events-metrics.md#track-request)).

Com base no comportamento anterior dessa métrica, o serviço prevê o intervalo de valores esperado. Se o valor real for consideravelmente maior, ele gerará um alerta.

Quando um alerta é gerado, o serviço realiza uma análise de cluster em várias dimensões das solicitações, a fim de tentar identificar um padrão de valores que caracteriza as falhas. No exemplo acima, a análise descobriu que a maioria das falhas está relacionada a um nome específico de solicitação, mas descobriu também que as falhas são independentes da instância do host ou do servidor.

Em seguida, o analisador encontra exceções e falhas de dependência associadas às solicitações identificadas no cluster, junto com um exemplo de qualquer log de rastreamento associado a essas solicitações.

A análise resultante é enviada por email, a menos que você tenha configurado para isso não acontecer.

Assim como os [alertas que você definiu manualmente](app-insights-alerts.md), é possível inspecionar o estado do alerta e configurá-lo na folha Alertas de seu recurso Application Insights. Mas, ao contrário de outros alertas, você não precisa configurar o Alerta de Falha Adaptável. Se quiser, você pode desabilitá-lo ou alterar o endereço de email de destino.

## Se você receber um alerta

Um alerta indica a detecção de uma anomalia na taxa de solicitações com falha. É provável que haja algum problema com seu aplicativo ou seu ambiente. Talvez a nova versão que você acabou de carregar não funcione tão bem; ou talvez uma dependência, por exemplo, um banco de dados ou recurso externo, não esteja funcionando corretamente.

Você pode decidir a urgência do problema com base na porcentagem de solicitações e no número de usuários afetados.

Em muitos casos, você poderá diagnosticar o problema rapidamente pelo nome da solicitação, exceções, dependências e outros dados fornecidos.

Mas se você precisar investigar melhor, os links em cada seção levarão você diretamente até uma [página pesquisa](app-insights-diagnostic-search.md) filtrada para mostrar solicitações, exceções, dependências ou rastreamentos relevantes. Você também pode abrir o [Portal do Azure](https://portal.azure.com), navegar até o recurso Application Insights de seu aplicativo e abrir a folha Falhas.

## Exame dos alertas recentes

Para examinar os alertas no portal, abra **Configurações, Eventos operacionais**.

![Resumo de alertas](./media/app-insights-nrt-proactive-diagnostics/040.png)

Clique em qualquer alerta para ver todos os detalhes.


## Configurar alertas 

> **A experiência de configuração do usuário ainda não está disponível.**
> 
> Envie um email para [ainrtpd@microsoft.com](mailto:ainrtpd@microsoft.com).
>
> O funcionamento pode ser da seguinte maneira:

Abra a página Alertas. O Alerta de Falha Adaptável está incluído junto com todos os alertas que você configurou manualmente, e é possível ver se está em estado de alerta atualmente.

![Na página Visão geral, clique no bloco Alertas. Ou em qualquer página Métricas, clique no botão Alertas.](./media/app-insights-nrt-proactive-diagnostics/020.png)

Clique no alerta para configurá-lo.

![Configuração](./media/app-insights-nrt-proactive-diagnostics/030.png)

Observe que você pode desativar o Alerta de Falha Adaptável, mas não pode excluí-lo (ou criar outro).


## Qual é a diferença...

O Diagnóstico Proativo NRT complementa outros recursos distintos, mas parecidos, do Application Insights.

* Os [Alertas de métrica](app-insights-alerts.md) são definidos por você e podem monitorar uma ampla variedade de métricas, como ocupação da CPU, taxas de solicitação, tempos de carregamento de página e assim por diante. Você pode usá-los para receber um aviso, por exemplo, se precisar adicionar mais recursos. Por outro lado, o Diagnóstico Proativo NRT cobre um pequeno grupo de métricas essenciais (atualmente, apenas a taxa de solicitações com falha), projetadas para notificar você quase em tempo real quando a taxa de solicitações com falha de seu aplicativo Web aumentar consideravelmente, em comparação com o comportamento normal do aplicativo Web.
* [Detecção proativa](app-insights-proactive-detection.md) também usa a inteligência de máquina para descobrir padrões incomuns em suas métricas, sem qualquer necessidade de configuração da sua parte. Mas, ao contrário dos Diagnósticos Proativos NRT, a finalidade da Detecção proativa é localizar segmentos de sua utilização diversa que podem estar sendo atendidos de forma inadequada, por exemplo, por páginas específicas em um tipo específico de navegador. A análise é realizada diariamente e, se qualquer resultado for encontrado, provavelmente será muito menos urgente do que um alerta. Por outro lado, a análise do Diagnóstico Proativo NRT é executada continuamente na telemetria recebida, e você receberá uma notificação em questão de minutos, se as taxas de falha do servidor forem maiores do que o esperado.


## Envie comentários

*Este recurso está em teste inicial. Estamos muito interessados em receber seus comentários. Envie seus comentários para:* [ainrtpd@microsoft.com](mailto:ainrtpd@microsoft.com) *ou clique no link de comentários na mensagem do alerta.*

<!---HONumber=AcomDC_0218_2016-->