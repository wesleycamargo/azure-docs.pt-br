---
title: "Solução de Problemas e Perguntas sobre o Azure Application Insights | Microsoft Docs"
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
ms.sourcegitcommit: 182e28e37eb56c547e28524f2a3e13f042238cb4
ms.openlocfilehash: e066a7fc671399ba44bec35a2ea860fccddb4cc5


---
# <a name="questions---application-insights-for-aspnet"></a>Perguntas - Application Insights para ASP.NET
## <a name="configuration-problems"></a>Problemas de configuração
*Estou tendo problemas para configurar:*

* [Aplicativo .NET](app-insights-asp-net-troubleshoot-no-data.md)
* [Monitorar um aplicativo já em execução](app-insights-monitor-performance-live-website-now.md#troubleshooting-runtime-configuration-of-application-insights)
* [Diagnóstico do Azure](app-insights-azure-diagnostics.md)
* [Aplicativo Web Java](app-insights-java-troubleshoot.md)
* [Outras plataformas](app-insights-platforms.md)

*Não recebo qualquer valor de meu servidor*

* [Definir exceções de firewall](app-insights-ip-addresses.md)
* [Configurar um servidor ASP.NET](app-insights-monitor-performance-live-website-now.md)
* [Configurar um servidor Java](app-insights-java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>É possível usar o Application Insights com...?
[Consultar Plataformas][platforms]

## <a name="is-it-free"></a>É gratuito?

Sim, para uso experimental. No plano de preço básico, seu aplicativo pode enviar uma determinada margem de dados por mês gratuitamente. A margem gratuita é grande o suficiente para cobrir o desenvolvimento e publicação de um aplicativo para um número reduzido de usuários. Você pode definir um limite para impedir que mais de uma determinada quantidade de dados seja processada.

Você precisa usar o plano Enterprise para obter determinados recursos, como a Exportação Contínua. Ela incorre em um custo diário.

[Leia o plano de preço](https://azure.microsoft.com/pricing/details/application-insights/).


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
* (Somente novos projetos ‑ se você [adicionar o Application Insights a um projeto existente][start], precisará fazer isso manualmente.) Insere trechos no código do cliente e do servidor para inicializá-los com a ID de recurso do Application Insights. Por exemplo, em um aplicativo MVC, o código é inserido na página mestra Views/Shared/_Layout.cshtml

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Como atualizar de versões anteriores do SDK?
Consulte as [notas de versão](app-insights-release-notes.md) para o SDK adequado ao seu tipo de aplicativo.

## <a name="a-nameupdateahow-can-i-change-which-azure-resource-my-project-sends-data-to"></a><a name="update"></a>Como alterar o recurso do Azure ao qual meu projeto envia dados?
No Gerenciador de Soluções, clique com o botão direito do mouse em `ApplicationInsights.config` e escolha **Atualizar o Application Insights**. Você pode enviar os dados para um recurso novo ou existente no Azure. O assistente de atualização altera a chave de instrumentação em Applicationinsights. config, que por sua vez determina para onde o SDK do servidor envia seus dados. A menos que você desmarque a opção "Atualizar tudo", a chave onde ele aparece em suas páginas da Web também será alterada.

#### <a name="a-namedataahow-long-is-data-retained-in-the-portal-is-it-secure"></a><a name="data"></a>Por quanto tempo os dados são mantidos no portal? É seguro?
Veja [Privacidade e Retenção de Dados][data].

## <a name="logging"></a>Registro em log
#### <a name="a-namepostahow-do-i-see-post-data-in-diagnostic-search"></a><a name="post"></a>Como eu vejo dados de POST na pesquisa de Diagnóstico?
Nós não registramos dados de POST automaticamente, mas você pode usar uma chamada TrackTrace: colocar os dados no parâmetro de mensagem. Esse parâmetro tem um limite de tamanho maior do que os limites nas propriedades de cadeia de caracteres, embora nele você não possa aplicar filtros.

## <a name="security"></a>Segurança
#### <a name="is-my-data-secure-in-the-portal-how-long-is-it-retained"></a>Meus dados estão seguros no portal? Por quanto tempo eles estão retidos?
Consulte [Privacidade e Retenção de Dados][data].

## <a name="a-nameq17a-have-i-enabled-everything-in-application-insights"></a><a name="q17"></a> Eu habilitei tudo no Application Insights?
| O que você deverá ver | Como obter isso | Por que você deseja isso |
| --- | --- | --- |
| Gráficos de disponibilidade |[Testes da Web](app-insights-monitor-web-app-availability.md) |Tenha certeza que o aplicativo Web está ativo |
| Desempenho do aplicativo para servidores: tempos de resposta... |[Adicionar o Application Insights ao seu projeto](app-insights-asp-net.md) ou [Instalar o AI Status Monitor no servidor](app-insights-monitor-performance-live-website-now.md) (ou escreva seu próprio código para [rastrear dependências](app-insights-api-custom-events-metrics.md#trackdependency)) |Detectar problemas de desempenho |
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

[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md



<!--HONumber=Feb17_HO2-->


