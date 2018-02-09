## <a name="test-your-code"></a>Testar seu código

### <a name="test-with-visual-studio"></a>Testar com Visual Studio
Se você estiver usando o Visual Studio, pressione **F5** para executar seu projeto. O navegador abre para o local http://<span></span>localhost:{port} e você visualiza o bota **Chamar a API do Microsoft Graph**.

<p/><!-- --> 

### <a name="test-with-python-or-other-web-server"></a>Testar com Python ou outro servidor Web
Se você não estiver usando o Visual Studio, verifique se o servidor Web está iniciado. Configure o servidor para escutar uma porta TCP com base no local do seu arquivo **index.html**. Para o Python, comece a escutar a porta, executando o terminal do prompt de comando da pasta de aplicativo:
 
```bash
python -m http.server 8080
```
Abra o navegador e digite http://<span></span>localhost:8080 ou http://<span></span>localhost:{port} onde **port** is a é a porta que o seu servidor Web está escutando. Você deve ver o conteúdo do seu arquivo index.html e o botão **Chamar a API do Microsoft Graph**.

## <a name="test-your-application"></a>Teste seu aplicativo

Depois que o navegador carregar o arquivo index.html, selecione **Chamar a API do Microsoft Graph**. Na primeira vez em que você executar o aplicativo, o navegador redirecionará para o ponto de extremidade v2.0 do Azure AD (Microsoft Azure Active Directory) e você será solicitado a entrar:
 
![Entre na sua conta SPA do JavaScript](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="provide-consent-for-application-access"></a>Fornecer autorização para acesso de aplicativo

Na primeira vez em que entrar no seu aplicativo, você também será solicitado a fornecer o consentimento para permitir que o aplicativo acesse seu perfil e entrar no:

![Forneça sua autorização para acesso do aplicativo](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Veja os resultados de aplicativo
Depois de entrar, você deverá ver as informações do seu perfil de usuário na caixa **Resposta de Chamada da API do Graph**.
 
![Resultados esperados da chamada à API do Microsoft Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

Você também deve ver as informações básicas sobre o token que foi adquirido nas caixas **Token de Acesso** e **Declarações de Token de ID** boxes.

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre escopos e permissões delegadas

A API do Microsoft Graph requer o escopo **user.read** para ler o perfil do usuário. Este escopo é automaticamente adicionado por padrão em cada aplicativo registrado no portal de registro. Outras APIs do Microsoft Graph, bem como APIs personalizadas do servidor de back-end, podem exigir escopos adicionais. A API do Microsoft Graph requer o escopo **Calendars.Read** para listar os calendários do usuário.

Para acessar os calendários do usuário no contexto de um aplicativo, adicione a permissão delegada **Calendars.Read** às informações de registro do aplicativo. Em seguida, adicione o escopo **Calendars.Read** à chamada **acquireTokenSilent**. 

>[!NOTE]
>Talvez o usuário precise fornecer autorizações adicionais à medida que o número de escopos aumentar.

Se uma API de back-end não requerer um escopo (não recomendado), você poderá usar o **clientId** como o escopo nas chamadas **acquireTokenSilent** e **acquireTokenRedirect**.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
