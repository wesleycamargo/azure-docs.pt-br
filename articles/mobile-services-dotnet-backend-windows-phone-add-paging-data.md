<properties pageTitle="Add paging to data (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Windows Phone app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/11/2014" ms.author="glenga"></tags>

# Refinar as consultas dos Serviços Móveis com paginação

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-add-paging-data" title="C# da Windows Store#">C# da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-add-paging-data" title="JavaScript da Windows Store">JavaScript da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-add-paging-data" title="Windows Phone" class="current">Windows Phone</a></div>

<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-add-paging-data" title="Back-end do .NET" class="current">Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-windows-phone-add-paging-data"  title="Back-end do JavaScript">Back-end do JavaScript</a></div>

Este tópico mostra como usar a paginação para gerenciar a quantidade de dados retornados a seu aplicativo Windows Phone dos Serviços Móveis. Neste tutorial, você usará com o cliente os métodos de consulta **Aceitar** e **Ignorar** para solicitar "páginas" específicas de dados.

> [WACOM.NOTE]Para evitar estouro de dados em clientes com dispositivo móvel, os Serviços Móveis implementam um limite automático de página que tem como padrão um máximo de 50 itens em uma resposta. Ao especificar o tamanho da página, você poderá explicitamente solicitar até 1.000 itens na resposta.

Este tutorial se baseia nas etapas e no aplicativo de exemplo do tutorial anterior [Introdução a dados][Introdução a dados]. Antes de iniciar este tutorial, você deve concluir ao menos o primeiro tutorial na série sobre como trabalhar com dados [Introdução aos dados][Introdução a dados].

## Define o tamanho da página do servidor

[WACOM.INCLUDE [mobile-services-windows-dotnet-paging][mobile-services-windows-dotnet-paging]]

## <a name="next-steps"> </a>Próximas etapas

Isso conclui o grupo de tutoriais que demonstram os conceitos básicos sobre como trabalhar com Serviços Móveis. Considere a possibilidade de obter mais informações sobre os seguintes tópicos de Serviços Móveis:

-   [Introdução à autenticação][Introdução à autenticação]
    
	Saiba como autenticar usuários de seu aplicativo com uma conta do Windows.

-   [Introdução às notificações por push][Introdução às notificações por push]
    
	Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [C# da Windows Store]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-add-paging-data "C# da Windows Store#"
  [JavaScript da Windows Store]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-add-paging-data "JavaScript da Windows Store"
  [Windows Phone]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-add-paging-data "Windows Phone"
  [Back-end do .NET]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-add-paging-data "Back-end do .NET"
  [Back-end do JavaScript]: /pt-br/documentation/articles/mobile-services-windows-phone-add-paging-data "Back-end do JavaScript"
  [Introdução a dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/
  [mobile-services-windows-dotnet-paging]: ../includes/mobile-services-windows-dotnet-paging.md
  [Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
  [Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/
