

Para ser capaz de autenticar usuários, você deve registrar seu aplicativo com um provedor de identidade. Você deve registrar o segredo do cliente gerado pelo provedor com os Serviços Móveis.

1. Faça logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e, em seguida, clique no seu serviço móvel.

   	![](./media/mobile-services-register-authentication/mobile-services-selection.png)

2. Clique na guia **Painel** e anote o valor da **URL do Serviço Móvel**.

   	![](./media/mobile-services-register-authentication/mobile-service-uri.png)

    Você talvez precise fornecer esse valor para o provedor de identidade ao registrar seu aplicativo.

3. Na lista abaixo, escolha um provedor de identidade com suporte e siga as etapas para registrar seu aplicativo com esse provedor:

 - <a href="/pt-br/develop/mobile/how-to-guides/register-for-microsoft-authentication/" target="_blank">Conta da Microsoft</a>
 - <a href="/pt-br/develop/mobile/how-to-guides/register-for-facebook-authentication/" target="_blank">Logon no Facebook</a>
 - <a href="/pt-br/develop/mobile/how-to-guides/register-for-twitter-authentication/" target="_blank">Logon no Twitter</a>
 - <a href="/pt-br/develop/mobile/how-to-guides/register-for-google-authentication/" target="_blank">Logon no Google</a>
 - <a href="/pt-br/documentation/articles/mobile-services-how-to-register-active-directory-authentication/" target="_blank">Azure Active Directory</a>


    Lembre-se de anotar os valores da identidade do cliente e segredo gerados pelo provedor.

    <div class="dev-callout"><b>Observação de Segurança</b>
	<p>O segredo gerado pelo provedor é uma credencial de segurança importante. Não compartilhe esse segredo com ninguém nem o distribua com seu aplicativo.</p>
    </div>

4. De volta ao Portal de Gerenciamento, clique na guia **Identidade**, insira os valores do identificador do aplicativo e do segredo compartilhado obtidos do seu provedor de identidade e clique em **Salvar**.

   	![](./media/mobile-services-register-authentication/mobile-identity-tab.png)

	O serviço móvel e seu aplicativo agora estão configurados para funcionar com o provedor de autenticação escolhido.

<!-- URLs. -->
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/

