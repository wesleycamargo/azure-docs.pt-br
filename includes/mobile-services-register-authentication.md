
Primeiro, você precisa registrar o aplicativo em um provedor de identidade no respectivo site e, em seguida, defina essas credenciais no seu serviço móvel.

1. No [Portal de Gerenciamento do Azure], navegue até seu serviço móvel, clique em **Painel** e anote o valor de **URL do Serviço Móvel**.

2. Registre seu aplicativo em um dos seguintes provedores de identidade com suporte.

	* [Google](mobile-services-how-to-register-google-authentication.md)
	* [Facebook](mobile-services-how-to-register-facebook-authentication.md)
	* [Twitter](mobile-services-how-to-register-twitter-authentication.md)
	* [Microsoft](mobile-services-how-to-register-microsoft-authentication.md)
	* [Active Directory do Azure](mobile-services-how-to-register-active-directory-authentication.md).  
	
    >[AZURE.IMPORTANT]Não se esqueça de definir corretamente o URI de redirecionamento para o serviço móvel no site do desenvolvedor do provedor de identidade. Como descrito nas instruções vinculadas de cada provedor acima, o caminho da URL de redirecionamento é diferente para um serviço móvel de back-end .NET (`/signin-<provider>`) em comparação com um serviço móvel de back-end JavaScript (`/login/<provider>`). Um URI de redirecionamento configurado incorretamente impede o cliente de entrar no aplicativo. <br/>Não distribua nem compartilhe o segredo do cliente.

3. De volta ao serviço móvel no [Portal de Gerenciamento do Azure], clique na guia **Identidade** e insira os valores do segredo e da ID do aplicativo que você acabou de obter do provedor de identidade.

Agora que você configurou o aplicativo e o serviço móvel para oferecer suporte a um provedor de identidade para autenticação, é possível repetir essas etapas para adicionar suporte a mais provedores de identidade.

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/

<!---HONumber=62-->