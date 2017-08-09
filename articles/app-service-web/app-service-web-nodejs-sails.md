---
title: "Implantar um aplicativo Web do Sails.js no Serviço de Aplicativo do Azure | Microsoft Docs"
description: "Saiba como implantar um aplicativo do Node.js no Serviço de Aplicativo do Azure. Este tutorial mostra como implantar um aplicativo Web Sails.js."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 8877ddc8-1476-45ae-9e7f-3c75917b4564
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/16/2016
ms.author: cephalin
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: deb9ce304069e6bd92518610a9953fb1f29cb555
ms.contentlocale: pt-br
ms.lasthandoff: 07/26/2017

---
# <a name="deploy-a-sailsjs-web-app-to-azure-app-service"></a>Implantar um aplicativo Web Sails.js no Serviço de Aplicativo do Azure
Este tutorial mostra como implantar um aplicativo Sails.js no Serviço de Aplicativo do Azure. No processo, você pode reunir algum conhecimento geral sobre como configurar seu aplicativo Node.js para a execução no Serviço de Aplicativo.

Aqui você aprenderá habilidades úteis, por exemplo:

* Configure um aplicativo Sails.js executado no Serviço de Aplicativo.
* Implante um aplicativo para o Serviço de Aplicativo da linha de comando.
* Ler os logs de stderr e stdout para solucionar problemas de implantação.
* Armazenar variáveis de ambiente fora do controle do código-fonte.
* Acessar as variáveis de ambiente do Azure do seu aplicativo.
* Conectar-se a um banco de dados (MongoDB).

Você deve ter conhecimento prático de Sails.js. Este tutorial não se destina a ajudá-lo com problemas relacionados à execução do Sail.js em geral.

## <a name="cli-versions-to-complete-the-task"></a>Versões da CLI para concluir a tarefa

Você pode concluir a tarefa usando uma das seguintes versões da CLI:

- [CLI do Azure 1.0](app-service-web-nodejs-sails-cli-nodejs.md) – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos
- [CLI 2.0 do Azure](app-service-web-nodejs-sails.md) – nossa última geração de CLI para o modelo de implantação de gerenciamento de recursos

## <a name="prerequisites"></a>Pré-requisitos
* [Node.js](https://nodejs.org/)
* [Sails.js](http://sailsjs.org/get-started)
* [Git](http://www.git-scm.com/downloads)
* [CLI 2.0 do Azure](/cli/azure/install-az-cli2)
* Uma conta do Microsoft Azure. Se não tiver uma conta, você poderá [inscrever-se para uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar seus benefícios de assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Você pode [Experimentar o Serviço de Aplicativo](https://azure.microsoft.com/try/app-service/) sem uma conta do Azure. Crie um aplicativo inicial e brinque com ele por até uma hora: não é necessário cartão de crédito ou compromissos.
> 
> 

## <a name="step-1-create-and-configure-a-sailsjs-app-locally"></a>Etapa 1: criar e configurar um aplicativo do Sails.js localmente
Primeiro, crie rapidamente um aplicativo do Sails.js padrão em seu ambiente de desenvolvimento seguindo estas etapas:

1. Abra o terminal de linha de comando de sua escolha e `CD` para um diretório de trabalho.
2. Crie um novo aplicativo Sails.js e execute-o:

        sails new <app_name>
        cd <app_name>
        sails lift

    Verifique se você pode navegar para a home page padrão em http://localhost:1377.

1. Em seguida, habilitar registro em log para o Azure. No diretório raiz, crie um arquivo chamado `iisnode.yml` e adicione as duas linhas a seguir:

        loggingEnabled: true
        logDirectory: iisnode

    O registro em log está habilitado para o servidor [iisnode](https://github.com/tjanczuk/iisnode) que o Serviço de Aplicativo do Azure usa para executar aplicativos Node.js. 
    Para obter mais informações sobre como isso funciona, consulte [Como depurar um aplicativo Web Node.js no Serviço de Aplicativo do Azure](web-sites-nodejs-debug.md).

2. Em seguida, configure o aplicativo Sails.js para usar variáveis de ambiente do Azure. Abra config/env/production.js para configurar seu ambiente de produção e definir `port` e `hookTimeout`:

        module.exports = {

            // Use process.env.port to handle web requests to the default HTTP port
            port: process.env.port,
            // Increase hooks timout to 30 seconds
            // This avoids the Sails.js error documented at https://github.com/balderdashy/sails/issues/2691
            hookTimeout: 30000,

            ...
        };

    Você pode encontrar a documentação para essas configurações na [Documentação do Sails.js](http://sailsjs.org/documentation/reference/configuration/sails-config).

4. Em seguida, codifique a versão do Node.js que você deseja usar. Em package.json, adicione a seguinte propriedade `engines` para definir a versão do Node.js como desejado.

        "engines": {
            "node": "6.9.1"
        },

5. Por fim, inicialize um repositório Git e confirme seus arquivos. Na raiz do aplicativo (na qual package.json está), execute os seguintes comandos do Git:

        git init
        git add .
        git commit -m "<your commit message>"

Seu código está pronto para ser implantado. 

## <a name="step-2-create-an-azure-app-and-deploy-sailsjs"></a>Etapa 2: criar um aplicativo do Azure e implantar Sails.js

Em seguida, crie o recurso de Serviço de Aplicativo no Azure e implante seu aplicativo Sails.js nele.

1. Faça logon no Azure da seguinte forma:

        az login

    Siga o prompt para continuar o logon em um navegador com uma conta da Microsoft que tenha sua assinatura do Azure.

3. Defina o usuário de implantação para o Serviço de Aplicativo. Mais tarde, você implantará o código usando essas credenciais.
   
        az appservice web deployment user set --user-name <username> --password <password>

3. Crie um [grupo de recursos](../azure-resource-manager/resource-group-overview.md) com um nome. Para este tutorial de Node.js, você não precisa realmente saber o que isso é.

        az group create --location "<location>" --name my-sailsjs-app-group

    Para ver quais possíveis valores você pode usar para `<location>`, use o comando `az appservice list-locations` da CLI.

3. Criar um novo [Plano do Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) "GRATUITO" com um nome. Para este tutorial de Node.js, saiba que você não será cobrado por aplicativos Web neste plano.

        az appservice plan create --name my-sailsjs-appservice-plan --resource-group my-sailsjs-app-group --sku FREE

4. Crie um novo aplicativo Web com um nome exclusivo no `<app_name>`.

        az appservice web create --name <app_name> --resource-group my-sailsjs-app-group --plan my-sailsjs-appservice-plan

## <a name="step-3-configure-and-deploy-your-sailsjs-app"></a>Etapa 3: Configurar e implantar seu aplicativo Sails.js

1. Configure a implantação Git local para o novo aplicativo Web com o seguinte comando:

        az appservice web source-control config-local-git --name <app_name> --resource-group my-sailsjs-app-group

    Você receberá uma saída JSON como esta, o que significa que o repositório Git remoto está configurado:

        {
        "url": "https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git"
        }

6. Adicione a URL no JSON como um Git remoto para seu repositório local (chamado de `azure` para manter a simplicidade).

        git remote add azure https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git
   
7. Implante o código de exemplo no Git remoto do `azure`. Quando solicitado, use as credenciais de implantação que você configurou anteriormente.

        git push azure master

7. Por fim, apenas abra seu aplicativo do Azure ativo no navegador:

        az appservice web browse --name <app_name> --resource-group my-sailsjs-app-group

    Agora você deve ver a mesma home page do Sails.js.

    ![](./media/app-service-web-nodejs-sails/sails-in-azure.png)

## <a name="troubleshoot-your-deployment"></a>Solucionar problemas de implantação
Se seu aplicativo Sails.js falhar por algum motivo no Serviço de Aplicativo, encontre os logs de stderr para ajudar na solução de problemas.
Para obter mais informações, consulte [Como depurar um aplicativo Web Node.js no Serviço de Aplicativo do Azure](web-sites-nodejs-debug.md).
Se o aplicativo tiver sido iniciado com êxito, o log de stdout deverá mostrar a você a mensagem familiar:

                   .-..-.
    
       Sails              <|    .-..-.
       v0.12.11            |\
                          /|.\
                         / || \
                       ,'  |'  \
                    .-'.-==|/_--'
                    `--'-------' 
       __---___--___---___--___---___--___
     ____---___--___---___--___---___--___-__

    Server lifted in `D:\home\site\wwwroot`
    To see your app, visit http://localhost:\\.\pipe\c775303c-0ebc-4854-8ddd-2e280aabccac
    To shut down Sails, press <CTRL> + C at any time.

Você pode controlar a granularidade dos logs do stdout no arquivo [config/log.js](http://sailsjs.org/#!/documentation/concepts/Logging) .

## <a name="connect-to-a-database-in-azure"></a>Conectar-se a um banco de dados no Azure
Para se conectar a um banco de dados no Azure, crie o banco de dados de sua escolha no Azure, como Banco de Dados SQL, MySQL, MongoDB, Cache (Redis) do Azure, dentre outros, e use o [adaptador de repositório de dados](https://github.com/balderdashy/sails#compatibility) correspondente para se conectar a ele. As etapas nesta seção mostram como se conectar ao MongoDB usando um [BD Cosmos do Azure](../documentdb/documentdb-protocol-mongodb.md), que pode dar suporte a conexões de cliente do MongoDB.

1. [Criar uma conta do BD Cosmos com suporte para protocolo MongoDB](../documentdb/documentdb-create-mongodb-account.md).
2. [Criar uma coleção e banco de dados do BD Cosmos](../documentdb/documentdb-create-collection.md). O nome da coleção não importa, mas você precisará do nome do banco de dados quando você se conectar de Sails.js.
3. [Localizar as informações de conexão para seu BD Cosmos](../cosmos-db/connect-mongodb-account.md#GetCustomConnection).
2. No seu terminal de linha de comando, instale o adaptador do MongoDB:

        npm install sails-mongo --save

3. Abra config/connections.js e adicione o seguinte objeto de conexão à lista:

        docDbMongo: {
            adapter: 'sails-mongo',
            user: process.env.dbuser,
            password: process.env.dbpassword,
            host: process.env.dbhost,
            port: process.env.dbport,
            database: process.env.dbname,
            ssl: true
        },

    > [!NOTE] 
    > A opção `ssl: true` é importante porque o [BD Cosmos a requer](../cosmos-db/connect-mongodb-account.md#connection-string-requirements). 
    >
    >

4. Para cada variável de ambiente (`process.env.*`), você precisa defini-la no Serviço de Aplicativo. Para isso, execute os comandos a seguir no seu terminal. Use as informações de conexão para seu BD Cosmos.

        az appservice web config appsettings update --settings dbuser="<database user>" --name <app_name> --resource-group my-sailsjs-app-group
        az appservice web config appsettings update --settings dbpassword="<database password>" --name <app_name> --resource-group my-sailsjs-app-group
        az appservice web config appsettings update --settings dbhost="<database hostname>" --name <app_name> --resource-group my-sailsjs-app-group
        az appservice web config appsettings update --settings dbport="<database port>" --name <app_name> --resource-group my-sailsjs-app-group
        az appservice web config appsettings update --settings dbname="<database name>" --name <app_name> --resource-group my-sailsjs-app-group

    Colocar suas configurações nas configurações de aplicativo do Azure mantém dados confidenciais fora do seu controle de origem (Git). Em seguida, você configurará seu ambiente de desenvolvimento para usar as mesmas informações de conexão.
5. Abra config/local.js e adicione o seguinte objeto de conexão:

        connections: {
            docDbMongo: {
                user: "<database user>",
                password: "<database password>",
                host: "<database hostname>",
                database: "<database name>",
                ssl: true
            },
        },

    Essa configuração substitui as configurações no arquivo config/connections.js para o ambiente local. Esse arquivo é excluído pelo .gitignore padrão em seu projeto, portanto não será armazenado no Git. Agora, você será capaz de se conectar ao BD Cosmos (MongoDB) tanto de seu aplicativo Web do Azure quanto do ambiente de desenvolvimento local.
6. Abra config/env/production.js para configurar seu ambiente de produção e adicione o seguinte objeto `models` :

        models: {
            connection: 'docDbMongo',
            migrate: 'safe'
        },
7. Abra config/env/development.js para configurar seu ambiente de desenvolvimento e adicione o seguinte objeto `models` :

        models: {
            connection: 'docDbMongo',
            migrate: 'alter'
        },

    `migrate: 'alter'` permite que você use os recursos de migração do banco de dados para criar e atualizar facilmente suas coleções ou tabelas de banco de dados. No entanto, o `migrate: 'safe'` é usado para seu ambiente (produção) do Azure, pois Sails.js não permite o uso de `migrate: 'alter'` em um ambiente de produção (confira a [Documentação do Sails.js](http://sailsjs.org/documentation/concepts/models-and-orm/model-settings)).
8. No terminal, [gere](http://sailsjs.org/documentation/reference/command-line-interface/sails-generate) um [API de plano gráfico](http://sailsjs.org/documentation/concepts/blueprints) do Sails.js, como você faria normalmente, e execute `sails lift` para criar o banco de dados com a migração de banco de dados do Sails.js. Por exemplo:

         sails generate api mywidget
         sails lift

    O modelo `mywidget` gerado por este comando está vazio, mas podemos usar isso para mostrar que temos conectividade com o banco de dados.
    Quando você executa o `sails lift`, ele cria as coleções e tabelas ausentes para os modelos usados por seu aplicativo.
9. Acesse a API de plano gráfico que você acabou de criar no navegador. Por exemplo:

        http://localhost:1337/mywidget/create

    A API deve retornar a entrada criada para você na janela do navegador, o que significa que a sua coleção foi criada com êxito.

        {"id":1,"createdAt":"2016-09-23T13:32:00.000Z","updatedAt":"2016-09-23T13:32:00.000Z"}
10. Agora, envie por push suas alterações para o Azure e navegue até seu aplicativo para verificar se ele ainda funciona.

         git add .
         git commit -m "<your commit message>"
         git push azure master
         az appservice web browse --name <app_name> --resource-group my-sailsjs-app-group

11. Acesse a API de plano gráfico de seu aplicativo Web do Azure. Por exemplo:

         http://<appname>.azurewebsites.net/mywidget/create

     Se a API retornar outra entrada nova, seu aplicativo Web do Azure estará falando com seu BD Cosmos (MongoDB).

## <a name="more-resources"></a>Mais recursos
* [Get started with Node.js web apps in Azure App Service (Introdução aos aplicativos Web do Node.js no Serviço de Aplicativo do Azure)](app-service-web-get-started-nodejs.md)
* [Usando Módulos no Node.js com aplicativos do Microsoft Azure](../nodejs-use-node-modules-azure-apps.md)

