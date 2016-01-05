<properties
	pageTitle="Atualização dos Serviços Móveis para o Serviço de Aplicativo do Azure - Node.js"
	description="Saiba como atualizar com facilidade seu aplicativo de Serviços Móveis para um Aplicativo Móvel do Serviço de Aplicativo"
	services="app-service\mobile"
	documentationCenter=""
	authors="christopheranderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile"
	ms.devlang="node"
	ms.topic="article"
	ms.date="12/02/2015"
	ms.author="chrande"/>

# Atualizar seu Serviço Móvel do Azure Node.js para o Serviço de Aplicativo

Os Aplicativos Móveis do Serviço de Aplicativo é uma nova maneira de compilar aplicativos móveis usando o Microsoft Azure. Para saber mais, confira [O que são Aplicativos Móveis?].

Este tópico descreve como atualizar um aplicativo de back-end Node.js dos Serviços Móveis do Azure para novos Aplicativos Móveis do Serviço de Aplicativo. Durante essa atualização, o seu aplicativo de Serviços Móveis pode continuar a operar.

Quando um back-end móvel é atualizado para o Serviço de Aplicativo do Azure, ele tem acesso a todos os recursos do Serviço de Aplicativo e é cobrado de acordo com os [preços do Serviço de Aplicativo] e não com os preços dos Serviços Móveis.

## Migração vs. atualização

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

>[AZURE.TIP]Recomendamos a [execução de uma migração](app-service-mobile-migrating-from-mobile-services.md) antes da execução de uma atualização. Dessa forma, você pode colocar as duas versões de seu aplicativo no mesmo Plano de Serviço de Aplicativo sem qualquer custo adicional.

### Aprimoramentos no SDK do servidor Node.js de Aplicativos Móveis

A atualização para o novo [SDK de Aplicativos Móveis](https://www.npmjs.com/package/azure-mobile-apps) fornece muitos aprimoramentos, incluindo:

- Com base na [estrutura do Express](http://expressjs.com/en/index.html), o novo SDK do Node é leve e foi projetado para acompanhar as novas versões do Node conforme elas forem surgindo. Você pode personalizar o comportamento do aplicativo com o middleware do Express.

- Aprimoramentos consideráveis de desempenho em comparação com o SDK dos Serviços Móveis.

- Agora você pode hospedar um site junto com o back-end móvel; da mesma forma, é fácil adicionar o Azure Mobile SDK a qualquer aplicativo expressv4 existente.

- Criado para o desenvolvimento local e de plataforma cruzada, o SDK dos Aplicativos Móveis pode ser desenvolvido e executado localmente nas plataformas do Windows, do Linux e do OSX. Agora é fácil usar técnicas comuns de desenvolvimento em Node, como a execução de testes [Mocha](https://mochajs.org/) antes da implantação.

- Você pode usar o Redis com módulos nativos, como o [hiredis](https://www.npmjs.com/package/hiredis); e uma vez que o Serviço de Aplicativo instalará os pacotes npm para você, não será necessário incluir na implantação binários em seus pacotes de implantação.

## <a name="overview"></a>Visão geral da atualização básica

Ao contrário do SDK de Aplicativos Móveis .NET, atualizar um back-end do Node dos Serviços Móveis para os Aplicativos Móveis não é tão simples quanto trocar pacotes. Agora você possui sua própria pilha de aplicativos, e o Azure não a controla mais. Portanto, você precisa criar um aplicativo básico do Express para hospedar o back-end móvel. Para os controladores de tabela e de API, os conceitos são semelhantes, mas agora você deve exportar objetos de tabela e as APIs de função mudaram um pouco. Este artigo explica as estratégias básicas de atualização. Mas antes de migrar, convém ler as [Instruções de back-end do Node](app-service-mobile-node-backend-how-to-use-server-sdk.md) antes de começar.

>[AZURE.TIP]Recomendamos a leitura e compreensão do restante deste tópico antes de iniciar uma atualização. Tome nota de todos os recursos que você usar que são descritos abaixo.

Os SDKs do cliente de Serviços Móveis **não** são compatíveis com o novo SDK do servidor de Aplicativos Móveis. Para permitir a continuidade do serviço de seu aplicativo, não publique alterações em um site que esteja servindo clientes publicados. Em vez disso, você deve criar um novo aplicativo móvel que sirva como uma duplicata. Você pode colocar esse aplicativo no mesmo plano de Serviço de Aplicativo para evitar custos adicionais.

Assim, você terá duas versões do aplicativo: uma que permanece inalterada e atende aos aplicativos publicados e outra que você pode atualizar e usar como uma nova versão do cliente. Você pode mover e testar seu código de acordo com seu ritmo, mas deve aplicar as correções de bug feitas às duas versões. Quando você achar que um número desejado de aplicativos cliente foram atualizados para a versão mais recente, será possível excluir o aplicativo migrado original se assim quiser. Isso não causará custos adicionais, se for hospedado no mesmo plano de Serviço de Aplicativo de seu Aplicativo Móvel.

A estrutura completa para o processo de atualização é a seguinte:

1. Criar um novo Aplicativo Móvel
2. Atualizar o projeto para usar os novos SDKs de Servidor
3. Lançar uma nova versão do aplicativo cliente
4. (Opcional) Excluir seu aplicativo de serviço móvel migrado original

## <a name="mobile-app-version"></a> Iniciando a atualização
A primeira etapa de atualização é criar o recurso de Aplicativo Móvel que hospedará a nova versão do seu aplicativo. Se você já tiver migrado um serviço móvel existente, convém criar esta versão no mesmo plano de hospedagem. Abra o [portal do Azure] e navegue até seu aplicativo migrado. Anote o Plano de Serviço de Aplicativo no qual ele está sendo executado.

### Criando uma segunda instância do aplicativo
Em seguida, crie uma segunda instância do aplicativo. Quando receber uma solicitação para escolher o Plano de Serviço de Aplicativo ou o "plano de hospedagem", escolha o plano de seu aplicativo migrado.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Você provavelmente desejará usar o mesmo banco de dados e Hub de Notificação, como você fez nos Serviços Móveis. Você pode copiar esses valores abrindo o [portal do Azure] e navegando até o aplicativo original, clicando em **Configurações** > **Configurações do aplicativo**. Em **Cadeias de Conexão**, copie `MS_NotificationHubConnectionString` e `MS_TableConnectionString`. Navegue até o novo site de atualização e cole-as, substituindo os valores existentes. Repita esse processo para quaisquer outras configurações de aplicativo exigidas por seu aplicativo. Se você não estiver usando um serviço migrado, leia as cadeias de conexão e as configurações de aplicativo da guia **Configurar** da seção Serviços Móveis do [portal do Azure].

### Criar um back-end básico do Aplicativo Móvel com o Node

Cada back-end de Node.js do Aplicativo Móvel do Serviço de Aplicativo do Azure inicia como um aplicativo ExpressJS. Você pode criar um aplicativo [Express](http://expressjs.com/en/index.html) básico da seguinte maneira:

1. Em um comando ou a janela do PowerShell, crie um novo diretório para seu projeto.

        mkdir basicapp

2. Execute o npm init para inicializar a estrutura do pacote.

        cd basicapp
        npm init

    O comando init npm solicitará um conjunto de perguntas para inicializar o projeto. Consulte a saída de exemplo abaixo

    ![A saída de init npm][0]

3. Instale as bibliotecas express e azure-mobile-apps do repositório npm.

        npm install --save express azure-mobile-apps

4. Crie um arquivo app.js para implementar o servidor móvel básico.

        var express = require('express'),
            azureMobileApps = require('azure-mobile-apps');

        var app = express(),
            mobile = azureMobileApps();

        // Important all tables in the 'tables' directory
        mobile.tables.import('./tables');
        mobile.api.import('./api');

        // Provide initialization of any tables that are statically defined
        mobile.tables.initialize().then(function () {
           // Add the mobile API so it is accessible as a Web API
           app.use(mobile);

           // Start listening on HTTP
           app.listen(process.env.PORT || 3000);
           console.log('Now listening on ' + (process.env.PORT || 3000)));
        });


## Atualizando o projeto de servidor

Os Aplicativos Móveis fornecem um novo [SDK do Servidor de Aplicativo Móvel] que oferece praticamente a mesma funcionalidade que o tempo de execução dos Serviços Móveis, mas agora você é proprietário do tempo de execução completo; os Aplicativos Móveis não forçam uma versão do Node ou quaisquer atualizações de código para você. Se você tiver executado as etapas acima, terá uma versão básica do tempo de execução móvel do Node disponível. Agora você pode começar a mover tabelas e a lógica de API do seu Serviço Móvel para o seu Aplicativo Móvel, personalizar a configuração do servidor, habilitar o envio por push, configurar a autenticação e muito mais.

### Configuração base

O servidor tem muitas configurações, mas tem diversos valores padrão para facilitar o uso inicial. Muitas das configurações serão definidas para você, no [portal do Azure], por meio dos menus de configuração **Dados**, **Autenticação/Autorização** e **Push**. Para desenvolvimento local, se você quiser usar os dados, autenticação e push, talvez seja necessário configurar seu ambiente de desenvolvimento local.

Você pode definir a configuração do servidor por meio de variáveis de ambiente que podem ser definidas por meio das Configurações do Aplicativo em seu back-end do Aplicativo Móvel.

Você pode personalizar ainda mais o SDK de Aplicativos Móveis, passando um [objeto de configuração](http://azure.github.io/azure-mobile-apps-node/global.html#configuration) para o inicializador ou [criando um arquivo chamado azureMobile.js](app-service-mobile-node-backend-how-to-use-server-sdk/#howto-config-localdev) na raiz do projeto.

### Trabalhando com dados e tabelas

O SDK vem com um provedor de dados na memória a fim de facilitar e agilizar a experiência inicial. Você deve passar a usar um Banco de Dados SQL logo no início, pois o provedor na memória perderá todos os dados na reinicialização e não permanecerá consistente entre várias instâncias.

Para começar a mudar sua lógica de negócios do seu Serviço Móvel para os Aplicativos Móveis, primeiro convém criar um arquivo com o nome da tabela (seguido por ‘.js’) no diretório `./tables`. Veja o exemplo completo de uma tabela de Aplicativo Móvel no [GitHub](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/todo/tables/TodoItem.js). A versão mais simples é:

    var azureMobileApps = require('azure-mobile-apps');

    // Create a new table definition
    var table = azureMobileApps.table();

    module.exports = table;

Para iniciar a portabilidade de sua lógica, para cada um dos seus `<tablename>.<operation>.js`, você precisará de uma função para sua tabela. Vamos adicionar uma função de leitura a um exemplo.

Em um Serviço Móvel com uma tabela TodoItem e uma operação de leitura que filtra os itens com base na ID de usuário, como esta:

  function(query, user, request) { query.where({ userId: user.userId}); request.execute(); }

A função que adicionamos ao código de tabela dos Aplicativos Móveis do Azure teria esta aparência:

    table.read(function (context) {
        context.query.where({ userId: context.user.id });
        return context.execute();
    });

Inspecionando o código, você pode ver que a maioria dos parâmetros de função de

### CORS

CORS pode ser habilitada por meio de uma [configuração de CORS](http://azure.github.io/azure-mobile-apps-node/global.html#corsConfiguration) no SDK.

As principais áreas com que você deve se preocupar ao usar CORS são os cabeçalhos `eTag` e `Location`, que devem ser permitidos para que os SDKs do cliente funcionem corretamente.

### Notificações por Push

Os SDK dos Hubs de Notificação do Azure sofreram algumas atualizações consideráveis desde os Serviços Móveis, portanto, algumas assinaturas de função dos Hubs de Notificação podem ser diferentes. Caso contrário, a funcionalidade será semelhante aos Serviços Móveis; o Azure Mobile SDK provisionará uma instância dos Hubs de Notificação se a Configuração do Aplicativo para os Hubs de Notificação existir e a exibirá em `context.push`. Encontre um exemplo no [GitHub](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/push-on-insert/tables/TodoItem.js), com a seção relevante mostrada abaixo:

    table.insert(function (context) {
        // For details of the Notification Hubs JavaScript SDK,
        // see https://azure.microsoft.com/pt-BR/documentation/articles/notification-hubs-nodejs-how-to-use-notification-hubs/
        logger.silly('Running TodoItem.insert');

        // This push uses a template mechanism, so we need a template/
        var payload = '<toast><visual><binding template="Toast01"><text id="1">INSERT</text></binding></visual></toast>';

        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    context.push.wns.send(null, payload, 'wns/toast', function (error) {
                        if (error) {
                            logger.error('Error while sending push notification: ', error);
                        } else {
                            logger.silly('Push notification sent successfully!');
                        }
                    });
                }
                // Don't forget to return the results from the context.execute()
                return results;
            })
            .catch(function (error) {
                logger.error('Error while running context.execute: ', error);
            });
    });


### Trabalhos agendados
Trabalhos agendados não são criados em aplicativos móveis, portanto, todos os trabalhos existentes que você tem no back-end do Serviço Móvel precisam ser atualizado individualmente. Uma opção é criar um [Trabalho Web] agendado no site de código do Aplicativo Móvel. Você também pode configurar uma API que contenha o código de trabalho e configurar o [Agendador do Azure] para atingir o ponto de extremidade como o esperado.

## <a name="authentication"></a>Considerações sobre autenticação

Os componentes de autenticação dos Serviços Móveis foram movidos para o recurso Autenticação/Autorização do Serviço de Aplicativo. Você pode aprender a habilitar essa opção para seu site lendo o tópico [Adicionar autenticação ao seu aplicativo móvel](app-service-mobile-ios-get-started-users.md).

Para alguns provedores, como o AAD, o Facebook e o Google, você deverá aproveitar o registro existente em seu aplicativo de cópia. Basta navegar até o portal do provedor de identidade e adicionar uma nova URL de redirecionamento ao registro. Em seguida, configure a Autenticação/Autorização do Serviço de Aplicativo com a ID do cliente e o segredo.

### Identidade de usuário e autorização de ação do controlador

Para limitar o acesso à sua tabela, configure isso no nível da tabela com `table.access = 'authenticated';`. Veja um exemplo completo no [GitHub](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/authentication/tables/TodoItem.js).

Você pode obter acesso às informações de identidade do usuário por meio do método `user.getIdentity` descrito [aqui](http://azure.github.io/azure-mobile-apps-node/module-azure-mobile-apps_auth_user.html#~getIdentity).

## <a name="updating-clients"></a>Atualizando de clientes
Quando você tiver um back-end do Aplicativo Móvel operacional, poderá trabalhar em uma nova versão de seu aplicativo cliente para consumi-lo. Os Aplicativos Móveis também incluem uma nova versão dos SDKs do cliente e, assim como a atualização do servidor acima, você precisará remover todas as referências aos SDKs dos Serviços Móveis antes de instalar as versões dos Aplicativos Móveis.

Uma das principais alterações entre as versões é que os construtores não exigem mais uma chave de aplicativo. Agora, basta passar a URL do Aplicativo Móvel. Por exemplo, em clientes .NET, o construtor `MobileServiceClient` agora é:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

Você pode ler sobre como instalar novos SDKs e usar a nova estrutura nos links abaixo:

- [iOS versão 3.0.0 ou posterior](app-service-mobile-ios-how-to-use-client-library.md)
- [.NET (Windows/Xamarin) versão 2.0.0 ou posterior](app-service-mobile-dotnet-how-to-use-client-library.md)

Se o seu aplicativo usa notificações por push, anote as instruções de registro específicas para cada plataforma, pois houveram algumas alterações também.

Quando a nova versão do cliente estiver pronta, faça um teste em seu projeto de servidor atualizado. Após validar o funcionamento, libere uma nova versão de seu aplicativo para os clientes. Eventualmente, depois que seus clientes tiverem a oportunidade de receber essas atualizações, você poderá excluir a versão de Serviços Móveis do seu aplicativo. Neste ponto, você atualizou completamente para um aplicativo móvel de serviço de aplicativo usando o servidor de aplicativos móveis SDK mais recente.

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png

<!-- URLs. -->

[Azure portal]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[O que são Aplicativos Móveis?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /pt-BR/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[SDK do Servidor de Aplicativo Móvel]: https://www.npmjs.com/package/azure-mobile-apps
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Agendador do Azure]: /pt-BR/documentation/services/scheduler/
[Trabalho Web]: ../app-service-web/websites-webjobs-resources.md
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[preços do Serviço de Aplicativo]: https://azure.microsoft.com/pt-BR/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md

[portal do Azure]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/pt-BR/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston

<!---HONumber=AcomDC_1210_2015-->