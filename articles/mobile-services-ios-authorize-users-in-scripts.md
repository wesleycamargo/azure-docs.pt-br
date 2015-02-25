<properties 
	pageTitle="Autorização no lado do serviço (iOS) | Centro de Desenvolvimento Móvel" 
	description="Saiba como autorizar usuários no back-end do JavaScript dos Serviços Móveis do Azure." 
	services="" 
	documentationCenter="ios" 
	authors="krisragh" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="krisragh"/>

# Autorização de atendimento a usuários dos Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]


Este tópico mostra como usar scripts de servidor para autorizar usuários autenticados a acessar dados nos Serviços Móveis do Azure por meio de um aplicativo iOS.  Neste tutorial você registra scripts com os serviços móveis para filtrar consultas com base no userId de um usuário autenticado, garantindo que cada usuário possa ver apenas seus próprios dados.

Este tutorial baseia-se no início rápido dos Serviços Móveis e no tutorial anterior [Introdução à autenticação]. Antes de iniciar este tutorial, você deve primeiro concluir o tutorial [Introdução à autenticação].  

## <a name="register-scripts"></a>Registrar scripts
Como o aplicativo Guia de início rápido lê e insere dados, você precisa registrar scripts para essas operações com base na tabela TodoItem.

1. Faça logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique em seu aplicativo.

   	![][0]

2. Clique na guia **Dados** e, em seguida, na tabela **TodoItem**.

   	![][1]

3. Clique em **Script** e selecione a operação **Inserir**.

   	![][2]

4. Substitua o script existente pela função a seguir e clique em **Salvar**.

        function insert(item, user, request) {
          item.userId = user.userId;
          request.execute();
        }

    Este script adiciona um valor userId ao item que é a ID do usuário autenticado, antes que ele seja inserido na tabela TodoItem.

    > [AZURE.NOTE] O esquema dinâmico deve ser habilitado na primeira vez que esse script de inserção for executado. Com o esquema dinâmico habilitado, os Serviços Móveis adicionam automaticamente a coluna **userId** à tabela **TodoItem** na primeira execução. Por padrão, o esquema dinâmico é habilitado para um novo serviço móvel e deve ser desabilitado antes que o aplicativo seja publicado na Windows Store.


5. Repita as etapas 3 e 4 para substituir a operação **Ler** existente pela seguinte função:

        function read(query, user, request) {
           query.where({ userId: user.userId });
           request.execute();
        }

   	Esse script filtra os objetos TodoItem retornados para que cada usuário receba apenas os itens inseridos por esse próprio usuário.

## Testar o aplicativo

1. No Xcode, abra o projeto que você modificou quando concluiu o tutorial [Introdução à autenticação].

2. Pressione o botão **Executar** para compilar o projeto, iniciar o aplicativo no emulador do iPhone e fazer logon com o provedor de identidade escolhido.

   	Observe que, desta vez, embora já existam itens na tabela TodoItem de tutoriais anteriores, nenhum item é retornado. Isso acontece porque os itens anteriores foram inseridos sem a coluna userId e agora têm valores nulos.

3. No aplicativo, insira o texto em **Inserir um TodoItem** e clique em **Salvar**.

   	![][3]

   	Isso insere o texto e o userId na tabela TodoItem, no serviço móvel. Como o novo item tem o valor userId correto, ele é retornado pelo serviço móvel e exibido na segunda coluna.

5. De volta à tabela **todoitem** no [Portal de Gerenciamento][Portal de Gerenciamento do Azure], clique em **Procurar** e verifique se cada item recém-adicionado agora tem um valor userId associado.

6. (Opcional) Se tiver contas de logon adicionais, você poderá verificar se os usuários podem ver apenas seus próprios dados fechando o aplicativo e, em seguida, executando-o novamente. Quando a caixa de diálogo de credenciais de logon for exibida, insira um logon diferente e, em seguida, verifique se os itens inseridos na conta anterior não são exibidos.

## Próximas etapas

Isso conclui os tutoriais que demonstram os conceitos básicos de como trabalhar com autenticação. Considere a possibilidade de obter mais informações sobre os seguintes tópicos de Serviços Móveis:

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
[0]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-insert-script-users.png
[3]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png

<!-- URLs. -->

[Notificações por push e Live Connect do Windows]: http://go.microsoft.com/fwlink/p/?LinkID=257677
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[Painel Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Introdução aos Serviços Móveis]: /en-us/develop/mobile/tutorials/get-started/#create-new-service
[Introdução aos dados]: /en-us/develop/mobile/tutorials/get-started-with-data-ios
[Introdução à autenticação]: /en-us/develop/mobile/tutorials/get-started-with-users-ios
[Introdução às notificações por push]: /en-us/develop/mobile/tutorials/get-started-with-push-ios

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
\n<!--HONumber=42-->
