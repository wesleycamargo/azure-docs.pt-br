<properties 
	pageTitle="Diagnóstico Proativo Quase em Tempo Real no Application Insights" 
	description="Alerta sobre padrões de falha incomuns em seu aplicativo e fornece a análise do diagnóstico. Nenhuma configuração é necessária." 
	services="application-insights" 
    documentationCenter=""
	authors="yorac" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/15/2016" 
	ms.author="awills"/>
 
# Diagnóstico proativo quase em tempo real

O [Visual Studio Application Insights](app-insights-overview.md) enviará uma notificação a você automaticamente, quase em tempo real, se for detectado um aumento anormal na taxa de solicitações com falha. Para ajudar você com a triagem e diagnóstico do problema, a notificação acompanha uma análise das características das solicitações com falha e a telemetria relacionada. Também há links para portal do Application Insights, onde você pode obter um diagnóstico mais detalhado. O recurso não precisa de qualquer configuração, pois usa algoritmos de aprendizado de máquina para prever a taxa normal de falhas.

Esse recurso funciona para aplicativos Web Java e ASP.NET, hospedados na nuvem ou em seus próprios servidores. Ele também funciona para qualquer aplicativo que gere telemetria de solicitação - por exemplo, se você tiver uma função de trabalho que chame [TrackRequest()](app-insights-api-custom-events-metrics.md#track-request).

Depois de configurar o [Application Insights para seu projeto](app-insights-get-started.md), e desde que o aplicativo gere certa quantidade mínima de telemetria, o Diagnóstico Proativo NRT levará 24 horas para aprender o comportamento normal do aplicativo antes que ele seja ligado e possa enviar alertas.

Veja a seguir exemplo do alerta:

![Exemplo de alerta inteligente mostrando a análise de cluster sobre a falha](./media/app-insights-nrt-proactive-diagnostics/010.png)

Observe o que ele diz:

* A taxa de falhas em comparação com o comportamento normal do aplicativo.
* Quantos usuários foram afetados – você sabe o quanto deve se preocupar.
* Um padrão característico associado às falhas. Neste exemplo, há um código de resposta específico, o nome da solicitação (operação) e a versão do aplicativo. Isso diz a você imediatamente onde começar a procurar em seu código. Outras possibilidades poderiam ser um navegador ou um sistema operacional cliente específico.
* A exceção, os rastreamentos de log e as falhas de dependência (bancos de dados ou outros componentes externos) que parecem estar associados às solicitações com falha caracterizadas.
* Vincula diretamente às pesquisas relevantes na telemetria no Application Insights.

Os [alertas de métrica](app-insights-alerts.md) comuns mostram que pode haver um problema. Mas o Diagnóstico Proativo NRT inicia o trabalho de diagnóstico para você, executando grande parte da análise que, de outra forma, você teria de fazer por conta própria. Você obtém os resultados empacotados organizadamente, o que ajuda a chegar rapidamente à raiz do problema.

## Como ele funciona

O Diagnóstico Proativo Quase em Tempo Real monitora a telemetria recebida de seu aplicativo, especialmente a taxa de solicitações com falha. Essa métrica conta o número de solicitações para o qual a propriedade `Successful request` é falsa. Por padrão, `Successful request== (resultCode < 400)` (a menos que você tenha escrito o código personalizado para [filtrar](app-insights-api-filtering-sampling.md#filtering) ou gerar suas próprias chamadas [TrackRequest](app-insights-api-custom-events-metrics.md#track-request)).

O desempenho do aplicativo tem um padrão típico de comportamento. Algumas solicitações serão mais propensas a falhas do que outras; a taxa geral de falha poderá aumentar à medida que a carga crescer. O Diagnóstico Proativo NRT usa o aprendizado de máquina para encontrar essas anomalias.

Como a telemetria entra no Application Insights desde o seu aplicativo Web, o Diagnóstico Proativo NRT compara o comportamento atual aos padrões vistos nos últimos dias. Se for observado um aumento anormal na taxa de falha em comparação com o desempenho anterior, uma análise será disparada.

Quando um alerta é gerado, o serviço realiza uma análise de cluster na solicitação com falha a fim de tentar identificar um padrão de valores que caracterize as falhas. No exemplo acima, a análise descobriu que a maioria das falhas são sobre um código de resultado específico, nome de solicitação, host da URL do servidor e instância de função. Por outro lado, a análise descobriu que a propriedade do sistema operacional do cliente é distribuída por vários valores, e portanto não é listada.

Quando seu serviço conta com essa telemetria, o analisador encontra uma exceção e uma falha de dependência associadas às solicitações identificadas no cluster, junto com um exemplo de qualquer log de rastreamento associado a essas solicitações.

A análise resultante é enviada como um alerta, a menos que você tenha configurado para isso não acontecer.

Assim como os [alertas que você definiu manualmente](app-insights-alerts.md), é possível inspecionar o estado do alerta e configurá-lo na folha Alertas de seu recurso Application Insights. Mas, ao contrário de outros alertas, não é necessário configurar o Diagnóstico Proativo NRT. Se quiser, você pode desabilitá-lo ou alterar o endereço de email de destino.

## Triagem e diagnóstico de um alerta

Um alerta indica a detecção de um aumento anormal na taxa de solicitações com falha. É provável que haja algum problema com seu aplicativo ou seu ambiente.

Você pode decidir a urgência do problema com base na porcentagem de solicitações e no número de usuários afetados. No exemplo acima, a taxa de falha de 15% é comparada a uma taxa normal de 1,3% e indica que está acontecendo alguma coisa errada. Vinte e dois usuários diferentes foram afetados pelas falhas em uma determinada operação. Se fosse seu aplicativo, você poderia avaliar a gravidade.

Em muitos casos, você poderá diagnosticar o problema rapidamente pelo nome da solicitação, exceção, falha de dependência e pelos dados de rastreamento fornecidos.

Há alguns outros indícios. Por exemplo, a taxa de falha de dependência neste exemplo é igual à taxa de exceção (89,3%). Isso sugere que a exceção provém diretamente da falha de dependência - dando uma ideia clara de onde é preciso começar a procurar em seu código.

Para investigar melhor, os links em cada seção levarão você diretamente até uma [página pesquisa](app-insights-diagnostic-search.md) filtrada para mostrar solicitações, exceções, dependências ou rastreamentos relevantes. Você também pode abrir o [portal do Azure](https://portal.azure.com), navegar até o recurso Application Insights de seu aplicativo e abrir a folha Falhas.

Neste exemplo, clicar no link "Exibir detalhes da falha de dependência" abre a folha de pesquisa do Application Insights na instrução SQL com a causa raiz: NULOs foram fornecidos em campos obrigatórios e não foram aprovados na validação durante a operação de salvamento.


![Pesquisa de diagnóstico](./media/app-insights-nrt-proactive-diagnostics/051.png)

## Exame dos alertas recentes

Para examinar os alertas no portal, abra **Configurações, Logs de auditoria**.

![Resumo de alertas](./media/app-insights-nrt-proactive-diagnostics/040.png)

Clique em qualquer alerta para ver todos os detalhes.


## Configurar alertas 

Abra a página Alertas. O Diagnóstico Proativo está incluído junto com todos os alertas configurados manualmente, e é possível ver se está em estado de alerta no momento.

![Na página Visão geral, clique no bloco Alertas. Ou em qualquer página Métricas, clique no botão Alertas.](./media/app-insights-nrt-proactive-diagnostics/021.png)

Clique no alerta para configurá-lo.

![Configuração](./media/app-insights-nrt-proactive-diagnostics/031.png)

Observe que é possível desabilitar o Diagnóstico Proativo, mas não excluí-lo (nem criar outro).


## Qual é a diferença...

O Diagnóstico Proativo NRT complementa outros recursos distintos, mas parecidos, do Application Insights.

* Os [Alertas de Métrica](app-insights-alerts.md) são definidos por você e podem monitorar uma ampla variedade de métricas, como a ocupação da CPU, as taxas de solicitação, os tempos de carregamento de página e assim por diante. Você pode usá-los para receber um aviso, por exemplo, se precisar adicionar mais recursos. Por outro lado, o Diagnóstico Proativo NRT cobre um pequeno grupo de métricas essenciais (atualmente, apenas a taxa de solicitações com falha), projetadas para notificar você quase em tempo real quando a taxa de solicitações com falha de seu aplicativo Web aumentar consideravelmente, em comparação com o comportamento normal do aplicativo Web.

    O Diagnóstico Proativo NRT ajusta automaticamente seu limite em resposta às condições predominantes.

    O Diagnóstico Proativo NRT inicia o trabalho de diagnóstico para você. 
* A [Detecção Proativa](app-insights-proactive-detection.md) também usa a inteligência de máquina para descobrir padrões incomuns em suas métricas, sem qualquer necessidade de configuração da sua parte. Mas, ao contrário dos Diagnósticos Proativos NRT, a finalidade da Detecção proativa é localizar segmentos de sua utilização diversa que podem estar sendo atendidos de forma inadequada, por exemplo, por páginas específicas em um tipo específico de navegador. A análise é realizada diariamente e, se qualquer resultado for encontrado, provavelmente será muito menos urgente do que um alerta. Por outro lado, a análise do Diagnóstico Proativo NRT é executada continuamente na telemetria recebida, e você receberá uma notificação em questão de minutos, se as taxas de falha do servidor forem maiores do que o esperado.

## Se você receber um alerta do Diagnóstico Proativo NRT

*Por que eu recebei esse alerta?*

*	Foi detectado um aumento anormal de solicitações com falha em comparação à linha de base normal do período anterior. Após a análise das falhas e a telemetria associada, acreditamos que há um problema que você deve examinar. 

*A notificação significa que, definitivamente, tenho um problema?*

*	Tentamos alertar sobre a interrupção do aplicativo, ou sobre sua degradação, embora só você possa compreender totalmente a semântica e o impacto no aplicativo ou nos usuários.

*Então, vocês examinam os meus dados?*

*	Não. O serviço é totalmente automático. Somente você recebe as notificações. Os dados são [privados](app-insights-data-retention-privacy.md).

*É necessário assinar este alerta?*

*	Não. Todas as telemetrias de solicitação de envio de aplicativo têm esta regra de alerta.

*Posso cancelar a assinatura ou ter as notificações enviadas para meus colegas em vez disso?*

*	Sim, em Regras de alerta, clique na regra Diagnóstico Proativo para configurá-lo. Você pode desabilitar o alerta ou alterar os destinatários do alerta. 

*Perdi o email. Onde posso encontrar as notificações no portal?*

*	Nos Logs de auditoria. Clique em Configurações, Logs de auditoria e em qualquer alerta para ver sua ocorrência, mas com a exibição detalhada limitada.

*Alguns dos alertas são problemas conhecidos e não quero recebê-los.*

*	Nós temos a supressão de alerta em nossa lista de pendências.


## Envie comentários

*Estamos muito interessados em saber sua opinião sobre isso. Envie seus comentários para:* [ainrtpd@microsoft.com](mailto:ainrtpd@microsoft.com).

<!---HONumber=AcomDC_0413_2016-->