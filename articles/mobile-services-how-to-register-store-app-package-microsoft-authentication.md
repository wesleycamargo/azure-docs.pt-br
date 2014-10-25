<properties linkid="develop-mobile-how-to-guides-register-windows-store-app-server-auth" urlDisplayName="Shared Access Signature Part 1" pageTitle="Register your Windows Store app package for Microsoft authentication" metaKeywords="" description="Learn how to register your Windows Store app for Microsoft authentication in your Azure Mobile Services application" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your Windows Store app package for Microsoft authentication" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Registrar seu pacote de aplicativos da Windows Store para a autenticação da Microsoft

Os Serviços Móveis do Azure oferecem suporte aos métodos de autenticação controlada pelo cliente e controlada pelo servidor. A autenticação controlada pelo servidor usa provedores de identidade, incluindo a Conta da Microsoft. Quando você usa uma Conta da Microsoft com autenticação controlada pelo servidor sem registrar seu aplicativo nos Serviços Móveis, os usuários serão solicitados a fornecer credenciais a cada solicitação de autenticação. Quando você registra seu aplicativo, as credenciais de logon da Conta da Microsoft são armazenadas em cache e podem ser usadas para autenticação sem que o usuário seja solicitado a fornecê-las novamente. Este tópico mostra como registrar seu pacote de aplicativos da Windows Store para obter uma experiência aprimorada de logon de Conta da Microsoft ao usar os Serviços Móveis do Azure para autenticação.

> [WACOM.NOTE]O Visual Studio 2013 facilita o registro de seu pacote de aplicativos da Windows Store com os Serviços Móveis. Para saber mais, confira [Início rápido: Adicionando notificações por push para um serviço móvel][Início rápido: Adicionando notificações por push para um serviço móvel] no Centro de Desenvolvimento do Windows.

A autenticação gerenciada pelo cliente pode ser usada para fornecer uma experiência de logon único em um dispositivo Windows usando o Live Connect. Se você usar APIs do Live Connect, não será necessário concluir as etapas deste tópico. Para obter mais informações, consulte [Autenticar seu aplicativo da Windows Store com o logon único do Live Connect][Autenticar seu aplicativo da Windows Store com o logon único do Live Connect].

> [WACOM.NOTE]A autenticação gerenciada pelo cliente usando o Live Connect ainda não tem suporte em um serviço móvel de back-end do .NET.

[WACOM.INCLUDE [mobile-services-register-windows-store-app][mobile-services-register-windows-store-app]]

Depois de registrar seu pacote de aplicativos, lembre-se de fornecer um valor de **true** para o *useSingleSignOn* quando você chamar o método [LoginAsync][LoginAsync]. Isso fornece aos usuários uma experiência de logon aprimorada ao usar uma Conta da Microsoft.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Início rápido: Adicionando notificações por push para um serviço móvel]: http://go.microsoft.com/fwlink/p/?LinkId=309101
  [Autenticar seu aplicativo da Windows Store com o logon único do Live Connect]: /pt-br/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
  [mobile-services-register-windows-store-app]: ../includes/mobile-services-register-windows-store-app.md
  [LoginAsync]: http://go.microsoft.com/fwlink/p/?LinkId=311594
