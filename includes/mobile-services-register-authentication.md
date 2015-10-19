
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

> [AZURE.IMPORTANT]Verifique se você definiu o URI de redirecionamento correto no site de desenvolvedores do provedor de identidade. Conforme descrito nas instruções vinculadas para cada provedor acima, o URI de redirecionamento pode ser diferente para um serviço de back-end do .NET em comparação com um serviço de back-end do JavaScript. Um URI de redirecionamento configurado incorretamente pode fazer com que a tela de logon não seja exibida corretamente e que o aplicativo funcione incorretamente de maneiras inesperadas.

<!---HONumber=Oct15_HO2-->