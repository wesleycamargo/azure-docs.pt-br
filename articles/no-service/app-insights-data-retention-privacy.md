<properties 
	pageTitle="Data retention and storage in Application Insights" 
	description="Retention and privacy policy statement" 
	services="application-insights" 
	documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/14/2014" 
	ms.author="awills"/>

# Retenção de dados e armazenamento no Application Insights 

*O Application Insights está em visualização.*


Este artigo responde as perguntas sobre como podemos armazenar a telemetria que o seu aplicativo envia para o Application Insights.

O Application Insights armazena dados em servidores do Microsoft Azure nos Estados Unidos. Baixe [o white paper de Segurança, Privacidade e Conformidade](http://go.microsoft.com/fwlink/?LinkId=392408) para obter detalhes sobre a política do Microsoft Azure. Algumas informações específicas sobre o Application Insights serão respondidas abaixo. 

#### Que quantidade de dados é armazenado? 

Embora o Application Insights esteja neste estágio de visualização gratuita, para cada conta, armazenamos: 

* Até 500 mensagens de telemetria por segundo (ou 30K por minuto). 

* Até 10 milhões de exibições de páginas ou eventos por mês. 

*O que acontece se eu exceder os limites?* 

* Quando estiver acima dos pequenos limites, enviaremos algumas mensagens após contá-los. Você não os vê na pesquisa de diagnóstico. A contagem de eventos estará correta. As médias de métricas baseiam-se em eventos que mantemos e que devem ser válidos. 

* Acima do limite mensal, os eventos param de ser gravados, para que suas métricas mostrem zeros após alguns dias. 

####Por quanto tempo a telemetria é mantida? 

* 7 dias para dados da instância visíveis na pesquisa de diagnóstico. Exibições de página individual, eventos, mensagens de log, rastreamentos e exceções. 

* 13 meses para dados agregados visíveis no gerenciador de métrica. As estatísticas de métricas, eventos e exceções (contas do tipo, função com falha e assim por diante) são mantidas em granulação de 1 minuto (ou menos) por 30 dias e granulação e granulação de 1 hora por 13 meses. 

####Onde os dados são mantidos? 

* Nos EUA. 

Há a opção de armazená-los na Europa ou em outro lugar? 

* Ainda não. 

####Quão seguros são os meus dados de telemetria? 

Os dados são armazenados em servidores do Microsoft Azure e para contas no Portal de Visualização do Azure, as restrições de conta são descritas no documento de segurança, privacidade e conformidade do Azure. Para contas no Portal Online do Visual Studio, aplica-se o documento de proteção de dados VS Online. 

É restrito o acesso aos seus dados por funcionários da Microsoft. Podemos acessar seus dados com a sua permissão e se isso for necessário para o seu uso do Application Insights. 

####Os dados são criptografados no servidores do Application Insights? 

Não no momento. 

####Podem ser enviadas informações pessoalmente identificáveis (PII) para o Application Insights? 

Sim. Tanto as informações de identificação pessoal enviadas para o Application Insights pelo seu código de telemetria personalizada, como as informações de identificação pessoal inclusas na telemetria padrão. A declaração de privacidade do Azure aplica-se ao Application Insights. 

Há várias maneiras como os dados podem ser enviados para o portal. Eles podem ser exibidos na pesquisa de diagnóstico. Membros da sua organização também podem exportar e baixar os dados. 

* Se você instalar o Monitor de Status ou adicioná-lo ao seu projeto, os rastreamentos de pilha são capturados quando ocorrem exceções ou alertas de desempenho. Eles podem incluir dados de parâmetro real como dados de SQL. 

* Se você inserir chamadas de telemetria como TrackEvent em seu código ou se você capturar mensagens do framework de log, as propriedades podem incluir informações de identificação pessoal. Também são aplicáveis os termos do Microsoft Online Services. Para especificamente o Application Insights, você deve estar em conformidade com as leis e regulamentos aplicáveis a privacidade e coleta de dados. Você deve determinar se a notificação ou outro consentimento são necessários antes de coletar as informações. 


####Qual telemetria é enviada ao usar o Application Insights? 

Há vários componentes que você pode instalar. (Consulte [Application Insights - introdução][start].) 

<table>
<tr><th>O que configurar</th><th>Telemetria enviada ao portal</th><th>Pode conter dados confidenciais?</th></tr>
<tr><td><a href="../app-insights-start-monitoring-app-health-usage/">Adicione o Application Insights ao seu projeto web</a></td>
  <td>Métricas: contagem de solicitações do servidor, tempo de resposta do servidor</td>
  <td>Não</td></tr>
<tr><td></td>
  <td>Relatórios de exceção do servidor</td><td>Despejos de pilha podem conter valores de parâmetro</td></tr>
<tr><td><a href="../app-insights-web-track-usage-custom-events-metrics/">Acompanhar métricas e eventos personalizados</a></td>
  <td>Eventos com propriedades anexadas ao seu código</td>
  <td>Sim, se seu código envia informações de identificação pessoal nas propriedades ou títulos</td></tr>
<tr><td><a href="../app-insights-search-diagnostic-logs/#trace">Telemetria de log e rastreamento</a></td><td>Mensagens de log e rastreamento</td><td>As mensagens de log podem conter informações de identificação pessoal</td></tr>
<tr><td><a href="../app-insights-web-track-usage/">Insira o script AI em suas páginas da web</a></td>
  <td>Id de contas e de usuários tornados anônimos</td><td>Não</td></tr>
<tr><td></td><td>Fim e início de sessão de usuário anônimo</td><td>Não</td></tr>
<tr><td></td><td>URI da página, horários de carga, tempo de sessão</td><td>Não</td></tr>
<tr><td><a href="../app-insights-monitor-performance-live-website-now/">Instalar o Monitor de Status no servidor</a></td>
  <td>Chamadas de dependência e durações</td>
  <td>Os dados de chamada podem conter parâmetros - por exemplo campos SQL</td></tr>
<tr><td></td><td>CPU, memória, rede e outros contadores de recurso</td><td>Não</td></tr>
<tr><td><a href="../app-insights-monitor-web-app-availability/">Testes da Web</a></td><td>Disponibilidade e tempos de resposta como exibidos na web</td><td>Não</td></tr>
</table>

## <a name="video"></a>Vídeos

#### Introdução

> [AZURE.VIDEO application-insights-introduction]

#### Introdução

> [AZURE.VIDEO getting-started-with-application-insights]




[AZURE.INCLUDE [app-insights-learn-more](../../includes/app-insights-learn-more.md)]



<!--HONumber=35.2-->
 