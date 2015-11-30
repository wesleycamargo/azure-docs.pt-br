<properties
	pageTitle="Como trabalhar com o SDK de servidor do back-end de nó para Aplicativos Móveis | Serviço de Aplicativo do Azure"
	description="Aprenda como trabalhar com o SDK do servidor de back-end de nó para Aplicativos Móveis do Serviço de Aplicativo do Azure."
	services="app-service\mobile"
	documentationCenter=""
	authors="adrianha"
	manager=""
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="node"
	ms.topic="article"
	ms.date="11/13/2015"
	ms.author="adrianha"/>

# Como usar o SDK do nó Aplicativos Móveis do Azure

Este artigo fornece informações detalhadas e exemplos de como trabalhar com um back-end de nó nos Aplicativos Móveis dos Serviços Móveis do Azure.

	> [AZURE.NOTE] This SDK is in PREVIEW.  As a result, we do not recommend that you use this SDK in production.  The examples
	in this document use v2.0.0-beta1 of azure-mobile-apps.

## <a name="Introduction"></a>Introdução

Os Aplicativos Móveis do Serviço de Aplicativo do Azure fornece a capacidade de adicionar uma API Web de acesso a dados otimizada para mobilidade a um aplicativo web. O SDK de Aplicativos Móveis do Serviço de Aplicativo do Azure é fornecido para o aplicativos Web ASP.NET e NodeJS e fornece as seguintes operações:

- Operações de tabela (Ler, Inserir, Atualizar Excluir) para acesso a dados
- Operações de API personalizadas

Ambas as operações permitem a autenticação em todos os provedores de identidade permitidos pelo Serviço de Aplicativo do Azure, incluindo provedores de identidade social como Facebook, Twitter, Google e Microsoft, além do Active Directory do Azure para a identidade corporativa.

Você pode encontrar exemplos para cada caso de uso no [diretório de exemplos no GitHub].

### <a name="howto-cmdline-basicapp"></a>Criar um nó básico de back-end usando a linha de comando

Cada back-end do nó de Aplicativo Móvel do Serviço de Aplicativo do Azure inicia como um aplicativo ExpressJS. ExpressJS é a estrutura de serviços Web mais popular disponível para o nó. Você pode criar um aplicativo básico ExpressJS Node da seguinte maneira:

1. Em um comando ou a janela do PowerShell, crie um novo diretório para seu projeto.

	```
	mkdir basicapp
	```

2. Execute o npm init para inicializar a estrutura do pacote.

	```
	cd basicapp
	npm init
	```

	O comando init npm solicitará um conjunto de perguntas para inicializar o projeto. Consulte a saída de exemplo abaixo

	![A saída de init npm][0]

3. Instale as bibliotecas express e azure-mobile-apps do repositório npm.

	```
	npm install --save express azure-mobile-apps
	```

4. Crie um arquivo app.js para implementar o servidor móvel básico.

	```
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
	```

Esse aplicativo cria uma API Web simples otimizada para mobilidade com um único ponto de extremidade, /tables/TodoItem, que fornece acesso não autenticado a um armazenamento de dados SQL subjacente usando um esquema dinâmico. Ele é adequado para os seguintes inícios rápidos de biblioteca de cliente:

- [Início rápido do cliente iOS]
- [Início rápido do cliente Xamarin.iOS]
- [Início rápido do cliente Xamarin.Android]
- [Início rápido do cliente Xamarin.Forms]
- [Início rápido de cliente Windows Phone]
- [Guia de início rápido do cliente HTML/Javascript]

Você pode encontrar o código para esse aplicativo básico no [exemplo de aplicativo básico no GitHub].

### <a name="howto-vs2015-basicapp"></a>Criar um back-end de nó com o Visual Studio 2015

O Visual Studio de 2015 requer uma extensão para desenvolver aplicativos Node no IDE. Para começar, baixe e instale as [Ferramentas do Node.js 1.1 para Visual Studio]. Depois que as ferramentas do Node.js para o Visual Studio estiverem instaladas, crie um aplicativo Express 4.x:

1. Abra o diálogo **Novo Projeto** (de **Arquivo** > **Novo** > **Projeto...**).

2. Expanda **Modelos** > **JavaScript** > **Node.js**.

3. Selecione o **aplicativo básico do Azure Node.js Express 4**.

4. Preencha o nome do projeto. Clique em *OK*.

	![Novo projeto do Visual Studio 2015][1]

5. Clique com o botão direito do mouse no nó **npm** e selecione **Instalar novos pacotes de npm...**.

6. Você precisará atualizar o catálogo npm quando criar seu primeiro aplicativo Node. Clique em **Atualizar**.

7. Digite _azure-mobile-apps_ na caixa de pesquisa. Clique no pacote **azure-mobile-apps 2.0.0** e clique em **Instalar Pacote**.

	![Instalar novos pacotes npm][2]

8. Clique em **Fechar**.

9. Abra o arquivo _app.js_ para adicionar suporte ao SDK de Aplicativos Móveis do Azure:

  a. Na linha 6, adicione o seguinte código:

    ```
    var bodyParser = require('body-parser');
    var azureMobileApps = require('azure-mobile-apps');
    ```

  b. Aproximadamente na linha 27, adicione o seguinte código:

	```
	app.use('/users', users);

	// Azure Mobile Apps Initialization
	var mobile = azureMobileApps();
	mobile.tables.add('TodoItem');
	app.use('mobile');
	```

  c. Salve o arquivo.

10. Execute o aplicativo localmente (a API será fornecida em http://localhost:3000) ou publique no Azure.

### <a name="howto-publish-to-azure"></a>Publicar seu back-end de nó no Azure

O Microsoft Azure fornece vários mecanismos de publicação de back-end do nó Aplicativos Móveis do Serviço de Aplicativo do Azure no serviço do Azure. Eles incluem a utilização das ferramentas de implantação integradas ao Visual Studio, das ferramentas de linha de comando e das opções de implantação contínua com base no controle de origem. Para saber mais sobre esse tópico, consulte o [Guia de implantação do Serviço de Aplicativo do Azure].

O Serviço de Aplicativo do Azure tem recomendações específicas para aplicativo Node que você deve examinar antes de implantar:

- Como [especificar a versão do nó]
- Como [usar Módulos de nó]

## <a name="TableOperations"></a>Operações de tabela

O SDK do servidor de nó azure-mobile-apps fornece mecanismos para expor as tabelas de dados armazenadas no SQL Azure como uma API Web. Cinco operações são fornecidas.

| Operação | Descrição |
| --------- | ----------- |
| GET /tables/\_tablename\_ | Obter todos os registros na tabela |
| GET /tables/\_tablename\_/:id | Obter um registro específico na tabela |
| POST /tables/\_tablename\_ | Criar um novo registro na tabela |
| PUT /tables/\_tablename\_/:id | Atualizar um registro existente na tabela |
| DELETE /tables/\_tablename\_/:id | Excluir um registro na tabela |

Essa API Web dá suporte a [OData] e estende o esquema da tabela para dar suporte a [à sincronização de dados offline].

### <a name="howto-dynamicschema"></a>Definir tabelas usando um esquema dinâmico

Antes de poder usar uma tabela, ela deve ser definida. As tabelas podem ser definidas com um esquema estático (quando o desenvolvedor define as colunas no esquema) ou dinamicamente (quando o SDK controla o esquema com base em solicitações de entrada). Além disso, o desenvolvedor pode controlar aspectos específicos da API Web adicionando código Javascript à definição.

Como uma prática recomendada, você deve definir cada tabela em um arquivo Javascript no diretório de tabelas e usar o método tables.import() para importar as tabelas. Estendendo o basic-app, o arquivo app.js seria ajustado:

```
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
```

Definir a tabela em. /tables/TodoItem.js:

```
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Additional configuration for the table goes here

module.exports = table;
```

As tabelas usam o esquema dinâmico por padrão. Para desabilitar o esquema dinâmico globalmente, defina a configuração do aplicativo **MS\_DynamicSchema** como falso no Portal do Azure.

Você pode encontrar um exemplo completo no [exemplo de tarefas pendentes no GitHub].

### <a name="howto-staticschema"></a>Definir tabelas usando um esquema estático

Você pode definir explicitamente as colunas que serão expostas usando a API Web. O SDK do nó azure-mobile-apps adicionará automaticamente as colunas adicionais necessárias à sincronização de dados offline para a lista fornecida por você. Por exemplo, os aplicativos de cliente QuickStart exigem uma tabela com duas colunas: texto (uma cadeia de caracteres) e completo (um valor booliano). Isso pode ser definido no arquivo JavaScript de definição de tabela (localizado no diretório de tabelas) da seguinte maneira:

```
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
```

Se você definir as tabelas estaticamente, também deverá chamar o método tables.initialize() para criar o esquema de banco de dados na inicialização. O método tables.initialize() retorna uma [promessa]; isso é usado para garantir que o serviço Web não atenda a solicitações antes do banco de dados que está sendo inicializado.

### <a name="howto-sqlexpress-setup"></a>Usar o SQL Express como um armazenamento de dados de desenvolvimento em sua máquina local

O SDK do nó de Aplicativos Móveis do Azure fornece três opções para dados do servidor pronto para uso:

- Usar o driver de **memória** para fornecer um repositório de exemplo não persistente
- Usar o driver de **sql** para fornecer um repositório de dados do SQL Express para desenvolvimento
- Usar o driver de **sql** para fornecer um repositório de dados do SQL Azure para produção

O SDK do nó Aplicativos Móveis do Azure usa o [pacote de nó mssql] para estabelecer e usar uma conexão com o SQL Express e com o SQL Azure. Este pacote requer que você habilite conexões TCP na instância do SQL Express.

   >[AZURE.NOTE]O driver de memória não fornece um conjunto completo de recursos para teste. Se você quiser testar localmente seu back-end, recomendamos o uso de um armazenamento de dados SQL Express e usando o driver do sql.

1. Baixe e instale o [Microsoft SQL Server 2014 Express]. Instale o SQL Server 2014 Express com a edição Ferramentas. A menos que você precise explicitamente do suporte de 64 bits, a versão de 32 bits consumirá menos memória durante a execução.

2. Execute o Gerenciador de Configurações do SQL Server 2014.

  a. Expanda o nó **SQL Server Network Configuration** no menu de árvore à esquerda. b. Clique em **Protocolos para SQLEXPRESS**. c. Clique em **TCP/IP** e selecione **Habilitar**. Clique em **OK** na caixa de diálogo pop-up. d. Clique com o botão direito do mouse em **TCP/IP** e selecione **Propriedades**. Clique na guia **Endereços IP**. f. Encontre o nó **IPAll**. No campo **Porta TCP**, insira **1433**.

  ![Configurar o SQL Express para TCP/IP][3]

  g. Clique em **OK**. Clique em **OK** no diálogo pop-up. h. Clique em **Serviços SQL Server** no menu de árvore no lado esquerdo. i. Clique em **SQL Server (SQLEXPRESS)** e selecione **Reiniciar** j. Feche o Gerenciador de Configurações do SQL Server 2014.

3. Crie Executar SQL Server 2014 Management Studio e conecte-se à instância do SQL Express local

  a. Clique com o botão direito do mouse em sua instância no Explorador de Objetos e selecione **Propriedades** b. Selecione a página **Segurança**. c. Verifique se os **modos SQL Server e autenticação do Windows** estão d selecionados. Clique em **OK**

  ![Configurar a autenticação do SQL Express][4]

  e. Expanda **Segurança** > **Logons** no Explorador de Objetos f. Clique em **Logons** e selecione **Novo Logon...** g. Insira um nome de logon. Selecione **Autenticação do SQL Server**. Digite uma senha e digite a mesma senha em **Confirmar senha**. Observe que a senha deve satisfazer a requisitos de complexidade do Windows. h. Clique em **OK**

  ![Adicionar um novo usuário ao SQL Express][5]

  i. Clique com o botão direito do mouse em seu novo logon e selecione **Propriedades** j. Selecione a página **funções de servidor** k. Marque a caixa de seleção ao lado da função de servidor **dbcreator** l. Clique em **OK** m. Feche o SQL Server 2015 Management Studio.

Não deixe de registrar o nome de usuário e senha que você selecionou. Talvez seja necessário atribuir funções de servidor ou permissões adicionais dependendo dos requisitos do banco de dados específico.

O aplicativo Node lerá a variável de ambiente **SQLCONNSTR\_MS\_TableConnectionString** para ler a cadeia de conexão desse banco de dados. Você pode definir isso em seu ambiente. Por exemplo, você pode usar o PowerShell para definir essa variável de ambiente:

```
$env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"
```

Observe que você deve acessar o banco de dados usando conexão TCP/IP e fornecer um nome de usuário e uma senha para a conexão.

### <a name="howto-config-localdev"></a>Configurar seu projeto para desenvolvimento local

Os Aplicativos Móveis do Azure leem um arquivo JavaScript chamado _azureMobile.js_ do sistema de arquivos local. Você não deve usar esse arquivo para configurar o SDK de Aplicativos móveis do Azure na produção; use as configurações de aplicativo no [Portal do Azure]. O arquivo _azureMobile.js_ deve exportar um objeto de configuração. As configurações mais comuns são:

- Configurações do banco de dados
- Configurações de registro em log de diagnóstico
- Configurações de CORS alternativas

Um exemplo de arquivo _azureMobile.js_ implementando as configurações de banco de dados fornecidas acima é dado abaixo:

```
module.exports = {
	cors: {
		origins: [ 'localhost' ]
	},
	data: {
		provider: 'sql',
		server: '127.0.0.1',
		database: 'mytestdatabase',
		user: 'azuremobile',
		password: 'T3stPa55word'
	},
	logging: {
		level: 'verbose'
	}
};
```

Recomendamos que você adicione _azureMobile.js_ ao arquivo _.gitignore_ (ou outro arquivo de controle do código fonte a ser ignorado) para impedir que as senhas sejam armazenadas na nuvem. Sempre configure as configurações de produção nas Configurações do Aplicativo no [Portal do Azure].

### <a name="howto-use-sqlazure"></a>Usar o SQL Azure como seu armazenamento de dados de produção

<!-- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

O uso do SQL Azure como armazenamento de dados é idêntico em todos os tipos de aplicativo do Serviço de Aplicativo do Azure. Se você não tiver feito isso, siga estas etapas para criar um novo back-end do Aplicativo Móvel.

1. Faça logon no [Portal do Azure].

2. Na parte superior esquerda da janela, clique no botão **+NOVO** > **Web + Celular** > **Aplicativo Móvel**, em seguida, forneça um nome ao back-end do aplicativo móvel.

3. Na caixa **Grupo de Recursos**, digite o mesmo nome do aplicativo.

4. O plano Serviço de Aplicativo Padrão será selecionado. Para alterar seu plano de Serviço do Aplicativo, clique em Plano de Serviço de Aplicativo > **+ Criar Novo**. Forneça um nome ao novo Plano de Serviço de Aplicativo e selecione um local apropriado. Clique em Camada de Preços e selecione uma camada de preços apropriada para o serviço. Selecione **Exibir tudo** para exibir mais opções de preço, como **Gratuito** e **Compartilhado**. Depois de selecionar a camada de preços, clique no botão **Selecionar**. De volta à folha **Plano do Serviço de Aplicativo**, clique em **OK**.

5. Clique em **Criar**. Isso cria um back-end de aplicativo móvel onde você, mais tarde, implantará o projeto do servidor. O provisionamento de um back-end de aplicativo móvel pode levar alguns minutos. Depois que o back-end do aplicativo móvel é provisionado, o portal abre a folha **Configurações** para o back-end do aplicativo móvel.

Depois que o back-end do Aplicativo Móvel for criado, você poderá conectar a um banco de dados existente do SQL Azure ao back-end do Aplicativo Móvel ou criar um novo banco de dados do SQL Azure. Neste TUTORIAL, criaremos um novo banco de dados SQL.

    > [AZURE.NOTE] If you already have a database in the same location as the new mobile app backend, you can instead choose **Use an existing database** and then select that database. The use of a database in a different location is not recommended because of additional bandwidth costs and higher latencies.

6. No novo back-end do aplicativo móvel, clique em **Configurações** > **Aplicativo Móvel** > **Dados** > **+ Adicionar**.

7. Na folha **Adicionar conexão de dados**, clique em **Banco de Dados SQL - Definir configurações necessárias** > **Criar um novo banco de dados**. Digite o nome do novo banco de dados no campo **Nome**.

8. Clique em **Servidor**. Na folha **Novo servidor**, insira um nome de servidor exclusivo no campo **Nome do servidor** e forneça um **Logon de administração de servidor** e **Senha**adequados. Verifique se **Permitir que os serviços do Azure acessem o servidor** está marcado. Clique em **OK**.

	![Criar um banco de dados SQL Azure][6]

9. Na folha **Novo banco de dados**, clique em **OK**.

10. De volta à folha **Adicionar conexão de dados**, selecione **Cadeia de conexão**, insira o logon e a senha que você forneceu ao criar o banco de dados. Se você usar um banco de dados existente, forneça as credenciais de logon desse banco de dados. Depois de inserir, clique em **OK**.

11. De volta novamente à folha **Adicionar conexão de dados**, clique em **OK** para criar o banco de dados.

<!-- END OF ALTERNATE INCLUDE -->

A criação do banco de dados pode levar alguns minutos. Use a área **Notificações** para monitorar o progresso da implantação. Não siga adiante enquanto o banco de dados não tiver sido implantado com êxito. Uma vez implantado com êxito, uma cadeia de conexão será criada para a instância de banco de dados do SQL Azure em suas configurações do aplicativo de back-end móvel. Você pode ver essa configuração de aplicativo em **Configurações** > **Configurações do aplicativo** > **Cadeias de conexão**.

### <a name="howto-tables-auth"></a>Exigir autenticação para acesso às tabelas

Se você quiser usar a autenticação do Serviço de Aplicativo com o ponto de extremidade de tabelas, deverá configurar autenticação de Serviço de Aplicativo no [Portal do Azure] primeiro. Para obter mais detalhes sobre como configurar a autenticação em um Serviço de Aplicativo do Azure, examine o Guia de Configuração para o provedor de identidade que você pretende usar:

- [Como configurar a autenticação do Active Directory do Azure]
- [Como configurar a autenticação do Facebook]
- [Como configurar a autenticação do Google]
- [Como configurar a autenticação da Microsoft]
- [Como configurar a autenticação do Twitter]

Cada tabela tem uma propriedade de acesso que pode ser usada para controlar o acesso à tabela. O exemplo a seguir mostra uma tabela estaticamente definida com autenticação necessária.

```
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
```

A propriedade de acesso pode ter dois valores

  - *autenticado* indica que o aplicativo cliente deve enviar um token de autenticação válido com a solicitação
  - *desabilitado* indica que essa tabela está desabilitada no momento

Se a propriedade de acesso estiver indefinida, o acesso não autenticado será permitido.

### <a name="howto-tables-disabled"></a>Desabilitar o acesso a operações de tabela específicas

Além de aparecer na tabela, a propriedade de acesso pode ser usada para controlar operações individuais. Há quatro operações:

  - *ler* é a operação RESTful GET na tabela
  - *inserir* é a operação RESTful POST na tabela
  - *atualizar* é a operação RESTful PATCH na tabela
  - *Excluir* é a operação RESTful DELETE na tabela

Por exemplo, você pode querer fornecer uma tabela não autenticada somente leitura. Isso pode ser fornecido pela definição de tabela abaixo:

```
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Read-Only table - only allow READ operations
table.read.access = undefined;
table.insert.access = 'disabled';
table.update.access = 'disabled';
table.delete.access = 'disabled';

module.exports = table;
```

### <a name="howto-tables-query"></a>Ajustar a consulta que é usada em operações de tabela

Um requisito comum para operações de tabela é fornecer uma exibição restrita dos dados. Por exemplo, você pode fornecer uma tabela que é marcada com a ID de usuário autenticado, de modo que o usuário só possa ler ou atualizar seus próprios registros. A definição da tabela abaixo fornece essa funcionalidade:

```
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
```

As operações que normalmente executam uma consulta têm uma propriedade de consulta que você pode ajustar com um cláusula where. A propriedade de consulta é um objeto [QueryJS] usado para converter uma consulta OData em algo que pode processar back-end de dados. Para casos de igualdade simples (como o mostrado acima), um mapa pode ser usado. Também é relativamente fácil adicionar cláusulas SQL específicas:

```
context.query.where('myfield eq ?', 'value');
```

### <a name="howto-tables-softdelete"></a>Configurar a Exclusão Reversível em uma tabela

A Exclusão Reversível não exclui registros. Em vez disso, ela os marca como excluídos no banco de dados definindo a coluna excluída como true. O SDK de Aplicativos Móveis do Azure remove automaticamente registros com exclusão reversível dos resultados, a menos que o SDK de cliente móvel use IncludeDeleted(). Para configurar uma tabela para exclusão reversível, defina a propriedade softDelete no arquivo de definição de tabela. Um exemplo poderia ser:

```
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
```

Você precisa estabelecer um mecanismo para limpar registros, seja usando um aplicativo cliente, um trabalho Web ou um mecanismo personalizado.

### <a name="howto-tables-seeding"></a>Propagar o banco de dados com dados

Ao criar um novo aplicativo, você pode querer propagar uma tabela com dados. Isso pode ser feito no arquivo JavaScript de definição de tabela da seguinte maneira:

```
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
```

É importante observar que a propagação de dados é feita somente quando a tabela é criada pelo SDK de Aplicativos Móveis do Azure. Se a tabela já existir no banco de dados, nenhum dado será injetado nela. Se o esquema dinâmico estiver habilitado, o esquema será inferido dos dados propagados.

É recomendável que você chame explicitamente o método Initialize () para criar a tabela quando o serviço começar a ser executado.

## <a name="CustomAPI"></a>API personalizada

Além da API de acesso a dados por meio do ponto de extremidade/tabelas, os Aplicativos Móveis do Azure podem fornecer cobertura de API personalizada. As APIs personalizadas são definidas de forma semelhante às definições de tabela e pode acessar todos os mesmos recursos, incluindo autenticação.

Se você quiser usar a autenticação do Serviço de Aplicativo com uma API personalizada, deve configurar autenticação de Serviço de Aplicativo no [Portal do Azure] primeiro. Para obter mais detalhes sobre como configurar a autenticação em um Serviço de Aplicativo do Azure, examine o Guia de Configuração para o provedor de identidade que você pretende usar:

- [Como configurar a autenticação do Active Directory do Azure]
- [Como configurar a autenticação do Facebook]
- [Como configurar a autenticação do Google]
- [Como configurar a autenticação da Microsoft]
- [Como configurar a autenticação do Twitter]

### <a name="howto-customapi-basic"></a>Definir uma API personalizada simples

As APIs personalizadas são definidas da mesma forma que a API de tabelas.

1. Criar um diretório **api**
2. Criar um arquivo JavaScript de definição de API no diretório **api**.
3. Use o método de importação para importar o diretório **api**.

Aqui está a definição de api do protótipo com base na amostra de aplicativo básico que usamos anteriormente.

```
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
```

Vamos pegar uma API simples que retorna a data do servidor usando o método _Date.now()_. Eis o arquivo api/date.js:

```
var api = {
	get: function (req, res, next) {
		var date = { currentTime: Date.now() };
		res.status(200).type('application/json').send(date);
	});
};

module.exports = api;
```

Cada parâmetro é um dos verbos padrão RESTful - GET, POST, PATCH ou DELETE. O método é uma função padrão [ExpressJS Middleware] que envia a saída necessária.

### <a name="howto-customapi-auth"></a>Solicitar autenticação para acesso a uma API personalizada

O SDK de aplicativos móveis do Azure implementa a autenticação da mesma forma para o ponto de extremidade de tabelas e para APIs personalizadas. Para adicionar autenticação à API desenvolvida na seção anterior, adicione uma propriedade **access**:

```
var api = {
	get: function (req, res, next) {
		var date = { currentTime: Date.now() };
		res.status(200).type('application/json').send(date);
	});
};
// All methods must be authenticated.
api.access = 'authenticated';

module.exports = api;
```

Você também pode especificar a autenticação em operações específicas:

```
var api = {
	get: function (req, res, next) {
		var date = { currentTime: Date.now() };
		res.status(200).type('application/json').send(date);
	});
};
// The GET methods must be authenticated.
api.get.access = 'authenticated';

module.exports = api;
```

O mesmo token que é usado para o ponto de extremidade de tabelas deve ser usado para APIs personalizadas que requerem autenticação.

## <a name="Debugging"></a>Depuração e solução de problemas

O Serviço de Aplicativo do Azure fornece várias técnicas de depuração e de solução de problemas para aplicativos Node. Todas essas técnicas estão disponíveis.

- [Monitorando um Serviço de Aplicativo do Azure]
- [Habilitar o registro em log de diagnósticos no Serviço de Aplicativo do Azure]
- [Solucionar problemas de um Serviço de Aplicativo do Azure no Visual Studio]

### <a name="howto-diagnostic-logs"></a>Gravar em logs de diagnóstico dos Aplicativos Móveis do Azure

Os aplicativos Node têm acesso a uma ampla gama de ferramentas de log de diagnóstico. Internamente, o SDK do nó Aplicativos Móveis do Azure usa [Winston] para registro em log de diagnóstico. Ele é ativado automaticamente habilitando o modo de depuração ou definindo a configuração de aplicativo **MS\_DebugMode** como true no [Portal do Azure]. Os logs gerados aparecerão nos logs de diagnóstico no [Portal do Azure].

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ../../includes/media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png

<!-- URLs -->
[Início rápido do cliente iOS]: app-service-mobile-ios-get-started.md
[Início rápido do cliente Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
[Início rápido do cliente Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
[Início rápido do cliente Xamarin.Forms]: app-service-mobile-xamarin-forms-get-started.md
[Início rápido de cliente Windows Phone]: app-service-mobile-windows-store-dotnet-get-started.md
[Guia de início rápido do cliente HTML/Javascript]: app-service-html-get-started.md
[à sincronização de dados offline]: app-service-mobile-offline-data-sync.md
[Como configurar a autenticação do Active Directory do Azure]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Como configurar a autenticação do Facebook]: app-service-mobile-how-to-configure-facebook-authentication.md
[Como configurar a autenticação do Google]: app-service-mobile-how-to-configure-google-authentication.md
[Como configurar a autenticação da Microsoft]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Como configurar a autenticação do Twitter]: app-service-mobile-how-to-configure-twitter-authentication.md
[Guia de implantação do Serviço de Aplicativo do Azure]: ../app-service-web/web-site-deploy.md
[Monitorando um Serviço de Aplicativo do Azure]: ../app-service-web/web-sites-monitor.md
[Habilitar o registro em log de diagnósticos no Serviço de Aplicativo do Azure]: ../app-service-web/web-sites-enable-diagnostic-log.md
[Solucionar problemas de um Serviço de Aplicativo do Azure no Visual Studio]: ../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md
[especificar a versão do nó]: ../nodejs-specify-node-version-azure-apps.md
[usar Módulos de nó]: ../nodejs-use-node-mobiles-azure-apps.md
[Create a new Azure App Service]: ../app-service-web/

[Portal do Azure]: https://portal.azure.com/
[OData]: http://www.odata.org
[promessa]: https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Global_Objects/Promise
[exemplo de aplicativo básico no GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[exemplo de tarefas pendentes no GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[diretório de exemplos no GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Ferramentas do Node.js 1.1 para Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[pacote de nó mssql]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/pt-BR/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston

<!---HONumber=Nov15_HO4-->