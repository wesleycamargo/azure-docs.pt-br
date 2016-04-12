<properties
	pageTitle="Notas de versão da extensão do Visual Studio para análise do desenvolvedor"
	description="As atualizações mais recentes das ferramentas do Visual Studio para análise do desenvolvedor"
	services="application-insights"
    documentationCenter=""
	authors="aruna"
	manager="douge"/>
<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/28/2016"
	ms.author="acearun"/>

# Notas de versão - ferramentas de análise do desenvolvedor
##### Análise do Application Insights e do HockeyApp no Visual Studio
## Versão 5.2
Estamos felizes em anunciar a introdução de cenários do HockeyApp no Visual Studio. A primeira integração que habilitamos é uma distribuição beta dos aplicativos Universais do Windows e do Windows Forms no VS.

A distribuição beta permite carregar versões anteriores dos aplicativos no HockeyApp para distribuição a um subconjunto escolhido de clientes ou testadores. Essa distribuição, combinada com os recursos de comentários dos usuário e coleção de panes, pode fornecer informações valiosas sobre o aplicativo antes de um amplo lançamento. Você pode usar essas informações para solucionar problemas com seu aplicativo antes que eles se tornem maiores (classificações baixas, comentários ruins etc.).

Confira como é simples carregar compilações na distribuição beta no VS...
### Aplicativos universais Windows
O menu de contexto do nó de um projeto UWP agora inclui uma opção para carregar sua compilação no HockeyApp.

![Menu de contexto do projeto para aplicativos universais](./media/app-insights-release-notes-vsix/UniversalContextMenu.png)

Escolha o item e veja a caixa de diálogo de carregamento do HockeyApp. Você precisará de uma conta do HockeyApp para carregar sua compilação. Não se preocupe se você for um usuário novo - criar uma conta é um processo simples.

Ao se conectar, você verá o formulário de carregamento no diálogo.

![Diálogo Carregar para aplicativos universais](./media/app-insights-release-notes-vsix/UniversalUploadDialog.png)

Escolha o conteúdo a ser carregado (appxbundle ou appx) e escolha as opções de versão no assistente. Se preferir, você poderá adicionar notas de versão na página seguinte. Escolha 'Concluir' para começar a carregar.

Quando o carregamento estiver concluído, você verá uma notificação do sistema para o HockeyApp com confirmação e um link para o aplicativo no portal do HockeyApp.

![Notificação de carregamento concluído](./media/app-insights-release-notes-vsix/UploadComplete.png)

É isso. Você acabou de carregar uma compilação para distribuição Beta com alguns cliques.

O portal do HockeyApp permite gerenciar o aplicativo de várias maneiras (convidar usuários, exibir comentários e relatórios de pane, alterar detalhes etc.).

![Portal do HockeyApp](./media/app-insights-release-notes-vsix/HockeyAppPortal.png)

Mais detalhes sobre o gerenciamento de aplicativos estão disponíveis na [base de dados de conhecimento do HockeyApp](http://support.hockeyapp.net/kb/app-management-2).

### Aplicativos do Windows Forms
O menu de contexto do nó de um projeto do Windows Forms inclui uma opção para carregar sua compilação no HockeyApp.

![Menu de contexto do projeto para aplicativos do Windows Forms](./media/app-insights-release-notes-vsix/WinFormContextMenu.png)

Isso torna a caixa de diálogo de carregamento do HockeyApp semelhante à dos aplicativos universais.

![Caixa de diálogo de carregamento para aplicativos do Windows Forms](./media/app-insights-release-notes-vsix/WinFormsUploadDialog.png)

Observe um campo extra nesse assistente — para especificar a versão do aplicativo. Para aplicativos universais, as informações são populadas no manifesto – o Win Forms, infelizmente, não tem um equivalente e, portanto, precisa ser especificado manualmente.

O restante do fluxo é semelhante aos aplicativos universais — escolha de compilação, opções de versão, adição de notas de versão, carregamento e gerenciamento no portal do HockeyApp.

É simples assim. Experimente e conte-nos o que você acha.
## Versão 4.3
### Pesquisar a telemetria nas sessões de depuração local
Com esta versão, introduzimos a capacidade de pesquisar a Application Insights Telemetry gerada na sessão de depuração do Visual Studio. Anteriormente, a pesquisa só era possível se seu aplicativo estivesse registrado no Application Insights. Com esta versão, seu aplicativo precisa apenas do SDK do Application Insights instalado para pesquisar a telemetria local.

#### Se você tiver um aplicativo do ASP.NET com o SDK do Application Insights

- Depure o aplicativo.
- Abra a Pesquisa do Application Insights usando uma das seguintes maneiras
	- Exibir Menu -> Outras Janelas -> Pesquisa do Application Insights
	- Clicar no botão de Barra de ferramentas do Application Insights
	- No Gerenciador de Soluções, expanda ApplicationInsights.config -> Pesquisar telemetria da sessão de depuração
- Se ainda não tiver se inscrito no Application Insights, a janela Pesquisar será aberta no modo “telemetria da sessão de depuração”.
- Clique no ícone de pesquisa para ver a telemetria local.

![Upload concluído](./media/app-insights-release-notes-vsix/LocalSearch.png)



##Versão 4.2
Nesta versão, adicionamos recursos para facilitar a pesquisa de dados no contexto de eventos, a capacidade de saltar para o código de mais eventos de dados e uma experiência simples para enviar dados de log para o Application Insights. Essa extensão é atualizada mensalmente. Se tiver comentários ou solicitações de recursos, envie-os para aidevtools@microsoft.com
###Experiência de registro em log sem cliques
Se já estiver usando rastreamento com NLog, Log4Net ou System.Diagnostics, você não precisará se preocupar sobre como mover todos os seus rastreamentos para o AI. Agora, estamos integrando os adaptadores de registro do Application Insights com a experiência de configuração normal. Se já tiver uma dessas estruturas de registros configuradas, você o da seguinte forma:
####Se já tiver o Application Insights adicionado
- Clique com o botão direito do mouse no Nó do projeto -> Application Insights -> Configurar Application Insights. Verifique se aparece a opção de adicionar o adaptador correto à janela de configuração.
- Ou quando você compilar a solução, observe o pop-up que aparece no canto superior direito da tela e clique em configurar. ![Notificação de Registro](./media/app-insights-release-notes-vsix/LoggingToast.png)

Depois de instalar o adaptador de log, você poderá executar o aplicativo e verificar se visualiza os dados na guia de ferramentas de diagnóstico, como se segue: ![Rastreamentos](./media/app-insights-release-notes-vsix/Traces.png)
###-Usuário pode saltar/localizar o código em que a propriedade de evento de telemetria é emitida
Com a nova versão, o usuário pode clicar em qualquer valor nos detalhes do evento e isso pesquisará uma cadeia de caracteres correspondente na solução aberta atual. Os resultados serão exibidos na lista "Encontrar Resultados" do Visual Studio, conforme mostrado abaixo: ![Localizar Correspondência](./media/app-insights-release-notes-vsix/FindMatch.png)
###-Nova tela para usuário não conectado na janela de Pesquisa
Melhoramos a aparência da nossa janela de Pesquisa para orientar os usuários a pesquisar seus dados em produção. ![Janela de Pesquisa](./media/app-insights-release-notes-vsix/SearchWindow.png)
###-Usuário pode ver todos os eventos de telemetria associados ao evento
Foi adicionada uma nova guia ao lado dos detalhes do evento, que contém um consultas predefinidas para exibir todos os dados relacionados ao evento de telemetria que o usuário está examinando. Por exemplo: a solicitação tem um campo chamado ID de operação e todos os eventos associados a essa solicitação terão a mesma ID de operação, portanto, se ocorrer uma exceção ao processar a solicitação, ele receberá a mesma ID de operação que a solicitação para tornar mais fácil encontrá-la e assim por diante. Portanto, o usuário que examina uma solicitação agora pode clicar em "Toda a telemetria para esta operação" e isso abrirá uma nova guia com os novos resultados da pesquisa. ![Itens Relacionados](./media/app-insights-release-notes-vsix/RelatedItems.png)
### -Acréscimo de histórico de avanço/retrocesso na Pesquisa
Agora, o usuário pode ir e voltar entre os resultados da pesquisa. ![Voltar](./media/app-insights-release-notes-vsix/GoBAck.png)

##Versão 4.1
Esta versão vem com uma quantidade de novos recursos e melhorias dos recursos existentes. Para obter essa versão, você precisa ter a Atualização 1 instalada em seu computador.

### Saltar de uma exceção para o método no código-fonte
Agora, os usuários que visualizam exceções de seus aplicativos de produção na janela de Pesquisa do Application Insights podem ir para o método em seu código onde a exceção está acontecendo. Basta carregar o projeto certo e nós cuidaremos do resto! (Para saber mais sobre a janela de Pesquisa, veja as notas de versão 4.0 abaixo)

#### Como ele funciona?

Quando uma solução não estiver aberta, a Pesquisa AI poderá ser usada sem abrir uma solução. Nesse caso, a área de rastreamento de pilha mostrará uma mensagem de informação, e muitos dos itens no rastreamento da pilha aparecerão esmaecidos.


Se as informações de arquivo estiverem disponíveis, alguns itens poderão ser links, mas o item de informação de solução ainda estará visível.

Clicar no hiperlink levará você para onde o método selecionado estiver em seu código. Pode haver uma diferença no número de versão, mas esse recurso virá em versões posteriores: ir para a versão correta do código.

![Clicar em exceção](./media/app-insights-release-notes-vsix/jumptocode.png)

###Novos pontos de entrada para a Experiência de Pesquisa no Gerenciador de Soluções

![Ponto de entrada no Gerenciador de Soluções](./media/app-insights-release-notes-vsix/searchentry.png)


###Pop-up de uma notificação do sistema quando a publicação é concluída
Um pop-up será exibido depois que o projeto for publicado online; portanto, você pode exibir os dados do Application Insights em produção.

![Pop-up](./media/app-insights-release-notes-vsix/publishtoast.png)

## Versão 4.0

###Pesquisar dados do Application Insights no Visual Studio
Assim como a Pesquisa no portal do Application Insights, você pode filtrar e pesquisar tipos de eventos, valores de propriedade e texto e inspecionar eventos individuais.

![Janela de pesquisa](./media/app-insights-release-notes-vsix/search.png)

###Confira dados provenientes de sua caixa local na janela Ferramentas de Diagnóstico

A telemetria também será exibida junto com outros dados de depuração no Hub de diagnóstico do Visual Studio. Isso é só dá suporte ao ASP.NET 4.5. O suporte para ASP. NET 5 estará disponível em versões futuras.

![Janela de hub de diagnóstico](./media/app-insights-release-notes-vsix/diagtools.png)

###Adicione o SDK ao seu projeto sem a necessidade de entrar no Azure

Você não precisa mais entrar no Azure para adicionar pacotes do Application Insights ao seu projeto, seja na caixa de diálogo Novo projeto ou no menu de contexto do projeto. Se você entrar, o SDK será instalado e configurado para enviar telemetria ao portal, como antes. Se você não entrar, o SDK será adicionado ao seu projeto e gerará telemetria para o hub de diagnóstico, e você poderá configurá-lo mais tarde, se desejar.

![Caixa de diálogo Novo Projeto](./media/app-insights-release-notes-vsix/newproject.png)

###Suporte a dispositivos

Em *Connect();* 2015, [anunciamos](https://azure.microsoft.com/blog/deep-diagnostics-for-web-apps-with-application-insights/) que a experiência de nossos DevOps Móveis para dispositivos é o HockeyApp. O HockeyApp ajuda a distribuir suas versões beta para testadores, a coletar e a analisar todas as falhas do aplicativo e a coletar comentários diretamente de seus clientes. O HockeyApp dá suporte a você em qualquer plataforma em que esteja criando o aplicativo móvel, seja iOS, Android ou Windows, ou uma solução de plataforma cruzada, como Unity, Cordova ou Xamarin.

Em versões futuras da extensão Application Insights, vamos introduzir novas funcionalidades para permitir uma experiência mais integrada entre o HockeyApp e o Visual Studio. A partir de agora, é possível começar a usar o HockeyApp apenas adicionando a referência do NuGet: veja a [documentação](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone) para obter mais informações.

<!-----------HONumber=AcomDC_0330_2016-->