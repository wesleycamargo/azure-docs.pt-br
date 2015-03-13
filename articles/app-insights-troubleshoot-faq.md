<properties 
	pageTitle="Solução de problemas e Perguntas e respostas sobre o Application Insights" 
	description="Algo não está claro ou não está funcionando? Tente aqui." 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2015-02-03" 
	ms.author="awills"/>
 
# Solução de problemas e Perguntas e respostas - Application Insights na visualização do Microsoft Azure

+ [Eu não vejo nenhuma opção de Adicionar o Application Insights ao meu projeto no Visual Studio](#q01)
+ [Meu novo projeto Web foi criado, mas não foi possível adicionar o Application Insights.](#q02)
+ [Eu adicionei o Application Insights com êxito e executei meu aplicativo, mas não vi nenhum dado no portal.](#q03)
+ [Eu não vejo nenhum dado na Análise de uso](#q04)
+ [Estou olhando para o painel inicial da visualização do Microsoft Azure. Como eu encontro meus dados no Application Insights?](#q05)
+ [Como posso alterar o recurso do Azure sob o qual meus dados são exibidos?](#update)
+ [Eu recebo um erro "Chave de instrumentação não pode ser vazio"](#emptykey)
+ [Na tela inicial da visualização do Microsoft Azure, aquele mapa mostra o status do meu aplicativo?](#q06)
+ [Quando eu adiciono o Application Insights ao meu aplicativo e abro o portal do Application Insights, tudo parece completamente diferente das suas capturas de tela.](#q07)
+ [Eu posso usar o Application Insights para monitorar um servidor Web de intranet?](#q08)
+ [Como eu posso obter dados para o Windows Phone ou a Windows Store?](#q09)
+ [Como eu posso ver os eventos e exibições de página que registrei no meu código?](#q10)
+ [Por que há duas versões do Application Insights?](#q11)
+ [Como posso obter de volta todos os recursos que eu tinha na versão do Application Insights no Visual Studio Online?](#q13)
+ [O que o Application Insights modifica no meu projeto?](#q14)
+ [Como eu encontro meus resultados no Application Insights?](#q15)
+ [Quais portas devo abrir em meu firewall?](#q16)
+ [Eu habilitei tudo no Application Insights?](#q17)
+ [Saiba mais](#next)



## <a name="q01"></a>Eu não vejo nenhuma opção de Adicionar o Application Insights ao meu projeto no Visual Studio

+ Certifique-se de que você tem o [Visual Studio Atualização 3](http://go.microsoft.com/fwlink/?LinkId=397827). As Ferramentas do Application Insights vêm pré-instaladas e você deve conseguir vê-las no Gerenciador de Extensões.
+ No momento, o Application Insights na visualização do Microsoft Azure está disponível somente para projetos da Web ASP.NET em C# ou Visual Basic.
+ Se você tiver um projeto existente, vá até o Gerenciador de Soluções e certifique-se de clicar no projeto Web (e não em outro projeto nem na solução). Você deve ver um item de menu "Adicionar o Application Insights ao projeto".
+ Se estiver criando um novo projeto, no Visual Studio, abra Arquivo > Novo Projeto e selecione {Visual C#|Visual Basic} > Web > Aplicativo Web ASP.NET. Deve haver uma opção de Adicionar o Application Insights ao projeto.

## <a name="q02"></a>Meu novo projeto Web foi criado, mas não foi possível adicionar o Application Insights.

Isso pode acontecer caso a comunicação com o portal do Application Insights falhe, ou se houver algum problema com a sua conta.

+ Confirme que você forneceu as credenciais de logon da conta certa do Azure. As credenciais do Microsoft Azure, que você vê na caixa de diálogo Novo projeto, podem ser diferentes das credenciais do Visual Studio Online que você vê no canto superior direito do Visual Studio.
+ Espere um pouco e então [adicione o Application Insights ao seu projeto existente][start].
+ Vá até as configurações da sua conta do Microsoft Azure e veja se há restrições. Veja se você pode adicionar manualmente um aplicativo do Application Insights.


## <a name="q03"></a>Eu adicionei o Application Insights com êxito e executei meu aplicativo, mas não vi nenhum dado no portal.

+ Você precisa fechar e abrir qualquer lâmina na qual estiver esperando dados. Na versão atual, o conteúdo de uma lâmina não é atualizado automaticamente.
+ No painel inicial do Microsoft Azure, veja o mapa de status de serviço. Se houver indicações de alerta, espere até que elas tenham voltado a OK; então, feche e abra novamente a lâmina do Application Insights de seu aplicativo.
+ Em seu firewall, você terá que abrir as portas TCP 80 e 443 para tráfego de saída de dc.services.visualstudio.com e f5.services.visualstudio.com.

## <a name="q04"></a>Eu não vejo nenhum dado na Análise de uso

+ Os dados vêm de scripts nas páginas da Web. Se tiver adicionado o Application Insights a um projeto Web existente, [você terá que adicionar os scripts manualmente][start].


## <a name="q05"></a>Estou olhando para o painel inicial da visualização do Microsoft Azure. Como eu encontro meus dados no Application Insights?

Você pode:

* Selecionar Navegar, Application Insights, o nome do seu projeto. Se não tiver nenhum projeto, você precisa [adicionar o Application Insights ao seu projeto Web no Visual Studio][start].

* No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto Web e selecione Abrir o portal do Application Insights.

## <a name="update"></a>Como posso alterar o recurso do Azure para o qual meu projeto envia dados?

No Gerenciador de Soluções, clique com o botão direito do mouse em `ApplicationInsights.config` e escolha **Atualizar Application Insights**. Você pode enviar os dados para um recurso novo ou existente no Azure. O assistente de atualização altera a chave de instrumentação em Applicationinsights. config, que por sua vez determina para onde o SDK do servidor envia seus dados. A menos que você desmarque a opção "Atualizar tudo", a chave onde ele aparece em suas páginas da Web também será alterada.

## <a name="emptykey"></a>Eu recebo um erro "Chave de instrumentação não pode ser vazio"

Parece que algo deu errado enquanto você instalava o Application Insights, ou talvez um adaptador de registro em log.

No Gerenciador de Soluções, clique com o botão direito do mouse em `ApplicationInsights.config` e escolha **Atualizar Application Insights**. Você obterá uma caixa de diálogo que solicita que você entre no Azure e crie um recurso de Application Insights, ou então reutilize um recurso existente.

## <a name="q06"></a>Na tela inicial da visualização do Microsoft Azure, aquele mapa mostra o status do meu aplicativo?

Não! Ele mostra o status de serviço do Azure. Para ver os resultados de seu teste na Web, selecione Navegar > Application Insights > (seu aplicativo) e depois veja os resultados do teste na Web. 


## <a name="q07"></a>Quando eu adiciono o Application Insights ao meu aplicativo e abro o portal do Application Insights, tudo parece completamente diferente das suas capturas de tela.

Você pode estar usando [a versão mais antiga das Ferramentas do Application Insights](http://msdn.microsoft.com/library/dn793604.aspx), que tem conexão com a versão do Visual Studio Online.

As páginas de ajuda que você está consultando se referem ao [Application Insights para a visualização do Microsoft Azure][start], que já vem ativado no Visual Studio Atualização 3. 

## <a name="q08"></a>Eu posso usar o Application Insights para monitorar um servidor Web de intranet?

Sim, você pode monitorar a integridade e o uso se o seu servidor puder enviar dados à Internet pública.

Se você quiser executar testes na Web para seu serviço, ele precisará ser acessível pela Internet pública.

## <a name="q09"></a>Como eu posso obter dados para o Windows Phone ou a Windows Store?

Ainda não tem suporte nessa versão do Microsoft Azure. Use a [versão do Visual Studio Online][older].


## <a name="q10"></a>Como eu posso ver os eventos e exibições de página que registrei no meu código?

Ainda não temos suporte para isso na versão do Microsoft Azure. Em breve. No momento, você pode experimentar a [versão anterior][older].


## <a name="q11"></a>Por que há duas versões do Application Insights?

O portal mais antigo faz parte do Visual Studio Online. Nós não faremos mais alterações significativas nessa versão. Se você tiver uma versão mais antiga das ferramentas do Application Insights para Visual Studio, elas têm conexão com o portal do Visual Studio Online.

O Visual Studio Atualização 3 vem com uma versão pré-instalada das novas ferramentas do Application Insights. Elas têm conexão com o novo portal do Application Insights, que é um componente da visualização do Microsoft Azure. Atualmente, estamos passando o Application Insights para esse novo ambiente. O trabalho ainda não está completo.

## <a name="q13"></a>Como posso obter de volta todos os recursos que eu tinha na versão do Application Insights do Visual Studio Online?

1. Acesse o Gerenciador de extensões do Visual Studio. 
2. Desinstale as Ferramentas do Application Insights.
3. Execute [o instalador da versão antiga das ferramentas](http://visualstudiogallery.msdn.microsoft.com/82367b81-3f97-4de1-bbf1-eaf52ddc635a) e leia seu [guia de introdução][older].

## <a name="q14"></a>O que o Application Insights modifica no meu projeto?

Os detalhes dependem do tipo de projeto. Para um aplicativo web: +


+ Adiciona estes arquivos ao seu projeto:

 + ApplicationInsights.config. 
 + ai.js


+ Instala estes pacotes NuGet:

 -  *API do Application Insights* - a API principal

 -  *API do Application Insights para Aplicativos Web* - usada para enviar telemetria a partir do servidor

 -  *API do Application Insights para Aplicativos JavaScript* - usada para enviar telemetria a partir do cliente

    Os pacotes incluem os seguintes assemblies:

 - Microsoft.ApplicationInsights

 - Microsoft.ApplicationInsights.Platform

+ Insere itens em:

 - Web.config

 - packages.config

+ (Somente novos projetos - se você [adicionar o Application Insights a um projeto existente][start], precisará fazer isso manualmente.) Insere trechos no código do cliente e do servidor para inicializá-los com a ID de recurso do Application Insights. Por exemplo, em um aplicativo MVC, o código é inserido na página mestra Views/Shared/_Layout.cshtml


## <a name="q15"></a>Como eu encontro meus resultados no Application Insights?
1. Abra o Microsoft Azure:
 - No Visual Studio, clique com o botão direito do mouse no projeto do aplicativo Web e selecione **Abrir o portal de visualização do Azure**.
 - Ou em um navegador da Web, você pode abrir sua conta na visualização do Microsoft Azure.

2. Selecione Navegar, Application Insights e selecione seu projeto.

## <a name="q16"></a>Há um firewall entre meu servidor ou máquina de desenvolvimento e a Internet pública. Que tráfego eu devo permitir que habilite o Application Insights?

Os dados de uso e desempenho são enviados para as portas TCP 80 e 443 em dc.services.visualstudio.com e f5.services.visualstudio.com.

Testes de disponibilidade da Web dependem do acesso de entrada em seu servidor Web na porta 80.

## <a name="q17"></a> Eu habilitei tudo no Application Insights?

<table border="1">
<tr><th>O que você deve ver</th><th>Como obtê-lo</th><th>Porque você deseja obtê-lo</th></tr>
<tr><td>Gráficos de disponibilidade</td><td><a href="../app-insights-monitor-web-app-availability/">Testes na web</a></td><td>Saiba que seu Web app está funcionando</td></tr>
<tr><td>Desempenho do apl. de servidor: tempos de resposta...
</td><td><a href="../app-insights-start-monitoring-app-health-usage/">Adicione o Application Insights ao seu projeto</a><br/>ou <br/><a href="../app-insights-monitor-performance-live-website-now/">Instale o Monitor de Status AI no servidor</a></td><td>Detecte problemas de desempenho</td></tr>
<!-- ####future#### <tr><td>Telemetria da dependência</td><td><a href="../app-insights-monitor-performance-live-website-now/">Instale o Monitor de Status AI no servidor</a></td><td>Diagnostique problemas com bancos de dados ou outros componentes externos</td></tr> -->
<!-- #####74.1#### <tr><td>Variáveis globais de servidor: CPU, memória,... </td><td><a href="../app-insights-monitor-performance-live-website-now/">Instale o Monitor de Status AI no servidor</a></td><td>Diagnostique problemas de capacidade</td></tr> --> 
<tr><td>Pesquise rastreamentos de log</td><td><a href="../app-insights-search-diagnostic-logs/">Adicione um adaptador de registro em log</a></td><td>Diagnostique exceções, problemas de desempenho</td></tr>
<tr><td>Noções básicas de uso do cliente: exibições de página, retornos,...</td><td><a href="../app-insights-start-monitoring-app-health-usage/#webclient">Inicializador de JavaScript em páginas da Web</a></td><td>Análise de uso</td></tr>
<tr><td>Métricas personalizadas do cliente</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">Acompanhamento de chamadas em páginas da Web</a></td><td>Aprimore a experiência do usuário</td></tr>
<tr><td>Métricas personalizadas do cliente</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">Acompanhamento de chamadas em código de servidor</a></td><td>Business intelligence</td></tr>
</table>

Se o serviço da Web está em execução em uma VM do Azure, você também pode [obter diagnóstico][azurediagnostic] lá.



[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]




[azurediagnostic]: ../insights-how-to-use-diagnostics/

[older]: http://www.visualstudio.com/get-started/get-usage-data-vs


<!--HONumber=46--> 
