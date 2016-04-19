<properties
	pageTitle="Implantar um aplicativo Web Sails.js no Serviço de Aplicativo do Azure"
	description="Saiba como implantar um aplicativo do Node.js no Serviço de Aplicativo do Azure. Este tutorial mostra como implantar um aplicativo Web Sails.js."
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
	ms.topic="article"
	ms.date="03/31/2016"
	ms.author="cephalin"/>

# Implantar um aplicativo Web Sails.js no Serviço de Aplicativo do Azure

Este tutorial mostra como implantar um aplicativo Sails.js no Serviço de Aplicativo do Azure. No processo, você pode reunir algum conhecimento geral sobre como configurar seu aplicativo Node.js para a execução no Serviço de Aplicativo.

## Pré-requisitos

- Node.js. Os binários de instalação estão [aqui](https://nodejs.org/).
- Sails.js. As instruções de instalação estão [aqui](http://sailsjs.org/get-started).
- Conhecimento prático dos Sails.js. Este tutorial não se destina a ajudá-lo com problemas relacionados à execução do Sail.js em geral.
- Git. Os binários de instalação estão [aqui](http://www.git-scm.com/downloads).
- CLI do Azure. As instruções de instalação estão [aqui](../xplat-cli-install.md).
- Uma conta do Microsoft Azure. Se não tiver uma conta, você poderá [inscrever-se para uma avaliação gratuita](/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar seus benefícios de assinante do Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

>[AZURE.NOTE] Para ver o Serviço de Aplicativo do Azure em ação antes de se inscrever para uma conta do Azure, acesse [Experimente o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751). Lá, você poderá criar imediatamente um aplicativo de curta duração inicial no Serviço de Aplicativo — sem exigência de cartão de crédito e sem compromissos.

## Etapa 1: Criar um aplicativo Sails.js no seu ambiente de desenvolvimento

Primeiro, crie rapidamente um aplicativo Sails.js padrão seguindo estas etapas:

1. Abra o terminal de linha de comando de sua escolha e `CD` para um diretório de trabalho.

2. Crie um novo aplicativo Sails.js e execute-o:

        sails new <appname>
        cd <appname>
        sails lift

    Verifique se você pode navegar para a home page padrão em http://localhost:1377.

## Etapa 2: Criar o recurso de aplicativo do Serviço de Aplicativo no Azure

Em seguida, crie o recurso de aplicativo do Serviço de Aplicativo no Azure. Você implantará seu aplicativo Sails.js nele posteriormente.

1. No mesmo terminal, efetue logon no Azure da seguinte forma:

        azure login

    Siga o prompt para continuar o logon em um navegador com uma conta da Microsoft que tenha sua assinatura do Azure.

2. Verifique se você ainda está no diretório raiz do projeto Sails.js. Crie o recurso de aplicativo do Serviço de Aplicativo no Azure com um nome de aplicativo exclusivo com o próximo comando. A URL do aplicativo Web será http://&lt;appname>.azurewebsites.net.

        azure site create --git <appname>

    Siga os prompts para selecionar uma região do Azure para a qual implantar. Se nunca tiver configurado credenciais de implantação do Git/FTP para sua assinatura do Azure, você também será solicitado a criá-las.

    Após o recurso de aplicativo do Serviço de Aplicativo ser criado:

    - O aplicativo Sails.js inicializado no Git,
    - Seu repositório inicializado no Git local é conectado ao novo aplicativo do Serviço de Aplicativo como um Git remoto, apropriadamente chamado “azure” e
    - E o arquivo iisnode.yml é criado no diretório raiz. Você pode usar esse arquivo para configurar o [iisnode](https://github.com/tjanczuk/iisnode), que o Serviço de Aplicativo usa para executar aplicativos Node.js.

## Etapa 3: Configurar e implantar seu aplicativo Sails.js

 O trabalho com um aplicativo Sails.js no Serviço de Aplicativo consiste em três etapas principais:

 - Configurar seu aplicativo para que ele seja executado no Serviço de Aplicativo
 - Implantá-lo no Serviço de Aplicativo
 - Ler os logs de stderr e stdout para solucionar problemas de implantação

Siga estas etapas:

1. Abra o novo arquivo iisnode.yml no diretório raiz e adicione as duas linhas a seguir:

        loggingEnabled: true
        logDirectory: iisnode

    Agora o registro em log está habilitado para o iisnode. Para obter mais informações sobre como isso funciona, consulte [Get stdout and stderr logs from iisnode](app-service-web-nodejs-sails.md#iisnodelog) (Obter os logs de stdout e stderr do iisnode).

2. Abra config/env/production.js para configurar seu ambiente de produção e definir `port` e `hookTimeout`:

        module.exports = {

            // Use process.env.port to handle web requests to the default HTTP port
            port: process.env.port,
            // Increase hooks timout to 30 seconds
            // This avoids the Sails.js error documented at https://github.com/balderdashy/sails/issues/2691
            hookTimeout: 30000,

            ...
        };

    Você pode encontrar a documentação para essas configurações na [Sails.js Documentation](http://sailsjs.org/documentation/reference/configuration/sails-config) (Documentação do Sails.js).

    Em seguida, você precisa garantir que o [Grunt](https://www.npmjs.com/package/grunt) é compatível com unidades de rede do Azure. No momento da elaboração deste artigo, o Grunt pode gerar o [erro "ENOTSUP: operação sem suporte no soquete"](https://github.com/isaacs/node-glob/issues/205) porque no momento ele usa um pacote [glob](https://www.npmjs.com/package/glob) desatualizado (v3.1.21), que não dá suporte às unidades de rede. As próximas etapas mostram como fazer o Grunt usar o [glob v5.0.14 ou superior](https://github.com/isaacs/node-glob/commit/bf3381e90e283624fbd652835e1aefa55d45e2c7).

3. Uma vez que `npm install` já foi executado quando o aplicativo foi criado, gere o npm-shrinkwrap.json na raiz do projeto:

        npm shrinkwrap

4. Abra o npm-shrinkwrap.json, localize o json para `"grunt":` e, em seguida, adicione a dependência para a versão do glob desejada. O json concluído deve ter esta aparência:

        "grunt": {
            "version": "0.4.5",
            "from": "grunt@0.4.5",
            "resolved": "https://registry.npmjs.org/grunt/-/grunt-0.4.5.tgz",
            "dependencies": {
                "glob": {
                    "version": "5.0.14",
                    "from": "glob@5.0.14",
                    "resolved": "https://registry.npmjs.org/glob/-/glob-5.0.14.tgz"
                }
            }
        },

5. Localize todas as referências ao glob pesquisando `"glob":`. Se qualquer referência for v3.1.21 ou inferior, altere o json para:

        "glob": {
            "version": "5.0.14",
            "from": "glob@5.0.14",
            "resolved": "https://registry.npmjs.org/glob/-/glob-5.0.14.tgz"
        }

6. Salve as alterações e teste-as para se certificar de que seu aplicativo ainda é executado localmente:

        npm install
        sails lift

4. Agora, use o git para implantar seu aplicativo no Azure:

        git add .
        git commit -m "<your commit message>"
        git push azure master

5. Por fim, apenas abra seu aplicativo do Azure ativo no navegador:

        azure site browse

    Agora você deve ver a mesma home page do Sails.js.
    
    ![](./media/app-service-web-nodejs-sails/sails-in-azure.png)

## Solucionar problemas de implantação

Se seu aplicativo Sails.js falhar por algum motivo no Serviço de Aplicativo, encontre os logs de stderr para ajudar na solução de problemas. Para obter mais informações, consulte [Get stdout and stderr logs from iisnode](app-service-web-nodejs-sails.md#iisnodelog) (Obter os logs de stdout e stderr do iisnode). Se ele foi iniciado com êxito, o log de stdout deve mostrar a mensagem familiar:

                .-..-.

    Sails              <|    .-..-.
    v0.12.1             |\
                        /|.\
                        / || \
                    ,'  |'  \
                    .-'.-==|/_--'
                    `--'-------' 
    __---___--___---___--___---___--___
    ____---___--___---___--___---___--___-__

    Server lifted in `D:\home\site\wwwroot`
    To see your app, visit http://localhost:\\.\pipe\a76e8111-663e-449d-956e-5c5deff2d304
    To shut down Sails, press <CTRL> + C at any time.

## Conectar-se a um banco de dados no Azure

Para se conectar a um banco de dados do Azure, crie o banco de dados de sua escolha no Azure, como Banco de Dados SQL, MySQL, MongoDB, Cache (Redis) do Azure etc., e use o [adaptador de repositório de dados](https://github.com/balderdashy/sails#compatibility) correspondente para se conectar a ele. As etapas nesta seção mostram como se conectar a um Banco de Dados SQL do Azure.

1. Siga o tutorial [aqui](../sql-database/sql-database-get-started.md) para criar um Banco de Dados SQL do Azure em branco em um novo SQL Server. As configurações de firewall padrão permitem que os serviços do Azure (por exemplo, o Serviço de Aplicativo) se conectem a ele.

2. No seu terminal de linha de comando, instale o adaptador do SQL Server:

        npm install sails-sqlserver --save

    Como você alterou o package.json, precisa regenerar o npm-shrinkwrap.json. Você fará isso a seguir.
    
3. Exclua o diretório node\_modules/.

4. Execute `npm shrinkwrap`.

5. Abra o npm-shrinkwrap.json novamente e atualize as versões do pacote do `glob` como você fez na seção anterior.

    Agora, volte para a tarefa principal.
        
3. Abra config/connections.js e adicione o seguinte json à lista de adaptadores:

        sqlserver: {
            adapter: 'sails-sqlserver',
            user: process.env.dbuser,
            password: process.env.dbpassword,
            host: process.env.sqlserver, 
            database: process.env.dbname,
            options: {
                encrypt: true   // use this for Azure databases
            }
        },

4. Para cada variável de ambiente (`process.env.*`), você precisa defini-lo no Serviço de Aplicativo. Para fazer isso, execute os comandos a seguir no seu terminal:

        azure site appsetting add dbuser="<database server administrator>"
        azure site appsetting add dbpassword="<database server password>"
        azure site appsetting add sqlserver="<database server name>.database.windows.net"
        azure site appsetting add dbname="<database name>"
        
4. Abra config/env/production.js para configurar seu ambiente de produção e definir `connection` e `migrate` no objeto JSON `models`:

        models: {
            connection: 'sqlserver',
            migrate: 'alter'
        },

4. No terminal, [gere](http://sailsjs.org/documentation/reference/command-line-interface/sails-generate) uma [API de plano gráfico](http://sailsjs.org/documentation/concepts/blueprints) do Sails.js como faria normalmente. Por exemplo:

         sails generate api mywidget
     
5. Salve todas as alterações, envie por push suas alterações para o Azure e navegue até seu aplicativo para verificar se ele ainda funciona.

        git add .
        git commit -m "<your commit message>"
        git push azure master
        azure site browse

6. Agora, acesse a API de plano gráfico que você acabou de criar no navegador. Por exemplo:

        http://<appname>.azurewebsites.net/widget/create
    
    A API deve retornar a entrada criada para você na janela do navegador:
    
        {"id":1,"createdAt":"2016-03-28T23:08:01.000Z","updatedAt":"2016-03-28T23:08:01.000Z"}

## Mais recursos

- [Get started with Node.js web apps in Azure App Service](app-service-web-nodejs-get-started.md) (Introdução aos aplicativos Web do Node.js no Serviço de Aplicativo do Azure)
- [Usando Módulos no Node.js com aplicativos do Microsoft Azure](../nodejs-use-node-modules-azure-apps.md)

<!---HONumber=AcomDC_0406_2016-->