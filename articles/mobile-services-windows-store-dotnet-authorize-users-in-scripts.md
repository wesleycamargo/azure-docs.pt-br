<properties pageTitle="Autorização do lado do servidor (Windows Store) | Mobile Dev Center" metaKeywords="" description="Saiba como autorizar usuários no back-end do JavaScript dos Serviços Móveis do Azure." metaCanonical="" services="" documentationCenter="Mobile" title="Autorização do lado do servidor de usuários dos Serviços Móveis" authors="glenga" solutions="" manager="" editor="" />

# Autorização do lado do servidor para usuários de Serviços Móveis

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts" title="Windows Store C#" class="current">Windows Store C#</a><a href="/pt-br/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/pt-br/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts" title="Windows Phone">Windows Phone</a><a href="/pt-br/documentation/articles/mobile-services-ios-authorize-users-in-scripts" title="iOS">iOS</a><a href="/pt-br/documentation/articles/mobile-services-android-authorize-users-in-scripts" title="Android">Android</a><a href="/pt-br/documentation/articles/mobile-services-html-authorize-users-in-scripts" title="HTML">HTML</a><a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-ios-authorize-users-in-scripts" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-android-authorize-users-in-scripts" title="Xamarin.Android">Xamarin.Android</a></div>
<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts/" title="Back-end do .NET">Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts/"  title="Back-end do JavaScript" class="current">Back-end do JavaScript</a></div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Este tópico mostra como autorizar os usuários autenticados para acessar dados nos Serviços Móveis do Azure de um aplicativo da Windows Store. Neste tutorial, você registra scripts nos Serviços Móveis para filtrar consultas com base na userId de um usuário autenticado, garantindo que cada usuário possa ver apenas seus próprios dados.</p>
<p>Você pode assistir a uma versão do vídeo deste tutorial clicando no clipe à direita.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Authenticate-and-Authorize-users-with-Server-Scripts-in-Windows-Azure-Mobile-Servi" target="_blank" class="label">assista ao tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/authorize-users-with-scripts-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Authenticate-and-Authorize-users-with-Server-Scripts-in-Windows-Azure-Mobile-Servi" target="_blank" class="dev-onpage-video"><span class="icon">Executar o vídeo</span></a> <span class="time">13:52</span></div>
</div>

Este tutorial baseia-se no início rápido dos Serviços Móveis e no tutorial anterior [Introdução à autenticação]. Antes de iniciar este tutorial, você deve primeiro concluir o tutorial [Introdução à autenticação].  

## <a name="register-scripts"></a>Registrar scripts
Como o aplicativo quickstart lê e insere dados, você precisará registrar scripts para essas operações com base na tabela TodoItem.

1. Faça logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e, em seguida, clique no seu aplicativo. 

   	![][0]

2. Clique na guia **Dados** e, em seguida, clique na tabela **TodoItem**.

   	![][1]

3. Clique em **Script** e selecione a operação **Inserir**.

   	![][2]

4. Substitua o script existente pela função a seguir e clique em **Salvar**.

        function insert(item, user, request) {
          item.userId = user.userId;    
          request.execute();
        }

    Este script adicionará um valor userId ao item, que é a ID do usuário autenticado, antes que ele seja inserido na tabela TodoItem. 

    <div class="dev-callout"><b>Observação</b>
	<p>O esquema dinâmico deve ser habilitado na primeira vez que esse script de inserção for executado. Com o esquema dinâmico habilitado, os Serviços Móveis adicionarão a coluna <strong>userId</strong> automaticamente à tabela <strong>TodoItem</strong> na primeira execução. Por padrão, o esquema dinâmico é habilitado para um novo serviço móvel e deve ser desabilitado antes que o aplicativo seja publicado na Windows Store.</p>
    </div>


5. Repita as etapas 3 e 4 para substituir a operação **Ler** por esta função:

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

   	Este script filtra os objetos de TodoItem retornados para que cada usuário receba apenas os itens inseridos que ele inseriu.

## Testar o aplicativo

1. No Visual Studio 2012 Express para Windows 8, abra o projeto modificado quando você concluiu o tutorial [Introdução à autenticação].

2. Pressione a tecla F5 para executar o aplicativo e fazer o logon com seu provedor de identidade. 

   	Observe que, neste momento, embora já haja itens na tabela TodoItem em tutoriais anteriores, nenhum item é retornado. Isto acontece porque os itens anteriores foram inseridos sem a coluna da userId e agora têm valores nulos.

3. No aplicativo, insira um texto em **Inserir um TodoItem** e clique em **Salvar**.

   	![][3]

   	Isto insere o texto e a userId na tabela TodoItem no serviço móvel. Como o novo item tem o valor de userId correto, ele é retornado pelo serviço móvel e exibido na segunda coluna.

5. Na tabela **todoitem** no [Portal de Gerenciamento][Azure Management Portal], clique em **Procurar** e verifique se cada item recém-adicionado agora tem um valor de userId associado.

6. (Opcional) Se tiver contas de logon adicionais, você poderá verificar se os usuários podem ver apenas seus próprios dados, fechando o aplicativo (Alt + F4) e, em seguida, executando-o novamente. Quando a caixa de diálogo de credenciais de logon for exibida, insira um logon diferente e, em seguida, verificar se os itens inseridos na conta anterior não são exibidos. 

## Próximas etapas

Isto conclui os tutoriais que demonstram as noções básicas de como trabalhar com autenticação. Considere a possibilidade de obter mais informações sobre os seguintes tópicos dos Serviços Móveis:

* [Começar com os dados]
  <br/>Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

* [Introdução às notificações por push] 
  <br/>Saiba como enviar uma notificação por push muito básica para o seu aplicativo.

* [Referência de script de servidor dos Serviços Móveis]
  <br/>Saiba mais sobre como registrar e usar scripts de servidor.
  
* [Referência conceitual de tutorial do .NET dos Serviços Móveis]
  <br/>Saiba mais sobre como usar os Serviços Móveis com o .NET.

<!-- Anchors. -->
[Registrar scripts de servidor]: #register-scripts
[Próximas etapas]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-insert-script-users.png
[3]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-quickstart-startup.png

<!-- URLs. -->
[Notificações por push do Windows e Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[Painel Meus Aplicativos]: http://go.microsoft.com/fwlink/?LinkId=262039
[Começar com os Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started/#create-new-service
[Começar com os dados]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-dotnet
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-dotnet
[JavaScript e HTML]: mobile-services-win8-javascript/

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Referência conceitual de tutorial do .NET dos Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-net-client-library

