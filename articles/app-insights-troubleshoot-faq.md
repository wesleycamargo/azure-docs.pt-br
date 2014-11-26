<properties title="Troubleshooting and Q & A about Application Insights" pageTitle="Troubleshooting and Q & A about Application Insights" description="Tips and troubleshooting" metaKeywords="analytics monitoring" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills" />

# Solução de problemas e Perguntas e respostas - Application Insights na visualização do Microsoft Azure

-   [Eu não vejo nenhuma opção de Adicionar o Application Insights ao meu projeto no Visual Studio][Eu não vejo nenhuma opção de Adicionar o Application Insights ao meu projeto no Visual Studio]
-   [Meu novo projeto Web foi criado, mas não foi possível adicionar o Application Insights.][Meu novo projeto Web foi criado, mas não foi possível adicionar o Application Insights.]
-   [Eu adicionei o Application Insights com êxito e executei meu aplicativo, mas não vi nenhum dado no portal.][Eu adicionei o Application Insights com êxito e executei meu aplicativo, mas não vi nenhum dado no portal.]
-   [Eu não vejo nenhum dado na Análise de uso][Eu não vejo nenhum dado na Análise de uso]
-   [Estou olhando para o painel inicial da visualização do Microsoft Azure. Como eu encontro meus dados no Application Insights?][Estou olhando para o painel inicial da visualização do Microsoft Azure. Como eu encontro meus dados no Application Insights?]
-   [Na tela inicial da visualização do Microsoft Azure, aquele mapa mostra o status do meu aplicativo?][Na tela inicial da visualização do Microsoft Azure, aquele mapa mostra o status do meu aplicativo?]
-   [Quando eu adiciono o Application Insights ao meu aplicativo e abro o portal do Application Insights, tudo parece completamente diferente das suas capturas de tela.][Quando eu adiciono o Application Insights ao meu aplicativo e abro o portal do Application Insights, tudo parece completamente diferente das suas capturas de tela.]
-   [Eu posso usar o Application Insights para monitorar um servidor Web de intranet?][Eu posso usar o Application Insights para monitorar um servidor Web de intranet?]
-   [Como eu posso obter dados para o Windows Phone ou a Windows Store?][Como eu posso obter dados para o Windows Phone ou a Windows Store?]
-   [Como eu posso ver os eventos e exibições de página que registrei no meu código?][Como eu posso ver os eventos e exibições de página que registrei no meu código?]
-   [Por que há duas versões do Application Insights?][Por que há duas versões do Application Insights?]
-   [O que está faltando atualmente na versão do Application Insights no Azure?][O que está faltando atualmente na versão do Application Insights no Azure?]
-   [Como posso obter de volta todos os recursos fantásticos que eu tinha na versão do Application Insights no Visual Studio Online?][Como posso obter de volta todos os recursos fantásticos que eu tinha na versão do Application Insights no Visual Studio Online?]
-   [O que o Application Insights modifica no meu projeto?][O que o Application Insights modifica no meu projeto?]
-   [Como eu encontro meus resultados no Application Insights?][Como eu encontro meus resultados no Application Insights?]
-   [Próximas etapas][Próximas etapas]

## <a name="q01"></a>Eu não vejo nenhuma opção de Adicionar o Application Insights ao meu projeto no Visual Studio

-   Certifique-se de que você tem o [Visual Studio Atualização 3][Visual Studio Atualização 3]. As Ferramentas do Application Insights vêm pré-instaladas e você deve conseguir vê-las no Gerenciador de extensões.
-   No momento, o Application Insights na visualização do Microsoft Azure está disponível somente para projetos da Web ASP.NET em C# ou Visual Basic.
-   Se você tiver um projeto existente, vá até o Gerenciador de Soluções e certifique-se de clicar no projeto Web (e não em outro projeto ou na solução). Você deve ver um item de menu “Adicionar o Application Insights ao projeto”.
-   Se estiver criando um novo projeto, no Visual Studio, abra Arquivo \> Novo Projeto e selecione {Visual C#|Visual Basic} \> Web \> Aplicativo Web ASP.NET. Deve haver uma opção de Adicionar o Application Insights ao projeto.

## <a name="q02"></a>Meu novo projeto Web foi criado, mas não foi possível adicionar o Application Insights.

Isso pode acontecer caso a comunicação com o portal do Application Insights falhe, ou se houver algum problema com a sua conta.

-   Confirme que você forneceu as credenciais de logon da conta certa do Azure. As credenciais do Microsoft Azure, que você vê na caixa de diálogo Novo projeto, podem ser diferentes das credenciais do Visual Studio Online que você vê no canto superior direito do Visual Studio.
-   Espere um pouco e [adicione o Application Insights ao seu projeto existente][adicione o Application Insights ao seu projeto existente].
-   Vá até as configurações da sua conta do Microsoft Azure e veja se há restrições. Veja se você pode adicionar manualmente um aplicativo do Application Insights.

## <a name="q03"></a>Eu adicionei o Application Insights com êxito e executei meu aplicativo, mas não vi nenhum dado no portal.

-   Você precisa fechar e abrir qualquer lâmina na qual estiver esperando dados. Na versão atual, o conteúdo de uma lâmina não é atualizado automaticamente.
-   No painel inicial do Microsoft Azure, veja o mapa de status de serviço. Se houver indicações de alerta, espere até que elas tenham voltado a OK e feche e abra novamente a lâmina do Application Insights de seu aplicativo.
-   No firewall de seu servidor Web, talvez você precise abrir a porta 443 for para tráfego de saída.

## <a name="q04"></a>Eu não vejo nenhum dado na Análise de uso

-   Os dados vêm de scripts nas páginas da Web. Se tiver adicionado o Application Insights a um projeto Web existente, [você terá que adicionar os scripts manualmente][adicione o Application Insights ao seu projeto existente].

## <a name="q05"></a>Estou olhando para o painel inicial da visualização do Microsoft Azure. Como eu encontro meus dados no Application Insights?

Você pode:

-   Selecionar Navegar, Application Insights, o nome do seu projeto. Se não tiver nenhum projeto, você precisa [adicionar o Application Insights ao seu projeto Web no Visual Studio][adicione o Application Insights ao seu projeto existente].

-   No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto Web e selecione Abrir o portal do Application Insights.

## <a name="q06"></a>Na tela inicial da visualização do Microsoft Azure, aquele mapa mostra o status do meu aplicativo?

Não! Ele mostra o status de serviço do Azure. Para ver os resultados de seu teste na Web, selecione Navegar \> Application Insights \> (seu aplicativo) e depois veja os resultados do teste na Web.

## <a name="q07"></a>Quando eu adiciono o Application Insights ao meu aplicativo e abro o portal do Application Insights, tudo parece completamente diferente das suas capturas de tela.

Você pode estar usando a versão mais antiga das Ferramentas do Application Insights, que tem conexão com a versão do Visual Studio Online.

As páginas de ajuda que você está consultando se referem ao Application Insights para a visualização do Microsoft Azure, que já vem ativado no Visual Studio Atualização 3.

## <a name="q08"></a>Eu posso usar o Application Insights para monitorar um servidor Web de intranet?

Sim, você pode monitorar a integridade e o uso se o seu servidor puder enviar dados à Internet pública.

Se você quiser executar testes na Web para seu serviço, ele precisará ser acessível pela Internet pública.

## <a name="q10"></a>Como eu posso ver os eventos e exibições de página que registrei no meu código?

Ainda não temos suporte para isso na versão do Microsoft Azure. Em breve. No momento, você pode experimentar a [versão anterior][versão anterior].

## <a name="q11"></a>Por que há duas versões do Application Insights?

O portal mais antigo faz parte do Visual Studio Online. Nós não faremos mais alterações significativas nessa versão. Se você tiver uma versão mais antiga das ferramentas do Application Insights para Visual Studio, elas têm conexão com o portal do Visual Studio Online.

O Visual Studio Atualização 3 vem com uma versão pré-instalada das novas ferramentas do Application Insights. Elas têm conexão com o novo portal do Application Insights, que é um componente da visualização do Microsoft Azure. Atualmente, estamos passando o Application Insights para esse novo ambiente. O trabalho ainda não está completo e há uma série de restrições.

## <a name="q12"></a>O que está faltando atualmente na versão do Application Insights no Azure?

Coisas incríveis estão a caminho.

Mas no momento, os principais recursos que estão faltando são: suporte para aplicativos para dispositivos como Windows Phone e Windows Store e relatórios de telemetria personalizada como `trackEvent()` e `trackPageView()`.

## <a name="q13"></a>Como posso obter de volta todos os recursos que eu tinha na versão do Application Insights no Visual Studio Online?

1.  Acesse o Gerenciador de extensões do Visual Studio.
2.  Desinstale as Ferramentas do Application Insights.
3.  Execute o [instalador da versão antiga das ferramentas][instalador da versão antiga das ferramentas] e leia seu [guia de introdução][versão anterior].

## <a name="q14"></a>O que o Application Insights modifica no meu projeto?

-   Adiciona estes arquivos ao seu projeto:

	-   ApplicationInsights.config.
	-   ai.js

-   Instala estes pacotes NuGet:

	-   *API do Application Insights* - a API principal

	-   *API do Application Insights para Aplicativos Web* - usada para enviar telemetria do servidor

	-   *API do Application Insights para Aplicativos JavaScript* - usada para enviar telemetria do cliente

	    Os pacotes incluem os seguintes assemblies:

	-   Microsoft.ApplicationInsights

	-   Microsoft.ApplicationInsights.Platform

-   (Somente novos projetos - se [adicionar o Application Insights a um projeto existente][adicione o Application Insights ao seu projeto existente], você precisará fazer isso manualmente.) Insere trechos no código do cliente e do servidor para inicializá-los com a ID de recurso do Application Insights. Por exemplo, em um aplicativo MVC, o código é inserido em:

	-   Views/Shared/\_Layout.cshtml da página mestra

	-   Web.config

	-   packages.config

## <a name="q15"></a>Como eu encontro meus resultados no Application Insights?

1.  Abra o Microsoft Azure:

	-   No Visual Studio, clique com o botão direito do mouse no projeto do aplicativo Web e selecione **Abrir o portal de visualização do Azure**.
	-   Ou em um navegador da Web, você pode abrir sua conta na visualização do Microsoft Azure.

1.  Selecione Navegar, Application Insights e selecione seu projeto.

## <a name="next"></a>Saiba mais

-   [Application Insights][Application Insights]
-   [Adicionar o Application Insights ao seu projeto][adicione o Application Insights ao seu projeto existente]
-   [Monitorar um servidor Web online agora][Monitorar um servidor Web online agora]
-   [Explorar métricas no Application Insights][Explorar métricas no Application Insights]
-   [Pesquisa de log de diagnóstico][Pesquisa de log de diagnóstico]
-   [Acompanhamento de disponibilidade com testes na Web][Acompanhamento de disponibilidade com testes na Web]
-   [Acompanhamento de uso com eventos e métricas][Acompanhamento de uso com eventos e métricas]
-   [Perguntas e respostas e solução de problemas][Perguntas e respostas e solução de problemas]

<!--Link references-->

  [Eu não vejo nenhuma opção de Adicionar o Application Insights ao meu projeto no Visual Studio]: #q01
  [Meu novo projeto Web foi criado, mas não foi possível adicionar o Application Insights.]: #q02
  [Eu adicionei o Application Insights com êxito e executei meu aplicativo, mas não vi nenhum dado no portal.]: #q03
  [Eu não vejo nenhum dado na Análise de uso]: #q04
  [Estou olhando para o painel inicial da visualização do Microsoft Azure. Como eu encontro meus dados no Application Insights?]: #q05
  [Na tela inicial da visualização do Microsoft Azure, aquele mapa mostra o status do meu aplicativo?]: #q06
  [Quando eu adiciono o Application Insights ao meu aplicativo e abro o portal do Application Insights, tudo parece completamente diferente das suas capturas de tela.]: #q07
  [Eu posso usar o Application Insights para monitorar um servidor Web de intranet?]: #q08
  [Como eu posso obter dados para o Windows Phone ou a Windows Store?]: #q09
  [Como eu posso ver os eventos e exibições de página que registrei no meu código?]: #q10
  [Por que há duas versões do Application Insights?]: #q11
  [O que está faltando atualmente na versão do Application Insights no Azure?]: #q12
  [Como posso obter de volta todos os recursos fantásticos que eu tinha na versão do Application Insights no Visual Studio Online?]: #q13
  [O que o Application Insights modifica no meu projeto?]: #q14
  [Como eu encontro meus resultados no Application Insights?]: #q15
  [Próximas etapas]: #next
  [Visual Studio Atualização 3]: http://go.microsoft.com/fwlink/?LinkId=397827
  [adicione o Application Insights ao seu projeto existente]: ../app-insights-monitor-application-health-usage/
  [versão anterior]: http://www.visualstudio.com/get-started/get-usage-data-vs
  [instalador da versão antiga das ferramentas]: http://visualstudiogallery.msdn.microsoft.com/82367b81-3f97-4de1-bbf1-eaf52ddc635a
  [Application Insights]: ../app-insights-get-started/
  [Monitorar um servidor Web online agora]: ../app-insights-monitor-performance-live-website-now/
  [Explorar métricas no Application Insights]: ../app-insights-explore-metrics/
  [Pesquisa de log de diagnóstico]: ../app-insights-search-diagnostic-logs/
  [Acompanhamento de disponibilidade com testes na Web]: ../app-insights-monitor-web-app-availability/
  [Acompanhamento de uso com eventos e métricas]: ../app-insights-track-usage-custom-events-metrics/
  [Perguntas e respostas e solução de problemas]: ../app-insights-troubleshoot-faq/
