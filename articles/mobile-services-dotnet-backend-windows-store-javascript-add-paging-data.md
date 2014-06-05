<properties pageTitle="Adicionar paginação aos dados (JavaScript) | Serviços Móveis do Azure" metaKeywords="" description="Saiba como usar paginação para gerenciar a quantidade de dados retornada a seu aplicativo JavaScript da Windows Store dos Serviços Móveis" metaCanonical="http://www.windowsazure.com/pt-br/develop/mobile/tutorials/add-paging-to-data-dotnet/" services="" documentationCenter="Mobile" title="Refinar as consultas dos Serviços Móveis com paginação" authors="" solutions="" manager="" editor="" />


# Refinar as consultas dos Serviços Móveis com paginação

> [AZURE.SELECTOR-LIST(Plataforma | Back-end )]
- [(Windows Store C# | .NET)](mobile-services-dotnet-backend-windows-store-dotnet-add-paging-data.md)
- [(Windows Store C# | JavaScript)](mobile-services-windows-store-dotnet-add-paging-data.md)
- [(JavaScript da Windows Store | .NET)](mobile-services-dotnet-backend-windows-store-javascript-add-paging-data.md)
- [(JavaScript da Windows Store | JavaScript)](mobile-services-windows-store-javascript-add-paging-data.md)
- [(Windows Phone | .NET)](mobile-services-dotnet-backend-windows-phone-add-paging-data)
- [(Windows Phone | JavaScript)](mobile-services-windows-phone-add-paging-data)
- [(iOS | JavaScript)](mobile-services-ios-add-paging-data)
- [(Android | JavaScript)](mobile-services-android-add-paging-data)
- [(HTML | .NET)](mobile-services-html-add-paging-data)
- [(Xamarin iOS | .NET)](partner-xamarin-mobile-services-ios-add-paging-data)
- [(Xamarin Android | .NET)](partner-xamarin-mobile-services-android-add-paging-data)


Este tópico mostra como usar a paginação para gerenciar a quantidade de dados retornados dos Serviços Móveis do Azure para seu aplicativo da Windows Store. Neste tutorial, você usará os métodos de consulta **Take** e **Skip** no cliente para solicitar "páginas" de dados específicas.

>[WACOM.NOTE]Para evitar estouro de dados em clientes de dispositivos móveis, os Serviços Móveis implementam um limite automático de página que tem como padrão um máximo de 50 itens em uma resposta. Ao especificar o tamanho da página, você poderá explicitamente solicitar até 1.000 itens na resposta.

Este tutorial baseia-se nas etapas e no aplicativo de exemplo do tutorial anterior [Introdução aos dados]. Antes de iniciar este tutorial, você deve concluir ao menos o primeiro tutorial na série sobre como trabalhar com dados [Introdução aos dados]. 

[WACOM.INCLUDE [mobile-services-javascript-paging](../includes/mobile-services-javascript-paging.md)]

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
[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-get-started/
[Introdução aos dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/
[Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/

[Portal de Gerenciamento]: https://manage.windowsazure.com/

