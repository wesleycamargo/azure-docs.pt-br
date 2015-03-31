

Para autenticar usuários, registrar seu aplicativo com um provedor de identidade e, em seguida, registrar as credenciais de cliente geradas pelo provedor nos Serviços Móveis do Azure.

1. Entre no Portal de Gerenciamento do Azure, clique em **Serviços Móveis** e, em seguida, clique em seu serviço móvel.

2. Clique na guia **Painel** e anote o valor da **URL do Serviço Móvel**. Talvez seja necessário fornecer este valor para o provedor de identidade ao registrar seu aplicativo.

3. Escolha um provedor de identidade com suporte na lista abaixo. Siga as etapas para registrar seu aplicativo com o provedor. Lembre-se de anotar os valores da identidade do cliente e segredo gerados pelo provedor.

 - <a href="/documentation/articles/mobile-services-how-to-register-microsoft-authentication/" target="_blank">Conta da Microsoft</a>
 - <a href="/documentation/articles/mobile-services-how-to-register-facebook-authentication/" target="_blank">Facebook</a>
 - <a href="/documentation/articles/mobile-services-how-to-register-twitter-authentication/" target="_blank">Twitter</a>
 - <a href="/documentation/articles/mobile-services-how-to-register-google-authentication/" target="_blank">Google </a>
 - <a href="/documentation/articles/mobile-services-how-to-register-active-directory-authentication/" target="_blank">Azure Active Directory</a>

    > [AZURE.IMPORTANT]O segredo gerado pelo provedor é uma credencial de segurança importante. Não compartilhe esse segredo com ninguém nem o distribua com seu aplicativo.

4. No Portal de Gerenciamento, clique na guia **Identidade**, digite os valores do identificador do aplicativo e do segredo compartilhados obtidos do provedor de identidade e clique em **Salvar**. O serviço móvel e seu aplicativo agora estão configurados para funcionar com o provedor de autenticação escolhido.

    > [AZURE.IMPORTANT] Verifique se você definiu o URI de redirecionamento correto no site de desenvolvedores do provedor de identidade. Conforme descrito nas instruções vinculadas para cada provedor acima, o URI de redirecionamento pode ser diferente para um serviço de back-end do .NET em comparação com um serviço de back-end do JavaScript. Um URI de redirecionamento configurado incorretamente pode fazer com que a tela de logon não seja exibida corretamente e que o aplicativo funcione incorretamente de maneiras inesperadas.

<!--HONumber=47-->
