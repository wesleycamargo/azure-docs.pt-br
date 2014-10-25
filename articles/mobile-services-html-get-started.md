<properties pageTitle="Get Started with Azure Mobile Services for HTML 5 apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for HTML development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="hero-article" ms.date="01/01/1900" ms.author="glenga"></tags>

# <a name="getting-started"> </a>Introdução aos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started][mobile-services-selector-get-started]]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Este tutorial mostra como adicionar um servi&ccedil;o de back-end baseado em nuvem a um aplicativo HTML usando os Servi&ccedil;os M&oacute;veis do Azure. Neste tutorial, voc&ecirc; criar&aacute; um novo servi&ccedil;o m&oacute;vel e um aplicativo simples <em>To do list</em> que armazena dados do aplicativo no novo servi&ccedil;o m&oacute;vel. Voc&ecirc; poder&aacute; ver uma vers&atilde;o em v&iacute;deo deste tutorial clicando no clipe &agrave; direita.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=287040" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-html-get-started-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=287040" target="_blank" class="dev-onpage-video"><span class="icon">Reproduzir o v&iacute;deo</span></a> <span class="time">03:51:00</span></div>

</div>

Uma captura de tela do aplicativo completo está disponível abaixo:

![][]

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais de Serviços Móveis para aplicativos HTML.

<div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> <p>Para concluir este tutorial, voc&ecirc; precisa de uma conta do Azure. Se voc&ecirc; n&atilde;o tiver uma conta, poder&aacute; criar uma conta de avalia&ccedil;&atilde;o gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F" target="_blank">Avalia&ccedil;&atilde;o gratuita do Azure</a>.</p></div>

### Requisitos adicionais

-   Este tutorial requer que você tenha um dos seguintes servidores web em execução no computador local:

    -   **No Windows**: IIS Express. O IIS Express é instalado pelo [Microsoft Web Platform Installer][Microsoft Web Platform Installer].
    -   **No MacOS X**: Python, que já deve estar instalado.
    -   **No Linux**: Python. Você deve instalar a [versão mais recente do Python][versão mais recente do Python].

    Você pode usar qualquer servidor web para hospedar o aplicativo, mas estes são os servidores web com suporte dos scripts baixados.

-   Um navegador da web que ofereça suporte a HTML5.

## <a name="create-new-service"> </a>Criar um novo serviço móvel

[WACOM.INCLUDE [mobile-services-create-new-service][mobile-services-create-new-service]]

## 

## <span class="short-header">Criar um novo aplicativo</span>Criar um novo aplicativo HTML

</h2>
Depois de criar seu serviço móvel, você poderá seguir um quickstart fácil no Portal de Gerenciamento para criar um novo aplicativo ou modificar um aplicativo existente para conexão a seu serviço móvel.

Nesta seção, você criará um novo aplicativo HTML que está conectado ao seu serviço móvel.

1.  No Portal de Gerenciamento, clique em **Serviços Móveis**e, em seguida, clique no serviço móvel que você acabou de criar.

2.  Na guia de início rápido, clique em **Windows** em **Escolher plataforma** e expanda **Criar um novo aplicativo HTML**.

    ![][1]

    Isso exibirá as três etapas fáceis para a criação e a hospedagem de um aplicativo HTML conectado ao seu serviço móvel.

    ![][2]

3.  Clique em **Criar tabela TodoItems** para criar uma tabela para armazenar dados de aplicativo.

4.  Em **Baixe e execute o aplicativo**, clique em **Baixar**.

    Isso baixará os arquivos do site para o aplicativo *To do list* de amostra que está conectado ao seu serviço móvel. Salve o arquivo compactado em seu computador local e anote onde ele foi salvo.

5.  Na guia **Configurar**, verifique se `localhost` já está relacionado na lista **Permitir solicitações de nomes de host** em **Compartilhamento de Recursos entre Origens (CORS)**. Se ele não estiver, digite `localhost` no campo **Nome do host** e clique em **Salvar**.

    ![][3]

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
    <p>Se voc&ecirc; implantar o aplicativo quickstart em um servidor web que n&atilde;o seja o localhost, adicione o nome do host do servidor web &agrave; lista <strong>Permitir solicita&ccedil;&otilde;es de nomes de host</strong>. Para obter mais informa&ccedil;&otilde;es, consulte <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/dn155871.aspx" target="_blank">Compartilhamento de recursos entre origens</a>.</p>
</div>

## Hospedar e executar seu aplicativo HTML

O estágio final deste tutorial consiste em hospedar e executar seu novo aplicativo no computador local.

1.  Navegue até o local onde salvou os arquivos de projeto compactados, expanda os arquivos em seu computador e inicie um dos arquivos de comando a seguir da subpasta **server**.

    -   **launch-windows** (computadores com Windows)
    -   **launch-mac.command** (computadores com Mac OS X)
    -   **launch-linux.sh** (computadores com Linux)

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
    <p>Em um computador Windows, digite 'R' quando o PowerShell solicitar que voc&ecirc; confirme se deseja executar o script. Seu navegador da web poder&aacute; avis&aacute;-lo para n&atilde;o executar o script por ele ter sido baixado da internet. Quando isso acontecer, voc&ecirc; dever&aacute; solicitar que o navegador continue para carregar o script.</p>
</div>

    Isso iniciará um servidor web no computador local para hospedar o novo aplicativo.

2.  Abra a URL <http://localhost:8000/> em um navegador da web para iniciar o aplicativo.

3.  No aplicativo, digite um texto significativo, como *Concluir o tutorial*, em **Inserir nova tarefa** e clique em **Adicionar**.

    ![][4]

    Isso envia uma solicitação de POST para o novo serviço móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os itens armazenados na tabela são retornados pelo serviço móvel, e os dados são exibidos na segunda coluna do aplicativo.

    <div class="dev-callout"> 
<b>Observa&ccedil;&atilde;o</b> 
<p>Voc&ecirc; poder&aacute; examinar o c&oacute;digo que acessa o seu servi&ccedil;o m&oacute;vel para consultar e inserir dados, que est&atilde;o localizados no arquivo app.js.</p> 
</div>

4.  No Portal de Gerenciamento, clique na guia **Dados** e, em seguida na tabela **TodoItems**.

    ![][5]

    Isso permite que você procure os dados inseridos pelo aplicativo na tabela.

    ![][6]

## <a name="next-steps"> </a>Próximas etapas

Agora que você concluiu o início rápido, aprenda a executar tarefas adicionais importantes nos Serviços Móveis:

-   **[Começar a trabalhar com dados][Começar a trabalhar com dados]**
    
	Saiba mais sobre armazenar e consultar dados usando os Serviços Móveis.

-   **[Chamar uma API personalizada a partir de um aplicativo HTML][Chamar uma API personalizada a partir de um aplicativo HTML]**
    
	Conecte seu aplicativo HTML à API personalizada hospedada nos Serviços Móveis.

-   **[Introdução à autenticação][Introdução à autenticação]**
    
	Saiba como autenticar usuários de seu aplicativo com um provedor de identidade.

-   **[Referência conceitual do tutorial do HTML/JavaScript de Serviços Móveis][Referência conceitual do tutorial do HTML/JavaScript de Serviços Móveis]**
    
	Saiba mais sobre como usar os Serviços Móveis com o HTML/JavaScript.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [assista ao tutorial]: http://go.microsoft.com/fwlink/?LinkId=287040
  []: ./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F
  [Microsoft Web Platform Installer]: http://go.microsoft.com/fwlink/p/?LinkId=286333
  [versão mais recente do Python]: http://go.microsoft.com/fwlink/p/?LinkId=286342
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/mobile-services-html-get-started/mobile-portal-quickstart-html.png
  [2]: ./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png
  [3]: ./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png
  [Compartilhamento de recursos entre origens]: http://msdn.microsoft.com/pt-br/library/windowsazure/dn155871.aspx
  [4]: ./media/mobile-services-html-get-started/mobile-quickstart-startup-html.png
  [5]: ./media/mobile-services-html-get-started/mobile-data-tab.png
  [6]: ./media/mobile-services-html-get-started/mobile-data-browse.png
  [Começar a trabalhar com dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-html
  [Chamar uma API personalizada a partir de um aplicativo HTML]: /pt-br/documentation/articles/mobile-services-html-call-custom-api
  [Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-html
  [Referência conceitual do tutorial do HTML/JavaScript de Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-html-js-client
