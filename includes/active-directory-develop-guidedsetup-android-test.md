## <a name="test-your-code"></a>Testar seu código

1. Implante o código no dispositivo/emulador.

2. Quando você estiver pronto para testar seu aplicativo, use uma conta do Azure Active Directory (conta corporativa ou de estudante) ou uma conta da Microsoft (live.com, outlook.com) para se conectar. 

    ![Teste seu aplicativo](media/active-directory-develop-guidedsetup-android-test/mainwindow.png)
    <br/><br/>
    ![Nome de usuário e senha](media/active-directory-develop-guidedsetup-android-test/usernameandpassword.png)

### <a name="provide-consent-for-application-access"></a>Fornecer autorização para acesso de aplicativo
Na primeira vez que você entrar no seu aplicativo, você também será solicitado a fornecer autorização para permitir que o aplicativo acesse seu perfil e o conecte, como mostrado aqui: 

![Forneça sua autorização para acesso do aplicativo](media/active-directory-develop-guidedsetup-android-test/androidconsent.png)


### <a name="view-application-results"></a>Veja os resultados de aplicativo
Depois de entrar, você deve ver os resultados retornados pela chamada à API do Microsoft Graph. A chamada ao ponto de extremidade **me** da API do Microsoft Graph retorna o [perfil do usuário](https://graph.microsoft.com/v1.0/me). Para obter uma lista de pontos de extremidade comuns do Microsoft Graph, consulte [Documentação para desenvolvedores da API do Microsoft Graph](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries).

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre escopos e permissões delegadas

A API do Microsoft Graph requer o escopo *user.read* para ler o perfil do usuário. Esse escopo é adicionado automaticamente, por padrão, a cada aplicativo que é registrado no Portal de Registro de Aplicativos. Outras APIs do Microsoft Graph, bem como APIs personalizadas do servidor de back-end, podem exigir escopos adicionais. A API do Microsoft Graph requer o escopo *Calendars.Read* para listar os calendários do usuário. 

Para acessar os calendários do usuário no contexto de um aplicativo, adicione a permissão delegada *Calendars.Read* às informações de registro do aplicativo. Em seguida, adicione o escopo *Calendars.Read* à chamada `acquireTokenSilent`. 

>[!NOTE]
>Talvez o usuário precise fornecer autorizações adicionais à medida que o número de escopos aumentar.

<!--end-collapse-->

[!INCLUDE [Help and support](active-directory-develop-help-support-include.md)]
