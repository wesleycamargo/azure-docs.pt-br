<properties
	pageTitle="Autorização de serviços de usuários nos Serviços Móveis com Back-end JavaScript | Centro de Desenvolvimento Móvel"
	description="Saiba como autorizar usuários no back-end JavaScript dos Serviços Móveis do Azure"
	services="mobile-services"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm=""
	ms.topic="article"
	ms.date="2/18/2015"
	ms.author="krisragh"/>

# Autorização de serviço de usuários nos serviços móveis

> [AZURE.SELECTOR-LIST (Platform | Backend)]
- [(Qualquer | .NET)](/pt-br/documentation/articles/mobile-services-dotnet-backend-service-side-authorization/)
- [(Qualquer | JavaScript)](/pt-br/documentation/articles/mobile-services-javascript-backend-service-side-authorization/)

Este tópico mostra como usar scripts do servidor para autorizar usuários. Neste tutorial, você registra scripts nos Serviços Móveis do Azure, filtra consultas com base em IDs de usuário e fornece acesso aos usuários apenas para seus próprios dados.

Esse tutorial se baseia o Início Rápido dos Serviços Móveis e se baseia no tutorial [Adicionar autenticação ao aplicativo de serviços móveis existentes]. Preencha [Adicionar autenticação ao aplicativo de serviços móveis existentes] primeiro.

## <a name="register-scripts"></a>Registrar scripts

1. Entre no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique em seu serviço móvel. Clique na guia **Dados** e clique na tabela **TodoItem**.

2. Clique em **Script** e selecione a operação **Inserir**.

   	![][2]

3. Substitua o script existente pela função a seguir e clique em **Salvar**. Esse script adiciona a ID de usuário do usuário autenticado para o item antes da inserção.

        function insert(item, user, request) {
          item.userId = user.userId;
          request.execute();
        }


    > [AZURE.NOTE] [O esquema dinâmico deve estar habilitado](https://msdn.microsoft.com/library/azure/jj193175.aspx) para isso funcionar. Essa configuração está habilitada por padrão para novos serviços móveis.

5. Da mesma forma, substitua a operação **Read** com a função a seguir. Esse script filtra objetos TodoItem retornados para que um usuário receba apenas os itens que eles inserem.

        function read(query, user, request) {
           query.where({ userId: user.userId });
           request.execute();
        }




## <a name="test-app"></a>Testar o aplicativo

1. Observe que, quando você executa agora seu aplicativo do cliente, embora já existam itens na tabela _TodoItem_ de tutoriais anteriores, nenhum item é retornado. Isso acontece porque os itens anteriores foram inseridos sem a coluna ID de usuário e agora têm valores nulos. Verifique se itens recentemente adicionados têm um valor userId associado na tabela _TodoItem_.

2. Se você tiver contas de logon adicionais, verifique se os usuários podem ver apenas seus próprios dados fechando e excluindo o aplicativo e executando novamente. Quando a caixa de diálogo de credenciais de logon for exibida, insira um logon diferente e verifique se os itens inseridos no logon anterior não são exibidos.

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
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started/#create-new-service
[Adicionar Serviços Móveis ao aplicativo existente]: /pt-br/develop/mobile/tutorials/get-started-with-data-ios
[Adicionar autenticação ao aplicativo de serviços móveis existentes]: /pt-br/develop/mobile/tutorials/get-started-with-users-ios
[Adicionar notificações por push ao aplicativo existente]: /pt-br/develop/mobile/tutorials/get-started-with-push-ios

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/

<!--HONumber=45--> 
