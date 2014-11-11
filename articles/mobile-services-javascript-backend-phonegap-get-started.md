<properties pageTitle="Get started with Mobile Services with PhoneGap | Mobile Dev Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for PhoneGap development for iOS, Android, and Windows Phone." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-phonegap" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

# <a name="getting-started"> </a>Introdução aos Serviços Móveis

Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo usando os Serviços Móveis do Azure. Neste tutorial, você criará um novo serviço móvel e um aplicativo simples *To do list* que armazena dados do aplicativo no novo serviço móvel.

Uma captura de tela do aplicativo completo está disponível abaixo:

![][0]

### <a name="additional-requirements"></a>Requisitos adicionais

A conclusão deste tutorial requer as ferramentas do PhoneGap (v3.2+ necessária para projetos do Windows Phone 8).

O PhoneGap oferece suporte ao desenvolvimento para várias plataformas. Além das próprias ferramentas do PhoneGap, você deve instalar as ferramentas para cada plataforma de destino:

-   Windows Phone: Instalar o [Visual Studio 2012 Express para Windows Phone][Visual Studio 2012 Express para Windows Phone]
-   iOS: Instalar o [Xcode][Xcode] (v4.4+ necessária)
-   Android: Instalar o [Android Developer Tools][Android Developer Tools]
    (O SDK dos Serviços Móveis para Android oferece suporte a aplicativos para o Android 2.2 ou versão posterior. O Android 4.2 ou versão posterior é necessário para executar o aplicativo de início rápido.)

## <a name="create-new-service"> </a>Criar um novo serviço móvel

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## <span class="short-header">Criar um novo aplicativo</span>Criar um novo aplicativo PhoneGap

Depois de criar seu serviço móvel, você poderá seguir um quickstart fácil no Portal de Gerenciamento para criar um novo aplicativo ou modificar um aplicativo existente para conexão a seu serviço móvel.

Nesta seção você criará um novo aplicativo PhoneGap que está conectado ao seu serviço móvel.

1.  No Portal de Gerenciamento, clique em **Serviços Móveis**e, em seguida, clique no serviço móvel que você acabou de criar.

2.  Na guia quickstart, clique em **PhoneGap** em **Escolher plataforma** e expanda **Criar um novo aplicativo PhoneGap**.

    ![][1]

    Isso exibirá as três etapas fáceis para a criação de um aplicativo PhoneGap conectado ao seu serviço móvel.

    ![][2]

3.  Se ainda não o fez, baixe e instale o PhoneGap e pelo menos uma das ferramentas de desenvolvimento da plataforma (Windows Phone, iOS e Android).

4.  Clique em **Criar tabela TodoItems** para criar uma tabela para armazenar dados de aplicativo.

5.  Em **Baixe e execute o aplicativo**, clique em **Baixar**.

    Isso baixa o projeto para o aplicativo de exemplo *To do list* que está conectado ao serviço móvel, juntamente com o SDK do JavaScript dos Serviços Móveis. Salve o arquivo de projeto compactado em seu computador local e anote onde ele foi salvo.

## Executar seu novo aplicativo PhoneGap

O estágio final deste tutorial é compilar e executar seu novo aplicativo.

1.  Navegue até o local onde você salvou os arquivos do projeto compactados e expanda os arquivos em seu computador.

2.  Abra e execute o projeto de acordo com as instruções abaixo para cada plataforma.

    -   **Windows Phone 8**

    1.  Windows Phone 8: Abra o arquivo .sln na pasta **platforms\\wp8** no Visual Studio 2012 Express para Windows Phone.

    2.  Pressione a tecla **F5** para recompilar o projeto e iniciar o aplicativo.

        ![][3]

    -   **iOS**

    1.  Abra o projeto na pasta **plataformas/ios** no Xcode.

    2.  Pressione o botão **Executar** para criar o projeto e iniciar o aplicativo no emulador do iPhone, que é o padrão para este projeto.

        ![][0]

    -   **Android**

        1.  No Eclipse, clique em **Arquivo**, em **Importar**, expanda **Android**, clique em **Código Android existente no espaço de trabalho** e clique em **Avançar.**

        2.  Clique em **Procurar**, navegue até o local dos arquivos de projeto expandidos, clique em **OK**, certifique-se de que o projeto TodoActivity está marcado e clique em **Concluir**.

            Isso importa os arquivos de projeto no espaço de trabalho atual.

            </p>
        3.  No menu **Executar**, clique em **Executar** para iniciar o projeto no emulador Android.

            ![][4]

        > [WACOM.NOTE]Para que seja possível executar o projeto no emulador do Android, você deve definir no mínimo um AVD (Dispositivo Virtual Android). Use o Gerenciador do AVD para criar e gerenciar esses dispositivos.

3.  Depois de iniciar o aplicativo em um dos emuladores de dispositivos móveis acima, digite texto na caixa de texto e, em seguida, clique em **Adicionar**.

    Isso envia uma solicitação de POST para o novo serviço móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela **TodoItem**. Os itens armazenados na tabela são retornados pelo serviço móvel, e os dados são exibidos na lista.

    <div class="dev-callout"><strong>Importante</strong> <p>As altera&ccedil;&otilde;es neste projeto de plataforma ser&atilde;o substitu&iacute;das se o projeto principal for recriado com as ferramentas do PhoneGap. Em vez disso, fa&ccedil;a altera&ccedil;&otilde;es no diret&oacute;rio www raiz do projeto, conforme descrito na se&ccedil;&atilde;o a seguir.</p></div>

4.  No Portal de Gerenciamento, clique na guia **Dados** e, em seguida na tabela **TodoItems**.

    ![][5]

    Isso permite que você procure os dados inseridos pelo aplicativo na tabela.

    ![][6]

## Fazer atualizações no aplicativo e recriar projetos para cada plataforma

1.  Faça as alterações nos arquivos de código no diretório ´www´ que, neste caso, é ´todolist/www´.

2.  Verifique se todas as ferramentas da plataforma de destino estão acessíveis no caminho do sistema.

3.  Abra um prompt de comando no diretório raiz do projeto e execute um dos seguintes comandos específicos à plataforma:

    -   **Windows Phone**

        Execute o seguinte comando no prompt de comando do Desenvolvedor do Visual Studio:

            phonegap local build wp8

    -   **iOS**

        Abra o terminal e execute o seguinte comando:

            phonegap local build ios

    -   **Android**

        Abra uma janela de prompt de comando ou de terminal e execute o seguinte comando:

            phonegap local build android

4.  Abra cada projeto no ambiente de desenvolvimento apropriado, conforme descrito na seção anterior.

> [WACOM.NOTE]Você pode examinar o código que acessa o seu serviço móvel para consultar e inserir dados, que estão localizados no arquivo js/index.js.

## <a name="next-steps"> </a>Próximas etapas

Agora que você concluiu o início rápido, aprenda a executar tarefas adicionais importantes nos Serviços Móveis:

-   [Começar a trabalhar com dados][Começar a trabalhar com dados]
    
	Saiba mais sobre armazenar e consultar dados usando os Serviços Móveis.

-   [Introdução à autenticação][Introdução à autenticação]
    
	 Saiba como autenticar usuários de seu aplicativo com um provedor de identidade.

<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [0]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-ios.png
  [Visual Studio 2012 Express para Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
  [Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Android Developer Tools]: https://go.microsoft.com/fwLink/p/?LinkID=280125
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot1.png
  [2]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot2.png
  [3]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-wp8.png
  [4]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-android.png
  [5]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-tab.png
  [6]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-browse.png
  [Começar a trabalhar com dados]: /pt-br/documentation/articles/mobile-services-html-get-started-data
  [Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-html-get-started-users
