<properties 
	pageTitle="Notas de versão da extensão Visual Studio para o Application Insights" 
	description="As atualizações mais recentes das ferramentas do Visual Studio para o Application Insights." 
	services="application-insights" 
    documentationCenter=""
	authors="dimazaid" 
	manager="douge"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/19/2016" 
	ms.author="dimazaid"/>
 
# Notas de versão das Ferramentas do Application Insights para Visual Studio v 4.1

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

Em *Connect ();* 2015, [anunciamos](https://azure.microsoft.com/blog/deep-diagnostics-for-web-apps-with-application-insights/) que a experiência de nossos DevOps móveis para dispositivos é o HockeyApp. O HockeyApp ajuda a distribuir suas versões beta para testadores, a coletar e a analisar todas as falhas do aplicativo e a coletar comentários diretamente de seus clientes. O HockeyApp dá suporte a você em qualquer plataforma em que esteja criando o aplicativo móvel, seja iOS, Android ou Windows, ou uma solução de plataforma cruzada, como Unity, Cordova ou Xamarin.

Em versões futuras da extensão Application Insights, vamos introduzir novas funcionalidades para permitir uma experiência mais integrada entre o HockeyApp e o Visual Studio. Agora você pode começar com o HockeyApp simplesmente adicionando a referência do NuGet: confira a [documentação](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone) para saber mais.

 

<!---HONumber=AcomDC_0121_2016-->