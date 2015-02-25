<properties 
	pageTitle="Adicionar paginação aos dados (iOS) | Centro de Desenvolvimento Móvel" 
	description="Saiba como usar paginação para gerenciar a quantidade de dados retornados para seu aplicativo iOS dos serviços móveis." 
	services="mobile-services" 
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

# Refinar as consultas dos Serviços Móveis com paginação

[AZURE.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

Este tópico mostra como usar a paginação para gerenciar a quantidade de dados retornados para seu aplicativo iOS dos Serviços Móveis do Azure. Neste tutorial, você usará as propriedades de consulta **fetchLimit** e **fetchOffset** no cliente para solicitar "páginas" específicas de dados.

> [AZURE.NOTE] Para evitar estouro de dados em clientes de dispositivos móveis, os Serviços Móveis implementam um limite automático de página que tem como padrão um máximo de 50 itens em uma resposta. Ao especificar o tamanho da página, você poderá explicitamente solicitar até 1.000 itens na resposta.

Este tutorial se baseia nas etapas e o aplicativo de exemplo do tutorial anterior, [Introdução aos dados]. Antes de começar este tutorial, você deve concluir pelo menos o primeiro tutorial sobre como trabalhar com a série de dados, [Introdução aos dados].

1. No Xcode, abra o projeto que você modificou quando concluiu o tutorial [Introdução aos dados].

2. Pressione o botão **executar** (Command + R) para compilar o projeto e iniciar o aplicativo, em seguida, digite texto na caixa de texto e clique no ícone com sinal de adição (**+**).

3. Repita a etapa anterior, pelo menos três vezes, para que você tenha mais de três itens armazenados na tabela TodoItem.

4. Abra o arquivo QSTodoService.m e localize o seguinte método:

        - (void)refreshDataOnSuccess:(QSCompletionBlock)completion

   	Substitua o corpo de todo o método pelo código a seguir.

        // Create a predicate that finds active items in which complete is false
        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
	
        // Retrieve the MSTable's MSQuery instance with the predicate you just created.
        MSQuery * query = [self.table queryWithPredicate:predicate];

        query.includeTotalCount = YES; // Request the total item count

        // Start with the first item, and retrieve only three items
        query.fetchOffset = 0;
        query.fetchLimit = 3;

        // Invoke the MSQuery instance directly, rather than using the MSTable helper methods.
        [query readWithCompletion:^(NSArray *results, NSInteger totalCount, NSError *error) {

            [self logErrorIfNotNil:error];
            if (!error)
            {
                // Log total count.
                NSLog(@"Total item count: %@",[NSString stringWithFormat:@"%zd", (ssize_t) totalCount]);
            }

            items = [results mutableCopy];

            // Let the caller know that we finished
            completion();
        }];

   	Essa consulta retorna os primeiros três itens que não estiverem marcados como concluídos.

5. Recrie e inicie o aplicativo.

    Observe que apenas os três primeiros resultados da tabela TodoItem são exibidos.

7. Atualize o método **refreshDataOnSuccess** mais uma vez localizando a seguinte linha de código:

        query.fetchOffset = 0;

   	Neste momento, defina o valor de **query.fetchOffset** para 3.

   	Essa consulta ignora os três primeiros resultados e retorna os três seguintes. Essa é efetivamente a segunda "página" de dados, onde o tamanho da página é de três itens.

    > [AZURE.NOTE] Este tutorial usa um cenário simplificado, definindo valores de paginação embutidos em código para as propriedades **fetchOffset** e **fetchLimit**. Em um aplicativo do mundo real, você pode usar consultas semelhantes às mencionadas acima com um controle de paginação ou interface do usuário comparável para permitir que os usuários naveguem para páginas anteriores e posteriores. Você também pode definir **query.includeTotalCount = YES** para obter a contagem total de todos os itens disponíveis no servidor, juntamente com os dados paginados.

## <a name="next-steps"> </a>Próximas Etapas

Isso conclui o grupo de tutoriais que demonstram os conceitos básicos sobre como trabalhar com Serviços Móveis. Considere a possibilidade de obter mais informações sobre o seguinte tópico de Serviços Móveis:

* [Introdução à autenticação]
  <br/>Saiba como autenticar os usuários do seu aplicativo com uma conta do Windows.

<!--
* [Introdução às notificações por push]
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.
-->

<!-- Anchors. -->

[Próximas etapas]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Introdução aos Serviços Móveis]: /en-us/develop/mobile/tutorials/get-started-ios
[Introdução aos dados]: /en-us/develop/mobile/tutorials/get-started-with-data-ios
[Introdução à autenticação]: /en-us/develop/mobile/tutorials/get-started-with-users-ios
[Introdução às notificações por push]: /en-us/develop/mobile/tutorials/get-started-with-push-ios

[Portal de Gerenciamento]: https://manage.windowsazure.com/
\n<!--HONumber=42-->
