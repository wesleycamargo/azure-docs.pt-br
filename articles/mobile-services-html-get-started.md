<properties pageTitle="Introdução aos Serviços Móveis do Azure para aplicativos de HTML 5" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for HTML development. " metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="hero-article" ms.date="11/21/2014" ms.author="glenga" />


# <a name="getting-started"> </a>Introdução aos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo HTML usando os Serviços Móveis do Azure. Neste tutorial, você criará um novo serviço móvel e um aplicativo simples de <em>Lista de pendências</em> que armazena dados do aplicativo no novo serviço móvel. Você poderá ver uma versão em vídeo deste tutorial clicando no clipe à direita.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=287040" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-html-get-started-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=287040" target="_blank" class="dev-onpage-video"><span class="icon">Executar o vídeo</span></a> <span class="time">3:51</span></div>
</div>
 
Uma captura de tela do aplicativo completo está disponível abaixo:

![][0]

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais de Serviços Móveis para aplicativos HTML. 

<div class="dev-callout"><strong>Observação</strong> <p>Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais informações, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F" target="_blank">Avaliação gratuita do Azure</a>.</p></div>

###Requisitos adicionais

+ Este tutorial requer que você tenha um dos seguintes servidores web em execução no computador local:

	+  **No Windows**: IIS Express.O IIS Express é instalado pelo [Microsoft Web Platform Installer].   
	+  **No MacOS X**:Python, que já deve estar instalado.
	+  **No Linux**: Python.Você deve instalar a [versão mais recente do Python]. 
	
	Você pode usar qualquer servidor Web para hospedar o aplicativo, mas estes são os servidores Web com suporte dos scripts baixados.  

+ Um navegador da Web que ofereça suporte a HTML5.


## <a name="create-new-service"> </a>Criar um novo serviço móvel

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## <h2>Criar um novo aplicativo HTML</h2>

Depois de criar seu serviço móvel, você poderá seguir um quickstart fácil no Portal de Gerenciamento para criar um novo aplicativo ou modificar um aplicativo existente para conexão a seu serviço móvel. 

Nesta seção, você criará um novo aplicativo HTML que está conectado ao seu serviço móvel.

1.  No Portal de Gerenciamento, clique em **Serviços Móveis** e clique no serviço móvel que você acabou de criar.

   
2. Na guia de início rápido, clique em **Windows** em **Escolher plataforma** e expanda **Criar um novo aplicativo HTML**.

   	![][6]

   	Isso exibirá as três etapas fáceis para a criação e a hospedagem de um aplicativo HTML conectado ao seu serviço móvel.

  	![][7]

3. Clique em **Criar tabela TodoItems** para criar uma tabela para armazenar dados do aplicativo.

4. Em **Baixe e execute seu aplicativo**, clique em **Baixar**. 

  	Isso baixará os arquivos do site para o aplicativo _To do list_ de amostra que está conectado ao seu serviço móvel. Salve o arquivo compactado em seu computador local e anote onde ele foi salvo.

5. Na guia **Configurar**, verifique se 'localhost' já está relacionado na lista **Permitir solicitações de nomes de host** em **Compartilhamento de Recursos entre Origens (CORS)**. Se ele não estiver, digite 'localhost' no campo **Nome do host** e clique em **Salvar**.

  	![][9]

	<div class="dev-callout"><b>Observação</b>
		<p>Se você implantar o aplicativo quickstart para um servidor Web que não seja localhost, deverá adicionar o nome do host do servidor Web à lista <strong>Permitir solicitações de nomes de host</strong> . Para obter mais informações, consulte <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/dn155871.aspx" target="_blank">Compartilhamento de recursos entre origens</a>.</p>
	</div>

## Hospedar e executar seu aplicativo HTML

O estágio final deste tutorial consiste em hospedar e executar seu novo aplicativo no computador local.

1. Navegue até o local onde salvou os arquivos de projeto compactados, expanda os arquivos em seu computador e inicie um dos arquivos de comando a seguir da subpasta **server**.

	+ **launch-windows** (computadores Windows) 
	+ **launch-mac.command** (computadores Mac OS X)
	+ **launch-linux.sh** (computadores Linux)

	<div class="dev-callout"><b>Observação</b>
		<p>Em um computador Windows, digite 'R' quando o PowerShell solicitar que você confirme se deseja executar o script. Seu navegador da Web poderá avisá-lo para não executar o script por ele ter sido baixado da Internet. Quando isso acontecer, você deverá solicitar que o navegador continue para carregar o script.</p>
	</div>

	Isso iniciará um servidor Web no computador local para hospedar o novo aplicativo.

2. Abrir a URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> em um navegador da Web para iniciar o aplicativo.

3. No aplicativo, digite um texto significativo, como _Concluir o tutorial_, em **Inserir nova tarefa** e clique em **Adicionar**.

   	![][10]

   	Isso envia uma solicitação de POST para o novo serviço móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os itens armazenados na tabela são retornados pelo serviço móvel, e os dados são exibidos na segunda coluna do aplicativo.

	<div class="dev-callout"> 
	<b>Observação</b> 
   	<p>Você poderá examinar o código que acessa o seu serviço móvel para consultar e inserir dados, que estão localizados no arquivo app.js.</p> 
 	</div>

4. No Portal de Gerenciamento, clique na guia **Dados** e na tabela **TodoItems**.

   	![][11]

   	Isso permite que você procure os dados inseridos pelo aplicativo na tabela.

   	![][12]

## <a name="next-steps"> </a>Próximas etapas
Agora que você concluiu o início rápido, aprenda a executar tarefas adicionais importantes nos Serviços Móveis: 

* **[Introdução aos dados]**
  <br/>Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.
  
* **[Chamar uma API personalizada por meio de um aplicativo HTML]**
  <br/>Conecte seu aplicativo HTML com uma API personalizada hospedada em Serviços Móveis.

* **[Introdução à autenticação]**
  <br/>Aprenda a autenticar usuários do aplicativo com um provedor de identidade.

* **[Referência conceitual do tutorial do HTML/JavaScript de Serviços Móveis]**
  <br/>Saiba mais sobre como usar os Serviços Móveis com HTML/JavaScript 

<!-- Anchors. -->
[Introdução aos Serviços Móveis]:#getting-started
[Criar um novo serviço móvel]:#create-new-service
[Definir a instância do serviço móvel]:#define-mobile-service-instance
[Próximas etapas]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png





[6]: ./media/mobile-services-html-get-started/mobile-portal-quickstart-html.png
[7]: ./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png

[9]: ./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png
[10]: ./media/mobile-services-html-get-started/mobile-quickstart-startup-html.png
[11]: ./media/mobile-services-html-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-html-get-started/mobile-data-browse.png


<!-- URLs. -->
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-html
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-html
[Chamar uma API personalizada por meio de um aplicativo HTML]: /pt-br/documentation/articles/mobile-services-html-call-custom-api 

[Portal de Gerenciamento]: https://manage.windowsazure.com/
[Microsoft Web Platform Installer]:  http://go.microsoft.com/fwlink/p/?LinkId=286333
[versão mais recente do Python]: http://go.microsoft.com/fwlink/p/?LinkId=286342
[Referência conceitual do tutorial do HTML/JavaScript de Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-html-js-client
[Compartilhamento de recursos entre origens]: http://msdn.microsoft.com/pt-br/library/windowsazure/dn155871.aspx

<!--HONumber=35_1-->
