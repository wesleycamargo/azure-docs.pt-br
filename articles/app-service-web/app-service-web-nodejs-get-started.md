---
title: "Introdução aos aplicativos Web do Node.js no Serviço de Aplicativo do Azure | Microsoft Docs"
description: "Saiba como implantar um aplicativo do Node.js em um aplicativo Web no Serviço de Aplicativo do Azure."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: wpickett
editor: 
ms.assetid: fb2b90c8-02b6-4700-929b-5de9a35d67cc
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: get-started-article
ms.date: 12/16/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 88405a9e67eb748acc9564022283004b5ebfcf48
ms.openlocfilehash: 63210a5539d1e5e5b7d1f5a60048d507e53038a5


---
# <a name="get-started-with-nodejs-web-apps-in-azure-app-service"></a>Introdução aos aplicativos Web do Node.js no Serviço de Aplicativo do Azure
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Este tutorial mostra como criar um aplicativo [Node.js] simples e implantá-lo no [Serviço de Aplicativo do Azure] de um ambiente de linha de comando, como cmd.exe ou bash. As instruções deste tutorial podem ser seguidas em qualquer sistema operacional que possa executar o Node.js.

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

<a name="prereq"></a>

## <a name="cli-versions-to-complete-the-task"></a>Versões da CLI para concluir a tarefa

Você pode concluir a tarefa usando uma das seguintes versões da CLI:

- [CLI do Azure 1.0](app-service-web-nodejs-get-started-cli-nodejs.md) – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos
- [CLI do Azure 2.0 (Visualização)](app-service-web-nodejs-get-started.md) - nossa próxima geração de CLI para o modelo de implantação de gerenciamento de recursos

## <a name="prerequisites"></a>Pré-requisitos
* [Node.js]
* [Bower]
* [Yeoman]
* [Git]
* [Visualização da CLI do Azure 2.0](/cli/azure/install-az-cli2)
* Uma conta do Microsoft Azure. Se não tiver uma conta, você poderá [inscrever-se para uma avaliação gratuita] ou [ativar seus benefícios de assinante do Visual Studio].

> [!NOTE]
> Você pode [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751) sem uma conta do Azure. Crie um aplicativo inicial e brinque com ele por até uma hora: não é necessário cartão de crédito ou compromissos.
> 
> 

## <a name="create-and-configure-a-simple-nodejs-app-for-azure"></a>Criar e configurar um aplicativo simples do Node.js para o Azure
1. Abra o terminal da linha de comando de sua escolha e instale o [gerador Express para Yeoman].
   
        npm install -g generator-express

2. `CD` para uma pasta de trabalho e gere um aplicativo expresso usando a seguinte sintaxe:
   
        yo express
   
    Escolha as seguintes opções quando solicitado:  
   
    `? Would you like to create a new directory for your project?` **Sim**  
    `? Enter directory name` **{appname}**  
    `? Select a version to install:` **MVC**  
    `? Select a view engine to use:` **Jade**  
    `? Select a css preprocessor to use (Sass Requires Ruby):` **Nenhum**  
    `? Select a database to use:` **Nenhum**  
    `? Select a build tool to use:` **Grunt**

3. `CD` para o diretório-raiz do novo aplicativo e inicie-o para verificar se ele é executado no ambiente de desenvolvimento:
   
        npm start
   
    No navegador, navegue até <http://localhost:3000> para verificar se você pode ver a home page do Express. Depois de verificar se o aplicativo é executado corretamente, use `Ctrl-C` para interrompê-lo.

6. Abra o arquivo ./config/config.js na raiz do aplicativo e altere a porta de produção para `process.env.port`. A propriedade `production` do objeto `config` deve ser semelhante ao exemplo a seguir:
   
        production: {
            root: rootPath,
            app: {
                name: 'express1'
            },
            port: process.env.port,
        }
   
    > [!NOTE] 
    > Por padrão, o Serviço de Aplicativo do Azure executa aplicativos Node.js com variáveis de ambiente `production` (`process.env.NODE_ENV="production"`.
    > Portanto, aqui a configuração permite que o aplicativo Node.js no Azure responda a solicitações Web na porta padrão em que iisnode escuta.
    >
    >

7. Abra ./package.json e adicione a propriedade `engines` a [especificar a versão desejada do Node.js](#version).
   
        "engines": {
            "node": "6.9.1"
        }, 

8. Salve as alterações, inicialize um repositório Git na raiz do o aplicativo e confirme o código:
   
        git add .
        git add -f config
        git commit -m "{your commit message}"

## <a name="deploy-your-nodejs-app-to-azure"></a>Implantar o aplicativo Node.js no Azure

1. Faça logon no Azure (é necessária a [CLI do Azure 2.0 Preview](#prereq)):
   
        az login
   
    Siga o prompt para continuar o logon em um navegador com uma conta da Microsoft que tenha sua assinatura do Azure.

3. Defina o usuário de implantação para o Serviço de Aplicativo. Mais tarde, você implantará o código usando essas credenciais.
   
        az appservice web deployment user set --user-name <username> --password <password>

3. Criar um novo [grupo de recursos](../azure-resource-manager/resource-group-overview.md). Para este tutorial de node.js, você não precisa realmente saber o que é.

        az group create --location "<location>" --name my-nodejs-app-group

    Para ver quais possíveis valores você pode usar para `<location>`, use o comando `az appservice list-locations` da CLI.

3. Criar um novo [plano do Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) ”GRATUITO”. Para este tutorial de node.js, saiba que você não será cobrado para aplicativos Web neste plano.

        az appservice plan create --name my-nodejs-appservice-plan --resource-group my-nodejs-app-group --sku FREE

4. Crie um novo aplicativo Web com um nome exclusivo no `<app_name>`.

        az appservice web create --name <app_name> --resource-group my-nodejs-app-group --plan my-nodejs-appservice-plan

5. Configure a implantação Git local para o novo aplicativo Web com o seguinte comando:

        az appservice web source-control config-local-git --name <app_name> --resource-group my-nodejs-app-group

    Você receberá uma saída JSON como esta, o que significa que o repositório Git remoto está configurado:

        {
        "url": "https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git"
        }

6. Adicione a URL no JSON como um Git remoto para seu repositório local (chamado de `azure` para manter a simplicidade).

        git remote add azure https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git
   
7. Implante o código de exemplo no Git remoto do `azure`. Quando solicitado, use as credenciais de implantação que você configurou anteriormente.

        git push azure master
   
    O gerador do Express já fornece um arquivo .gitignore, então, o `git push` não consumirá largura de banda tentando carregar o diretório node_modules/.

9. Por fim, abra o aplicativo do Azure ativo no navegador:
   
        az appservice web browse --name <app_name> --resource-group my-nodejs-app-group
   
    Agora você deve ver o aplicativo Web do Node.js em execução no Serviço de Aplicativo do Azure.
   
    ![Exemplo de navegação para o aplicativo implantado.][deployed-express-app]

## <a name="update-your-nodejs-web-app"></a>Atualizar o aplicativo Web do Node.js
Para fazer atualizações no aplicativo Web do Node.js em execução no Serviço de Aplicativo, basta executar `git add`, `git commit` e `git push` como você fez ao implantar pela primeira vez o aplicativo Web.

## <a name="how-app-service-deploys-your-nodejs-app"></a>Como o Serviço de Aplicativo implanta o aplicativo Node.js
O Serviço de Aplicativo do Azure usa [iisnode] para executar aplicativos Node.js. A Azure CLI 2.0 Preview e o mecanismo do Kudu (implantação do Git) trabalham juntos para proporcionar uma experiência simplificada quando você desenvolve e implanta aplicativos do Node.js por meio da linha de comando. 

* Você pode criar um arquivo iisnode.yml no diretório raiz e usá-lo para personalizar propriedades de iisnode. Todos os parâmetros configuráveis estão documentados [aqui](https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/iisnode.yml).
* Em `git push azure master`, o Kudu automatiza as seguintes tarefas de implantação:
  
  * Se package.json estiver na raiz do repositório, execute `npm install --production`.
  * Gere um Web.config para iisnode que aponte para o script de inicialização em package.json (por exemplo, server.js ou app.js).
  * Personalize web.config para preparar o aplicativo para depuração com Node-Inspector.

## <a name="use-a-nodejs-framework"></a>Usar uma estrutura de Node.js
Se você usa uma estrutura Node.js popular, como [Sails.js][SAILSJS] ou [MEAN.js][MEANJS] para desenvolver os aplicativos, poderá implantá-los no Serviço de Aplicativo. Estruturas populares do Node.js têm suas peculiaridades específicas, e suas dependências de pacotes são atualizadas continuamente. No entanto, o Serviço de Aplicativo disponibiliza os logs stdout e stderr para você, para que possa saber exatamente o que está acontecendo com o aplicativo e fazer as alterações adequadas. Para saber mais, confira [Obter os logs de stdout e stderr do iisnode](#iisnodelog).

Os tutoriais a seguir mostram como trabalhar com uma estrutura específica no Serviço de Aplicativo:

* [Implantar um aplicativo Web do Sails.js no Serviço de Aplicativo do Azure]
* [Criar um aplicativo de chat do Node.js com Socket.IO no Serviço de Aplicativo do Azure]
* [Como usar io.js com Aplicativos Web do Serviço de Aplicativo do Azure]

<a name="version"></a>

## <a name="use-a-specific-nodejs-engine"></a>Usar um mecanismo específico do Node.js
No fluxo de trabalho típico, você instrui o Serviço de Aplicativo a usar um mecanismo específico do Node.js, como faria normalmente em package.json.
Por exemplo:

    "engines": {
        "node": "6.9.1"
    }, 

O mecanismo de implantação do Kudu determina qual mecanismo do Node.js deve ser usado na seguinte ordem:

* Primeiro, examine iisnode.yml para ver se `nodeProcessCommandLine` foi especificado. Em caso afirmativo, use-o.
* Em seguida, examine package.json para ver se `"node": "..."` foi especificado no objeto `engines`. Em caso afirmativo, use-o.
* Escolha uma versão do Node.js padrão por padrão.

Para obter a lista atualizada de todas as versões com suporte do Node.js/NPM no Serviço de Aplicativo do Azure, acesse a seguinte URL para o aplicativo:

    https://<app_name>.scm.azurewebsites.net/api/diagnostics/runtime

> [!NOTE]
> É recomendável que você defina explicitamente o mecanismo do Node.js desejado. A versão padrão do Node.js pode mudar, e talvez você obtenha erros em seu aplicativo Web do Azure, porque a versão padrão do Node.js não é apropriada para seu aplicativo.
> 
> 

<a name="iisnodelog"></a>

## <a name="get-stdout-and-stderr-logs-from-iisnode"></a>Obter os logs de stdout e stderr do iisnode
Para ler os logs do iisnode, siga estas etapas.

> [!NOTE]
> Depois que você concluir essas etapas, os arquivos de log poderão não existir até ocorrer um erro.
> 
> 

1. Abra o arquivo iisnode.yml que a CLI do Azure 2.0 Preview fornece.
2. Defina os dois seguintes parâmetros: 
   
        loggingEnabled: true
        logDirectory: iisnode
   
    Juntos, eles instruem iisnode no Serviço de Aplicativo a colocar a saída de stdout e stderror no diretório D:\home\site\wwwroot\**iisnode**.
3. Salve as alterações e envie-as ao Azure com os seguintes comandos do Git:
   
        git add .
        git commit -m "{your commit message}"
        git push azure master
   
    iisnode agora está configurado. As próximas etapas mostram como acessar esses logs.
4. No navegador, acesse o console de depuração do Kudu para o aplicativo, que é:
   
        https://{appname}.scm.azurewebsites.net/DebugConsole 
   
    Essa URL é diferente da URL do aplicativo Web, com a adição de "*.scm.*" ao nome DNS. para o nome DNS. Se omitir essa adição à URL, você obterá um erro 404.
5. Navegue até D:\home\site\wwwroot\iisnode
   
    ![Navegue até o local dos arquivos de log de iisnode.][iislog-kudu-console-find]
6. Clique no ícone **Editar** do log que você deseja ler. Você também poderá clicar em **Baixar** ou **Excluir**, se desejar.
   
    ![Abrir um arquivo de log de iisnode.][iislog-kudu-console-open]
   
    Agora você pode ver o log para ajudá-lo a depurar sua implantação do Serviço de Aplicativo.
   
    ![Examinar um arquivo de log de iisnode.][iislog-kudu-console-read]

## <a name="debug-your-app-with-node-inspector"></a>Depurar o aplicativo com o Node-Inspector
Se usar o Node-Inspector para depurar aplicativos Node.js, você poderá usá-lo para o aplicativo ativo do Serviço de Aplicativo. O Node-Inspector é pré-instalado na instalação do iisnode para o Serviço de Aplicativo. Se você implantar por meio do Git, o web.config gerado automaticamente do Kudu já conterá toda a configuração necessária para habilitar o Node-Inspector.

Para habilitar o Node-Inspector, siga estas etapas:

1. Abra iisnode.yml na raiz do repositório e especifique os seguintes parâmetros: 
   
        debuggingEnabled: true
        debuggerExtensionDll: iisnode-inspector.dll
2. Salve as alterações e envie-as ao Azure com os seguintes comandos do Git:
   
        git add .
        git commit -m "{your commit message}"
        git push azure master
3. Agora, basta navegar até arquivo de inicialização do aplicativo, conforme especificado pelo script de inicialização no package.json, com a opção /debug adicionada à URL. Por exemplo,
   
        http://{appname}.azurewebsites.net/server.js/debug
   
    Ou,
   
        http://{appname}.azurewebsites.net/app.js/debug

## <a name="more-resources"></a>Mais recursos
* [Especificar uma versão do Node.js em um aplicativo do Azure](../nodejs-specify-node-version-azure-apps.md)
* [Guia de práticas recomendadas e solução de problemas para aplicativos Node.js no Azure](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)
* [Como depurar um aplicativo Web Node.js no Serviço de Aplicativo do Azure](web-sites-nodejs-debug.md)
* [Usando Módulos no Node.js com aplicativos do Microsoft Azure](../nodejs-use-node-modules-azure-apps.md)
* [Aplicativos Web do Serviço de Aplicativo do Azure: Node.js](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx)
* [Centro de Desenvolvedores do Node.js](/develop/nodejs/)
* [Introdução a aplicativos Web no Serviço de Aplicativo do Azure](app-service-web-get-started.md)
* [Explorar o Console de Depuração Super Secret Kudu]

<!-- URL List -->

[Serviço de Aplicativo do Azure]: ../app-service/app-service-value-prop-what-is.md
[ativar seus benefícios de assinante do Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=623901
[Bower]: http://bower.io/
[Criar um aplicativo de chat do Node.js com Socket.IO no Serviço de Aplicativo do Azure]: ./web-sites-nodejs-chat-app-socketio.md
[Implantar um aplicativo Web do Sails.js no Serviço de Aplicativo do Azure]: ./app-service-web-nodejs-sails.md
[Explorar o Console de Depuração Super Secret Kudu]: /documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[gerador Express para Yeoman]: https://github.com/petecoop/generator-express
[Git]: http://www.git-scm.com/downloads
[Como usar io.js com Aplicativos Web do Serviço de Aplicativo do Azure]: ./web-sites-nodejs-iojs.md
[iisnode]: https://github.com/tjanczuk/iisnode/wiki
[MEANJS]: http://meanjs.org/
[Node.js]: http://nodejs.org
[SAILSJS]: http://sailsjs.org/
[inscrever-se para uma avaliação gratuita]: http://go.microsoft.com/fwlink/?LinkId=623901
[web app]: ./app-service-web-overview.md
[Yeoman]: http://yeoman.io/

<!-- IMG List -->

[deployed-express-app]: ./media/app-service-web-nodejs-get-started/deployed-express-app.png
[iislog-kudu-console-find]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-navigate.png
[iislog-kudu-console-open]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-open.png
[iislog-kudu-console-read]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-read.png



<!--HONumber=Jan17_HO3-->


