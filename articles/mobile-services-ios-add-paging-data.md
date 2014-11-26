<properties linkid="develop-mobile-tutorials-add-paging-to-data-ios" urlDisplayName="Add paging to data" pageTitle="Add paging to data (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your iOS app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# Refinar as consultas dos Serviços Móveis com paginação

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-dotnet" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-js" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS" class="current">iOS</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-android" title="Android" class="current">Android</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-html" title="HTML" class="current">HTML</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/pt-br/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

Este tópico mostra como usar a paginação para gerenciar a quantidade de dados retornados para seu aplicativo iOS dos Serviços Móveis do Azure. Neste tutorial, você usará as propriedades de consulta **fetchLimit** e **fetchOffset** no cliente para solicitar "páginas" de dados específicas.

<div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Para evitar estouro de dados em clientes de dispositivos m&oacute;veis, os Servi&ccedil;os M&oacute;veis implementam um limite autom&aacute;tico de p&aacute;gina que tem como padr&atilde;o um m&aacute;ximo de 50 itens em uma resposta. Ao especificar o tamanho da p&aacute;gina, voc&ecirc; poder&aacute; explicitamente solicitar at&eacute; 1.000 itens na resposta.</p>
</div>

Este tutorial se baseia nas etapas e no aplicativo de exemplo do tutorial anterior [Introdução a dados][Introdução a dados]. Antes de iniciar este tutorial, você deve concluir ao menos o primeiro tutorial na série sobre como trabalhar com dados [Introdução aos dados][Introdução a dados].

1.  No Xcode, abra o projeto que você modificou quando concluiu o tutorial [Introdução aos dados][Introdução a dados].

2.  Pressione o botão **Executar** (Command + R) para compilar o projeto e iniciar o aplicativo e, em seguida, insira texto na caixa de texto e clique no ícone de sinal de adição (**+**).

3.  Repita a etapa anterior, pelo menos três vezes, para que você tenha mais de três itens armazenados na tabela TodoItem.

4.  Abra o arquivo QSTodoService.m e localize o seguinte método:

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

5.  Recrie e inicie o aplicativo.

    Observe que apenas os três primeiros resultados da tabela TodoItem são exibidos.

6.  Atualize o método **refreshDataOnSuccess** mais uma vez localizando a seguinte linha de código:

        query.fetchOffset = 0;

    Desta vez, defina o valor de **query.fetchOffset** para 3.

    Essa consulta ignora os três primeiros resultados e retorna os três seguintes. Essa é efetivamente a segunda "página" de dados, onde o tamanho da página é de três itens.

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Este tutorial usa um cen&aacute;rio simplificado definindo valores de pagina&ccedil;&atilde;o embutidos em c&oacute;digo para as propriedades <strong>fetchOffset</strong> e <strong>fetchLimit</strong>. Em um aplicativo do mundo real, voc&ecirc; pode usar consultas semelhantes &agrave;s mencionadas acima com um controle de pagina&ccedil;&atilde;o ou interface do usu&aacute;rio compar&aacute;vel para permitir que os usu&aacute;rios naveguem para p&aacute;ginas anteriores e posteriores. Voc&ecirc; tamb&eacute;m pode definir **query.includeTotalCount = YES** para obter a contagem total de todos os itens dispon&iacute;veis no servidor, ao longo com os dados paginados.</p>
</div>

## <a name="next-steps"> </a>Próximas etapas

Isso conclui o grupo de tutoriais que demonstram os conceitos básicos sobre como trabalhar com Serviços Móveis. Se desejar obter mais informações sobre o seguinte tópico de Serviços Móveis:

-   [Introdução à autenticação][Introdução à autenticação]
    Saiba como autenticar usuários de seu aplicativo com uma conta do Windows.

<!-- * [Get started with push notifications]    <br/>Learn how to send a very basic push notification to your app. --> 
 
 


  [Introdução a dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-ios
  [Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-ios
