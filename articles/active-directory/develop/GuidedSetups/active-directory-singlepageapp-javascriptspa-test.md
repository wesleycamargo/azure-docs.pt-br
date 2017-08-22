
## <a name="test-your-code"></a>Testar seu código

Se você estiver usando o Visual Studio, pressione `F5` para executar seu projeto. O navegador será aberto e direcionará você para *http://localhost:{porta}*, em que você verá o botão *Chamar API do Microsoft Graph*.

Se você não estiver usando o Visual Studio, certifique-se de que seu servidor Web foi iniciado e de que a pasta que contém o aplicativo Web JavaScript foi configurada no seu servidor Web. Abra o navegador e digite *http://localhost: {porta}/caminho* – em que *porta* corresponde à porta que o servidor Web está escutando e *caminho* é o caminho para o index.html.

Clique no botão *Chamar a API do Microsoft Graph*. Se esta for a primeira vez, uma janela pop-up será exibida solicitando que o usuário entre.
 
![Captura de tela de exemplo](media/active-directory-singlepageapp-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="consent"></a>Consentimento
Na primeira vez que você entrar no aplicativo, será apresentada uma tela de consentimento semelhante à tela abaixo, que você precisará aceitar explicitamente:

 ![Tela de consentimento](media/active-directory-singlepageapp-javascriptspa-test/javascriptspaconsent.png)

Como você está consultando a API do Microsoft Graph, talvez você veja outra página de consentimento. Isso ocorre devido ao *consentimento dinâmico* – em que um consentimento é necessário para cada escopo solicitado pelo aplicativo. Para o aplicativo de exemplo gerado por este guia, o escopo *user.read* é fornecido e, portanto, é necessário dar consentimento para que esse aplicativo leia o perfil do usuário.

> [!IMPORTANT]
> No momento, devido a um problema conhecido com o javascript *msal*, é necessário desabilitar o bloqueador de pop-up em navegadores como o Chrome e o Firefox para que a tela *consentimento dinâmico* possa funcionar adequadamente. É necessário desabilitar o bloqueador de pop-up na primeira vez em que um usuário chama a API do Microsoft Graph usando `acquireTokenPopup`.

### <a name="expected-results"></a>Resultados esperados
Você deverá ver as informações de perfil do usuário retornadas pela resposta à chamada à API do Microsoft Graph.
 
 ![Resultados](media/active-directory-singlepageapp-javascriptspa-test/javascriptsparesults.png)

Você também verá informações básicas sobre o token adquirido nas caixas *Token de Acesso* e *Declarações do Token de ID*.

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre escopos e permissões delegadas

A API do Microsoft Graph requer o escopo `user.read` para ler o perfil do usuário. Esse escopo é adicionado automaticamente, por padrão, a cada aplicativo que é registrado em nosso portal de registro. Algumas outras APIs do Microsoft Graph, bem como APIs personalizadas do servidor de back-end, podem exigir escopos adicionais. Por exemplo, para o Microsoft Graph, o escopo `Calendars.Read` é necessário para listar os calendários do usuário. Para acessar o calendário do usuário no contexto de um aplicativo, é necessário adicionar a permissão delegada `Calendars.Read` às informações de registro do aplicativo e, em seguida, adicionar o escopo `Calendars.Read` à chamada `acquireTokenSilent`. Talvez o usuário precise fornecer consentimentos adicionais à medida que o número de escopos aumentar.

Se uma API de back-end não exigir um escopo (não recomendado), será possível usar o `clientId` como o escopo nas chamadas `acquireTokenSilent` e/ou `acquireTokenPopup`.

<!--end-collapse-->
