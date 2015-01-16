<properties urlDisplayName="Add paging to data" pageTitle="Adicionar paginação aos dados (Xamarin Android) | Centro de desenvolvimento móvel" metaKeywords="" description="Saiba como usar paginação para gerenciar a quantidade de dados retornados para seu aplicativo Android Xamarin dos serviços móveis."  services="mobile-services" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="donnam" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="donnam" />

# Refinar as consultas dos Serviços Móveis com paginação

[WACOM.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]


Este tópico mostra como usar a paginação para gerenciar a quantidade de dados retornados ao seu aplicativo Xamarin.Android dos Serviços Móveis do Azure. Neste tutorial, você usará os métodos de consulta **Take** e **Skip** no cliente para solicitar "páginas" de dados específicas.

<div class="dev-callout"><b>Observação</b>
<p>Para evitar estouro de dados em clientes de dispositivos móveis, os Serviços Móveis implementam um limite automático de página que tem como padrão um máximo de 50 itens em uma resposta. Ao especificar o tamanho da página, você pode solicitar explicitamente até 1.000 itens na resposta.</p>
</div>

Esse tutorial se baseia nas etapas e no aplicativo de exemplo do tutorial anterior [Introdução aos dados]. Antes de iniciar este tutorial, você deve concluir ao menos o primeiro tutorial na série sobre como trabalhar com dados [Introdução aos dados]. 

1. No Xamarin Studio, abra o projeto que você criou quando concluiu o tutorial [Introdução aos Serviços Móveis].

2. Clique em **Executar** para iniciar o aplicativo, digite texto na caixa de texto e clique no botão **Adicionar**.

3. Repita a etapa anterior, pelo menos três vezes, para que você tenha mais de três itens armazenados na tabela TodoItem. 

4. No arquivo **TodoActivity.cs**, substitua a consulta LINQ no método **RefreshItemsFromTableAsync** pela seguinte consulta:

		var list = await todoTable.Where(item => item.Complete == false)
						          .Take(3)
			                      .ToListAsync();

	  Essa consulta retorna os primeiros três itens que não estiverem marcados como concluídos.

5. Recrie e inicie o aplicativo. 
   
    Observe que apenas os três primeiros resultados da tabela TodoItem são exibidos. 

6. (Opcional) Exiba o URI da solicitação enviada ao serviço móvel usando software de inspeção de mensagem, como as ferramentas de desenvolvedor do navegador ou o [Fiddler]. 

   	Observe que o método `Take(3)` foi convertido na opção de consulta `$top=3` no URI da consulta.

7. Atualize a consulta LINQ no método **RefreshItemsFromTableAsync** método mais uma vez com a seguinte consulta:
            
			var list = await todoTable.Where(item => item.Complete == false)
							          .Skip(3)
				                      .Take(3)
                 				      .ToListAsync();

   	Essa consulta ignora os três primeiros resultados e retorna os três seguintes. Essa é efetivamente a segunda "página" de dados, onde o tamanho da página é de três itens.

    <div class="dev-callout"><b>Observação</b>
    <p>Este tutorial usa um cenário simplificado passando valores de paginação embutidos em código para os métodos <strong>Take</strong> e <strong>Skip</strong>. Em um aplicativo do mundo real, você pode usar consultas semelhantes às mencionadas acima com um controle de paginação ou interface do usuário comparável para permitir que os usuários naveguem para páginas anteriores e posteriores. Você também pode chamar o método <strong>includeTotalCount</strong> para obter a contagem total dos itens disponíveis no servidor, junto com os dados paginados.</p>
    </div>

8. (Opcional) Novamente, você pode ver o URI da solicitação enviada ao serviço móvel. 

   	Observe que o método `Skip(3)` foi convertido na opção de consulta `$skip=3` no URI da consulta.

## <a name="next-steps"> </a>Próximas etapas

Isso conclui o grupo de tutoriais que demonstram os conceitos básicos sobre como trabalhar com Serviços Móveis. Considere a possibilidade de obter mais informações sobre os seguintes tópicos de Serviços Móveis:

* [Introdução à autenticação]
  <br/>Saiba como autenticar os usuários do seu aplicativo com uma conta do Windows.
 
* [Introdução às notificações por push] 
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

<!-- Anchors. -->

[Próximas etapas]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started-xamarin-android
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-android
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-xamarin-android
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-xamarin-android

[Portal de Gerenciamento]: https://manage.windowsazure.com/

