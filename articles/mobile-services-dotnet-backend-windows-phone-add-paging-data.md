<properties 
	pageTitle="Adicionar paginação aos dados (Windows Phone) | Centro de Desenvolvimento Móvel" 
	description="Saiba como usar paginação para gerenciar a quantidade de dados retornados a seu aplicativo Windows Phone dos serviços móveis." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/11/2014" 
	ms.author="glenga"/>

# Refinar as consultas dos Serviços Móveis com paginação

[AZURE.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

Este tópico mostra como usar a paginação para gerenciar a quantidade de dados retornada a seu aplicativo Windows Phone dos Serviços Móveis do Azure. Neste tutorial, você usará os métodos de consulta **Take** e **Skip** no cliente para solicitar "páginas" de dados específicas.

>[AZURE.NOTE]Para evitar estouro de dados em clientes de dispositivos móveis, os Serviços Móveis implementam um limite automático de página que tem como padrão um máximo de 50 itens em uma resposta. Ao especificar o tamanho da página, você poderá explicitamente solicitar até 1.000 itens na resposta.

Este tutorial baseia-se nas etapas e no aplicativo de exemplo do tutorial anterior, [Adicionar Serviços Móveis a um aplicativo existente](/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/). Antes de iniciar esse tutorial, você deve concluir o tutorial atual. 

##Define o tamanho da página do servidor

[AZURE.INCLUDE [mobile-services-windows-dotnet-paging](../includes/mobile-services-windows-dotnet-paging.md)]

## <a name="next-steps"> </a>Próximas Etapas

Isso conclui o grupo de tutoriais que demonstram os conceitos básicos sobre como trabalhar com Serviços Móveis. Saiba mais sobre os seguintes tópicos de Serviços Móveis:

* [Introdução à autenticação]
  <br/>Saiba como autenticar os usuários do seu aplicativo com uma conta do Windows.

* [Introdução às notificações por push] 
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

<!-- Anchors. -->

[Próximas etapas]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[Introdução aos dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/
[Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
[Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/


[Portal de Gerenciamento]: https://manage.windowsazure.com/



<!--HONumber=42-->
