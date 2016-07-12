<properties
	pageTitle="Introdução aos aplicativos Web do Node.js no Serviço de Aplicativo do Azure"
	description="Saiba como implantar um aplicativo do Node.js em um aplicativo Web no Serviço de Aplicativo do Azure."
	services="app-service\web"
	documentationCenter="nodejs"
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="get-started-article"
	ms.date="07/01/2016"
	ms.author="cephalin"/>

# Introdução aos aplicativos Web do Node.js no Serviço de Aplicativo do Azure

[AZURE.INCLUDE [guias](../../includes/app-service-web-get-started-nav-tabs.md)]

Este tutorial mostra como criar um aplicativo [Node.js][NODEJS] simples e implantá-lo em um [aplicativo Web] no [Serviço de Aplicativo do Azure] de um ambiente de linha de comando, como cmd.exe ou bash. As instruções deste tutorial podem ser seguidas em qualquer sistema operacional que seja capaz de executar o Node.js.

<a name="prereq"></a>
## Pré-requisitos

- **Node.js** ([clique aqui para instalar][NODEJS])
- **Bower** ([clique aqui para instalar][BOWER])
- **Yeoman** ([clique aqui para instalar][YEOMAN])
- **Git** ([clique aqui para instalar][GIT])
- **CLI do Azure** ([clique aqui para instalar][Azure CLI])
- Uma conta do Microsoft Azure. Se não tiver uma conta, você poderá [inscrever-se para uma avaliação gratuita] ou [ativar seus benefícios de assinante do Visual Studio].

## Criar e implantar um aplicativo web simples do Node.js

1. Abra o terminal da linha de comando de sua escolha e instale o [gerador Express para Yeoman].

        npm install -g generator-express

2. `CD` para uma pasta de trabalho e gere um aplicativo expresso usando a seguinte sintaxe:

        yo express
        
    Escolha as seguintes opções quando solicitado:

    `? Would you like to create a new directory for your project?` **Sim** `? Enter directory name` **{nome\_aplicativo}** `? Select a version to install:` **MVC** `? Select a view engine to use:` **Jade** `? Select a css preprocessor to use (Sass Requires Ruby):` **Nenhum** `? Select a database to use:` **Nenhum** `? Select a build tool to use:` **Grunt**

3. `CD` para o diretório-raiz do novo aplicativo e inicie-o para verificar se ele é executado no ambiente de desenvolvimento:

        npm start

    No navegador, navegue até <http://localhost:3000> para verificar se você pode ver a home page do Express. Depois de verificar se o aplicativo é executado corretamente, use `Ctrl-C` para interrompê-lo.
    
1. Faça logon no Azure da seguinte forma (é necessária a [CLI do Azure](#prereq) para isso):

        azure login

    Siga o prompt para continuar o logon em um navegador com uma conta da Microsoft que tenha sua assinatura do Azure.

2. Verifique se você está no diretório raiz do aplicativo e crie o recurso de aplicativo do Serviço de Aplicativo no Azure com um nome de aplicativo exclusivo com o próximo comando. Por exemplo: http://{appname}.azurewebsites.net

        azure site create --git {appname}

    Siga os prompts para selecionar uma região do Azure para a qual implantar. Se nunca tiver configurado credenciais de implantação do Git/FTP para sua assinatura do Azure, você também será solicitado a criá-las.

3. Abra o arquivo ./config/config.js na raiz do aplicativo e altere a porta de produção para `process.env.port`. A propriedade `production` do objeto `config` deve ser semelhante ao exemplo a seguir.

        production: {
            root: rootPath,
            app: {
                name: 'express1'
            },
            port: process.env.port,
        }

    Isso permite que o aplicativo Node.js responda a solicitações Web na porta padrão que iisnode escuta.
    
4. Salve as alterações e use o git para implantar o aplicativo no Azure:

        git add .
        git commit -m "{your commit message}"
        git push azure master

    O gerador do Express já fornece um arquivo .gitignore, então, o `git push` não consumirá largura de banda tentando carregar o diretório node\_modules/.

5. Por fim, abra o aplicativo do Azure ativo no navegador:

        azure site browse

    Agora você deve ver o aplicativo Web do Node.js em execução no Serviço de Aplicativo do Azure.
    
    ![Exemplo de navegação para o aplicativo implantado.][deployed-express-app]

## Atualizar o aplicativo Web do Node.js

Para fazer atualizações no aplicativo Web do Node.js em execução no Serviço de Aplicativo, basta executar `git add`, `git commit` e `git push` como você fez ao implantar pela primeira vez o aplicativo Web.
     
## Como o Serviço de Aplicativo implanta o aplicativo Node.js

O Serviço de Aplicativo do Azure usa [iisnode] para executar aplicativos Node.js. A CLI do Azure e o mecanismo do Kudu (implantação do Git) trabalham juntos para proporcionar uma experiência simplificada quando você desenvolve e implanta aplicativos do Node.js por meio da linha de comando.

- `azure site create --git` reconhece o padrão comum do Node.js do server.js ou app.js e cria um iisnode.yml no diretório-raiz. Você pode usar esse arquivo para personalizar o iisnode.
- Em `git push azure master`, o Kudu automatiza as seguintes tarefas de implantação:

    - Se package.json estiver na raiz do repositório, execute `npm install --production`.
    - Gere um Web.config para iisnode que aponte para o script de inicialização em package.json (por exemplo, server.js ou app.js).
    - Personalize web.config para preparar o aplicativo para depuração com Node-Inspector.
    
## Usar uma estrutura de Node.js

Se você usa uma estrutura Node.js popular, como [Sails.js][SAILSJS] ou [MEAN.js][MEANJS] para desenvolver os aplicativos, pode implantá-los no Serviço de Aplicativo. Estruturas populares do Node.js têm suas peculiaridades específicas, e suas dependências de pacotes são atualizadas continuamente. No entanto, o Serviço de Aplicativo disponibiliza os logs stdout e stderr para você, para que possa saber exatamente o que está acontecendo com o aplicativo e fazer as alterações adequadas. Para saber mais, confira [Obter os logs de stdout e stderr do iisnode](#iisnodelog).

Os tutoriais a seguir mostram como trabalhar com uma estrutura específica no Serviço de Aplicativo:

- [Implantar um aplicativo Web do Sails.js no Serviço de Aplicativo do Azure]
- [Criar um aplicativo de chat do Node.js com Socket.IO no Serviço de Aplicativo do Azure]
- [Como usar io.js com Aplicativos Web do Serviço de Aplicativo do Azure]

## Usar um mecanismo específico do Node.js

No fluxo de trabalho típico, você pode instruir o Serviço de Aplicativo a usar um mecanismo específico do Node.js, como faria normalmente em package.json. Por exemplo:

    "engines": {
        "node": "5.5.0"
    }, 

O mecanismo de implantação do Kudu determina qual mecanismo do Node.js deve ser usado na seguinte ordem:

- Primeiro, examine iisnode.yml para ver se `nodeProcessCommandLine` foi especificado. Em caso afirmativo, use-o.
- Em seguida, examine package.json para ver se `"node": "..."` foi especificado no objeto `engines`. Em caso afirmativo, use-o.
- Escolha uma versão do Node.js padrão por padrão.

<a name="iisnodelog"></a>
## Obter os logs de stdout e stderr do iisnode

Para ler logs de iisnode, use as etapas a seguir.

> [AZURE.NOTE] Depois que você concluir essas etapas, os arquivos de log poderão não existir até ocorrer um erro.

1. Abra o arquivo iisnode.yml que a CLI do Azure fornece.

2. Defina os dois seguintes parâmetros:

        loggingEnabled: true
        logDirectory: iisnode
    
    Juntos, eles instruem iisnode no Serviço de Aplicativo a colocar a saída de stdout e stderror no diretório D:\\home\\site\\wwwroot**iisnode**.

3. Salve as alterações e envie-as ao Azure com os seguintes comandos do Git:

        git add .
        git commit -m "{your commit message}"
        git push azure master
   
   iisnode agora está configurado. As próximas etapas mostram como acessar esses logs.
     
4. No navegador, acesse o console de depuração do Kudu para o aplicativo, que é:

        https://{appname}.scm.azurewebsites.net/DebugConsole 

    Observe que essa URL é diferente da URL do aplicativo Web, com a adição de "*.scm.*" ao nome DNS. Se omitir essa adição à URL, você obterá um erro 404.

5. Navegue até D:\\home\\site\\wwwroot\\iisnode

    ![Navegue até o local dos arquivos de log de iisnode.][iislog-kudu-console-find]

6. Clique no ícone **Editar** do log que você deseja ler. Você também poderá clicar em **Baixar** ou **Excluir**, se desejar.

    ![Abrir um arquivo de log de iisnode.][iislog-kudu-console-open]

    Agora você pode ver o log para ajudá-lo a depurar sua implantação do Serviço de Aplicativo.
    
    ![Examinar um arquivo de log de iisnode.][iislog-kudu-console-read]

## Depurar o aplicativo com o Node-Inspector

Se usar o Node-Inspector para depurar aplicativos Node.js, você poderá usá-lo para o aplicativo ativo do Serviço de Aplicativo. O Node-Inspector é pré-instalado na instalação do iisnode para o Serviço de Aplicativo. Se você implantar por meio do Git, o web.config gerado automaticamente do Kudu já conterá toda a configuração necessária para habilitar o Node-Inspector.

Para habilitar o Node-Inspector, siga estas etapas:

1. Abra iisnode.yml na raiz do repositório e especifique os seguintes parâmetros:

        debuggingEnabled: true
        debuggerExtensionDll: iisnode-inspector.dll

3. Salve as alterações e envie-as ao Azure com os seguintes comandos do Git:

        git add .
        git commit -m "{your commit message}"
        git push azure master
   
4. Agora, basta navegar até arquivo de inicialização do aplicativo, conforme especificado pelo script de inicialização no package.json, com a opção /debug adicionada à URL. Por exemplo,

        http://{appname}.azurewebsites.net/server.js/debug
    
    Ou,
    
        http://{appname}.azurewebsites.net/app.js/debug

## Mais recursos

- [Especificar uma versão do Node.js em um aplicativo do Azure](../nodejs-specify-node-version-azure-apps.md)
- [Guia de práticas recomendadas e solução de problemas para aplicativos Node.js no Azure](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)
- [Como depurar um aplicativo Web Node.js no Serviço de Aplicativo do Azure](web-sites-nodejs-debug.md)
- [Usando Módulos no Node.js com aplicativos do Microsoft Azure](../nodejs-use-node-modules-azure-apps.md)
- [Aplicativos Web do Serviço de Aplicativo do Azure: Node.js](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx)
- [Centro de Desenvolvedores do Node.js](/develop/nodejs/)
- [Introdução a aplicativos Web no Serviço de Aplicativo do Azure](app-service-web-get-started.md)
- [Explorar o Console de Depuração Super Secret Kudu]

<!-- URL List -->

[Azure CLI]: ../xplat-cli-install.md
[Serviço de Aplicativo do Azure]: ../app-service/app-service-value-prop-what-is.md
[ativar seus benefícios de assinante do Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=623901
[BOWER]: http://bower.io/
[Criar um aplicativo de chat do Node.js com Socket.IO no Serviço de Aplicativo do Azure]: ./web-sites-nodejs-chat-app-socketio.md
[Implantar um aplicativo Web do Sails.js no Serviço de Aplicativo do Azure]: ./app-service-web-nodejs-sails.md
[Explorar o Console de Depuração Super Secret Kudu]: /documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[gerador Express para Yeoman]: https://github.com/petecoop/generator-express
[GIT]: http://www.git-scm.com/downloads
[Como usar io.js com Aplicativos Web do Serviço de Aplicativo do Azure]: ./web-sites-nodejs-iojs.md
[iisnode]: https://github.com/tjanczuk/iisnode/wiki
[MEANJS]: http://meanjs.org/
[NODEJS]: http://nodejs.org
[SAILSJS]: http://sailsjs.org/
[inscrever-se para uma avaliação gratuita]: http://go.microsoft.com/fwlink/?LinkId=623901
[aplicativo Web]: ./app-service-web-overview.md
[YEOMAN]: http://yeoman.io/

<!-- IMG List -->

[deployed-express-app]: ./media/app-service-web-nodejs-get-started/deployed-express-app.png
[iislog-kudu-console-find]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-navigate.png
[iislog-kudu-console-open]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-open.png
[iislog-kudu-console-read]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-read.png

<!---HONumber=AcomDC_0706_2016-->