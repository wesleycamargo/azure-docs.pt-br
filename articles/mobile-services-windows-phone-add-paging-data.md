<properties pageTitle="Adicionar paginação aos dados (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Saiba como usar paginação para gerenciar a quantidade de dados retornada a seu aplicativo Windows Phone dos Serviços Móveis" metaCanonical="" services="" documentationCenter="Mobile" title="Refinar as consultas dos Serviços Móveis com paginação" authors="glenga" solutions="" manager="" editor="" />

# Refinar as consultas dos Serviços Móveis com paginação

> [AZURE.SELECTOR-LIST (Platform | Backend )]
- [(Windows Store C# | .NET)](mobile-services-dotnet-backend-windows-store-dotnet-add-paging-data.md)
- [(Windows Store C# | JavaScript)](mobile-services-windows-store-dotnet-add-paging-data.md)
- [(Windows Store JavaScript | .NET)](mobile-services-dotnet-backend-windows-store-javascript-add-paging-data.md)
- [(Windows Store JavaScript | JavaScript)](mobile-services-windows-store-javascript-add-paging-data.md)
- [(Windows Phone | .NET)](mobile-services-dotnet-backend-windows-phone-add-paging-data)
- [(Windows Phone | JavaScript)](mobile-services-windows-phone-add-paging-data)
- [(iOS | JavaScript)](mobile-services-ios-add-paging-data)
- [(Android | JavaScript)](mobile-services-android-add-paging-data)
- [(HTML | .NET)](mobile-services-html-add-paging-data)
- [(Xamarin iOS | .NET)](partner-xamarin-mobile-services-ios-add-paging-data)
- [(Xamarin Android | .NET)](partner-xamarin-mobile-services-android-add-paging-data)


Este tópico mostra como usar a paginação para gerenciar a quantidade de dados retornados a seu aplicativo Windows Phone dos Serviços Móveis. Neste tutorial, você usará os métodos de consulta **Take** e **Skip** no cliente para solicitar "páginas" de dados específicas.

>[WACOM.NOTE]Para evitar estouro de dados em clientes de dispositivos móveis, os Serviços Móveis implementam um limite automático de página que tem como padrão um máximo de 50 itens em uma resposta. Ao especificar o tamanho da página, você poderá explicitamente solicitar até 1.000 itens na resposta.

Este tutorial se baseia nas etapas e no aplicativo de exemplo do tutorial anterior [Introdução a dados]. Antes de iniciar este tutorial, você deve concluir ao menos o primeiro tutorial na série sobre como trabalhar com dados [Introdução aos dados]. 

[WACOM.INCLUDE [mobile-services-windows-dotnet-paging](../includes/mobile-services-windows-dotnet-paging.md)]

## <a name="next-steps"> </a>Próximas etapas

Isso conclui o grupo de tutoriais que demonstram os conceitos básicos sobre como trabalhar com Serviços Móveis. Considere a possibilidade de obter mais informações sobre os seguintes tópicos de Serviços Móveis:

* [Introdução à autenticação]
  <br/>Saiba como autenticar os usuários do seu aplicativo com uma conta do Windows.

* [Introdução às notificações de envio] 
  <br/>Aprender a enviar uma notificação de envio muito básica para seu aplicativo.

<!-- Anchors. -->

[Próximas etapas]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started/
[Introdução aos dados]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started-data/
[Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started-users/
[Introdução às notificações de envio]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started-push/


[Portal de Gerenciamento]: https://manage.windowsazure.com/

