

As novas APIs do Twitter v1.1 exigem que o aplicativo seja autenticado para acessar recursos. Primeiro, você precisa obter as credenciais necessárias para solicitar acesso usando o OAuth 2.0. Em seguida, você vai armazená-las com segurança nas configurações do aplicativo para seu serviço móvel.

1. Se ainda não o fez, conclua as etapas do tópico <a href="/pt-br/documentation/articles/mobile-services-how-to-register-twitter-authentication/" target="_blank">Registrar seus aplicativos para fazer logon no Twitter com os Serviços Móveis</a>. 
  
  	O Twitter gera as credenciais necessárias para habilitar seu acesso às APIs do Twitter v1.1. Você pode obter essas credenciais no site de Desenvolvedores do Twitter. 

2. Navegue até o site <a href="http://go.microsoft.com/fwlink/p/?LinkId=268300" target="_blank">Desenvolvedores do Twitter</a>, entre usando suas credenciais de conta do Twitter, navegue até **Meus Aplicativos** e selecione seu aplicativo do Twitter.

    ![](./media/mobile-services-register-twitter-access/mobile-twitter-my-apps.png)

3. Na guia **Detalhes** do aplicativo, anote os seguintes valores:

	+ **Chave do consumidor**
	+ **Segredo do consumidor**
	+ **Token de acesso**
	+ **Segredo do token de acesso**

	![](./media/mobile-services-register-twitter-access/mobile-twitter-app-secrets.png)

4. Faça o logon no [Portal de Gerenciamento do Microsoft Azure], clique em **Serviços Móveis** e clique em seu serviço móvel.

5. Clique na guia **Identidade**, insira os valores de **Chave do consumidor** e **Segredo do consumidor** obtidos no Twitter e clique em **Salvar**. 

	![](./media/mobile-services-register-twitter-access/mobile-identity-tab-twitter-only.png)

2. Clique na guia **Configurar**, role para baixo até **Configurações do aplicativo** e digite um par de **Nome** e **Valor** para cada uma das opções a seguir que você obteve no site do Twitter e clique em **Salvar**.

	+ `TWITTER_ACCESS_TOKEN`
	+ `TWITTER_ACCESS_TOKEN_SECRET`

	![](./media/mobile-services-register-twitter-access/mobile-schedule-job-app-settings.png)

	Isso armazena o token de acesso do Twitter nas configurações do aplicativo. Como as credenciais do consumidor na guia **Identidade**, as credenciais de acesso também são armazenadas criptografadas nas configurações do aplicativo, e você pode acessá-las em seus scripts de servidor sem embuti-las em código no arquivo de script. Para obter mais informações, consulte [Configurações do aplicativo].

<!-- URLs. -->
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[WindowsAzure.com]: http://www.windowsazure.com/
[Portal de Gerenciamento do Windows Azure]: https://manage.windowsazure.com/
[Registrar seus aplicativos para logon no Twitter com os Serviços Móveis]: /pt-br/documentation/articles/mobile-services-how-to-register-twitter-authentication
[Desenvolvedores do Twitter]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Configurações do aplicativo]: http://msdn.microsoft.com/pt-br/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
