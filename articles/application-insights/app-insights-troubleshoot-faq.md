<properties 
	pageTitle="Solução de Problemas e Perguntas sobre o Application Insights" 
	description="Há algo no Visual Studio Application Insights que não está claro ou não está funcionando? Tente aqui." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/13/2016" 
	ms.author="awills"/>
 
# Perguntas - Application Insights para ASP.NET

## Problemas de configuração

*Estou tendo problemas para configurar:*

* [Aplicativo .NET](app-insights-asp-net-troubleshoot-no-data.md)
* [Monitorar um aplicativo já em execução](app-insights-monitor-performance-live-website-now.md#troubleshooting)
* [Diagnóstico do Azure](app-insights-azure-diagnostics.md)
* [Aplicativo Web Java](app-insights-java-troubleshoot.md)
* [Outras plataformas](app-insights-platforms.md)


## É possível usar o Application Insights com...?

[Consultar plataformas][platforms]


## É gratuito?

* Sim, se você escolher a [faixa de preços](app-insights-pricing.md) gratuita. Você obtém a maioria dos recursos e uma cota mais ampla de dados. 
* Você deve fornecer os dados de cartão de crédito para se registrar com o Microsoft Azure, mas nenhum encargo será cobrado, a menos que você use outro serviço do Azure pago ou explicitamente atualize para uma faixa cobrada.
* Se seu aplicativo enviar mais dados do que a cota mensal por camada gratuita, eles deixarão de ser registrados em log. Se isso acontecer, você pode optar por começar a pagar ou aguardar até que a cota seja reiniciada no final do mês.
* Dados básicos de uso e da sessão não estão sujeitos a uma cota.
* Há também uma avaliação gratuita de 30 dias, durante a qual você obtém os recursos pagos gratuitamente.
* Cada recurso de aplicativo tem uma cota separada, e você define sua faixa de preço de modo independente dos outros.

#### O que obtenho se pagar?

* Uma maior [cota mensal de dados](https://azure.microsoft.com/pricing/details/application-insights/).
* Opção de pagamento excedente para continuar coletando dados acima da cota mensal. Se os seus dados ficarem acima da cota, você será cobrado por Mb.
* [Exportação contínua](app-insights-export-telemetry.md).


## <a name="q14"></a>O que o Application Insights modifica no meu projeto?

Os detalhes dependem do tipo de projeto. Para um aplicativo Web:


+ Adiciona estes arquivos ao seu projeto:

 + ApplicationInsights.config.
 + ai.js


+ Instala estes pacotes NuGet:

 -  *API do Application Insights* - a API principal

 -  *API do Application Insights para Aplicativos Web* - usada para enviar telemetria do servidor

 -  *API do Application Insights para Aplicativos JavaScript* - usada para enviar telemetria do cliente

    Os pacotes incluem os seguintes assemblies:

 - Microsoft.ApplicationInsights

 - Microsoft.ApplicationInsights.Platform

+ Insere itens em:

 - Web.config

 - packages.config

+ (Somente novos projetos - se [adicionar o Application Insights a um projeto existente][start], você precisará fazer isso manualmente.) Insere trechos no código do cliente e do servidor para inicializá-los com a ID de recurso do Application Insights. Por exemplo, em um aplicativo MVC, o código é inserido na página mestra Views/Shared/\_Layout.cshtml


## Como atualizar de versões anteriores do SDK?

Consulte as [notas de versão](app-insights-release-notes.md) para o SDK adequado ao seu tipo de aplicativo.



## <a name="update"></a>Como alterar o recurso do Azure ao qual meu projeto envia dados?

No Gerenciador de Soluções, clique com o botão direito do mouse em `ApplicationInsights.config` e escolha **Atualizar o Application Insights**. Você pode enviar os dados para um recurso novo ou existente no Azure. O assistente de atualização altera a chave de instrumentação em Applicationinsights. config, que por sua vez determina para onde o SDK do servidor envia seus dados. A menos que você desmarque a opção "Atualizar tudo", a chave onde ele aparece em suas páginas da Web também será alterada.


#### <a name="data"></a>Por quanto tempo os dados são mantidos no portal? É seguro?

Faça uma consulta a [Privacidade e Retenção de Dados][data].

## Registro em log

#### <a name="post"></a>Como eu vejo dados de POST na pesquisa de Diagnóstico?

Nós não registramos dados de POST automaticamente, mas você pode usar uma chamada TrackTrace: colocar os dados no parâmetro de mensagem. Esse parâmetro tem um limite de tamanho maior do que os limites nas propriedades de cadeia de caracteres, embora nele você não possa aplicar filtros.

## Segurança

#### Meus dados estão seguros no portal? Por quanto tempo eles estão retidos?

Consulte [Privacidade e Retenção de Dados][data].


## <a name="q17"></a> Eu habilitei tudo no Application Insights?

<table border="1">
<tr><th>O que você deverá ver</th><th>Como obter isso</th><th>Por que você deseja isso</th></tr>
<tr><td>Gráficos de disponibilidade</td><td><a href="../app-insights-monitor-web-app-availability/">Testes da Web</a></td><td>Tenha certeza que o aplicativo Web está ativo</td></tr>
<tr><td>Desempenho do aplicativo para servidores: tempos de resposta...
</td><td><a href="../app-insights-asp-net/">Adicione o Application Insights ao seu projeto</a><br/>ou o <br/><a href="../app-insights-monitor-performance-live-website-now/">Instalar AI Status Monitor no servidor</a>(ou escreva seu próprio código para <a href="../app-insights-api-custom-events-metrics/#track-dependency">rastrear dependências</a>)</td><td>Detectar problemas de desempenho</td></tr>
<tr><td>Telemetria de dependência</td><td><a href="../app-insights-monitor-performance-live-website-now/">Instalar o Monitor de Status do Application Insights no servidor</a></td><td>Diagnosticar problemas com bancos de dados ou outros componentes externos</td></tr>
<tr><td>Obter rastreamentos de pilha por meio de exceções</td><td><a href="../app-insights-search-diagnostic-logs/#exceptions">Inserir chamadas TrackException em seu código</a> (mas alguns são informados automaticamente)</td><td>Detectar e diagnosticar exceções</td></tr>
<tr><td>Pesquisar rastreamentos de log</td><td><a href="../app-insights-search-diagnostic-logs/">Adicionar um adaptador de log</a></td><td>Diagnosticar exceções, problemas de desempenho</td></tr>
<tr><td>Noções básicas de uso do cliente: modos de exibição de página, sessões,...</td><td><a href="../app-insights-javascript/">Inicializador de JavaScript em páginas da Web</a></td><td>Análise de uso</td></tr>
<tr><td>Métricas de cliente personalizadas</td><td><a href="../app-insights-api-custom-events-metrics/">Rastreando chamadas em páginas da Web</a></td><td>Aprimorar a experiência do usuário</td></tr>
<tr><td>Métricas de servidor personalizadas</td><td><a href="../app-insights-api-custom-events-metrics/">Rastreando chamadas no código do servidor</a></td><td>Business intelligence</td></tr>
</table>


## Automação

Você pode [gravar scripts do PowerShell](app-insights-powershell.md) para criar e atualizar recursos do Application Insights.

## Mais respostas

* [Fórum do Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/pt-BR/home?forum=ApplicationInsights)


<!--Link references-->

[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_0525_2016-->