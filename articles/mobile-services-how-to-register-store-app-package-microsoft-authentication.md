<properties urlDisplayName="Shared Access Signature Part 1" pageTitle="Registrar seu pacote do aplicativo da Windows Store para a autenticação da Microsoft" metaKeywords="" description="Saiba como registrar seu aplicativo da Windows Store para autenticação da Microsoft em seu aplicativo de serviços móveis do Azure" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your Windows Store app package for Microsoft authentication" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />

# Registrar seu pacote do aplicativo da Windows Store para a autenticação da Microsoft

Os Serviços Móveis do Azure dão suporte aos métodos de autenticação controlada pelo cliente e controlada pelo servidor. A autenticação controlada pelo servidor usa provedores de identidade, incluindo a Conta da Microsoft. Quando você usa uma Conta da Microsoft com autenticação controlada pelo servidor sem registrar seu aplicativo nos Serviços Móveis, os usuários são solicitados a fornecer credenciais a cada solicitação de autenticação. Quando você registra seu aplicativo, as credenciais de logon da Conta da Microsoft são armazenadas em cache e podem ser usadas para autenticação sem que o usuário seja solicitado a fornecê-las novamente. Este tópico mostra como registrar seu pacote do aplicativo da Windows Store para obter uma experiência aprimorada de logon de Conta da Microsoft ao usar os Serviços Móveis do Azure para autenticação. 

>[WACOM.NOTE]O Visual Studio 2013 facilita o registro de seu pacote do aplicativo da Windows Store com os Serviços Móveis. Para obter mais informações, consulte <a href="http://go.microsoft.com/fwlink/p/?LinkId=309101">Início rápido: Adicionando notificações por push para um serviço móvel</a> na Central de Desenvolvimento do Windows.

A autenticação gerenciada pelo cliente pode ser usada para fornecer uma experiência de logon único em um dispositivo Windows usando o Live Connect. Se você usar APIs do Live Connect, não será necessário concluir as etapas deste tópico. Para obter mais informações, consulte [Autenticar seu aplicativo da Windows Store com o logon único do Live Connect].   

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

Depois de registrar seu pacote do aplicativo, lembre-se de fornecer um valor <strong>verdadeiro</strong> para <em>useSingleSignOn</em> ao chamar o método <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> . Isso fornece aos usuários uma experiência de logon aprimorada ao usar uma Conta da Microsoft.

<!-- Anchors. -->
<!-- Images. -->


<!-- URLs. -->
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-dotnet/
[Autenticar seu aplicativo da Windows Store com o logon único do Live Connect]: /pt-br/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Introdução aos usuários de C#]: /pt-br/develop/mobile/tutorials/get-started-with-users-dotnet/
[Introdução aos usuários de JavaScript ]: /pt-br/develop/mobile/tutorials/get-started-with-users-js/

<!--HONumber=35.1-->
