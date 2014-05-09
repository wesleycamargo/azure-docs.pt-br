<properties linkid="develop-mobile-how-to-guides-register-windows-store-app-server-auth" urlDisplayName="Assinatura de Acesso Compartilhado - parte 1" pageTitle="Registrar seu pacote de aplicativos da Windows Store para autenticação da Microsoft" metaKeywords="" description="Saiba como registrar seu aplicativo da Windows Store para autenticação da Microsoft em seu aplicativo de Serviços Móveis do Azure" metaCanonical="" services="" documentationCenter="Mobile" title="Registrar seu pacote de aplicativos da Windows Store para autenticação da Microsoft" authors="glenga" solutions="" manager="" editor="" />

# Registrar seu pacote de aplicativos da Windows Store para autenticação da Microsoft

Os Serviços Móveis do Azure oferecem suporte aos métodos de autenticação controlada pelo cliente e controlada pelo servidor. A autenticação controlada pelo servidor usa provedores de identidade, incluindo a Conta da Microsoft. Quando você usa uma Conta da Microsoft com autenticação controlada pelo servidor sem registrar seu aplicativo nos Serviços Móveis, os usuários serão solicitados a fornecer credenciais a cada solicitação de autenticação. Quando você registra seu aplicativo, as credenciais de logon da Conta da Microsoft são armazenadas em cache e podem ser usadas para autenticação sem que o usuário seja solicitado a fornecê-las novamente. Este tópico mostra como registrar seu pacote de aplicativos da Windows Store para obter uma experiência aprimorada de logon de Conta da Microsoft ao usar os Serviços Móveis do Azure para autenticação. 

A autenticação controlada pelo cliente pode ser usada para fornecer uma experiência de logon único em um dispositivo Windows usando o Live Connect. Se você usar APIs do Live Connect, não será necessário concluir as etapas deste tópico. Para obter mais informações, consulte [Autenticar seu aplicativo da Windows Store com o logon único do Live Connect].   

O registro do aplicativo também permite que enviar notificações por push ao aplicativo. Se você já concluiu o tutorial [Introdução às notificações por push] para seu aplicativo, você já terá concluído as etapas deste tópico.

<div class="dev-callout"><b>Observação</b>
	<p>O <em>Visual Studio 2013 Preview</em> inclui novos recursos que facilitam o registro de seu pacote de aplicativos da Windows Store com os Serviços Móveis. Para obter mais informações, consulte <a href="http://go.microsoft.com/fwlink/p/?LinkId=309101">Guia de início rápido: Adicionando notificações por push a um serviço móvel (a página pode estar em inglês)</a> no Centro de Desenvolvimento do Windows.</p>
</div>

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

Depois de registrar seu pacote de aplicativos, lembre-se de fornecer um valor de <strong>true</strong> para o <em>useSingleSignOn</em> quando você chamar o método <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a>. Isso fornece aos usuários uma experiência de logon aprimorada ao usar uma Conta da Microsoft.

<!-- Anchors. -->
<!-- Images. -->


<!-- URLs. -->
[Introdução à notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-dotnet/
[Autenticar seu aplicativo da Windows Store com o logon único do Live Connect]: /pt-br/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Introdução aos usuários do C#]: /pt-br/develop/mobile/tutorials/get-started-with-users-dotnet/
[Introdução aos usuários do JavaScript]: /pt-br/develop/mobile/tutorials/get-started-with-users-js/

