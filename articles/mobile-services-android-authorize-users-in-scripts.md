<properties pageTitle="Autorização do lado do serviço (Android) | Mobile Dev Center" metaKeywords="" description="Saiba como autorizar usuários no back-end do JavaScript dos Serviços Móveis do Azure." metaCanonical="" services="" documentationCenter="Mobile" title="Autorização do lado do serviço para usuários dos Serviços Móveis" authors="glenga" solutions="" manager="" editor="" />

# Autorização do lado do serviço para usuários dos Serviços Móveis

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts" title="C# da Windows Store ">C# da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts" title="JavaScript da Windows Store Java">JavaScript da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts" title="Windows Phone">Windows Phone</a><a href="/pt-br/documentation/articles/mobile-services-ios-authorize-users-in-scripts" title="iOS">iOS</a><a href="/pt-br/documentation/articles/mobile-services-android-authorize-users-in-scripts" title="Android" class="current">Android</a><a href="/pt-br/documentation/articles/mobile-services-html-authorize-users-in-scripts" title="HTML">HTML</a><a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-ios-authorize-users-in-scripts" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-android-authorize-users-in-scripts" title="Xamarin.Android">Xamarin.Android</a></div>
<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-android-authorize-users-in-scripts/"  title="Back-end do JavaScript" class="current">Back-end do JavaScript</a></div>	

Este tópico mostra como usar scripts de servidor para autorizar usuários autenticados a acessar dados nos Serviços Móveis do Azure a partir de um aplicativo Android.  Neste tutorial, você registra scripts nos Serviços Móveis para filtrar consultas com base na userId de um usuário autenticado, garantindo que cada usuário possa ver apenas seus próprios dados.

Este tutorial baseia-se no início rápido dos Serviços Móveis e no tutorial anterior [Introdução à autenticação]. Antes de iniciar este tutorial, você deve primeiro concluir o tutorial [Introdução à autenticação].  

## <a name="register-scripts"></a>Registrar scripts
Como o aplicativo quickstart lê e insere dados, você precisa registrar scripts para essas operações com base na tabela TodoItem.

1. Faça login no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique no seu aplicativo. 

   	![][0]

2. Clique na guia **Dados** e clique na tabela **TodoItem**.

   	![][1]

3. Clique em **Script** e selecione a operação **Inserir**.

   	![][2]

4. Substitua o script existente pela função a seguir e clique em **Salvar**.

        function insert(item, user, request) {
          item.userId = user.userId;    
          request.execute();
        }

    Este script adiciona um valor de userId ao item que é a ID do usuário autenticado, antes que ele seja inserido na tabela TodoItem. 

    <div class="dev-callout"><b>Observação</b>
	<p>O esquema dinâmico deve ser habilitado na primeira vez que esse script de inserção for executado. Com o esquema dinâmico habilitado, os Serviços Móveis automaticamente adicionam a coluna <strong>userId</strong> à tabela <strong>TodoItem</strong> na primeira execução. Por padrão, o esquema dinâmico é habilitado para um novo serviço móvel e deve ser desabilitado antes que o aplicativo seja publicado na Windows Store.</p>
    </div>


5. Repita as etapas 3 e 4 para substituir a operação **Ler** por esta função:

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

   	Este script filtra os objetos de TodoItem retornados para que cada usuário receba apenas os itens inseridos por ele.

## Testar o aplicativo

1. No Eclipse, abra o projeto que você modificou quando concluiu o tutorial [Introdução à autenticação].

2. No menu **Executar**, clique em **Executar** para iniciar o aplicativo e entrar com o provedor de identidade escolhido. 

   	Observe que, desta vez, embora já existam itens na tabela TodoItem de tutoriais anteriores, nenhum item é retornado. Isso acontece porque os itens anteriores foram inseridos sem a coluna userId e agora têm valores nulos.

3. No aplicativo, insira texto em **Inserir um TodoItem** e clique em **Salvar**.

   	Isso insere o texto e a userId na tabela TodoItem no serviço móvel. Como o novo item tem o valor de userId correto, ele é retornado pelo serviço móvel e exibido na segunda coluna.

5. Na tabela **todoitem** no [Portal de Gerenciamento][Azure Management Portal], clique em **Procurar** e verifique se cada item recém-adicionado agora tem um valor de userId associado.

6. (Opcional) Se tiver contas de logon adicionais, você poderá verificar se os usuários podem ver apenas seus próprios dados fechando o aplicativo e, em seguida, executando-o novamente. Quando a caixa de diálogo de credenciais de logon for exibida, insira um logon diferente e, em seguida, verifique se os itens inseridos na conta anterior não são exibidos.

## Próximas etapas

Isso conclui os tutoriais que demonstram as noções básicas de como trabalhar com autenticação. Considere a possibilidade de obter mais informações sobre os seguintes tópicos de Serviços Móveis:

* [Introdução aos dados]
  <br/>Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

* [Introdução às notificações por push] 
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

* [Referência de script de servidor dos Serviços Móveis]
  <br/>Saiba mais sobre como registrar e usar scripts de servidor.

<!-- Anchors. -->
[Registrar scripts de servidor]: #register-scripts
[Próximas etapas]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-android-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-android-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-android-authorize-users-in-scripts/mobile-insert-script-users.png


<!-- URLs. -->
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[Painel Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-android
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-android
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-android
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-android

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/

