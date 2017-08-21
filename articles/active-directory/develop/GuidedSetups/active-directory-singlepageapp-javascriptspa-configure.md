
### <a name="create-an-application-express"></a>Criar um aplicativo (Expresso)
Agora é necessário registrar seu aplicativo no Portal de Registro de Aplicativos da Microsoft:

1.  Registre o aplicativo por meio do [Portal de Registro de Aplicativos da Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure)
2.  Insira um nome para o aplicativo e seu email
3.  Certifique-se de que a opção *Instalação Guiada* está marcada
4.  Siga as instruções para obter a ID do aplicativo e colá-lo no código

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Adicionar as informações de registro do aplicativo à sua solução (Avançado)

1. Acesse o [Portal de Registro de Aplicativos da Microsoft](https://apps.dev.microsoft.com/portal/register-app) para registrar um aplicativo
2. Insira um nome para o aplicativo e seu email 
3. Certifique-se de que a opção *Instalação Guiada* está desmarcada
4.  Clique em `Add Platform` e selecione `Web`
5. Adicione uma URL de redirecionamento ao seu aplicativo. Uma URL de redirecionamento é a URL para sua página `index.html` com base em seu servidor Web
6. Clique em *Salvar*

> #### <a name="visual-studio-instructions-for-obtaining-redirect-url-using-ssl"></a>Instruções do Visual Studio para obter a URL de redirecionamento usando SSL
> Se você estiver usando o Visual Studio, configure seu projeto para usar o SSL e, em seguida, use a URL do SSL para configurar as informações de registro do seu aplicativo usando as instruções abaixo:
> 1.    No Gerenciador de Soluções, selecione o projeto e examine a janela `Properties` (se uma janela Propriedades não for exibida, pressione F4)
> 2.    Altere `SSL Enabled` para `True`
> 3.    Copie o valor de `SSL URL` para a área de transferência:<br/> ![Propriedades do projeto](media/active-directory-singlepageapp-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 4.    Selecione o menu `Project` e selecione `{Project} Properties...` (em que {Project} é o nome do seu projeto)
> 5.    Abra a guia `Web`
> 6.    Cole o valor de `SSL URL` no campo `Project Url`
> 7.    Volte para o Portal de Registro do Aplicativo e cole o valor no `Redirect URL` como URL de redirecionamento, em seguida, clique em *salvar*


#### <a name="configure-your-javascript-spa-application"></a>Configure seu aplicativo JavaScript SPA

1.  Crie um arquivo chamado `msalconfig.js` que contém as informações de registro do aplicativo. Se você estiver usando o Visual Studio, selecione o projeto (pasta raiz do projeto), clique com botão direito do mouse e selecione: `Add` > `New Item` > `JavaScript File`. Nomeie-o `msalconfig.js`
2.  Adicione o seguinte código ao seu arquivo `msalconfig.js`:

```javascript
var msalconfig = {
    clientID: "Enter_the_Application_Id_here",
    redirectUri: location.origin
};
```
<ol start="3">
<li>
Substitua <code>Enter_the_Application_Id_here</code> pela ID do Aplicativo que você acabou de registrar
</li>
</ol>