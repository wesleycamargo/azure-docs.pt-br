Para ser capaz de autenticar usuários, você deve registrar seu aplicativo com um provedor de identidade. Você deve registrar o segredo do cliente gerado pelo provedor com os Serviços Móveis.

1.  Faça o logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique em seu serviço móvel.

    ![][]

2.  Clique na guia **Painel** e anote o valor da **URL do Serviço Móvel**.

    ![][1]

    Talvez seja necessário fornecer este valor para o provedor de identidade ao registrar seu aplicativo.

3.  Escolha um provedor de identidade suportado na lista abaixo e siga as etapas para registrar seu aplicativo com o provedor:

-   [Conta da Microsoft][Conta da Microsoft]
-   [Logon no Facebook][Logon no Facebook]
-   [Logon no Twitter][Logon no Twitter]
-   [Logon no Google][Logon no Google]
-   [Azure Active Directory][Azure Active Directory]

    Lembre-se de anotar os valores da identidade do cliente e segredo gerados pelo provedor.

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o de Seguran&ccedil;a</b>
<p>O segredo gerado pelo provedor &eacute; uma credencial de seguran&ccedil;a importante. N&atilde;o compartilhe esse segredo com ningu&eacute;m nem o distribua com seu aplicativo.</p>
</div>

1.  No Portal de Gerenciamento, clique na guia **Identidade**, digite os valores do identificador do aplicativo e do segredo compartilhados obtidos do provedor de identidade e clique em **Salvar**.

    ![][2]

    O serviço móvel e seu aplicativo agora estão configurados para funcionar com o provedor de autenticação escolhido.

<!-- URLs. -->

  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-register-authentication/mobile-services-selection.png
  [1]: ./media/mobile-services-register-authentication/mobile-service-uri.png
  [Conta da Microsoft]: /pt-br/documentation/articles/mobile-services-how-to-register-microsoft-authentication/
  [Logon no Facebook]: /pt-br/documentation/articles/mobile-services-how-to-register-facebook-authentication/
  [Logon no Twitter]: /pt-br/documentation/articles/mobile-services-how-to-register-twitter-authentication/
  [Logon no Google]: /pt-br/documentation/articles/mobile-services-how-to-register-google-authentication/
  [Azure Active Directory]: /pt-br/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [2]: ./media/mobile-services-register-authentication/mobile-identity-tab.png
