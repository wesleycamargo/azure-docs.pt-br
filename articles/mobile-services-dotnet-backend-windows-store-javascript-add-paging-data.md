<properties 
	pageTitle="Adicionar paginação aos dados (JavaScript) - Serviços Móveis do Azure" 
	description="Saiba como usar paginação para gerenciar a quantidade de data returned to your Windows Store JavaScript app from Mobile Services." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/24/2014" 
	ms.author="glenga"/>


# Refinar as consultas dos Serviços Móveis com paginação

[AZURE.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

Este tópico mostra como usar a paginação para gerenciar a quantidade de dados retornada ao seu aplicativo de Serviços Móveis do Azure da Windows Store. Neste tutorial, você usará os métodos de consulta **Take** e **Skip** no cliente para solicitar "páginas" de dados específicas.

>[AZURE.NOTE]Para evitar estouro de dados em clientes de dispositivos móveis, os Serviços Móveis implementam um limite automático de página que tem como padrão um máximo de 50 itens em uma resposta. Ao especificar o tamanho da página, você poderá explicitamente solicitar até 1.000 itens na resposta.

Este tutorial se baseia nas etapas e o aplicativo de exemplo do tutorial anterior, [Introdução aos dados]. Antes de começar este tutorial, você deve concluir pelo menos o primeiro tutorial sobre como trabalhar com a série de dados, [Introdução aos dados]. 

[AZURE.INCLUDE [mobile-services-javascript-paging](../includes/mobile-services-javascript-paging.md)]

## <a name="next-steps"> </a>Próximas Etapas

Isso conclui o grupo de tutoriais que demonstram os conceitos básicos sobre como trabalhar com Serviços Móveis. Considere a possibilidade de obter mais informações sobre os seguintes tópicos de Serviços Móveis:

* [Introdução à autenticação]
  <br/>Saiba como autenticar os usuários do seu aplicativo com uma conta do Windows.

* [Introdução às notificações por push] 
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

<!-- Anchors. -->

[Próximas etapas]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Introdução aos Serviços Móveis]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-get-started/
[Introdução aos dados]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/
[Introdução à autenticação]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[Introdução às notificações por push]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/

[Portal de Gerenciamento]: https://manage.windowsazure.com/
\n<!--HONumber=42-->
