<properties pageTitle="Adicionar paginação aos dados (Windows Phone) | Centro de desenvolvimento móvel" metaKeywords="" description="Saiba como usar paginação para gerenciar a quantidade de dados retornados a seu aplicativo Windows Phone dos serviços móveis." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="glenga" />

# Refinar as consultas dos Serviços Móveis com paginação

[WACOM.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

Este tópico mostra como usar a paginação para gerenciar a quantidade de dados retornada a seu aplicativo Windows Phone dos Serviços Móveis do Azure. Neste tutorial, você usará os métodos de consulta **Take** e **Skip** no cliente para solicitar "páginas" de dados específicas.

>[WACOM.NOTE]Para evitar estouro de dados em clientes de dispositivos móveis, os Serviços Móveis implementam um limite automático de página que tem como padrão um máximo de 50 itens em uma resposta. Ao especificar o tamanho da página, você pode solicitar explicitamente até 1.000 itens na resposta.

Este tutorial se baseia nas etapas e no aplicativo de exemplo do tutorial anterior, [Adicionar Serviços Móveis a um aplicativo existente](/pt-br/documentation/articles/mobile-services-windows-phone-get-started-data/). Antes de iniciar esse tutorial, você deve concluir o tutorial atual.  

[WACOM.INCLUDE [móvel-serviços-windows-dotnet-paginação](../includes/mobile-services-windows-dotnet-paging.md)]

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
[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started/
[Introdução aos dados]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started-data/
[Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started-users/
[Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-windows-phone-get-started-push/


[Portal de Gerenciamento]: https://manage.windowsazure.com/
