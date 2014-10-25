<properties pageTitle="Work with a JavaScript backend mobile service" metaKeywords="server scripts, mobile devices, Azure, scheduler" description="Provides examples on how to define, register, and use server scripts in Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Work with server scripts in Mobile Services" authors="ricksal" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# Trabalhar com um serviço móvel de back-end do JavaScript

<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-how-to-use/" title="Back-end do .NET">Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-how-to-use-server-scripts/"  title="Back-end do JavaScript" class="current">Back-end do JavaScript</a></div>

Este artigo fornece informações detalhadas e exemplos de como trabalhar com um back-end do JavaScript nos Serviços Móveis do Azure. Este tópico é dividido nas seguintes seções:

-   [Introdução][Introdução]
-   [Operações de tabela][Operações de tabela]
    -   [Como: Registrar-se para operações de tabela][Como: Registrar-se para operações de tabela]
    -   [Como: Substituir a resposta padrão][Como: Substituir a resposta padrão]
    -   [Como: Substituir executar com êxito][Como: Substituir executar com êxito]
    -   [Como: Substituir o gerenciamento de erros padrão][Como: Substituir o gerenciamento de erros padrão]
    -   [Como: Adicionar parâmetros personalizados][Como: Adicionar parâmetros personalizados]
    -   [Como: Trabalhar com usuários da tabela][Como: Trabalhar com usuários da tabela]
-   [API personalizada][API personalizada]
    -   [Como: Definir uma API personalizada][Como: Definir uma API personalizada]
    -   [Como: Implementar métodos HTTP][Como: Implementar métodos HTTP]
    -   [Como: Enviar e receber dados como XML][Como: Enviar e receber dados como XML]
    -   [Como: Trabalhar com usuários e cabeçalhos em uma API personalizada][Como: Trabalhar com usuários e cabeçalhos em uma API personalizada]
    -   [Como: Definir várias rotas em uma API personalizada][Como: Definir várias rotas em uma API personalizada]
-   [Agendador de Trabalhos][Agendador de Trabalhos]
    -   [Como: Definir os scripts de trabalho agendados][Agendador de Trabalhos]
-   [Controle do código-fonte, código compartilhado e funções do auxiliar][Controle do código-fonte, código compartilhado e funções do auxiliar]
    -   [Como: Carregar módulos Node.js][Como: Carregar módulos Node.js]
    -   [Como: Usar funções de auxiliar][Como: Usar funções de auxiliar]
    -   [Como: Compartilhar o código de compartilhamento usando o controle do código-fonte][Como: Compartilhar o código de compartilhamento usando o controle do código-fonte]
    -   [Como: Trabalhar com configurações de aplicativo][Como: Trabalhar com configurações de aplicativo]
-   [Usando a ferramenta de linha de comando][Usando a ferramenta de linha de comando]
-   [Trabalhando com tabelas][Trabalhando com tabelas]
    -   [Como: Acessar tabelas de scripts][Como: Acessar tabelas de scripts]
    -   [Como: Executar inserções em massa][Como: Executar inserções em massa]
    -   [Como: Mapear tipos JSON para tipos de banco de dados][Como: Mapear tipos JSON para tipos de banco de dados]
    -   [Usando o Transact-SQL para acessar tabelas][Usando o Transact-SQL para acessar tabelas]
-   [Depuração e solução de problemas][Depuração e solução de problemas]
    -   [Como: Gravar saída para os logs de serviços móveis][Como: Gravar saída para os logs de serviços móveis]

## <a name="intro"></a> Introdução

No serviço móvel de back-end do JavaScript, você pode definir lógica de negócios personalizada como o código JavaScript armazenado e executado no servidor. Esse código de script do servidor é atribuído a uma das seguintes funções de servidor:

-   [Operações de inserção, leitura, atualização ou exclusão em uma determinada tabela][Operações de tabela].
-   [Trabalhos agendados][Agendador de Trabalhos].
-   [Métodos HTTP definidos em uma API personalizada][API personalizada].

A assinatura da função principal no script de servidor depende do contexto em que o script é usado. Você também pode definir códigos de script comuns como módulos nodes.js compartilhados entre scripts. Para obter mais informações, consulte [Controle do código-fonte e código compartilhado][Controle do código-fonte, código compartilhado e funções do auxiliar]

Para obter descrições das funções e objetos de script de servidor individuais, consulte [Referência de script de servidor dos Serviços Móveis][Referência de script de servidor dos Serviços Móveis]

## <a name="table-scripts"></a>Operações de tabela

Um script de operação de tabela é um script de servidor registrado para uma operação em uma tabela - inserir, ler, atualizar ou excluir (*del*). O nome do script deve coincidir com o tipo de operação para o qual ele está registrado. Somente um script pode ser registrado para uma determinada operação de tabela. O script é executado sempre que a operação indicada é invocada por uma solicitação REST—por exemplo, quando uma solicitação POST é recebida para inserir um item na tabela. Os Serviços Móveis não preservam o estado entre as execuções de script. Como um novo contexto global é criado sempre que um script é executado, qualquer variável de estado definida no script será reinicializada. Se você deseja armazenar o estado de uma solicitação, crie uma tabela no seu serviço móvel e depois leia e grave o estado na tabela. Para obter mais informações, consulte [Como Acessar tabelas de scripts][Como: Acessar tabelas de scripts].

Você grava scripts de operação da tabela se for necessário impor uma lógica de negócios personalizada quando a operação é executada. Por exemplo, o seguinte script rejeita as operações de inserção em que o tamanho do campo `text` é maior do que dez caracteres:

    function insert(item, user, request) {
        if (item.text.length > 10) {
            request.respond(statusCodes.BAD_REQUEST, 
                'Text length must be less than 10 characters');
        } else {
            request.execute();
        }
    }

Uma função de script de tabela sempre usa três argumentos.

-   O primeiro argumento varia de acordo com a operação da tabela.

    -   Para inserções e atualizações, trata-se de um objeto **item**, que é uma representação JSON da linha afetada pela operação. Isso permite que você acesse os valores de coluna por nome, por exemplo, *item.Proprietário*, onde *Proprietário* é um dos nomes na representação JSON.
    -   Para uma exclusão, trata-se da ID do registro a ser excluído.
    -   E para uma leitura, trata-se de um [objeto de consulta][objeto de consulta] que especifica o conjunto de linhas a ser retornado.
-   O segundo argumento é sempre um [objeto de usuário][objeto de usuário] que representa o usuário que enviou a solicitação.

-   O terceiro argumento é sempre um [objeto de solicitação][objeto de solicitação], por meio do qual você pode controlar a execução da operação solicitada e a resposta enviada ao cliente.

Aqui estão as assinaturas das funções principais canônicas para as operações de tabela:

-   [Insert][Insert]: `function insert (item, user, request) { ... }`
-   [Update][Update]: `function update (item, user, request) { ... }`
-   [Delete][Delete]: `function del (id, user, request) { ... }`
-   [Read][Read]: `function read (query, user, request) { ... }`

> [WACOM.NOTE]Uma função registrada na operação de exclusão deve ser nomeada *del* porque a exclusão é uma palavra reservada em JavaScript.

Todos os scripts de servidor possuem uma função principal e podem ter funções de auxiliar opcionais. Mesmo que um script de servidor possa ter sido criado para uma tabela específica, ele também pode fazer referência a outras tabelas no mesmo banco de dados. Você também pode definir funções comuns como módulos que podem ser compartilhados entre scripts. Para obter mais informações, consulte [Controle do código-fonte e código compartilhado][Controle do código-fonte, código compartilhado e funções do auxiliar]

### <a name="register-table-scripts"></a>Como: Registrar scripts de tabela

Você pode definir scripts de servidor que são registrados para uma operação de tabela em uma das seguintes maneiras:

-   No [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure]. Scripts para operações de tabela são acessadas na guia **Scripts** de uma determinada tabela. Aqui é mostrado o código padrão registrado para o script de inserção da tabela `TodoItem`. Você pode substituir esse código pela sua própria lógica de negócios personalizada.

    ![1][1]

    Para saber como fazer isso, consulte [Validar e modificar dados em Serviços Móveis usando scripts de servidor][Validar e modificar dados em Serviços Móveis usando scripts de servidor].

-   Usando o controle do código-fonte. Quando o controle do código-fonte está habilitado, basta criar um arquivo chamado *`<table>`*.*`<operation>`*.js na subpasta .\\service\\table no repositório git, em que *`<table>`* é o nome da tabela e *`<operation>`* é a operação da tabela registrada. Para obter mais informações, consulte [Controle do código-fonte e código compartilhado][Controle do código-fonte, código compartilhado e funções do auxiliar]

-   No prompt de comando usando a ferramenta de linha de comando do Azure. Para obter mais informações, consulte [Usando a ferramenta de linha de comando][Usando a ferramenta de linha de comando].

Um script de operação de tabela deve chamar pelo menos uma das funções a seguir do [objeto de solicitação][objeto de solicitação] para garantir que o cliente receba uma resposta.

-   **função executar**: A operação é concluída conforme solicitado e a resposta padrão é retornada.

-   **função responder**: Uma resposta personalizada é retornada.

<div class="dev-callout"><strong>Importante</strong>
<p>Quando um script possui um caminho de c&oacute;digo nos quais nem <b>executar</b> nem <b>responder</b> s&atilde;o invocados, a opera&ccedil;&atilde;o poder&aacute; parar de responder.</p></div>

O seguinte script chama a função **executar** para concluir a operação de dados solicitada pelo cliente:

    function insert(item, user, request) { 
        request.execute(); 
    }

Neste exemplo, o item é inserido no banco de dados e o código de status correspondente é retornado para o usuário.

Quando a função **executar** é chamada, o valor `item`[, consulta][objeto de consulta], ou o `id` passado como primeiro argumento para a função de script é usado para realizar a operação. Para uma operação de inserção, atualização ou consulta, você pode modificar o item ou a consulta antes de chamar **executar**:

    function insert(item, user, request) { 
        item.scriptComment =
            'this was added by a script and will be saved to the database'; 
        request.execute(); 
    } 

    function update(item, user, request) { 
        item.scriptComment = 
            'this was added by a script and will be saved to the database'; 
        request.execute(); 
    } 

    function read(query, user, request) { 
        // Only return records for the current user         
        query.where({ userid: user.userId}); 
        request.execute(); 
    }

> [WACOM.NOTE]Em um script de exclusão, a alteração do valor da variável userId fornecida não afeta o registro a ser excluído.

Para obter mais exemplos, consulte [Leitura e gravação de dados][Leitura e gravação de dados], [Modificar a solicitação][Modificar a solicitação] e [Validar dados][Validar dados].

### <a name="override-response"></a>Como: Substituir a resposta padrão

Você também pode usar um script para implementar a lógica de validação que pode substituir o comportamento de resposta padrão. Se a validação falhar, bastará chamar a função **responder**, em vez de **executar**, e gravar a resposta para o cliente:

    function insert(item, user, request) {
        if (item.userId !== user.userId) {
            request.respond(statusCodes.FORBIDDEN, 
            'You may only insert records with your userId.');
        } else {
            request.execute();
        }
    }

Neste exemplo, a solicitação é rejeitada quando o item inserido não tem uma propriedade `userId` correspondente ao `userId` do [objeto de usuário][objeto de usuário] fornecido para o cliente autenticado. Neste caso, uma operação de banco de dados (*inserir*) não é realizada, e uma resposta que tenha um código de status HTTP 403 e uma mensagem de erro personalizada é retornada ao cliente. Para obter mais exemplos, consulte [Modificar a resposta][Modificar a resposta].

### <a name="override-success"></a>Como: Substituir executar com êxito

Por padrão, em uma operação de tabela, a função **executar** grava as respostas automaticamente. No entanto, você pode passar dois parâmetros opcionais para a função de execução que substituem seu comportamento em caso de sucesso e/ou erro.

Passando um manipulador de **sucesso** ao chamar a execução, você pode modificar os resultados de uma consulta antes de gravá-los na resposta. O seguinte exemplo chama `execute({ success: function(results) { ... })` para realizar um trabalho adicional depois que os dados são lidos com base no banco de dados, mas antes da resposta ser gravada:

    function read(query, user, request) {
        request.execute({
            success: function(results) {
                results.forEach(function(r) {
                    r.scriptComment = 
                    'this was added by a script after querying the database';
                });
                request.respond();
            }
        });
    }

Quando você fornece um manipulador de **sucesso** para a função **executar**, você também deve chamar a função **responder** como parte do manipulador de **sucesso**, de maneira que o tempo de execução saiba que o script foi concluído e que a resposta pode ser gravada. Quando você chama **responder** sem passar argumentos, os Serviços Móveis geram a resposta padrão.

> [WACOM.NOTE]Você pode chamar **responder** sem argumentos para chamar a resposta padrão depois de você chamar primeiro a função **executar**.

### <a name="override-error"></a>Como: Substituir o gerenciamento de erros padrão

A função **executar** poderá falhar se houver uma perda de conectividade com o banco de dados, um objeto inválido ou uma consulta incorreta. Por padrão, quando um erro ocorre, scripts de servidor registram o erro e gravam um resultado do erro na resposta. Como os Serviços Móveis fornecem o gerenciamento de erros padrão, não é necessário gerenciar erros que podem ocorrer no serviço.

Você pode substituir o gerenciamento de erro padrão ao implementar um gerenciamento de erro explícito se você quiser que uma determinada ação de compensação ou quando você desejar usar o objeto global de console para gravar informações mais detalhadas no log. Para isso, atribua um manipulador de **erro** à função **executar**:

    function update(item, user, request) { 
      request.execute({ 
        error: function(err) { 
          // Do some custom logging, then call respond. 
          request.respond(); 
        } 
      }); 
    }

Quando você fornece um manipulador de erro, os Serviços Móveis retornam um resultado de erro para o cliente quando **responder** é chamado.

Você também pode fornecer tanto um manipulador de **sucesso** quanto de **erro**, se desejar.

### <a name="access-headers"></a>Como: Acessar parâmetros personalizados.

Quando você envia uma solicitação para seu serviço móvel, você pode incluir parâmetros personalizados no URI da solicitação para instruir seus scripts de operação de tabela como processar uma determinada solicitação. Em seguida, você pode modificar o script para inspecionar o parâmetro para determinar o caminho de processamento.

Por exemplo, o seguinte URI para uma solicitação POST informa o serviço para não permitir a inserção de uma novo *TodoItem* que tem o mesmo valor de texto:

        https://todolist.azure-mobile.net/tables/TodoItem?duplicateText=false

Esses parâmetros de consulta personalizados são acessados como valores JSON da propriedade de **parâmetros** do [objeto de solicitação][objeto de solicitação]. O objeto de **solicitação** é fornecido pelos Serviços Móveis para qualquer função registrada para uma operação de tabela. O seguinte script de servidor para a operação de inserção verifica o valor do parâmetro `duplicateText`antes da operação de inserção ser executada:

        function insert(item, user, request) {
            var todoItemTable = tables.getTable('TodoItem');
            // Check the supplied custom parameter to see if
            // we should allow duplicate text items to be inserted.        
            if (request.parameters.duplicateText === 'false') {
                // Find all existing items with the same text
                // and that are not marked 'complete'. 
                todoItemTable.where({
                    text: item.text,
                    complete: false
                }).read({
                    success: insertItemIfNotComplete
                });
            } else {
                request.execute();
            }

            function insertItemIfNotComplete(existingItems) {
                if (existingItems.length > 0) {
                    request.respond(statusCodes.CONFLICT, 
                        "Duplicate items are not allowed.");
                } else {
                    // Insert the item as normal. 
                    request.execute();
                }
            }
        }

Observe que, em **insertItemIfNotComplete**, a função **executar** do [objeto de solicitação][objeto de solicitação] é invocada para inserir o item quando não há texto duplicado. Do contrário, a função **responder** é invocada para notificar o cliente da duplicata.

Observe a sintaxe da chamada para a função **sucesso** no código acima:

                }).read({
                    success: insertItemIfNotComplete
                });

Em JavaScript é uma versão compacta do equivalente mais demorado:

        success: function(results) 
        { 
            insertItemIfNotComplete(results); 
        }

### <a name="work-with-users"></a>Como: Trabalhar com usuários

Nos Serviços Móveis do Azure, você pode usar um provedor de identidade para autenticar usuários. Para obter mais informações, consulte [Comece a usar a autenticação][Comece a usar a autenticação]. Quando um usuário autenticado chama uma operação de tabela, os Serviços Móveis usam o [objeto de usuário][objeto de usuário] para fornecer informações sobre o usuário para a função de script registrado. A propriedade **userId** pode ser usada para armazenar e recuperar informações específicas do usuário. O exemplo a seguir define a propriedade do proprietário de um item com base no userId de um usuário autenticado:

    function insert(item, user, request) {
        item.owner = user.userId;
        request.execute();
    }

O exemplo a seguir adiciona um filtro extra à consulta com base no **userId** de um usuário autenticado. Este filtro restringe o resultado somente aos itens que pertencem ao usuário atual:

    function read(query, user, request) {
        query.where({
            owner: user.userId
        });
        request.execute();
    }

## <a name="custom-api"></a>API personalizada

Uma API personalizada é um ponto de extremidade no serviço móvel que é acessado por um ou mais dos métodos HTTP padrão: GET, POST, PUT, PATCH, DELETE. A exportação de uma função separada pode ser definida para cada método HTTP suportado pela API personalizada, todas em um único arquivo de script. O script registrado é chamado quando é recebida uma solicitação para a API personalizada usando o método em questão. Para obter mais informações, consulte [API personalizada][2].

Quando funções de API personalizadas são chamadas pelo tempo de execução dos Serviços Móveis, tanto um objeto de [solicitação][objeto de solicitação] quanto de [resposta][resposta] são fornecidos. Esses objetos expõem a funcionalidade da [biblioteca de express.js][biblioteca de express.js], que pode ser aproveitada pelos seus scripts. A seguinte API personalizada chamada **hello** é um exemplo muito simples que retorna *Hello, world!* em resposta a uma solicitação POST:

        exports.post = function(request, response) {
            response.send(200, "{ message: 'Hello, world!' }");
        } 

A função **enviar** no [objeto resposta][resposta] retorna a resposta desejada para o cliente. Esse código é chamado através do envio de uma solicitação POST para a seguinte URL:

        https://todolist.azure-mobile.net/api/hello  

O estado global é mantido entre as execuções.

### <a name="define-custom-api"></a>Como: Definir uma API personalizada

Você pode definir scripts de servidor que são registrados para métodos HTTP em um ponto de extremidade de API personalizada em uma das seguintes maneiras:

-   No [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure]. Scripts de API personalizadas são criados e modificados na guia **API**. O código de script de servidor está na guia **Scripts** de uma determinada API personalizada. Aqui está o script invocado por uma solicitação POST para o ponto de extremidade da API personalizada `CompleteAll`.

    ![2][3]

    Permissões de acesso para métodos de API personalizadas são atribuídas na guia Permissões. Para ver como essa API personalizada foi criado, consulte [Chamar uma API personalizada do cliente][Chamar uma API personalizada do cliente].

-   Usando o controle do código-fonte. Quando o controle do código-fonte está habilitado, basta criar um arquivo chamado *`<custom_api>`*.js na subpasta .\\service\\api no repositório git, em que *`<custom_api>`* é o nome da API personalizada registrada. Esse arquivo de script contém uma função *exported* para cada método HTTP exposto pela API personalizada. Permissões são definidas em um arquivo de .json complementar. Para obter mais informações, consulte [Controle do código-fonte e código compartilhado][Controle do código-fonte, código compartilhado e funções do auxiliar]

-   No prompt de comando usando a ferramenta de linha de comando do Azure. Para obter mais informações, consulte [Usando a ferramenta de linha de comando][Usando a ferramenta de linha de comando].

### <a name="handle-methods"></a>Como: Implementar métodos HTTP

Uma API personalizada pode manipular um ou mais métodos HTTP, GET, POST, PUT, PATCH e DELETE. Uma função exportada é definida para cada método HTTP gerenciado pela API personalizada. Um único arquivo de código de API personalizada pode exportar uma ou todas as seguintes funções:

        exports.get = function(request, response) { ... };
        exports.post = function(request, response) { ... };
        exports.patch = function(request, response) { ... };
        exports.put = function(request, response) { ... };
        exports.delete = function(request, response) { ... };

O ponto de extremidade de API personalizada não pode ser chamado usando um método HTTP que não foi implementado no script de servidor e uma resposta de erro 405 (método não permitido) é retornada. Níveis de permissão separados podem ser atribuídos a cada método HTTP de suporte.

### <a name="api-return-xml"></a>Como: Enviar e receber dados como XML

Quando os clientes armazenam e recuperam dados, os Serviços Móveis usam JavaScript Object Notation (JSON) para representar os dados no corpo da mensagem. No entanto, há situações em que você deseja usar uma carga XML. Por exemplo, aplicativos da Windows Store têm uma funcionalidade interna de notificações periódicas que requer do serviço a emissão de XML. Para obter mais informações, consulte [Definir uma API personalizada que dá suporte a notificações periódicas][Definir uma API personalizada que dá suporte a notificações periódicas].

A seguinte função de API personalizada **OrderPizza** retorna um documento XML simples como a carga de resposta:

        exports.get = function(request, response) {
          response.set('content-type', 'application/xml');
          var xml = '<?xml version="1.0"?><PizzaOrderForm><PizzaOrderForm/>';
          response.send(200, xml);
        };

Esta função de API personalizada é chamada por uma solicitação HTTP GET para o ponto de extremidade a seguir:

        https://todolist.azure-mobile.net/api/orderpizza

### <a name="get-api-user"></a>Como: Trabalhar com usuários e cabeçalhos em uma API personalizada

Nos Serviços Móveis do Azure, você pode usar um provedor de identidade para autenticar usuários. Para obter mais informações, consulte [Comece a usar a autenticação][Comece a usar a autenticação]. Quando um usuário autenticado solicita uma API personalizada, os Serviços Móveis usam o [objeto de usuário][objeto de usuário] para fornecer informações sobre o usuário para o código de API personalizada. O [objeto de usuário][objeto de usuário] é acessado a partir da propriedade do usuário do [objeto de solicitação][objeto de solicitação]. A propriedade **userId** pode ser usada para armazenar e recuperar informações específicas do usuário.

A seguinte função de API personalizada **OrderPizza** define a propriedade do proprietário de um item com base no userId de um usuário autenticado:

        exports.post = function(request, response) {
            var userTable = request.service.tables.getTable('user');
            userTable.lookup(request.user.userId, {
                success: function(userRecord) {
                    callPizzaAPI(userRecord, request.body, function(orderResult) {
                        response.send(201, orderResult);
                    });
                }
            });
        
        };

Esta função de API personalizada é chamada por uma solicitação HTTP POST para o ponto de extremidade a seguir:

        https://<service>.azure-mobile.net/api/orderpizza

Você também pode acessar um cabeçalho HTTP específico do [objeto de solicitação][objeto de solicitação], conforme mostrado no código a seguir:

        exports.get = function(request, response) {    
            var header = request.header('my-custom-header');
            response.send(200, "You sent: " + header);
        };

Este exemplo simples mostra um cabeçalho personalizado chamado `my-custom-header` e, em seguida, retorna o valor na resposta.

### <a name="api-routes"></a>Como: Definir várias rotas em uma API personalizada

Os Serviços Móveis permitem que você defina vários caminhos ou rotas em uma API personalizada. Por exemplo, solicitações de HTTP GET para as seguintes URLs em uma API personalizada **calculadora** chamará uma função **adicionar** ou **subtrair**, respectivamente:

-   `https://<service>.azure-mobile.net/api/calculator/add`
-   `https://<service>.azure-mobile.net/api/calculator/sub`

Várias rotas são definidas através da exportação de uma função de **registro**, que passou um objeto **api** objeto (semelhante ao [objeto expresso em express.js][objeto expresso em express.js]), usado para registrar rotas sob o ponto de extremidade da API personalizada. O exemplo a seguir implementa os métodos **adicionar** e **subtrair** na API personalizada **calculadora**:

        exports.register = function (api) {
            api.get('add', add);
            api.get('sub', subtract);
        }
        
        function add(req, res) {
            var result = parseInt(req.query.a) + parseInt(req.query.b);
            res.send(200, { result: result });
        }
        
        function subtract(req, res) {
            var result = parseInt(req.query.a) - parseInt(req.query.b);
            res.send(200, { result: result });
        }

O objeto **api** objeto passado para a função **registrar** expõe uma função para cada método HTTP (**get**, **post**, **put**, **patch**, **delete**). Essas funções registram uma rota para uma função definida de um método específico de HTTP. Cada função aceita dois parâmetros, o primeiro é o nome de rota e o segundo é a função registrada para a rota.

As duas rotas no exemplo acima de API personalizada podem ser chamadas por solicitações HTTP GET da seguinte maneira (mostrada com a resposta):

-   `https://<service>.azure-mobile.net/api/calculator/add?a=1&b=2`

        {"result":3}

-   `https://<service>.azure-mobile.net/api/calculator/sub?a=3&b=5`

        {"result":-2}

## <a name="scheduler-scripts"></a>Agendador de Trabalhos

Os Serviços Móveis permitem que você defina os scripts de servidor que são executados como trabalhos em uma programação fixa ou sob demanda a partir do Portal de Gerenciamento. Trabalhos agendados são úteis para a execução de tarefas periódicas como limpeza de dados de tabela e processamento em lote. Para obter mais informações, consulte [Agendar trabalhos][Agendar trabalhos].

Scripts registrados para trabalhos agendados têm uma função principal com o mesmo nome que trabalho. Como um script agendado não é chamado por uma solicitação HTTP, não há nenhum contexto que possa ser transmitido pelo tempo de execução do servidor e a função não usa nenhum parâmetro. Como outros tipos de scripts, você pode ter funções de sub-rotina e requerem que os módulos sejam compartilhados. Para obter mais informações, consulte [Controle do código-fonte, código compartilhado e funções do auxiliar][Controle do código-fonte, código compartilhado e funções do auxiliar].

### <a name="scheduler-scripts"></a>Como: Definir os scripts de trabalho agendados

Um script de servidor pode ser atribuído a um trabalho que está definido no Agendador dos Serviços Móveis. Esses scripts pertencem ao trabalho e são executados de acordo com o plano de trabalho. (Você também pode usar o [Portal de Gerenciamento][Portal de Gerenciamento do Azure] para executar trabalhos sob demanda.) Um script que define um trabalho agendado não tem parâmetros porque os Serviços Móveis não transmitem nenhum dado; ele é executado como uma função JavaScript normal e não interage diretamente com os Serviços Móveis.

Você pode definir os trabalhos agendados das seguintes maneiras:

-   No [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure] na guia **Script** do Agendador:

    ![3][4]

    Para obter mais informações sobre como fazer isso, consulte [Agendar trabalhos de back-end nos Serviços Móveis][Agendar trabalhos de back-end nos Serviços Móveis].

-   No prompt de comando usando a ferramenta de linha de comando do Azure. Para obter mais informações, consulte [Usando a ferramenta de linha de comando][Usando a ferramenta de linha de comando].

> [WACOM.NOTE]Se você tiver habilitado o controle do código-fonte, é possível editar os arquivos de script do trabalho agendado diretamente na subpasta .\\service\\scheduler em seu repositório git. Para obter mais informações, consulte [Como Compartilhar o código de compartilhamento usando o controle do código-fonte][Como: Compartilhar o código de compartilhamento usando o controle do código-fonte].

## <a name="shared-code"></a>Controle do código-fonte, código compartilhado e funções do auxiliar

Como os Serviços Móveis usam o Node.js no servidor, seus scripts já possuem acesso aos módulos internos do Node.js. Você também pode usar o controle do código-fonte para definir seus próprios módulos ou adicionar outros módulos Node.js a seu serviço.

A seguir estão apenas alguns dos módulos mais úteis que podem ser aproveitados em seus scripts usando a função global **require**:

-   **azure**: Expõe a funcionalidade do SDK do Azure para Node.js. Para obter mais informações, consulte o [SDK do Azure para Node.js][SDK do Azure para Node.js].
-   **crypto**: Oferece o recurso de criptografia do OpenSSL. Para obter mais informações, consulte a [Documentação do Node.js][Documentação do Node.js].
-   **path**: Contém utilitários para trabalhar com caminhos de arquivo. Para obter mais informações, consulte a [Documentação do Node.js][5].
-   **querystring**: Contém utilitários para trabalhar com cadeias de consulta. Para obter mais informações, consulte a [Documentação do Node.js][6].
-   **request**: Envia solicitações HTTP aos serviços REST externos, como o Twitter e o Facebook. Para obter mais informações, consulte [Enviar solicitação HTTP][Enviar solicitação HTTP].
-   **sendgrid**: Envia um email usando o serviço de email Sendgrid do Azure. Para obter mais informações, consulte [Enviar e-mail dos Serviços Móveis com SendGrid][Enviar e-mail dos Serviços Móveis com SendGrid].
-   **url**: Contém utilitários para analisar e resolver URLs. Para obter mais informações, consulte a [Documentação do Node.js][7].
-   **util**: Contém vários utilitários, como formatação de cadeia de caracteres e verificação de tipo de objeto. Para obter mais informações, consulte a [Documentação do Node.js][8].
-   **zlib**: Expõe funcionalidade de compactação como gzip e deflate. Para obter mais informações, consulte a [Documentação do Node.js][9].

### <a name="modules-helper-functions"></a>Como: Utilizar módulos

Os Serviços Móveis apresentam um conjunto de módulos que os scripts podem carregar usando a função global **require**. Por exemplo, um script pode exigir **request** para fazer solicitações HTTP:

    function update(item, user, request) { 
        var httpRequest = require('request'); 
        httpRequest('http://www.google.com', function(err, response, body) { 
            ... 
        }); 
    } 

### <a name="shared-code-source-control"></a>Como: Compartilhar o código de compartilhamento usando o controle do código-fonte

Você pode usar o controle do código-fonte com o Gerenciador de pacotes do Node.js (npm) para controlar quais módulos estão disponíveis para o serviço móvel. Há duas maneiras de fazer isso:

-   Para módulos que são publicados e instalados pelo npm, use o arquivo package.json para declarar quais pacotes você deseja instalar em seu serviço móvel. Dessa forma, o serviço sempre tem acesso à versão mais recente dos pacotes necessários. O arquivo package.json permanece no diretório `.\service`. Para obter mais informações, consulte [Suporte para package.json nos Serviços Móveis do Azure][Suporte para package.json nos Serviços Móveis do Azure].

-   Para módulos personalizados ou particulares, você pode usar npm para instalar manualmente o módulo no diretório `.\service\node_modules` do controle do código-fonte. Para obter um exemplo de como carregar um módulo manualmente, consulte [Utilizar código compartilhado e módulos do Node. js em seus scripts de servidor][Utilizar código compartilhado e módulos do Node. js em seus scripts de servidor].

    > [WACOM.NOTE]Quando `node_modules` já existir na hierarquia de diretório, NPM criará o subdiretório `\node-uuid` nela em vez de criar um novo `node_modules` no repositório. Nesse caso, simplesmente exclua o diretório `node_modules` existente.

Após você confirmar o arquivo package.json ou módulos personalizados para o repositório do serviço móvel, use **require** para fazer referência aos módulos pelo nome.

> [WACOM.NOTE] Módulos que você especifica no package.json ou carrega em seu serviço móvel são usados somente em seu código de script de servidor. Esses módulos não são usados pelo tempo de execução dos Serviços Móveis.

### <a name="helper-functions"></a>Como: Usar funções de auxiliar

Além de exigirem módulos, scripts de servidor individuais podem incluir funções de auxiliar. Essas funções estão separadas da função principal, que pode ser usada para decompor o código no script.

No seguinte exemplo, um script de tabela está registrado na operação de inserção, o que inclui a função auxiliar **handleUnapprovedItem**:

    function insert(item, user, request) {
        if (!item.approved) {
            handleUnapprovedItem(item, user, request);
        } else {
            request.execute();
        }
    }

    function handleUnapprovedItem(item, user, request) {
        // Do something with the supplied item, user, or request objects.
    }

Em um script, as funções de auxiliar devem ser declaradas após a função principal. Você deve declarar todas as variáveis no script. Variáveis não declaradas causam erro.

Funções de auxiliar também podem ser definidas uma vez e compartilhadas entre scripts de servidor. Para compartilhar uma função entre scripts, as funções devem ser exportadas e o arquivo de script deve estar no diretório `.\service\shared\`. Aqui está um modelo de como exportar uma função compartilhada em um arquivo `.\services\shared\helpers.js`:

        exports.handleUnapprovedItem = function (tables, user, callback) {
            
            // Do something with the supplied tables or user objects and 
            // return a value to the callback function.
        };

Depois você pode usar uma função como essa em um script de operação de tabela:

        function insert(item, user, request) {
            var helper = require('../shared/helper');
            helper.handleUnapprovedItem(tables, user, function(result) {
                    
                    // Do something based on the result.
                    request.execute();
                }
            }
        }

Neste exemplo, você deve transmitir um [objeto de tabela][objeto de tabela] e um [objeto de usuário][objeto de usuário] para a função compartilhada. Isso ocorre porque os scripts compartilhados não podem acessar o [objeto de tabelas][objeto de tabela] global e o [objeto de usuário][objeto de usuário] só somente no contexto de uma solicitação.

Arquivos de script são carregados em um diretório compartilhado utilizando o [controle do código-fonte][Como: Compartilhar o código de compartilhamento usando o controle do código-fonte] ou usando a [ferramenta de linha de comando][Usando a ferramenta de linha de comando].

### <a name="app-settings"></a>Como: Trabalhar com configurações de aplicativo

Os Serviços Móveis permitem que você armazene com segurança os valores como configurações de aplicativo, que podem ser acessadas por seus scripts de servidor em tempo de execução. Quando você adiciona dados às configurações de aplicativo do serviço móvel, os pares de nome/valor são armazenados criptografados e você pode acessá-los em seus scripts de servidor sem codificá-los manualmente no arquivo de script. Para obter mais informações, consulte [Configurações do aplicativo][Configurações do aplicativo].

O seguinte exemplo de API personalizada usa o [objeto de serviço][objeto de serviço] fornecido para recuperar um valor de configuração do aplicativo.

        exports.get = function(request, response) {
        
            // Get the MY_CUSTOM_SETTING value from app settings.
            var customSetting = 
                request.service.config.appSettings.my_custom_setting;
                
            // Do something and then send a response.

        }

O código a seguir usa o módulo de configuração para recuperar valores de token de acesso ao Twitter, armazenados nas configurações do aplicativo, que são usados em um script de trabalho agendado:

        // Get the service configuration module.
        var config = require('mobileservice-config');

        // Get the stored Twitter consumer key and secret. 
        var consumerKey = config.twitterConsumerKey,
            consumerSecret = config.twitterConsumerSecret
        // Get the Twitter access token from app settings.    
        var accessToken= config.appSettings.TWITTER_ACCESS_TOKEN,
            accessTokenSecret = config.appSettings.TWITTER_ACCESS_TOKEN_SECRET;

Observe que esse código também recupera os valores de chave de consumidor do Twitter armazenados na guia **Identidade** no portal. Como um **objeto de config.** não está disponível em operação de tabela e scripts de trabalho agendado, você deve exigir o módulo de configuração para acessar as configurações do aplicativo. Para um exemplo completo, consulte [Agendar trabalhos de back-end nos Serviços Móveis][Agendar trabalhos de back-end nos Serviços Móveis].

## <a name="command-prompt"></a>Usando a ferramenta de linha de comando

Nos Serviços Móveis, você pode criar, modificar e excluir scripts de servidor usando a ferramenta de linha de comando do Azure. Antes de carregar os seus scripts, certifique-se de que você está usando a estrutura de diretório a seguir:

![4][10]

Observe que essa estrutura de diretório é a mesma do repositório git ao usar o controle do código-fonte.

Carregando arquivos de script da ferramenta de linha de comando, você deve primeiro navegar até o diretório `.\services\`. O seguinte comando carrega um script chamado `todoitem.insert.js` do subdiretório `table`:

        ~$azure mobile script upload todolist table/todoitem.insert.js
        info:    Executing command mobile script upload
        info:    mobile script upload command OK

O comando a seguir retorna informações sobre todos os arquivos de script mantidos no serviço móvel:

        ~$ azure mobile script list todolist
        info:    Executing command mobile script list
        + Retrieving script information
        info:    Table scripts
        data:    Name                       Size
        data:    -------------------------  ----
        data:    table/channels.insert      1980
        data:    table/TodoItem.insert      5504
        data:    table/TodoItem.read        64
        info:    Shared scripts
        data:    Name              Size
        data:    ----------------  ----
        data:    shared/helper.js  62
        data:    shared/uuid.js    7452
        info:    Scheduled job scripts
        data:    Job name    Script name           Status    Interval     Last run  Next run
        data:    ----------  --------------------  --------  -----------  --------  --------
        data:    getUpdates  scheduler/getUpdates  disabled  15 [minute]  N/A       N/A
        info:    Custom API scripts
        data:    Name                    Get          Put          Post         Patch        Delete
        data:    ----------------------  -----------  -----------  -----------  -----------  -----------
        data:    completeall             application  application  application  application  application
        data:    register_notifications  application  application  user         application  application
        info:    mobile script list command OK

Para obter mais informações, consulte [Comandos para gerenciar os Serviços Móveis do Azure][Comandos para gerenciar os Serviços Móveis do Azure].

## <a name="working-with-tables"></a>Trabalhando com tabelas

Muitos cenários nos Serviços Móveis exigem scripts de servidor para acessar tabelas no banco de dados. Por exemplo. como os Serviços Móveis não preservam o estado entre as execuções de script, todos os dados que precisam ser persistentes entre as execuções de script devem ser armazenados em tabelas. Você também deve examinar entradas em uma tabela de permissões ou armazenar os dados de auditoria em vez de apenas gravar no log, onde os dados têm uma duração limitada e não podem ser acessados por meio de programação.

Os Serviços Móveis possuem duas maneiras de acessar tabelas: usando um proxy [objeto de tabela][11] ou pela composição de consultas Transact-SQL usando o [objeto mssql][objeto mssql]. O [objeto de tabela][11] facilita o acesso aos dados de tabela do seu código de script de servidor, mas o [objeto mssql][objeto mssql] oferece suporte a operações mais complexas de dados e fornece mais flexibilidade.

### <a name="access-tables"></a>Como: Acessar tabelas de scripts

A maneira mais fácil para acessar tabelas do script é usando o [objeto de tabelas][objeto de tabela]. A função **getTable** retorna uma instância de [objeto de tabela][11] que é um proxy para acessar a tabela solicitada. Em seguida, você pode chamar funções no proxy para acessar e alterar dados.

Scripts registrados para operações de tabela e trabalhos agendados podem acessar o [objeto de tabelas][objeto de tabela] como um objeto global. Esta linha de código é um proxy da tabela *TodoItems* a partir do [objeto de tabelas][objeto de tabela] global:

        var todoItemsTable = tables.getTable('TodoItems');

Scripts de API personalizada podem acessar o [objeto de tabelas][objeto de tabela] da propriedade **serviço** do [objeto de solicitação][objeto de solicitação] fornecido. Esta linha de código obtém o [objeto de tabelas][objeto de tabela] da solicitação:

        var todoItemsTable = request.service.tables.getTable('TodoItem');

<div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong>
<p>Fun&ccedil;&otilde;es compartilhadas n&atilde;o podem acessar o objeto de <strong>tabelas</strong> diretamente. Em uma fun&ccedil;&atilde;o compartilhada, voc&ecirc; deve transmitir o objeto de tabelas para a fun&ccedil;&atilde;o.</p></div>

Depois de ter um [objeto de tabela][11], é possível chamar uma ou mais funções de operação de tabelas: inserir, atualizar, excluir ou ler. Este exemplo lê as permissões de usuário de uma tabela de permissões:

    function insert(item, user, request) {
        var permissionsTable = tables.getTable('permissions');

        permissionsTable
            .where({ userId: user.userId, permission: 'submit order'})
            .read({ success: checkPermissions });
            
        function checkPermissions(results) {
            if(results.length > 0) {
                // Permission record was found. Continue normal execution.
                request.execute();
            } else {
                console.log('User %s attempted to submit an order without permissions.', user.userId);
                request.respond(statusCodes.FORBIDDEN, 'You do not have permission to submit orders.');
            }
        }
    }

O exemplo a seguir grava informações de auditoria em uma tabela **de auditoria**:

    function update(item, user, request) {
        request.execute({ success: insertAuditEntry });
        
        function insertAuditEntry() {
            var auditTable = tables.getTable('audit');
            var audit = {
                record: 'checkins',
                recordId: item.id,
                timestamp: new Date(),
                values: JSON.stringify(item)
            };
            auditTable.insert(audit, {
                success: function() {
                    // Write to the response now that all data operations are complete
                    request.respond();
                }
            });
        }
    }

Um exemplo final é o exemplo de código aqui: [Como: Acessar parâmetros personalizados][Como: Adicionar parâmetros personalizados].

### <a name="bulk-inserts"></a>Como: Executar inserções em massa

Se você usar um loop **para** ou **enquanto** para inserir diretamente um grande número de itens (1000, por exemplo) em uma tabela, você pode encontrar um limite de conexões do SQL que faz com que algumas das inserções falhem. Sua solicitação não pode ser concluída ou poderá retornar um Erro Interno do Servidor HTTP 500. Para evitar esse problema, você pode inserir os itens em lotes de 10 aproximadamente. Depois de inserir o primeiro lote, envie o segundo lote e assim por diante.

Usando o script a seguir, você pode definir o tamanho de um lote de registros para inserir em paralelo. Recomendamos que você mantenha o número de registros pequeno. A função **insertItems** chama ela mesma recursivamente quando um lote de inserção assíncrono é concluído. Aquele para o loop no final insere um registro por vez e chama **insertComplete** para êxito e **errorHandler** para erro. **insertComplete** controla se **insertItems** será chamado recursivamente para o próximo lote ou se o trabalho é feito e o script deve sair.

        var todoTable = tables.getTable('TodoItem');
        var recordsToInsert = 1000;
        var batchSize = 10; 
        var totalCount = 0;
        var errorCount = 0; 
        
        function insertItems() {        
            var batchCompletedCount = 0;  
        
            var insertComplete = function() { 
                batchCompletedCount++; 
                totalCount++; 
                if(batchCompletedCount === batchSize || totalCount === recordsToInsert) {                        
                    if(totalCount < recordsToInsert) {
                        // kick off the next batch 
                        insertItems(); 
                    } else { 
                        // or we are done, report the status of the job 
                        // to the log and don't do any more processing 
                        console.log("Insert complete. %d Records processed. There were %d errors.", totalCount, errorCount); 
                    } 
                } 
            }; 
        
            var errorHandler = function(err) { 
                errorCount++; 
                console.warn("Ignoring insert failure as part of batch.", err); 
                insertComplete(); 
            };
        
            for(var i = 0; i < batchSize; i++) { 
                var item = { text: "This is item number: " + totalCount + i }; 
                todoTable.insert(item, { 
                    success: insertComplete, 
                    error: errorHandler 
                }); 
            } 
        } 
        
        insertItems(); 

O exemplo de código completo e discussão correspondente, podem ser encontrados nesta [postagem no blog][postagem no blog]. Se você usar esse código, é possível adaptá-lo à sua situação específica e testá-lo integralmente.

### <a name="JSON-types"></a>Como: Mapear tipos JSON para tipos de banco de dados

As coleções de tipos de dados no cliente e em um banco de dados dos Serviços Móveis são diferentes. Às vezes, elas são mapeadas facilmente entre si e outras vezes, não. Os Serviços Móveis realizam uma série de transformações de tipo no mapeamento:

-   Os tipos específicos de idioma do cliente são serializados no JSON.
-   A representação JSON é traduzida em JavaScript antes de ser exibida em scripts de servidor.
-   Os tipos de dados JavaScript são convertidos em tipos de banco de dados SQL quando salvos usando-se o [objeto de tabelas][objeto de tabela].

A transformação do esquema de cliente em JSON varia de acordo com as plataformas. JSON.NET é usado em clientes Windows Store e Windows Phone. O cliente Android usa a biblioteca gson. O cliente iOS usa a classe NSJSONSerialization. O comportamento de serialização padrão de cada uma dessas bibliotecas é usado, exceto objetos de data convertidos em cadeias de caracteres JSON que contém a data codificada usando ISO 8601.

Quando você estiver gravando scripts de servidor que usam as funções [inserir][Insert], [atualizar][Update], [ler][Read] ou [excluir][Delete], é possível acessar a representação de JavaScript de seus dados. Os Serviços Móveis usam a função de desserialização do Node.js ([JSON.parse][JSON.parse]) para transformar o JSON na conexão em objetos JavaScript. No entanto, os Serviços Móveis fazem uma transformação para extrair objetos de **data** de cadeias de caracteres ISO 8601.

Quando você usa o [objeto de tabelas][objeto de tabela] ou o [objeto mssql][objeto mssql], ou quando deixa os scripts de tabela em execução, os objetos JavaScript desserializados são inseridos no banco de dados SQL. Nesse processo, as propriedades do objeto são mapeadas por tipos de T-SQL:

<table border="1">
<tr>
<td>Propriedade JavaScript</td>
<td>Tipo T-SQL</td>
</tr><tr>
<td>Número</td>
<td>Float(53)</td>
</tr><tr>
<td>Booliano</td>
<td>Bit</td>
</tr><tr>
<td>Data</td>
<td>DateTimeOffset(3)</td>
</tr>
<tr>
<td>Cadeia de caracteres</td>
<td>Nvarchar(max)</td>
</tr>
<tr>
<td>Buffer</td>
<td>Sem suporte</td>
</tr><tr>
<td>Objeto</td>
<td>Sem suporte</td>
</tr><tr>
<td>Matriz</td>
<td>Sem suporte</td>
</tr><tr>
<td>Fluxo</td>
<td>Sem suporte</td>
</tr>
</table> 

### <a name="TSQL"></a>Usando o Transact-SQL para acessar tabelas

A maneira mais fácil de trabalhar com dados de tabela de scripts de servidor é usando um proxy de [objeto de tabela][11]. No entanto, há cenários mais avançados que não são suportados pelo [objeto de tabela][11], tais como consultas de junção e outras consultas complexas e chamada de procedimentos armazenados. Nesses casos, você deve executar as instruções Transact-SQL diretamente mediante a tabela relacional usando o [objeto mssql][objeto mssql] Esse objeto fornece as seguintes funções:

-   **query**: executa uma consulta, especificada por uma cadeia de caracteres TSQL; os resultados são enviados para o retorno de chamada **sucesso** no objeto **opções**. A consulta pode incluir parâmetros se o parâmetro *params* estiver presente.
-   **queryRaw**: é como a *query*, exceto pelo conjunto de resultados retornado da consulta estar em um formato "bruto" (veja o exemplo abaixo).
-   **open**: usado para obter uma conexão com o banco de dados dos Serviços Móveis e, em seguida, você poderá usar o objeto de conexão para invocar operações de banco de dados, como transações.

Esses métodos oferecem cada vez mais controle de nível baixo sobre o processamento de consulta.

-   [Como: Executar uma consulta estática][Como: Executar uma consulta estática]
-   [Como: Executar uma consulta dinâmica][Como: Executar uma consulta dinâmica]
-   [Como: Associar tabelas relacionais][Como: Associar tabelas relacionais]
-   [Como: Executar uma consulta que retorna *resultados* brutos][Como: Executar uma consulta que retorna *resultados* brutos]
-   [Como: Obter acesso a uma conexão de banco de dados][Como: Obter acesso a uma conexão de banco de dados]

#### <a name="static-query"></a>Como: Executar uma consulta estática

A consulta a seguir não tem parâmetros e retorna três registros da tabela `statusupdate`. O conjunto de linhas está no formato JSON padrão.

        mssql.query('select top 3 * from statusupdates', {
            success: function(results) {
                console.log(results);
            },
            error: function(err) {
                console.log("error is: " + err);
            }
        });

#### <a name="dynamic-query"></a>Como: Executar uma consulta parametrizada dinâmica

O exemplo a seguir implementa autorização personalizada pela leitura de permissões para cada usuário da tabela de permissões. O espaço reservado (?) será substituído pelo parâmetro fornecido quando a consulta é executada.

            var sql = "SELECT _id FROM permissions WHERE userId = ? AND permission = 'submit order'";
            mssql.query(sql, [user.userId], {
                success: function(results) {
                    if (results.length > 0) {
                        // Permission record was found. Continue normal execution. 
                        request.execute();
                    } else {
                        console.log('User %s attempted to submit an order without permissions.', user.userId);
                        request.respond(statusCodes.FORBIDDEN, 'You do not have permission to submit orders.');
                    }
                },
                error: function(err) {
                    console.log("error is: " + err);
                }   
            });

#### <a name="joins"></a>Como: Associar tabelas relacionais

Você pode associar duas tabelas usando o método **query** do [objeto mssql][objeto mssql] para transmitir o código TSQL que implementa a associação. Vamos supor que temos alguns itens em nossa tabela **ToDoItem** e cada item da tabela possui uma propriedade de **prioridade**, que corresponde a uma coluna na tabela. Um item pode ser assim:

        { text: 'Take out the trash', complete: false, priority: 1}

Suponhamos também que temos uma tabela adicional chamada **Priority** com linhas que contêm um **número** de prioridade e uma **descrição** de texto. Por exemplo, o número de prioridade 1 pode ter a descrição "Crítico", com o objeto parecido com o seguinte:

        { number: 1, description: 'Critical'}

Agora substituímos o número de **prioridade** número no nosso item pela descrição de texto do número de prioridade. Podemos fazer isso com uma associação relacional das duas tabelas.

        mssql.query('SELECT t.text, t.complete, p.description FROM ToDoItem as t INNER JOIN Priority as p ON t.priority = p.number', {
            success: function(results) {
                console.log(results);
            },
            error: function(err) {
                console.log("error is: " + err);
        });

O script associa duas tabelas e grava os resultados no log. Os objetos resultantes podem ser assim:

        { text: 'Take out the trash', complete: false, description: 'Critical'}

#### <a name="raw"></a>Como: Executar uma consulta que retorna *resultados* brutos

Este exemplo executa a consulta como antes, mas retorna o conjunto de resultados no formato "bruto" que exige que você o analise linha por linha e coluna por coluna. Um cenário possível para isso é se você precisa de acesso aos tipos de dados não suportado pelos Serviços Móveis. Esse código simplesmente grava a saída para o log de console para que você possa inspecionar o formato bruto.

        mssql.queryRaw('SELECT * FROM ToDoItem', {
            success: function(results) {
                console.log(results);
            },
            error: function(err) {
                console.log("error is: " + err);
            }
        });

Este é o resultado da execução desta consulta. Ele contém metadados sobre cada coluna da tabela, seguidos de uma representação de linhas e colunas.

        { meta: 
           [ { name: 'id',
               size: 19,
               nullable: false,
               type: 'number',
               sqlType: 'bigint identity' },
             { name: 'text',
               size: 0,
               nullable: true,
               type: 'text',
               sqlType: 'nvarchar' },
             { name: 'complete',
               size: 1,
               nullable: true,
               type: 'boolean',
               sqlType: 'bit' },
             { name: 'priority',
               size: 53,
               nullable: true,
               type: 'number',
               sqlType: 'float' } ],
          rows: 
           [ [ 1, 'good idea for the future', null, 3 ],
             [ 2, 'this is important but not so much', null, 2 ],
             [ 3, 'fix this bug now', null, 0 ],
             [ 4, 'we need to fix this one real soon now', null, 1 ],
           ] }

#### <a name="connection"></a>Como: Obter acesso a uma conexão de banco de dados

Você pode usar o método **open** para obter acesso à conexão de banco de dados. Um motivo para fazer isso seria se você precisasse usar transações de banco de dados.

A execução com êxito do **open** faz com que a conexão de banco de dados seja transmitida para a função **êxito** como um parâmetro. Você pode chamar qualquer uma das funções a seguir no objeto de **conexão**: *close*, *queryRaw*, *query*, *beginTransaction*, *commit* e *rollback*.

            mssql.open({
                success: function(connection) {
                    connection.query(//query to execute);
                },
                error: function(err) {
                    console.log("error is: " + err);
                }
            });

## <a name="debugging"></a>Depuração e solução de problemas

A principal maneira para depurar e solucionar problemas de scripts de servidor é pela gravação no log de serviço. Por padrão, os Serviços Móveis gravam erros que ocorrem durante a execução do script de serviço nos logs de serviço. Os scripts também podem gravar nos logs. Gravar em logs é uma ótima maneira de depurar seus scripts e confirmar que eles estão se comportando como desejado.

### <a name="write-to-logs"></a>Como: Gravar saída para os logs de serviços móveis

Para gravar nos logs, use o [objeto console][objeto console] global. Use a função **log** ou **info** para registrar avisos de nível de informação. As funções **warning** e **error** registram seus respectivos níveis, que são chamados nos logs.

<div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong>
<p>Para exibir os logs do servi&ccedil;o m&oacute;vel, fa&ccedil;a logon no <a href="https://manage.windowsazure.com/">Portal de Gerenciamento</a>, selecione o servi&ccedil;o m&oacute;vel e escolha a guia <strong>Logs</strong>.</p></div>

Você também pode usar as funções de registro do [objeto de console][objeto console] para formatar suas mensagens usando parâmetros. O exemplo a seguir fornece um objeto JSON como um parâmetro para a cadeia de caracteres da mensagem:

    function insert(item, user, request) {
        console.log("Inserting item '%j' for user '%j'.", item, user);  
        request.execute();
    }

Observe que a cadeia de caracteres `%j` é usada como o espaço reservado de um objeto JSON e que os parâmetros são fornecidos em ordem sequencial.

Para evitar sobrecarregar o log, você deve remover ou desabilitar chamadas para console.log() que não são necessárias para uso de produção.

<!-- Anchors. --> 

<!-- URLs. -->

  [Back-end do .NET]: /pt-br/documentation/articles/mobile-services-dotnet-backend-how-to-use/ "Back-end do .NET"
  [Back-end do JavaScript]: /pt-br/documentation/articles/mobile-services-how-to-use-server-scripts/ "Back-end do JavaScript"
  [Introdução]: #intro
  [Operações de tabela]: #table-scripts
  [Como: Registrar-se para operações de tabela]: #register-table-scripts
  [Como: Substituir a resposta padrão]: #override-response
  [Como: Substituir executar com êxito]: #override-success
  [Como: Substituir o gerenciamento de erros padrão]: #override-error
  [Como: Adicionar parâmetros personalizados]: #access-headers
  [Como: Trabalhar com usuários da tabela]: #work-with-users
  [API personalizada]: #custom-api
  [Como: Definir uma API personalizada]: #define-custom-api
  [Como: Implementar métodos HTTP]: #handle-methods
  [Como: Enviar e receber dados como XML]: #api-return-xml
  [Como: Trabalhar com usuários e cabeçalhos em uma API personalizada]: #get-api-user
  [Como: Definir várias rotas em uma API personalizada]: #api-routes
  [Agendador de Trabalhos]: #scheduler-scripts
  [Controle do código-fonte, código compartilhado e funções do auxiliar]: #shared-code
  [Como: Carregar módulos Node.js]: #modules-helper-functions
  [Como: Usar funções de auxiliar]: #helper-functions
  [Como: Compartilhar o código de compartilhamento usando o controle do código-fonte]: #shared-code-source-control
  [Como: Trabalhar com configurações de aplicativo]: #app-settings
  [Usando a ferramenta de linha de comando]: #command-prompt
  [Trabalhando com tabelas]: #working-with-tables
  [Como: Acessar tabelas de scripts]: #access-tables
  [Como: Executar inserções em massa]: #bulk-inserts
  [Como: Mapear tipos JSON para tipos de banco de dados]: #JSON-types
  [Usando o Transact-SQL para acessar tabelas]: #TSQL
  [Depuração e solução de problemas]: #debugging
  [Como: Gravar saída para os logs de serviços móveis]: #write-to-logs
  [Referência de script de servidor dos Serviços Móveis]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj554226.aspx
  [objeto de consulta]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj613353.aspx
  [objeto de usuário]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj554220.aspx
  [objeto de solicitação]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj554218.aspx
  [Insert]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj554229.aspx
  [Update]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj554214.aspx
  [Delete]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj554215.aspx
  [Read]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj554224.aspx
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-how-to-use-server-scripts/1-mobile-insert-script-users.png
  [Validar e modificar dados em Serviços Móveis usando scripts de servidor]: /pt-br/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet/
  [Leitura e gravação de dados]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj631640.aspx
  [Modificar a solicitação]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj631635.aspx
  [Validar dados]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj631638.aspx
  [Modificar a resposta]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj631631.aspx
  [Comece a usar a autenticação]: http://go.microsoft.com/fwlink/p/?LinkId=287177
  [2]: http://msdn.microsoft.com/pt-br/library/windowsazure/dn280974.aspx
  [resposta]: http://msdn.microsoft.com/pt-br/library/windowsazure/dn303373.aspx
  [biblioteca de express.js]: http://go.microsoft.com/fwlink/p/?LinkId=309046
  [3]: ./media/mobile-services-how-to-use-server-scripts/2-mobile-custom-api-script.png
  [Chamar uma API personalizada do cliente]: /pt-br/develop/mobile/tutorials/call-custom-api-dotnet/#define-custom-api
  [Definir uma API personalizada que dá suporte a notificações periódicas]: /pt-br/develop/mobile/tutorials/create-pull-notifications-dotnet/
  [objeto expresso em express.js]: http://expressjs.com/api.html#express
  [Agendar trabalhos]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj860528.aspx
  [4]: ./media/mobile-services-how-to-use-server-scripts/3-mobile-schedule-job-script.png
  [Agendar trabalhos de back-end nos Serviços Móveis]: /pt-br/develop/mobile/tutorials/schedule-backend-tasks/
  [SDK do Azure para Node.js]: http://go.microsoft.com/fwlink/p/?LinkId=275539
  [Documentação do Node.js]: http://go.microsoft.com/fwlink/p/?LinkId=288802
  [5]: http://go.microsoft.com/fwlink/p/?LinkId=288803
  [6]: http://go.microsoft.com/fwlink/p/?LinkId=288804
  [Enviar solicitação HTTP]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj631641.aspx
  [Enviar e-mail dos Serviços Móveis com SendGrid]: /pt-br/develop/mobile/tutorials/send-email-with-sendgrid/
  [7]: http://go.microsoft.com/fwlink/p/?LinkId=288805
  [8]: http://go.microsoft.com/fwlink/p/?LinkId=288806
  [9]: http://go.microsoft.com/fwlink/p/?LinkId=288807
  [Suporte para package.json nos Serviços Móveis do Azure]: http://go.microsoft.com/fwlink/p/?LinkId=391036
  [Utilizar código compartilhado e módulos do Node. js em seus scripts de servidor]: /pt-br/develop/mobile/tutorials/store-scripts-in-source-control/#use-npm
  [objeto de tabela]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj614364.aspx
  [Configurações do aplicativo]: http://msdn.microsoft.com/pt-br/library/dn529070.aspx
  [objeto de serviço]: http://msdn.microsoft.com/pt-br/library/windowsazure/dn303371.aspx
  [10]: ./media/mobile-services-how-to-use-server-scripts/4-mobile-source-local-cli.png
  [Comandos para gerenciar os Serviços Móveis do Azure]: /pt-br/manage/linux/other-resources/command-line-tools/#Commands_to_manage_mobile_services/#Mobile_Scripts
  [11]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj554210.aspx
  [objeto mssql]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj554212.aspx
  [postagem no blog]: http://blogs.msdn.com/b/jpsanders/archive/2013/03/20/server-script-to-insert-table-items-in-windows-azure-mobile-services.aspx
  [JSON.parse]: http://es5.github.io/#x15.12
  [Como: Executar uma consulta estática]: #static-query
  [Como: Executar uma consulta dinâmica]: #dynamic-query
  [Como: Associar tabelas relacionais]: #joins
  [Como: Executar uma consulta que retorna *resultados* brutos]: #raw
  [Como: Obter acesso a uma conexão de banco de dados]: #connection
  [objeto console]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj554209.aspx
