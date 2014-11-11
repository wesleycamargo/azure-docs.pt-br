<properties linkid="develop-mobile-tutorials-get-started-with-data-ios" urlDisplayName="Get Started with Data" pageTitle="Get started with data (iOS) | Mobile Dev Center" metaKeywords="Azure iOS data, Azure mobile services data, " description="Learn how to get started using Mobile Services to leverage data in your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="09/19/2014" ms.author="glenga" />

# Introdução aos dados nos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Este tópico mostra como usar os Serviços Móveis do Azure para utilizar dados em um aplicativo iOS. Neste tutorial, você baixará um aplicativo que armazena dados na memória, criará um novo serviço móvel, integrará o serviço móvel ao aplicativo e fará logon no Portal de Gerenciamento do Azure para exibir as alterações nos dados feitas durante a execução do aplicativo.

<div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>O objetivo deste tutorial &eacute; ajudar voc&ecirc; a compreender melhor como os Servi&ccedil;os M&oacute;veis permitem usar o Azure para armazenar e recuperar dados de um aplicativo iOS. Desse modo, este t&oacute;pico explica muitas das etapas que s&atilde;o conclu&iacute;das para voc&ecirc; no Guia de in&iacute;cio r&aacute;pido dos Servi&ccedil;os M&oacute;veis. Se esta for sua primeira experi&ecirc;ncia com os Servi&ccedil;os M&oacute;veis, primeiro conclua o tutorial <a href="/pt-br/develop/mobile/tutorials/get-started-ios">Introdu&ccedil;&atilde;o aos Servi&ccedil;os M&oacute;veis</a>.</p>
</div>

Este tutorial apresenta e explica as seguintes etapas básicas:

1.  [Baixar o projeto do aplicativo iOS][Baixar o projeto do aplicativo iOS]
2.  [Criar o serviço móvel][Criar o serviço móvel]
3.  [Adicionar uma tabela de dados para armazenamento][Adicionar uma tabela de dados para armazenamento]
4.  [Atualizar o aplicativo para usar Serviços Móveis][Atualizar o aplicativo para usar Serviços Móveis]
5.  [Testar o aplicativo com os Serviços Móveis][Testar o aplicativo com os Serviços Móveis]

Este tutorial exige o seguinte:

-   [SDK do iOS para Serviços Móveis][SDK do iOS para Serviços Móveis] e [XCode 4.5][XCode 4.5] e iOS 5.0 ou versões posteriores.
-   Uma conta do Microsoft Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][Avaliação gratuita do Azure].
    </p>
    </div>

## <a name="download-app"></a>Baixar o projeto GetStartedWithData

Este tutorial é baseado no [aplicativo GetStartedWithData][aplicativo GetStartedWithData], que é um aplicativo iOS. A interface do usuário desse aplicativo é idêntica a do aplicativo gerado pelo início rápido do iOS dos Serviços Móveis, com a exceção de que os itens são armazenados localmente na memória.

1.  Baixe o [aplicativo de exemplo][aplicativo GetStartedWithData] GetStartedWithData.

2.  No Xcode, abra o projeto baixado e examine o arquivo TodoService.m.

    Observe que há oito comentários **// TODO** que especificam as etapas que você deve seguir para fazer com que esse aplicativo funcione com o serviço móvel.

3.  Pressione o botão **Executar** (ou a tecla Command+R) para recompilar o projeto e iniciar o aplicativo.

4.  No aplicativo, digite algum texto na caixa de texto e clique no botão **+**.

    ![][0]

    Observe que o texto salvo é exibido na lista abaixo.

## <a name="create-service"></a>Criar um novo serviço móvel no Portal de Gerenciamento

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a>Adicionar uma nova tabela ao serviço móvel

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

## <a name="update-app"></a>Atualizar o aplicativo para usar o serviço móvel para acesso a dados

[WACOM.INCLUDE [mobile-services-ios-enable-mobile-service-access](../includes/mobile-services-ios-enable-mobile-service-access.md)]

## <a name="test-app"></a>Testar o aplicativo com seu novo serviço móvel

1.  Em Xcode, selecione um emulador no qual implantar (iPhone ou iPad), pressione o botão **Executar** (ou a tecla Command+R) para recompilar o projeto e iniciar o aplicativo.

    Isso executa seu cliente de Serviços Móveis do Azure, criado com o SDK do iOS, que consulta itens em seu serviço móvel.

2.  Como antes, digite texto na caixa de texto e clique no botão **+**.

    Isso envia um novo item como uma inserção no serviço móvel.

3.  No [Portal de Gerenciamento][Portal de Gerenciamento], clique em **Serviços Móveis** e, em seguida, clique em seu serviço móvel.

4.  Clique na guia **Dados** e, em seguida, clique em **Procurar**.

    ![][1]

    Observe que a tabela **TodoItem** agora contém dados com valores de id gerados pelos Serviços Móveis, e que as colunas foram automaticamente adicionadas à tabela para coincidir com a classe TodoItem no aplicativo.

Isso conclui o tutorial **Introdução aos dados** para iOS.

## <a name="next-steps"></a>Próximas etapas

Este tutorial demonstrou os conceitos básicos de como habilitar um aplicativo iOS para trabalhar com dados nos Serviços Móveis.

Em seguida, considere fazer um dos seguintes tutoriais com base no aplicativo GetStartedWithData que você criou neste tutorial:

-   [Validar e modificar dados com scripts][Validar e modificar dados com scripts]
    
Saiba mais sobre como usar scripts de servidor nos Serviços Móveis para validar e alterar os dados enviados do seu aplicativo.

-   [Refinar consultas com paginação][Refinar consultas com paginação]
    
	Saiba como usar a paginação em consultas para controlar a quantidade de dados processada em uma única solicitação.

	Depois de ter concluído a série de dados, tente estes outros tutoriais do iOS:

-   [Introdução à autenticação][Introdução à autenticação]
    
	Saiba como autenticar usuários de seu aplicativo.

-   [Introdução às notificações por push][Introdução às notificações por push]
    
	Saiba como enviar uma notificação por push bastante básica a seu aplicativo com os Serviços Móveis.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started-data]: ../includes/mobile-services-selector-get-started-data.md
  [Baixar o projeto do aplicativo iOS]: #download-app
  [Criar o serviço móvel]: #create-service
  [Adicionar uma tabela de dados para armazenamento]: #add-table
  [Atualizar o aplicativo para usar Serviços Móveis]: #update-app
  [Testar o aplicativo com os Serviços Móveis]: #test-app
  [SDK do iOS para Serviços Móveis]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F
  [aplicativo GetStartedWithData]: http://go.microsoft.com/fwlink/p/?LinkId=268622
  [0]: ./media/mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [mobile-services-create-new-service-data-2]: ../includes/mobile-services-create-new-service-data-2.md
  [mobile-services-ios-enable-mobile-service-access]: ../includes/mobile-services-ios-enable-mobile-service-access.md
  [Portal de Gerenciamento]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png
  [Validar e modificar dados com scripts]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [Refinar consultas com paginação]: /pt-br/develop/mobile/tutorials/add-paging-to-data-ios
  [Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-ios
  [Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-ios
