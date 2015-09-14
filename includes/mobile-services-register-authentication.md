
1. Clique em Portal do Azure > **Serviços Móveis** > seu serviço móvel > **Painel** e anote o valor da **URL de Serviço Móvel**.

2. Registre seu aplicativo em um ou mais dos seguintes provedores de autenticação:
   * [Google](mobile-services-how-to-register-google-authentication.md)
   * [Facebook](mobile-services-how-to-register-facebook-authentication.md)
   * [Twitter](mobile-services-how-to-register-twitter-authentication.md)
   * [Microsoft](mobile-services-how-to-register-microsoft-authentication.md)
   * [Active Directory do Azure](mobile-services-how-to-register-active-directory-authentication.md). 
   
   Anote os valores de identidade do cliente e segredo do cliente gerados pelo provedor. Não distribua nem compartilhe o segredo do cliente.

3. No portal do Azure, clique em **Serviços Móveis** > seu serviço móvel > **Identidade** > configurações de seu provedor de identidade, insira sua ID do cliente e o valor secreto do provedor. 
 
Você configurou seu aplicativo e o serviço móvel para funcionar com seu provedor de autenticação. Opcionalmente, você pode repetir todas essas etapas para cada provedor de identidade adicional ao qual deseja dar suporte.

    > [AZURE.IMPORTANT] Verify that you've set the correct redirect URI on your identity provider's developer site. As described in the linked instructions for each provider above, the redirect URI may be different for a .NET backend service vs. for a JavaScript backend service. An incorrectly configured redirect URI may result in the login screen not being displayed properly and the app malfunctioning in unexpected ways.

<!---HONumber=September15_HO1-->