---
title: "Solução de Problemas e Perguntas sobre o Application Insights"
description: "Há algo no Azure Application Insights que não esteja claro ou não esteja funcionando? Tente aqui."
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: dea21a59b189d1d3d474cbc5e67f64df485a1981
ms.openlocfilehash: 0d860856b4c871c2c8e3622597ba3753bf62cf4f


---
# <a name="questions---application-insights-for-aspnet"></a>Perguntas - Application Insights para ASP.NET
## <a name="configuration-problems"></a>Problemas de configuração
*Estou tendo problemas para configurar:*

* [Aplicativo .NET](app-insights-asp-net-troubleshoot-no-data.md)
* [Monitorar um aplicativo já em execução](app-insights-monitor-performance-live-website-now.md#troubleshooting)
* [Diagnóstico do Azure](app-insights-azure-diagnostics.md)
* [Aplicativo Web Java](app-insights-java-troubleshoot.md)
* [Outras plataformas](app-insights-platforms.md)

*Não recebo qualquer valor de meu servidor*

* [Definir exceções de firewall](app-insights-ip-addresses.md)
* [Configurar um servidor ASP.NET](app-insights-monitor-performance-live-website-now.md)
* [Configurar um servidor Java](app-insights-java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>É possível usar o Application Insights com...?
[Consulte Plataformas][plataformas]

## <a name="is-it-free"></a>É gratuito?
* Sim, se você escolher a [faixa de preços](app-insights-pricing.md)gratuita. Você obtém a maioria dos recursos e uma cota mais ampla de dados.
* Você deve fornecer os dados de cartão de crédito para se registrar com o Microsoft Azure, mas nenhum encargo será cobrado, a menos que você use outro serviço do Azure pago ou explicitamente atualize para uma faixa cobrada.
* Se seu aplicativo enviar mais dados do que a cota mensal por camada gratuita, eles deixarão de ser registrados em log. Se isso acontecer, você pode optar por começar a pagar ou aguardar até que a cota seja reiniciada no final do mês.
* Dados básicos de uso e da sessão não estão sujeitos a uma cota.
* Há também uma avaliação gratuita de 30 dias, durante a qual você obtém os recursos pagos gratuitamente.
* Cada recurso de aplicativo tem uma cota separada, e você define sua faixa de preço de modo independente dos outros.

#### <a name="what-do-i-get-if-i-pay"></a>O que obtenho se pagar?
* Uma maior [cota mensal de dados](https://azure.microsoft.com/pricing/details/application-insights/).
* Opção de pagamento excedente para continuar coletando dados acima da cota mensal. Se os seus dados ficarem acima da cota, você será cobrado por Mb.
* [Exportação contínua](app-insights-export-telemetry.md).

## <a name="a-nameq14awhat-does-application-insights-modify-in-my-project"></a><a name="q14"></a>O que o Application Insights modifica no meu projeto?
Os detalhes dependem do tipo de projeto. Para um aplicativo Web:

* Adiciona estes arquivos ao seu projeto:
  
  * ApplicationInsights.config.
  * ai.js
* Instala estes pacotes NuGet:
  
  * *API do Application Insights* - a API principal
  * *API do Application Insights para Aplicativos Web* - usada para enviar telemetria do servidor
  * *API do Application Insights para Aplicativos JavaScript* - usada para enviar telemetria do cliente
    
    Os pacotes incluem os seguintes assemblies:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Insere itens em:
  
  * Web.config
  * packages.config
* (Somente novos projetos - se [adicionar o Application Insights a um projeto existente][iniciar], você precisará fazer isso manualmente.) Insere trechos no código do cliente e do servidor para inicializá-los com a ID de recurso do Application Insights. Por exemplo, em um aplicativo MVC, o código é inserido na página mestra Views/Shared/_Layout.cshtml

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Como atualizar de versões anteriores do SDK?
Consulte as [notas de versão](app-insights-release-notes.md) para o SDK adequado ao seu tipo de aplicativo.

## <a name="a-nameupdateahow-can-i-change-which-azure-resource-my-project-sends-data-to"></a><a name="update"></a>Como alterar o recurso do Azure ao qual meu projeto envia dados?
No Gerenciador de Soluções, clique com o botão direito do mouse em `ApplicationInsights.config` e escolha **Atualizar o Application Insights**. Você pode enviar os dados para um recurso novo ou existente no Azure. O assistente de atualização altera a chave de instrumentação em Applicationinsights. config, que por sua vez determina para onde o SDK do servidor envia seus dados. A menos que você desmarque a opção "Atualizar tudo", a chave onde ele aparece em suas páginas da Web também será alterada.

#### <a name="a-namedataahow-long-is-data-retained-in-the-portal-is-it-secure"></a><a name="data"></a>Por quanto tempo os dados são mantidos no portal? É seguro?
Consulte [Privacidade e Retenção de Dados][dados].

## <a name="logging"></a>Registro em log
#### <a name="a-namepostahow-do-i-see-post-data-in-diagnostic-search"></a><a name="post"></a>Como eu vejo dados de POST na pesquisa de Diagnóstico?
Nós não registramos dados de POST automaticamente, mas você pode usar uma chamada TrackTrace: colocar os dados no parâmetro de mensagem. Esse parâmetro tem um limite de tamanho maior do que os limites nas propriedades de cadeia de caracteres, embora nele você não possa aplicar filtros.

## <a name="security"></a>Segurança
#### <a name="is-my-data-secure-in-the-portal-how-long-is-it-retained"></a>Meus dados estão seguros no portal? Por quanto tempo eles estão retidos?
Consulte [Privacidade e retenção de dados][dados].

## <a name="a-nameq17a-have-i-enabled-everything-in-application-insights"></a><a name="q17"></a> Eu habilitei tudo no Application Insights?
| O que você deverá ver | Como obter isso | Por que você deseja isso |
| --- | --- | --- |
| Gráficos de disponibilidade |[Testes da Web](app-insights-monitor-web-app-availability.md) |Tenha certeza que o aplicativo Web está ativo |
| Desempenho do aplicativo para servidores: tempos de resposta... |[Adicionar o Application Insights ao seu projeto](app-insights-asp-net.md) ou [Instalar o AI Status Monitor no servidor](app-insights-monitor-performance-live-website-now.md) (ou escreva seu próprio código para [rastrear dependências](app-insights-api-custom-events-metrics.md#track-dependency)) |Detectar problemas de desempenho |
| Telemetria de dependência |[Instalar o AI Status Monitor no servidor](app-insights-monitor-performance-live-website-now.md) |Diagnosticar problemas com bancos de dados ou outros componentes externos |
| Obter rastreamentos de pilha por meio de exceções |[Inserir chamadas TrackException em seu código](app-insights-search-diagnostic-logs.md#exceptions) (mas alguns são informados automaticamente) |Detectar e diagnosticar exceções |
| Pesquisar rastreamentos de log |[Adicionar um adaptador de registro em log](app-insights-search-diagnostic-logs.md) |Diagnosticar exceções, problemas de desempenho |
| Noções básicas de uso do cliente: modos de exibição de página, sessões,... |[Inicializador de JavaScript em páginas da Web](app-insights-javascript.md) |Análise de uso |
| Métricas de cliente personalizadas |[Rastreando chamadas em páginas da Web](app-insights-api-custom-events-metrics.md) |Aprimorar a experiência do usuário |
| Métricas de servidor personalizadas |[Rastreando chamadas no servidor](app-insights-api-custom-events-metrics.md) |Business intelligence |

## <a name="automation"></a>Automação
Você pode [gravar scripts do PowerShell](app-insights-powershell.md) para criar e atualizar recursos do Application Insights.

## <a name="more-answers"></a>Mais respostas
* [Fórum do Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[dados]: app-insights-data-retention-privacy.md
[plataformas]: app-insights-platforms.md
[iniciar]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md



<!--HONumber=Nov16_HO3-->


