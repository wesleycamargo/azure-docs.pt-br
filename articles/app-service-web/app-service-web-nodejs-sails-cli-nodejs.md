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
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: 9d43405539ced1dadcaa0cb11bb0c3dd42dee2b9
ms.lasthandoff: 03/01/2017


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
- [CLI do Azure 2.0](app-service-web-nodejs-sails.md) – nossa próxima geração de CLI para o modelo de implantação do resource manager

## <a name="prerequisites"></a>Pré-requisitos
* [Node.js](https://nodejs.org/)
* [Sails.js](http://sailsjs.org/get-started)
* [Git](http://www.git-scm.com/downloads)
* [CLI do Azure](../xplat-cli-install.md)
* Uma conta do Microsoft Azure. Se não tiver uma conta, você poderá [inscrever-se para uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar seus benefícios de assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Você pode [Experimentar o Serviço de Aplicativo](https://azure.microsoft.com/try/app-service/) sem uma conta do Azure. Crie um aplicativo inicial e brinque com ele por até uma hora: não é necessário cartão de crédito ou compromissos.
> 
> 

## <a name="step-1-create-a-sailsjs-app-locally"></a>Etapa 1: criar um aplicativo do Sails.js localmente
Primeiro, crie rapidamente um aplicativo do Sails.js padrão em seu ambiente de desenvolvimento seguindo estas etapas:

1. Abra o terminal de linha de comando de sua escolha e `CD` para um diretório de trabalho.
2. Crie um novo aplicativo Sails.js e execute-o:

        sails new <appname>
        cd <appname>
        sails lift

    Verifique se você pode navegar para a home page padrão em http://localhost:1377.

## <a name="step-2-create-the-azure-app-resource"></a>Etapa 2: criar o recurso de aplicativo do Azure
Em seguida, crie o recurso do Serviço de Aplicativo no Azure. Você implantará seu aplicativo Sails.js nele posteriormente.

1. Faça logon no Azure da seguinte forma:
2. No mesmo terminal, altere para o modo ASM e faça logon no Azure:

        azure config mode asm
        azure login

    Siga o prompt para continuar o logon em um navegador com uma conta da Microsoft que tenha sua assinatura do Azure.

3. Defina o usuário de implantação para o Serviço de Aplicativo. Mais tarde, você implantará o código usando as credenciais.
   
        azure site deployment user set --username <username> --pass <password>

3. Verifique se você ainda está no diretório raiz do projeto Sails.js. Crie o recurso de aplicativo do Serviço de Aplicativo no Azure com um nome de aplicativo exclusivo com o próximo comando. A URL do aplicativo Web é http://&lt;nomedoaplicativo>.azurewebsites.net.

        azure site create --git <appname>

    Siga os prompts para selecionar uma região do Azure para a qual implantar. Após o recurso de aplicativo do Serviço de Aplicativo ser criado:

   * O aplicativo Sails.js inicializado no Git,
   * Seu repositório inicializado no Git local é conectado ao novo aplicativo do Serviço de Aplicativo como um Git remoto, apropriadamente chamado “azure” e
   * E o arquivo iisnode.yml é criado no diretório raiz. Você pode usar esse arquivo para configurar o [iisnode](https://github.com/tjanczuk/iisnode), que o Serviço de Aplicativo usa para executar aplicativos Node.js.

## <a name="step-3-configure-and-deploy-your-sailsjs-app"></a>Etapa 3: Configurar e implantar seu aplicativo Sails.js
 O trabalho com um aplicativo Sails.js no Serviço de Aplicativo consiste em três etapas principais:

* Configurar seu aplicativo para que ele seja executado no Serviço de Aplicativo
* Implantá-lo no Serviço de Aplicativo
* Ler os logs de stderr e stdout para solucionar problemas de implantação

Siga estas etapas:

1. Abra o novo arquivo iisnode.yml no diretório raiz e adicione as duas linhas a seguir:

        loggingEnabled: true
        logDirectory: iisnode

    O registro em log está habilitado para o servidor [iisnode](https://github.com/tjanczuk/iisnode) que o Serviço de Aplicativo do Azure usa para executar aplicativos Node.js. 
    Para saber mais sobre como isso funciona, confira  [Obter os logs de stdout e stderr do iisnode](app-service-web-nodejs-get-started.md#iisnodelog).
2. Abra config/env/production.js para configurar seu ambiente de produção e definir `port` e `hookTimeout`:

        module.exports = {

            // Use process.env.port to handle web requests to the default HTTP port
            port: process.env.port,
            // Increase hooks timout to 30 seconds
            // This avoids the Sails.js error documented at https://github.com/balderdashy/sails/issues/2691
            hookTimeout: 30000,

            ...
        };

    Você pode encontrar a documentação para essas configurações na  [Documentação do Sails.js](http://sailsjs.org/documentation/reference/configuration/sails-config).

4. Em package.json, adicione a seguinte propriedade `engines` para definir a versão do Node.js como desejado.

        "engines": {
            "node": "6.9.1"
        },
5. Salve as alterações e teste-as para se certificar de que seu aplicativo ainda é executado localmente. Para fazer isso, exclua a pasta `node_modules` e execute:

        npm install
        sails lift
6. Agora, use o git para implantar seu aplicativo no Azure:

        git add .
        git commit -m "<your commit message>"
        git push azure master
7. Por fim, apenas abra seu aplicativo do Azure ativo no navegador:

        azure site browse

    Agora você deve ver a mesma home page do Sails.js.

    ![](./media/app-service-web-nodejs-sails/sails-in-azure.png)

## <a name="troubleshoot-your-deployment"></a>Solucionar problemas de implantação
Se seu aplicativo Sails.js falhar por algum motivo no Serviço de Aplicativo, encontre os logs de stderr para ajudar na solução de problemas.
Para saber mais, confira [Obter os logs de stdout e stderr do iisnode](app-service-web-nodejs-get-started.md#get-stdout-and-stderr-logs-from-iisnode).
Se ele foi iniciado com êxito, o log de stdout deve mostrar a mensagem familiar:

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
Para se conectar a um banco de dados no Azure, crie o banco de dados de sua escolha no Azure, como Banco de Dados SQL, MySQL, MongoDB, Cache (Redis) do Azure, dentre outros, e use o [adaptador de repositório de dados](https://github.com/balderdashy/sails#compatibility) correspondente para se conectar a ele. As etapas nesta seção mostram como se conectar ao MongoDB usando um banco de dados [Azure DocumentDB](../documentdb/documentdb-protocol-mongodb.md), que pode dar suporte a conexões de cliente do MongoDB.

1. [Crie uma conta do DocumentDB com suporte de protocolo para MongoDB](../documentdb/documentdb-create-mongodb-account.md).
2. [Criar uma coleção e banco de dados DocumentDB](../documentdb/documentdb-create-collection.md). O nome da coleção não importa, mas você precisará do nome do banco de dados quando você se conectar de Sails.js.
3. [Localize as informações de conexão para o banco de dados DocumentDB](../documentdb/documentdb-connect-mongodb-account.md#a-idgetcustomconnectiona-get-the-mongodb-connection-string-to-customize).
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
    > A opção `ssl: true` é importante porque o [Azure DocumentDB a exige](../documentdb/documentdb-connect-mongodb-account.md#connection-string-requirements). 
    >
    >

4. Para cada variável de ambiente (`process.env.*`), você precisa defini-la no Serviço de Aplicativo. Para isso, execute os comandos a seguir no seu terminal. Use as informações de conexão para o banco de dados DocumentDB.

        azure site appsetting add dbuser="<database user>"
        azure site appsetting add dbpassword="<database password>"
        azure site appsetting add dbhost="<database hostname>"
        azure site appsetting add dbport="<database port>"
        azure site appsetting add dbname="<database name>"

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

    Essa configuração substitui as configurações no arquivo config/connections.js para o ambiente local. Esse arquivo é excluído pelo .gitignore padrão em seu projeto, portanto não será armazenado no Git. Agora, você será capaz de se conectar ao banco de dados DocumentDB (MongoDB) tanto de seu aplicativo Web do Azure quanto do ambiente de desenvolvimento local.
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

    `migrate: 'alter'` permite que você use os recursos de migração do banco de dados para criar e atualizar facilmente suas coleções ou tabelas de banco de dados. No entanto, o `migrate: 'safe'` é usado para seu ambiente (produção) do Azure, pois Sails.js não permite o uso de `migrate: 'alter'` em um ambiente de produção (confira a  [Documentação do Sails.js](http://sailsjs.org/documentation/concepts/models-and-orm/model-settings)).
8. No terminal, [gere](http://sailsjs.org/documentation/reference/command-line-interface/sails-generate) um [API de plano gráfico](http://sailsjs.org/documentation/concepts/blueprints) do Sails.js, como você faria normalmente, e execute `sails lift` para criar o banco de dados com a migração de banco de dados do Sails.js. Por exemplo:

         sails generate api mywidget
         sails lift

    O modelo `mywidget` gerado por este comando está vazio, mas podemos usar isso para mostrar que temos conectividade com o banco de dados.
    Quando você executa o `sails lift`, ele cria as coleções ou tabelas ausentes para os modelos usados por seu aplicativo.
9. Acesse a API de plano gráfico que você acabou de criar no navegador. Por exemplo:

        http://localhost:1337/mywidget/create

    A API deve retornar a entrada criada para você na janela do navegador, o que significa que seu banco de dados foi criado com êxito.

        {"id":1,"createdAt":"2016-09-23T13:32:00.000Z","updatedAt":"2016-09-23T13:32:00.000Z"}
10. Agora, envie por push suas alterações para o Azure e navegue até seu aplicativo para verificar se ele ainda funciona.

         git add .
         git commit -m "<your commit message>"
         git push azure master
         azure site browse
11. Acesse a API de plano gráfico de seu aplicativo Web do Azure. Por exemplo:

         http://<appname>.azurewebsites.net/mywidget/create

     Se a API retornar outra entrada nova, seu aplicativo Web do Azure estará falando com seu banco de dados DocumentDB (MongoDB).

## <a name="more-resources"></a>Mais recursos
* [Get started with Node.js web apps in Azure App Service (Introdução aos aplicativos Web do Node.js no Serviço de Aplicativo do Azure)](app-service-web-nodejs-get-started.md)
* [Usando Módulos no Node.js com aplicativos do Microsoft Azure](../nodejs-use-node-modules-azure-apps.md)

