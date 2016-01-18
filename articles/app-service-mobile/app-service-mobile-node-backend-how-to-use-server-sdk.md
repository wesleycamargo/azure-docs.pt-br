<properties
	pageTitle="Como trabalhar com o SDK de servidor back-end do Node.js para Aplicativos Móveis | Serviço de Aplicativo do Azure"
	description="Aprenda a trabalhar com o SDK do servidor back-end do Node.js para Aplicativos Móveis do Serviço de Aplicativo do Azure."
	services="app-service\mobile"
	documentationCenter=""
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="node"
	ms.topic="article"
	ms.date="12/02/2015"
	ms.author="adrianhall"/>

# Como usar o SDK do Node.js para Aplicativos Móveis do Azure

[AZURE.INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Este artigo fornece informações detalhadas e exemplos de como trabalhar com um back-end de Node.js nos Aplicativos Móveis do Serviço de Aplicativo do Azure.

> [AZURE.NOTE]Este SDK está em versão de Visualização. Como resultado, não recomendamos que você use esse SDK em produção. Os exemplos neste documento usam v2.0.0-beta2 do [azure-mobile-apps].

## <a name="Introduction"></a>Introdução

Os Aplicativos Móveis do Serviço de Aplicativo do Azure fornece a capacidade de adicionar uma API Web de acesso a dados otimizada para mobilidade a um aplicativo web. O SDK de Aplicativos Móveis do Serviço de Aplicativo do Azure é fornecido para aplicativos Web ASP.NET e Node.js. O SDK oferece as seguintes operações:

- Operações de tabela (Ler, Inserir, Atualizar Excluir) para acesso a dados
- Operações de API personalizadas

Ambas as operações permitem a autenticação em todos os provedores de identidade permitidos pelo Serviço de Aplicativo do Azure, incluindo provedores de identidade social como Facebook, Twitter, Google e Microsoft, além do Active Directory do Azure para a identidade corporativa.

Você pode encontrar exemplos para cada caso de uso no [diretório de exemplos no GitHub].

### <a name="howto-cmdline-basicapp"></a>Como criar um back-end de Node.js básico usando a linha de comando

Cada back-end de Node.js do Aplicativo Móvel do Serviço de Aplicativo do Azure inicia como um aplicativo ExpressJS. ExpressJS é a estrutura de serviços Web mais popular disponível para o Node.js. Você pode criar um aplicativo [Express] básico da seguinte maneira:

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

		// Define a TodoItem table
		mobile.tables.add('TodoItem');

		// Add the mobile API so it is accessible as a Web API
		app.use(mobile);

		// Start listening on HTTP
		app.listen(process.env.PORT || 3000);

Esse aplicativo cria uma API Web simples otimizada para celular com um único ponto de extremidade, (`/tables/TodoItem`) que fornece acesso não autenticado a um armazenamento de dados SQL subjacente usando um esquema dinâmico. Ele é adequado para os seguintes inícios rápidos de biblioteca de cliente:

- [Início rápido do cliente Android]
- [Início rápido do cliente iOS]
- [Início rápido de cliente Windows Store]
- [Início rápido do cliente Xamarin.iOS]
- [Início rápido do cliente Xamarin.Android]
- [Início rápido do cliente Xamarin.Forms]


Você pode encontrar o código para esse aplicativo básico no [exemplo de aplicativo básico no GitHub].

### <a name="howto-vs2015-basicapp"></a>Como criar um back-end de nó com o Visual Studio 2015

O Visual Studio 2015 exige uma extensão para desenvolver aplicativos Node.js no IDE. Para começar, baixe e instale as [Ferramentas do Node.js 1.1 para Visual Studio]. Depois que as ferramentas do Node.js para o Visual Studio estiverem instaladas, crie um aplicativo Express 4.x:

1. Abra a caixa de diálogo **Novo Projeto** (de **Arquivo** > **Novo** > **Projeto...**).

2. Expanda **Modelos** > **JavaScript** > **Node.js**.

3. Selecione o **Aplicativo básico do Azure Node.js Express 4**.

4. Preencha o nome do projeto. Clique em *OK*.

	![Novo projeto do Visual Studio 2015][1]

5. Clique com o botão direito no nó **npm** e selecione **Instalar Novos pacotes de npm...**.

6. Talvez você precise atualizar o catálogo npm quando criar seu primeiro aplicativo do Node.js. Se isso for necessário, você será solicitado a clicar em **Atualizar**.

7. Digite _azure-mobile-apps_ na caixa de pesquisa. Clique no pacote **azure-mobile-apps 2.0.0** e clique em **Instalar Pacote**.

	![Instalar novos pacotes npm][2]

8. Clique em **Fechar**.

9. Abra o arquivo _app.js_ para adicionar suporte ao SDK de Aplicativos Móveis do Azure: Na linha 6, na parte inferior das exigências de declarações da biblioteca, adicione o seguinte código:

        var bodyParser = require('body-parser');
        var azureMobileApps = require('azure-mobile-apps');

    Aproximadamente na linha 27, após outras instruções app.use, adicione o seguinte código:

        app.use('/users', users);

        // Azure Mobile Apps Initialization
        var mobile = azureMobileApps();
        mobile.tables.add('TodoItem');
        app.use('mobile');

    Salve o arquivo.

10. Execute o aplicativo localmente (a API será fornecida em http://localhost:3000) ou publique no Azure.

### <a name="download-quickstart"></a>Como baixar o projeto de código de início rápido do back-end de Node.js usando Git

Quando você cria um novo back-end de Aplicativo Móvel do Node.js usando a folha **Início Rápido** no portal, um novo projeto do Node.js é criado e implantado em seu site. Você pode adicionar tabelas e APIs e editar arquivos de código para o back-end de Node.js no portal. Você também pode usar uma das várias ferramentas de implantação a fim de baixar o projeto de back-end e adicionar ou modificar tabelas e APIs, e publicar novamente o projeto. Para saber mais, confira o [Guia de implantação do Serviço de Aplicativo do Azure]. o procedimento a seguir usa um repositório Git para baixar o código de projeto de início rápido.

1. Instale o Git, caso ainda não tenha feito isso. As etapas necessárias para instalar o Git variam de acordo com o sistema operacional. Consulte [Instalando o Git](http://git-scm.com/book/en/Getting-Started-Installing-Git) para distribuições específicas de sistemas operacionais e orientações de instalação.

2. Siga as etapas em [Habilitar o repositório do aplicativo Web](../app-service-web/web-sites-publish-source-control.md#Step4) para habilitar o repositório Git para seu site de back-end, anotando o nome de usuário e a senha de implantação.

3. Na folha de seu back-end do Aplicativo Móvel, anote a configuração de **URL de clone de Git**.

4.  Execute o comando `git clone` em uma ferramenta de linha de comando com reconhecimento de Git usando a URL de clone de Git, digitando a senha quando for necessário, como no exemplo a seguir:

		$ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

5. Navegue até o diretório local, no exemplo acima é /todolist, e observe que os arquivos do projeto foram baixados. Na subpasta /tables, você encontrará um arquivo todoitem.json, que define as permissões na tabela, e o arquivo todoitem.js, que define os scripts de operação CRUD da tabela.

6. Depois de fazer as alterações nos arquivos do projeto, execute os seguintes comandos para adicionar, confirmar e carregar as alterações no site:

		$ git commit -m "updated the table script"
		$ git push origin master

	Ao adicionar novos arquivos ao projeto, primeiro você precisa executar o comando `git add .`.

O site é publicado novamente sempre que um novo conjunto de confirmações é enviado ao site.

### <a name="howto-publish-to-azure"></a>Como publicar seu back-end de Node.js no Azure

O Microsoft Azure fornece vários mecanismos de publicação de back-end de Node.js dos Aplicativos Móveis do Serviço de Aplicativo do Azure no serviço do Azure. Eles incluem a utilização das ferramentas de implantação integradas ao Visual Studio, das ferramentas de linha de comando e das opções de implantação contínua com base no controle de origem. Para saber mais sobre esse tópico, confira o [Guia de implantação do Serviço de Aplicativo do Azure].

O Serviço de Aplicativo do Azure tem recomendações específicas para aplicativo Node.js que você deve examinar antes de implantar:

- Como [especificar a versão do Node]
- Como [usar módulos do Node]

## <a name="TableOperations"></a>Operações de tabela

O SDK do servidor de Node.js do azure-mobile-apps fornece mecanismos para expor tabelas de dados armazenadas no Banco de Dados SQL do Azure como uma API Web. Cinco operações são fornecidas.

| Operação | Descrição |
| --------- | ----------- |
| GET /tables/\_tablename\_ | Obter todos os registros na tabela |
| GET /tables/\_tablename\_/:id | Obter um registro específico na tabela |
| POST /tables/\_tablename\_ | Criar um novo registro na tabela |
| PATCH /tables/\_tablename\_/:id | Atualizar um registro existente na tabela |
| DELETE /tables/\_tablename\_/:id | Excluir um registro na tabela |

Essa API Web dá suporte a [OData] e estende o esquema da tabela para dar suporte a [à sincronização de dados offline].

### <a name="howto-dynamicschema"></a>Como definir tabelas usando um esquema dinâmico

Antes de poder usar uma tabela, ela deve ser definida. As tabelas podem ser definidas com um esquema estático (quando o desenvolvedor define as colunas no esquema) ou dinamicamente (quando o SDK controla o esquema com base em solicitações de entrada). Além disso, o desenvolvedor pode controlar aspectos específicos da API Web adicionando código Javascript à definição.

Como uma prática recomendada, você deve definir cada tabela em um arquivo Javascript no diretório de tabelas e usar o método tables.import() para importar as tabelas. Estendendo o basic-app, o arquivo app.js seria ajustado:

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
	    mobile = azureMobileApps();

    // Define the database schema that is exposed
    mobile.tables.import('./tables');

    // Provide initialization of any tables that are statically defined
    mobile.tables.initialize().then(function () {
        // Add the mobile API so it is accessible as a Web API
        app.use(mobile);

        // Start listening on HTTP
        app.listen(process.env.PORT || 3000);
    });

Definir a tabela em. /tables/TodoItem.js:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Additional configuration for the table goes here

    module.exports = table;

As tabelas usam o esquema dinâmico por padrão. Para desabilitar o esquema dinâmico globalmente, defina a Configuração do Aplicativo **MS\_DynamicSchema** como falso no Portal do Azure.

Você pode encontrar um exemplo completo no [exemplo de tarefas pendentes no GitHub].

### <a name="howto-staticschema"></a>Como definir tabelas usando um esquema estático

Você pode definir explicitamente as colunas que serão expostas usando a API Web. O SDK do NOde.js do azure-mobile-apps adicionará automaticamente as colunas adicionais necessárias à sincronização de dados offline para a lista fornecida por você. Por exemplo, os aplicativos de cliente QuickStart exigem uma tabela com duas colunas: texto (uma cadeia de caracteres) e completo (um valor booliano). Isso pode ser definido no arquivo JavaScript de definição de tabela (localizado no diretório de tabelas) da seguinte maneira:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    module.exports = table;

Se você definir as tabelas estaticamente, também deverá chamar o método tables.initialize() para criar o esquema de banco de dados na inicialização. O método tables.initialize() retorna uma [Promessa]; isso é usado para garantir que o serviço Web não atenda a solicitações antes do banco de dados que está sendo inicializado.

### <a name="howto-sqlexpress-setup"></a>Como usar o SQL Express como um armazenamento de dados de desenvolvimento em sua máquina local

O SDK do Node dos Aplicativos Móveis do Azure fornece três opções para fornecer dados prontos para uso:

- Usar o driver de **memória** para fornecer um repositório de exemplo não persistente
- Usar o driver de **mssql** para fornecer um repositório de dados do SQL Express para desenvolvimento
- Use o driver de **mssql** para fornecer um repositório de dados do Banco de Dados SQL do Azure para produção

O SDK do Node.js dos Aplicativos Móveis do Azure usa o [pacote de Node.js mssql] para estabelecer e usar uma conexão com o SQL Express e com o Banco de Dados SQL do Azure. Este pacote requer que você habilite conexões TCP na instância do SQL Express.

> [AZURE.TIP]O driver de memória não fornece um conjunto completo de recursos para teste. Se você quiser testar localmente seu back-end, recomendamos o uso de um repositório de dados SQL Express e o driver do mssql.

1. Baixe e instale o [Microsoft SQL Server 2014 Express]. Instale o SQL Server 2014 Express com a edição Ferramentas. A menos que você precise explicitamente do suporte de 64 bits, a versão de 32 bits consumirá menos memória durante a execução.

2. Execute o Gerenciador de Configurações do SQL Server 2014.

  1. Expanda o nó **Configuração da Rede do SQL Server** no menu de árvore à esquerda.
  2. Clique em **Protocolos para SQLEXPRESS**.
  3. Clique com o botão direito em **TCP/IP** e escolha **Habilitar**. Clique em **OK** na caixa de diálogo pop-up.
  4. Clique com o botão direito em **TCP/IP** e escolha **Propriedades**.
  5. Clique na guia **Endereços IP**.
  6. Encontre o nó **IPAll**. No campo **Porta TCP**, insira **1433**.

	 	 ![Configure SQL Express for TCP/IP][3]
  7. Clique em **OK**. Clique em **OK** no diálogo pop-up.
  8. Clique em **Serviços do SQL Server** no menu de árvore no lado esquerdo.
  9. Clique com o botão direito em **SQL Server (SQLEXPRESS)** e escolha **Reiniciar**
  10. Feche o Gerenciador de Configurações do SQL Server 2014.

3. Executar o SQL Server 2014 Management Studio e conectar-se à instância do SQL Express local

  1. Clique com o botão direito em sua instância no Explorador de Objetos e escolha **Propriedades**
  2. Escolha a página **Segurança**.
  3. Verifique se a opção **Modo de Autenticação do SQL Server e do Windows** está selecionada
  4. Clique em **OK**

  		![Configure SQL Express Authentication][4]

  5. Expanda **Segurança** > **Logons** no Explorador de Objetos
  6. Clique com o botão direito em **Logons** e escolha **Novo Logon...**
  7. Insira um nome de logon. Selecione **Autenticação do SQL Server**. Digite uma Senha e, depois, digite a mesma senha em **Confirmar senha**. Observe que a senha deve atender aos requisitos de complexidade do Windows.
  8. Clique em **OK**

  		![Add a new user to SQL Express][5]

  9. Clique com o botão direito em seu novo logon e escolha **Propriedades**
  10. Escolha a página **Funções de Servidor**
  11. Marque a caixa ao lado da função de servidor **dbcreator**
  12. Clique em **OK**
  13. Feche o SQL Server 2015 Management Studio.

Não deixe de registrar o nome de usuário e senha que você selecionou. Talvez seja necessário atribuir funções de servidor ou permissões adicionais dependendo dos requisitos do banco de dados específico.

O aplicativo Node.js lerá a variável de ambiente **SQLCONNSTR\_MS\_TableConnectionString** para ler a cadeia de conexão desse banco de dados. Você pode definir isso em seu ambiente. Por exemplo, você pode usar o PowerShell para definir essa variável de ambiente:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Observe que você deve acessar o banco de dados usando conexão TCP/IP e fornecer um nome de usuário e uma senha para a conexão.

### <a name="howto-config-localdev"></a>Como configurar seu projeto para desenvolvimento local

Os Aplicativos Móveis do Azure leem um arquivo JavaScript chamado _azureMobile.js_ no sistema de arquivos local. Você não deve usar esse arquivo para configurar o SDK dos Aplicativos Móveis do Azure em produção; em vez disso, use as Configurações de Aplicativo no [Portal do Azure]. O arquivo _azureMobile.js_ deve exportar um objeto de configuração. As configurações mais comuns são:

- Configurações do banco de dados
- Configurações de registro em log de diagnóstico
- Configurações de CORS alternativas

Um exemplo de arquivo _azureMobile.js_ implementando as configurações de banco de dados fornecidas acima é dado abaixo:

    module.exports = {
        cors: {
            origins: [ 'localhost' ]
        },
        data: {
            provider: 'mssql',
            server: '127.0.0.1',
            database: 'mytestdatabase',
            user: 'azuremobile',
            password: 'T3stPa55word'
        },
        logging: {
            level: 'verbose'
        }
    };

Recomendamos que você adicione _azureMobile.js_ ao arquivo _.gitignore_ (ou outro arquivo de controle do código-fonte a ser ignorado) para impedir que as senhas sejam armazenadas na nuvem. Sempre defina as configurações de produção nas Configurações do Aplicativo no [Portal do Azure].

### <a name="howto-appsettings"><a>Configurações de Aplicativo para configurar seu Aplicativo Móvel

A maioria das configurações no arquivo _azureMobile.js_ tem uma Configuração do Aplicativo equivalente no [Portal do Azure]. Use a lista a seguir para configurar seu aplicativo nas Configurações de Aplicativo:

| Configurações de Aplicativo | Configuração _azureMobile.js_ | Descrição | Valores Válidos |
| :-------------------------- | :------------------------ | :---------------------------------------- | :------------------------------------------ |
| **MS\_MobileAppName** | name | O nome do aplicativo | cadeia de caracteres |
| **MS\_MobileLoggingLevel** | logging.level | Nível de log mínimo das mensagens a serem registradas | erro, aviso, informações, detalhado, depuração, simples |
| **MS\_DebugMode** | depurar | Habilitar ou desabilitar o modo de depuração | verdadeiro, falso |
| **MS\_TableSchema** | data.schema | Nome do esquema padrão para tabelas SQL | cadeia de caracteres (padrão: dbo) |
| **MS\_DynamicSchema** | data.dynamicSchema | Habilitar ou desabilitar o modo de depuração | verdadeiro, falso |
| **MS\_DisableVersionHeader** | versão (definido como indefinido)| Desabilita o cabeçalho X-ZUMO-Server-Version | verdadeiro, falso |
| **MS\_SkipVersionCheck** | skipversioncheck | Desabilita a verificação de versão de API do cliente | verdadeiro, falso |

Para definir uma Configuração do Aplicativo:

1. Faça logon no [Portal do Azure].
2. Selecione **Todos os recursos** ou **Serviços de Aplicativos** e clique no nome do seu Aplicativo Móvel.
3. A folha Configurações será aberta por padrão; se não abrir, clique em **Configurações**.
4. Clique em **Configurações do aplicativo** no menu GERAL.
5. Role até a seção Configurações de Aplicativo.
6. Se a configuração do aplicativo já existir, clique no valor da configuração do aplicativo para editá-lo.
7. Se a configuração do aplicativo não existir, insira a Configuração do Aplicativo na caixa Chave e o valor na caixa Valor.
8. Quando você tiver concluído, clique em **Salvar**.

A alteração da maioria das Configurações do Aplicativo requer o reinício do serviço.

### <a name="howto-use-sqlazure"></a>Como usar o Banco de Dados SQL como o armazenamento de dados de produção

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

O uso do Banco de Dados SQL do Azure como armazenamento de dados é idêntico em todos os tipos de aplicativo do Serviço de Aplicativo do Azure. Se você não tiver feito isso, siga estas etapas para criar um novo back-end do Aplicativo Móvel.

1. Faça logon no [Portal do Azure].

2. No canto superior esquerdo da janela, clique no botão **+NOVO** > **Web + Celular** > **Aplicativo Móvel** e forneça um nome ao back-end do Aplicativo Móvel.

3. Na caixa **Grupo de Recursos**, digite o mesmo nome do aplicativo.

4. O plano Serviço de Aplicativo Padrão será selecionado. Se você deseja alterar o plano do Serviço do Aplicativo, é possível fazer isso clicando em Plano do Serviço de Aplicativo > **+ Criar Novo**. Forneça um nome ao novo Plano de Serviço de Aplicativo e selecione um local apropriado. Clique em Camada de Preços e selecione uma camada de preços apropriada para o serviço. Selecione **Exibir tudo** para exibir mais opções de preço, como **Gratuito** e **Compartilhado**. Depois de selecionar a camada de preços, clique no botão **Selecionar**. De volta à folha **Plano de Serviço de Aplicativo**, clique em **OK**.

5. Clique em **Criar**. Isso cria um back-end de aplicativo móvel onde você, mais tarde, implantará o projeto do servidor. O provisionamento de um back-end de aplicativo móvel pode levar alguns minutos. Depois que o back-end do Aplicativo Móvel for provisionado, o portal abrirá a folha **Configurações** do back-end do Aplicativo Móvel.

Depois que o back-end do Aplicativo Móvel for criado, você poderá conectar um banco de dados existente do SQL ao back-end do Aplicativo Móvel ou criar um novo Banco de Dados SQL do Azure. Nesta seção, criaremos um novo Banco de Dados SQL.

> [AZURE.NOTE]Se já houver um banco de dados no mesmo local do novo back-end do aplicativo móvel, você poderá escolher **Usar um banco de dados existente** e selecionar esse banco de dados. O uso de um banco de dados em um local diferente não é recomendado devido a latências maiores e aos custos adicionais de largura de banda.

6. No novo back-end do Aplicativo Móvel, clique em **Configurações** > **Aplicativo Móvel** > **Dados** > **+ Adicionar**.

7. Na folha **Adicionar conexão de dados**, clique em **Banco de Dados SQL - Definir configurações necessárias** > **Criar um novo banco de dados**. Insira o nome do novo banco de dados no campo **Nome**.

8. Clique em **Servidor**. Na folha **Novo servidor**, insira um nome de servidor exclusivo no campo **Nome do servidor** e forneça um **Logon de administração de servidor** e **Senha**adequados. Verifique se **Permitir que os serviços do Azure acessem o servidor** está marcado. Clique em **OK**.

	![Criar um Banco de Dados SQL do Azure][6]

9. Na folha **Novo banco de dados**, clique em **OK**.

10. De volta à folha **Adicionar conexão de dados**, selecione **Cadeia de conexão**, insira o logon e a senha que você acabou de fornecer ao criar o banco de dados. Se você usar um banco de dados existente, forneça as credenciais de logon desse banco de dados. Depois de inserir, clique em **OK**.

11. De volta à folha **Adicionar conexão de dados**, clique em **OK** para criar o banco de dados.

<!--- END OF ALTERNATE INCLUDE -->

A criação do banco de dados pode levar alguns minutos. Use a área **Notificações** para monitorar o progresso da implantação. Não siga adiante enquanto o banco de dados não tiver sido implantado com êxito. Uma vez implantado com êxito, uma Cadeia de conexão será criada para a instância do Banco de Dados SQL em suas configurações do aplicativo back-end móvel. Você pode ver essa configuração do aplicativo em **Configurações** > **Configurações do aplicativo** > **Cadeias de conexão**.

### <a name="howto-tables-auth"></a>Como exigir autenticação para acesso às tabelas

Se você deseja usar a Autenticação do Serviço de Aplicativo com o ponto de extremidade das tabelas, precisa configurar a Autenticação do Serviço de Aplicativo no [Portal do Azure] primeiro. Para obter mais detalhes sobre como configurar a autenticação em um Serviço de Aplicativo do Azure, examine o Guia de Configuração para o provedor de identidade que você pretende usar:

- [Como configurar a autenticação do Active Directory do Azure]
- [Como configurar a autenticação do Facebook]
- [Como configurar a autenticação do Google]
- [Como configurar a autenticação da Microsoft]
- [Como configurar a autenticação do Twitter]

Cada tabela tem uma propriedade de acesso que pode ser usada para controlar o acesso à tabela. O exemplo a seguir mostra uma tabela estaticamente definida com autenticação necessária.

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

A propriedade de acesso pode assumir um dentre três valores

  - *anonymous* indica que o aplicativo cliente tem permissão para ler os dados sem autenticação
  - *authenticated* indica que o aplicativo cliente deve enviar um token de autenticação válido com a solicitação
  - *desabilitado* indica que essa tabela está desabilitada no momento

Se a propriedade de acesso estiver indefinida, o acesso não autenticado será permitido.

### <a name="howto-tables-disabled"></a>Como desabilitar o acesso a operações de tabela específicas

Além de aparecer na tabela, a propriedade de acesso pode ser usada para controlar operações individuais. Há quatro operações:

  - *read* é a operação RESTful GET na tabela
  - *insert* é a operação RESTful POST na tabela
  - *update* é a operação RESTful PATCH na tabela
  - *delete* é a operação RESTful DELETE na tabela

Por exemplo, você pode querer fornecer uma tabela não autenticada somente leitura. Isso pode ser fornecido pela definição de tabela abaixo:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Read-Only table - only allow READ operations
    table.read.access = 'anonymous';
    table.insert.access = 'disabled';
    table.update.access = 'disabled';
    table.delete.access = 'disabled';

    module.exports = table;

### <a name="howto-tables-query"></a>Como ajustar a consulta usada em operações de tabela

Um requisito comum para operações de tabela é fornecer uma exibição restrita dos dados. Por exemplo, você pode fornecer uma tabela que é marcada com a ID de usuário autenticado, de modo que o usuário só possa ler ou atualizar seus próprios registros. A definição da tabela abaixo fornece essa funcionalidade:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define a static schema for the table
    table.columns = {
        "userId": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;

    // Require authentication for this table
    table.access = 'authenticated';

    // Ensure that only records for the authenticated user are retrieved
    table.read(function (context) {
		context.query.where({ userId: context.user.id });
		return context.execute();
	});

    // When adding records, add or overwrite the userId with the authenticated user
    table.insert(function (context) {
	    context.item.userId = context.user.id;
	    return context.execute();
    }

    module.exports = table;

As operações que normalmente executam uma consulta têm uma propriedade de consulta que você pode ajustar com um cláusula where. A propriedade de consulta é um objeto [QueryJS] usado para converter uma consulta OData em algo que pode ser processado pelo back-end de dados. Para casos de igualdade simples (como o mostrado acima), um mapa pode ser usado. Também é relativamente fácil adicionar cláusulas SQL específicas:

    context.query.where('myfield eq ?', 'value');

### <a name="howto-tables-softdelete"></a>Como configurar a Exclusão Reversível em uma tabela

A Exclusão Reversível não exclui registros. Em vez disso, ela os marca como excluídos no banco de dados definindo a coluna excluída como true. O SDK de Aplicativos Móveis do Azure remove automaticamente registros com exclusão reversível dos resultados, a menos que o SDK de cliente móvel use IncludeDeleted(). Para configurar uma tabela para exclusão reversível, defina a propriedade softDelete no arquivo de definição de tabela. Um exemplo poderia ser:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
		"complete": "boolean"
	};

	// Turn off dynamic schema
	table.dynamicSchema = false;

	// Turn on Soft Delete
	table.softDelete = true;

	// Require authentication to access the table
	table.access = 'authenticated';

	module.exports = table;

Você precisa estabelecer um mecanismo para limpar registros, seja usando um aplicativo cliente, um trabalho Web ou um mecanismo personalizado.

### <a name="howto-tables-seeding"></a>Como propagar seu banco de dados com dados

Ao criar um novo aplicativo, você pode querer propagar uma tabela com dados. Isso pode ser feito no arquivo JavaScript de definição de tabela da seguinte maneira:

	var azureMobileApps = require('azure-mobile-apps');

	var table = azureMobileApps.table();

	// Define the columns within the table
	table.columns = {
		"text": "string",
		"complete": "boolean"
	};
	table.seed = [
		{ text: 'Example 1', complete: false },
		{ text: 'Example 2', complete: true }
	];

	// Turn off dynamic schema
	table.dynamicSchema = false;

	// Require authentication to access the table
	table.access = 'authenticated';

	module.exports = table;

É importante observar que a propagação de dados é feita somente quando a tabela é criada pelo SDK de Aplicativos Móveis do Azure. Se a tabela já existir no banco de dados, nenhum dado será injetado nela. Se o esquema dinâmico estiver habilitado, o esquema será inferido dos dados propagados.

É recomendável que você chame explicitamente o método Initialize () para criar a tabela quando o serviço começar a ser executado.

## <a name="CustomAPI"></a>APIs personalizadas

Além da API de acesso a dados por meio do ponto de extremidade/tabelas, os Aplicativos Móveis do Azure podem fornecer cobertura de API personalizada. As APIs personalizadas são definidas de forma semelhante às definições de tabela e pode acessar todos os mesmos recursos, incluindo autenticação.

Se você deseja usar a Autenticação do Serviço de Aplicativo com uma API Personalizada, precisa configurar a Autenticação do Serviço de Aplicativo no [Portal do Azure] primeiro. Para obter mais detalhes sobre como configurar a autenticação em um Serviço de Aplicativo do Azure, examine o Guia de Configuração para o provedor de identidade que você pretende usar:

- [Como configurar a autenticação do Active Directory do Azure]
- [Como configurar a autenticação do Facebook]
- [Como configurar a autenticação do Google]
- [Como configurar a autenticação da Microsoft]
- [Como configurar a autenticação do Twitter]

### <a name="howto-customapi-basic"></a>Como definir uma API personalizada simples

As APIs personalizadas são definidas da mesma forma que a API de tabelas.

1. Crie um diretório **api**
2. Crie um arquivo JavaScript de definição de API no diretório **api**.
3. Use o método import para importar o diretório **api**.

Aqui está a definição de api do protótipo com base na amostra de aplicativo básico que usamos anteriormente.

	var express = require('express'),
		azureMobileApps = require('azure-mobile-apps');

	var app = express(),
		mobile = azureMobileApps();

	// Import the Custom API
	mobile.api.import('./api');

	// Add the mobile API so it is accessible as a Web API
	app.use(mobile);

	// Start listening on HTTP
	app.listen(process.env.PORT || 3000);

Vamos pegar uma API simples que retorna a data do servidor usando o método _Date.now()_. Eis o arquivo api/date.js:

	var api = {
		get: function (req, res, next) {
			var date = { currentTime: Date.now() };
			res.status(200).type('application/json').send(date);
		});
	};

	module.exports = api;

Cada parâmetro é um dos verbos padrão RESTful - GET, POST, PATCH ou DELETE. O método é uma função padrão [ExpressJS Middleware] que envia a saída necessária.

### <a name="howto-customapi-auth"></a>Como solicitar autenticação para acesso a uma API personalizada

O SDK de aplicativos móveis do Azure implementa a autenticação da mesma forma para o ponto de extremidade de tabelas e para APIs personalizadas. Para adicionar autenticação à API desenvolvida na seção anterior, adicione uma propriedade **access**:

	var api = {
		get: function (req, res, next) {
			var date = { currentTime: Date.now() };
			res.status(200).type('application/json').send(date);
		});
	};
	// All methods must be authenticated.
	api.access = 'authenticated';

	module.exports = api;

Você também pode especificar a autenticação em operações específicas:

	var api = {
		get: function (req, res, next) {
			var date = { currentTime: Date.now() };
			res.status(200).type('application/json').send(date);
		});
	};
	// The GET methods must be authenticated.
	api.get.access = 'authenticated';

	module.exports = api;

O mesmo token que é usado para o ponto de extremidade de tabelas deve ser usado para APIs personalizadas que requerem autenticação.

## <a name="Debugging"></a>Depuração e solução de problemas

O Serviço de Aplicativo do Azure fornece várias técnicas de depuração e de solução de problemas para aplicativos Node.js. Todas essas técnicas estão disponíveis.

- [Monitorando um Serviço de Aplicativo do Azure]
- [Habilitar o registro em log de diagnósticos no Serviço de Aplicativo do Azure]
- [Solucionar problemas de um Serviço de Aplicativo do Azure no Visual Studio]

### <a name="howto-diagnostic-logs"></a>Como gravar nos logs de diagnóstico dos Aplicativos Móveis do Azure

Os aplicativos Node.js têm acesso a uma ampla gama de ferramentas de log de diagnóstico. Internamente, o SDK do Node.js dos Aplicativos Móveis do Azure usa o [Winston] para o registro em log de diagnóstico. Ele é ativado automaticamente habilitando o modo de depuração ou definindo a configuração de aplicativo **MS\_DebugMode** como true no [Portal do Azure]. Os logs gerados aparecerão nos Logs de Diagnóstico no [Portal do Azure].

## <a name="in-portal-editing"></a>Experiência de edição de código no portal

As ferramentas especiais no Portal do Azure facilitam o trabalho com um projeto de back-end do Node.js sem a necessidade de baixar o projeto de código. Tabelas e APIs fáceis de usar permitem que você crie e trabalhe com tabelas e APIs personalizadas diretamente no Portal. Você pode até editar a operação da tabela e os scripts da API diretamente no Portal usando o editor "Monaco" do Visual Studio Team Services.

### <a name="work-easy-tables"></a>Como trabalhar com tabelas fáceis no portal do Azure

Quando você clica em **Tabelas fáceis** em suas configurações de site de back-end, você pode adicionar uma nova tabela ou modificar ou excluir uma tabela existente. Você também pode ver dados na tabela.

![Trabalhar com Tabelas fáceis](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-tables.png)

Os comandos a seguir estão disponíveis na barra de comandos de uma tabela:

+ **Alterar permissões**: modifique a permissão para operações de leitura, inserção, atualização e exclusão na tabela. As opções são permitir acesso anônimo, exigir autenticação ou desabilitar todo o acesso à operação. Isso modifica o arquivo de código do projeto table.json.
+ **Editar script**: o arquivo de script da tabela é aberto no editor do Visual Studio Team Services.
+ **Gerenciar esquema**: adicione ou exclua colunas, ou altere o índice da tabela.
+ **Limpar tabela**: trunca uma tabela existente excluindo todas as linhas de dados, mas deixando o esquema inalterado.
+ **Excluir linhas**: exclua linhas individuais de dados.
+ **Exibir logs de streaming**: conecta você ao serviço de log de streaming de seu site.

###<a name="work-easy-apis"></a>Como trabalhar com APIs fáceis no portal do Azure

Quando você clica em **APIs fáceis** em suas configurações de site de back-end, pode adicionar um novo ponto de extremidade de API personalizada ou modificar ou excluir um ponto de extremidade de API existente.

![Trabalhar com APIs fáceis](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-apis.png)

No portal, você pode alterar as permissões de acesso de uma determinada ação HTTP, editar o arquivo de script da API no editor do Visual Studio Team Services ou exibir os logs de streaming.

###<a name="online-editor"></a>Como editar o código no Visual Studio Team Services

O portal do Azure permite a edição dos arquivos de script de back-end do Node.js no Visual Studio Team Services sem a necessidade de baixar o projeto no computador local. Para editar arquivos de script no editor online:

1. Na folha do back-end de Aplicativo Móvel, clique em **Todas as configurações** > em **Tabelas fáceis** ou **APIs fáceis**, clique em uma tabela ou API e clique em **Editar script**. O arquivo de script é aberto no editor do Visual Studio Team Services.

	![Editor de código do Visual Studio Team Services](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-visual-studio-editor.png)

2. Faça as alterações no arquivo de código no editor online. As alterações são salvas automaticamente enquanto você digita.

Também é possível executar, do editor, o código no site


<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ../../includes/media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png

<!-- URLs -->
[Início rápido do cliente Android]: app-service-mobile-android-get-started.md
[Início rápido do cliente iOS]: app-service-mobile-ios-get-started.md
[Início rápido do cliente Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
[Início rápido do cliente Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
[Início rápido do cliente Xamarin.Forms]: app-service-mobile-xamarin-forms-get-started.md
[Início rápido de cliente Windows Store]: app-service-mobile-windows-store-dotnet-get-started.md
[HTML/Javascript Client QuickStart]: app-service-html-get-started.md
[à sincronização de dados offline]: app-service-mobile-offline-data-sync.md
[Como configurar a autenticação do Active Directory do Azure]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Como configurar a autenticação do Facebook]: app-service-mobile-how-to-configure-facebook-authentication.md
[Como configurar a autenticação do Google]: app-service-mobile-how-to-configure-google-authentication.md
[Como configurar a autenticação da Microsoft]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Como configurar a autenticação do Twitter]: app-service-mobile-how-to-configure-twitter-authentication.md
[Guia de implantação do Serviço de Aplicativo do Azure]: ../app-service-web/web-sites-deploy.md
[Monitorando um Serviço de Aplicativo do Azure]: ../app-service-web/web-sites-monitor.md
[Habilitar o registro em log de diagnósticos no Serviço de Aplicativo do Azure]: ../app-service-web/web-sites-enable-diagnostic-log.md
[Solucionar problemas de um Serviço de Aplicativo do Azure no Visual Studio]: ../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md
[especificar a versão do Node]: ../nodejs-specify-node-version-azure-apps.md
[usar módulos do Node]: ../nodejs-use-node-mobiles-azure-apps.md
[Create a new Azure App Service]: ../app-service-web/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: http://expressjs.com/

[Portal do Azure]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promessa]: https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Global_Objects/Promise
[exemplo de aplicativo básico no GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[exemplo de tarefas pendentes no GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[diretório de exemplos no GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Ferramentas do Node.js 1.1 para Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[pacote de Node.js mssql]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/pt-BR/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston

<!---HONumber=AcomDC_0107_2016-->