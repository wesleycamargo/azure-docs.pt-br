
## <a name="setting-up-your-web-server-or-project"></a>Configuração do servidor Web ou do projeto

> Prefere baixar este projeto do Visual Studio de exemplo? [Baixe um projeto](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/master.zip) e vá para a [Etapa de configuração](#create-an-application-express) para configurar o exemplo de código antes de executá-lo.

## <a name="prerequisites"></a>Pré-requisitos
Um servidor Web local como [http-server](https://www.npmjs.com/package/http-server/), [Node.js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core) ou a integração do IIS Express com o [Visual Studio 2017](https://www.visualstudio.com/downloads/) é necessário para executar a instalação guiada. 

As instruções neste guia são baseadas no Visual Studio 2017, mas fique à vontade para usar qualquer outro ambiente de desenvolvimento ou editor de HTML/JavaScript.


## <a name="create-your-project-visual-studio-only"></a>Criar seu projeto (apenas Visual Studio)

Se você estiver usando o Visual Studio e estiver criando um novo projeto, siga as etapas abaixo para criar uma nova solução do Visual Studio:
1.  No Visual Studio:  `File` > `New` > `Project`
2.  Em `Visual C#\Web`, selecione `ASP.NET Web Application (.NET Framework)`
3.  Nomeie o aplicativo e clique em *OK*
4.  Em `New ASP.NET Web Application`, selecione `Empty`


## <a name="create-your-single-page-applications-ui"></a>Criar a interface do usuário do aplicativo de página única
1.  Crie um arquivo index.html para seu JavaScript SPA. Se você estiver usando o Visual Studio, selecione o projeto (pasta raiz do projeto), clique com botão direito do mouse e selecione: `Add` > `New Item` > `HTML page` e nomeie-o index.html
2.  Adicione o seguinte código à sua página:
```html
<!DOCTYPE html>
<html>
    <head>
        <link href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
        <title>JavaScript SPA Guided Setup</title>
    </head>
    <body style="margin: 40px">
        <button id="callGraphButton" type="button" class="btn btn-primary" onclick="callGraphAPI()">Call Microsoft Graph API</button>
        <div id="errorMessage" class="text-danger"></div>
        <div class="hidden">
            <h3>Graph API Call Response</h3>
            <pre class="well" id="graphResponse"></pre>
        </div>
        <div class="hidden">
            <h3>Access Token</h3>
            <pre class="well" id="accessToken"></pre>
        </div>
        <div class="hidden">
            <h3>ID Token Claims</h3>
            <pre class="well" id="userInfo"></pre>
        </div>
        <button id="signOutButton" type="button" class="btn btn-primary hidden" onclick="signOut()">Sign out</button>

        <script src="//secure.aadcdn.microsoftonline-p.com/lib/0.1.1/js/msal.min.js"></script>
        <script type="text/javascript" src="msalconfig.js"></script>
    
        <!-- The 'bluebird' and 'fetch' references below are required if you need to run this application on Internet Explorer -->
        <script src="//cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
        <script src="//cdnjs.cloudflare.com/ajax/libs/fetch/2.0.3/fetch.min.js"></script>
    </body>
</html>
````
