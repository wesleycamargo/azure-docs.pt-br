<properties
	pageTitle="Autorização no lado do serviço de usuários em um serviço móvel de back-end do JavaScript | Serviços Móveis do Azure"
	description="Saiba como autorizar usuários no back-end JavaScript dos Serviços Móveis do Azure"
	services="mobile-services"
	documentationCenter=""
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.topic="article"
	ms.devlang="javascript"
	ms.date="05/20/2015"
	ms.author="krisragh"/>

# Autorização de serviço de usuários nos Serviços Móveis

> [AZURE.SELECTOR-LIST (Platform | Backend)]
- [(Any | .NET)](mobile-services-dotnet-backend-service-side-authorization.md)
- [(Any | Javascript)](mobile-services-javascript-backend-service-side-authorization.md)

Este tópico mostra como usar scripts do servidor para autorizar usuários. Neste tutorial, você registra scripts nos Serviços Móveis do Azure, filtra consultas com base em IDs de usuário e fornece acesso aos usuários apenas para seus próprios dados. Filtrar os resultados da consulta do usuário pela ID de usuário é a forma mais básica de autorização. Dependendo do seu cenário específico, você também poderá criar tabelas de Usuários ou Funções para monitorar informações mais detalhadas de autorização do usuário, como quais pontos de extremidade um determinado usuário tem permissão para acessar.

Esse tutorial se baseia no Início Rápido dos Serviços Móveis e elabora o tutorial [Adicionar autenticação ao aplicativo de Serviços Móveis existentes]. Conclua primeiro [Adicionar autenticação ao aplicativo de Serviços Móveis existentes].

## <a name="register-scripts"></a>Registrar scripts

1. Entre no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique em seu serviço móvel. Clique na guia **Dados** e clique na tabela **TodoItem**.

2. Clique em **Script**, selecione a operação **Inserir**, substitua o script existente pela função a seguir e clique em **Salvar**.

        function insert(item, user, request) {
          item.userId = user.userId;
          request.execute();
        }

	Esse script adiciona a ID de usuário do usuário autenticado para o item antes da inserção.

    >[AZURE.NOTE]Verifique se [dynamic-schema](https://msdn.microsoft.com/library/azure/jj193175.aspx) está habilitado. Caso contrário, a coluna *userId* coluna não é adicionada automaticamente. Essa configuração está habilitada por padrão para novos serviços móveis.

3. Da mesma forma, substitua a operação **Ler** com a função a seguir. Esse script filtra objetos TodoItem retornados para que um usuário receba apenas os itens que eles inserem.

        function read(query, user, request) {
           query.where({ userId: user.userId });
           request.execute();
        }

## <a name="test-app"></a>Testar o aplicativo

1. Observe que, quando você executa agora seu aplicativo do cliente, embora já existam itens na tabela _TodoItem_ de tutoriais anteriores, nenhum item é retornado. Isso acontece porque os itens anteriores foram inseridos sem a coluna ID de usuário e agora têm valores nulos. Verifique se itens recentemente adicionados têm um valor userId associado na tabela _TodoItem_.

2. Se você tiver contas de logon adicionais, verifique se os usuários podem ver apenas seus próprios dados fechando e excluindo o aplicativo e executando novamente. Quando a caixa de diálogo de credenciais de logon for exibida, insira um logon diferente e verifique se os itens inseridos no logon anterior não são exibidos.

<!-- Anchors. -->
[Register server scripts]: #register-scripts
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->

[Windows Push Notifications & Live Connect]: http://go.microsoft.com/fwlink/p/?LinkID=257677
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Adicionar autenticação ao aplicativo de Serviços Móveis existentes]: /develop/mobile/tutorials/get-started-with-users-ios

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
 

<!---HONumber=August15_HO6-->