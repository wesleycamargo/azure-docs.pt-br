<properties
	pageTitle="Introdução aos Serviços Móveis do Azure para aplicativos de HTML 5"
	description="Siga este tutorial para começar a usar o os Serviços Móveis do Azure para desenvolvimento de HTML."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html5"
	ms.devlang="javascript"
	ms.topic="article" 
	ms.date="07/25/2015"
	ms.author="glenga"/>


# <a name="getting-started"></a>Introdução aos Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

##Visão geral 

Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo HTML usando os Serviços Móveis do Azure. Neste tutorial, você criará um novo serviço móvel e um aplicativo simples *To do list* que armazena dados do aplicativo no novo serviço móvel. Você pode ver a seguinte versão em vídeo deste tutorial.

> [AZURE.VIDEO mobile-get-started-html]
 
Uma captura de tela do aplicativo completo está disponível abaixo:

![][0]

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais de Serviços Móveis para aplicativos HTML. Para um aplicativo PhoneGap/Cordova, consulte a [versão PhoneGap/Cordova](mobile-services-javascript-backend-phonegap-get-started.md) deste tutorial.

##Pré-requisitos

Os itens a seguir são necessários para concluir este tutorial:

+ Você precisa ter um dos seguintes servidores web em execução no computador local:

	+  **No Windows**: IIS Express. O IIS Express é instalado pelo [Microsoft Web Platform Installer].
	+  **No MacOS X**: Python, que já deve estar instalado.
	+  **No Linux**: Python. Você deve instalar a [versão mais recente do Python].

	Você pode usar qualquer servidor web para hospedar o aplicativo, mas estes são os servidores web com suporte dos scripts baixados.

+ Um navegador da Web que ofereça suporte a HTML5.
+ Uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F"%20target="_blank). 


## <a name="create-new-service"> </a>Criar um novo serviço móvel

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Criar um novo aplicativo HTML

Depois de criar seu serviço móvel, você poderá seguir um guia de início rápido fácil no Portal de Gerenciamento para criar um novo aplicativo ou modificar um aplicativo existente para conexão a seu serviço móvel.

Nesta seção, você criará um novo aplicativo HTML que está conectado ao seu serviço móvel.

1.  No Portal de Gerenciamento, clique em **Serviços Móveis**e, em seguida, clique no serviço móvel que você acabou de criar.


2. Na guia de início rápido, clique em **Windows** em **Escolher plataforma** e expanda **Criar um novo aplicativo HTML**.

   	![][6]

   	Isso exibirá as três etapas fáceis para a criação e a hospedagem de um aplicativo HTML conectado ao seu serviço móvel.

  	![][7]

3. Clique em **Criar tabela TodoItems** para criar uma tabela para armazenar dados de aplicativo.

4. Em **Baixe e execute o aplicativo**, clique em **Baixar**.

  	Isso baixará os arquivos do site para o aplicativo _To do list_ de amostra que está conectado ao seu serviço móvel. Salve o arquivo compactado em seu computador local e anote onde ele foi salvo.

5. Na guia **Configurar**, verifique se `localhost` já está relacionado na lista **Permitir solicitações de nomes de host** em **Compartilhamento de Recursos entre Origens (CORS)**. Se ele não estiver, digite `localhost` no campo **Nome do host** e clique em **Salvar**.

  	![][9]

	> [AZURE.IMPORTANT]Se você implantar o aplicativo quickstart em um servidor web que não seja o localhost, adicione o nome do host do servidor web à lista **Permitir solicitações de nomes de host**. Para obter mais informações, consulte [Compartilhamento de recursos entre origens](http://msdn.microsoft.com/library/windowsazure/dn155871.aspx"%20target="_blank).

## Hospedar e executar seu aplicativo HTML

O estágio final deste tutorial consiste em hospedar e executar seu novo aplicativo no computador local.

1. Navegue até o local onde salvou os arquivos de projeto compactados, expanda os arquivos em seu computador e inicie um dos arquivos de comando a seguir da subpasta **server**.

	+ ** launch-windows** (computadores com Windows)
	+ ** launch-mac.command** (computadores com Mac OS X)
	+ ** launch-linux.sh** (computadores com Linux)

	> [AZURE.NOTE]Em um computador Windows, digite `R` quando o PowerShell solicitar que você confirme se deseja executar o script. Seu navegador da web poderá avisá-lo para não executar o script por ele ter sido baixado da internet. Quando isso acontecer, você deverá solicitar que o navegador continue para carregar o script.

	Isso iniciará um servidor web no computador local para hospedar o novo aplicativo.

2. Abra a URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> em um navegador da Web para iniciar o aplicativo.

3. No aplicativo, digite um texto significativo, como _Concluir o tutorial_, em **Inserir nova tarefa** e clique em **Adicionar**.

   	![][10]

   	Isso envia uma solicitação de POST para o novo serviço móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os itens armazenados na tabela são retornados pelo serviço móvel, e os dados são exibidos na segunda coluna do aplicativo.

	> [AZURE.NOTE]Você poderá examinar o código que acessa o seu serviço móvel para consultar e inserir dados, que estão localizados no arquivo app.js.

4. No Portal de Gerenciamento, clique na guia **Dados** e, em seguida na tabela **TodoItems**.

   	![][11]

   	Isso permite que você procure os dados inseridos pelo aplicativo na tabela.

   	![][12]

## <a name="next-steps"> </a>Próximas etapas
Agora que você concluiu o início rápido, aprenda a executar tarefas adicionais importantes nos Serviços Móveis:

* **[Introdução aos dados]** <br/>Saiba mais sobre armazenar e consultar dados usando os Serviços Móveis.

* **[Chamar uma API personalizada de um aplicativo HTML]** <br/>Conecte seu aplicativo HTML à API personalizada hospedada nos Serviços Móveis.

* **[Introdução à autenticação]** <br/>Saiba como autenticar usuários de seu aplicativo com um provedor de identidade.

* **[Referência conceitual do tutorial do HTML/JavaScript de Serviços Móveis]** <br/>Saiba mais sobre como usar os Serviços Móveis com o HTML/JavaScript

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png

[6]: ./media/mobile-services-html-get-started/mobile-portal-quickstart-html.png
[7]: ./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png

[9]: ./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png
[10]: ./media/mobile-services-html-get-started/mobile-quickstart-startup-html.png
[11]: ./media/mobile-services-html-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-html-get-started/mobile-data-browse.png


<!-- URLs. -->
[Introdução aos dados]: mobile-services-html-get-started-data.md
[Introdução à autenticação]: mobile-services-html-get-started-users.md
[Chamar uma API personalizada de um aplicativo HTML]: mobile-services-html-call-custom-api.md

[Management Portal]: https://manage.windowsazure.com/
[Microsoft Web Platform Installer]: http://go.microsoft.com/fwlink/p/?LinkId=286333
[versão mais recente do Python]: http://go.microsoft.com/fwlink/p/?LinkId=286342
[Referência conceitual do tutorial do HTML/JavaScript de Serviços Móveis]: mobile-services-html-how-to-use-client-library.md
[Cross-origin resource sharing]: http://msdn.microsoft.com/library/azure/dn155871.aspx
 

<!---HONumber=August15_HO6-->