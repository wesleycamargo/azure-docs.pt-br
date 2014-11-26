<properties linkid="develop-php-website-with-storage" urlDisplayName="Web w/ Storage" pageTitle="PHP website with table storage - Azure tutorial" metaKeywords="Azure table storage PHP, Azure PHP website, Azure PHP web site, Azure PHP tutorial, Azure PHP example" description="This tutorial shows you how to create a PHP website and use the Azure Tables storage service in the back-end." metaCanonical="" services="web-sites,storage" documentationCenter="PHP" title="Create a PHP Website using Azure Storage" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />

# Criar um site PHP usando armazenamento do Azure

Este tutorial mostra como criar um site PHP e usar o serviço de armazenamento de tabelas do Azure no back-end. Este tutorial assume que você tem [PHP][PHP] e um servidor web instalados no seu computador. As instruções deste tutorial podem ser seguidas em qualquer sistema operacional, incluindo o Windows, o Mac e o Linux. Ao completar este guia, você terá um site PHP em execução no Azure e o acesso ao serviço de armazenamento de tabela.

Você aprenderá a:

-   Como instalar as bibliotecas de cliente Azure e incluí-las em seu aplicativo.
-   Como usar as bibliotecas de cliente para criar tabelas e para criar, consultar e excluir entidades de tabela.
-   Como criar uma conta de armazenamento do Azure e configurar seu aplicativo para usá-lo.
-   Como criar um Site do Azure e implantar a ele usando Git

Você criará um aplicativo simples da web Tasklist no PHP. A seguinte é uma captura de tela do aplicativo concluído:

![Site PHP do Azure][Site PHP do Azure]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Instalar as bibliotecas de cliente do Azure

Para instalar as Bibliotecas de Clientes do PHP através do computador, siga estas etapas:

1.  [Instalar o Git.][Instalar o Git.]

    > [WACOM.NOTE]
    > No Windows, também será necessário adicionar o Git executável à variável de ambiente PATH.

2.  Crie um arquivo chamado **composer.json** na raiz do seu projeto e adicione o seguinte código a ele:

        {
            "require": {
                "microsoft/windowsazure": "*"
            },          
            "repositories": [
                {
                    "type": "pear",
                    "url": "http://pear.php.net"
                }
            ],
            "minimum-stability": "dev"
        }

3.  Baixe o **[composer.phar][composer.phar]** na raiz do seu projeto.

4.  Abra um prompt de comando e execute esta função na raiz do projeto

        php composer.phar install

## Introdução à biblioteca de clientes

Há quatro etapas básicas que devem ser executadas antes de fazer uma chamada para um API do Azure usando as bibliotecas. Você criará um script de inicialização que irá executar essas etapas.

-   Crie um arquivo chamado **init.php**.

-   Primeiro, inclua o script do carregador automático:

        require_once 'vendor\autoload.php'; 

-   Inclua os namespaces que você usará.

    Para criar qualquer cliente de serviço do Azure é necessário usar a classe **ServicesBuilder**:

        use WindowsAzure\Common\ServicesBuilder;

    Para capturar exceções produzidas por qualquer chamada de API, é necessário a classe **ServiceException**:

        use WindowsAzure\Common\ServiceException;

-   Para criar uma instância de cliente de serviço será necessário uma sequência de conexão válida. O formato para as cadeias de conexão do serviço Tabela é:

    Para acessar um serviço ao vivo:

        DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

    Para acessar o armazenamento do emulador:

        UseDevelopmentStorage=true

-   Use o método de fábrica `ServicesBuilder::createTableService` para instanciar um invólucro em torno das chamadas de serviço da tabela.

        $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    O `$tableRestProxy` contém um método para cada chamada REST disponível nas tabelas do Azure.

## Criando uma tabela

Antes de armazenar dados, você primeiro precisa criar um recipiente para ele, a tabela.

-   Crie um arquivo chamado **createtable.php**.

-   Primeiro, inclua o script de inicialização que você acabou de criar. Você estará incluindo o script em cada arquivo acessando o Azure:

        <?php
        require_once "init.php";

-   Em seguida, faça uma chamada para *createTable* passando o nome da tabela. Da mesma forma para outros armazenamentos de tabela NoSQL, nenhum esquema é necessário para tabelas do Azure.

        try {
            $tableRestProxy->createTable('tasks');
        }
        catch(ServiceException $e){
            $code = $e->getCode();
            $error_message = $e->getMessage();
            echo $code.": ".$error_message."<br />";
        }
        ?>

    Os códigos de erro e a verificação de mensagens são encontrados aqui: [][]<http://msdn.microsoft.com/pt-BR/library/windowsazure/dd179438.aspx></a>

## Consultando uma tabela

A home page do aplicativo Tasklist deve listar todas as tarefas existentes e permite a inserção de novos produtos.

-   Crie um arquivo chamado **index.php** e insira o seguinte código HTML e PHP que formarão o cabeçalho da página:

        <html>
        <head>
            <title>Index</title>
            <style type="text/css">
                body { background-color: #fff; border-top: solid 10px #000;
                    color: #333; font-size: .85em; margin: 20; padding: 20;
                    font-family: "Segoe UI", Verdana, Helvetica, Sans-Serif;
                }
                h1, h2, h3,{ color: #000; margin-bottom: 0; padding-bottom: 0; }
                h1 { font-size: 2em; }
                h2 { font-size: 1.75em; }
                h3 { font-size: 1.2em; }
                table { margin-top: 0.75em; }
                th { font-size: 1.2em; text-align: left; border: none; padding-left: 0; }
                td { padding: 0.25em 2em 0.25em 0em; border: 0 none; }
            </style>
        </head>
        <body>
        <h1>My ToDo List <font color="grey" size="5">(powered by PHP and Azure Tables) </font></h1>
        <?php       
        require_once "init.php";

-   Para consultar tabelas do Azure para **todas as entidades** armazenadas na tabela *tarefas*, você chamará o método *queryEntities* passando apenas o nome da tabela. Na seção **Atualizar uma entidade** abaixo, você também verá como passar um filtro de consulta para uma entidade específica.

        try {
            $result = $tableRestProxy->queryEntities('tasks');
        }
        catch(ServiceException $e){
            $code = $e->getCode();
            $error_message = $e->getMessage();
            echo $code.": ".$error_message."<br />";
        }

-   Para iterar em entidades no conjunto de resultados:

        $entities = $result->getEntities();

        for ($i = 0; $i < count($entities); $i++) {

-   Depois que você obtiver uma `Entity`, o modelo para leitura de dados é `Entity->getPropertyValue('[name]')`:

            if ($i == 0) {
                echo "<table border='1'>
                <tr>
                    <td>Name</td>
                    <td>Category</td>
                    <td>Date</td>
                    <td>Mark Complete?</td>
                    <td>Delete?</td>
                </tr>";
            }
            echo "
                <tr>
                    <td>".$entities[$i]->getPropertyValue('name')."</td>
                    <td>".$entities[$i]->getPropertyValue('category')."</td>
                    <td>".$entities[$i]->getPropertyValue('date')."</td>";
                    if ($entities[$i]->getPropertyValue('complete') == false)
                        echo "<td><a href='markitem.php?complete=true&pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Mark Complete</a></td>";
                    else
                        echo "<td><a href='markitem.php?complete=false&pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Unmark Complete</a></td>";
                    echo "
                    <td><a href='deleteitem.php?pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Delete</a></td>
                </tr>";
        }

        if ($i > 0)
            echo "</table>";
        else
            echo "<h3>No items on list.</h3>";
        ?>

-   Por fim, você deve inserir o formulário que fornece dados para o script de inserção de tarefa e concluir o HTML:

            <hr/>
            <form action="additem.php" method="post">
                <table border="1">
                    <tr>
                        <td>Item Name: </td>
                        <td><input name="itemname" type="textbox"/></td>
                    </tr>
                    <tr>
                        <td>Category: </td>
                        <td><input name="category" type="textbox"/></td>
                    </tr>
                    <tr>
                        <td>Date: </td>
                        <td><input name="date" type="textbox"/></td>
                    </tr>
                </table>
                <input type="submit" value="Add item"/>
            </form>
        </body>
        </html>

## Inserir entidades em uma tabela

Seu aplicativo pode ler todos os itens armazenados na tabela. Desde que não haja qualquer punho, vamos adicionar uma função que grava os dados no banco de dados.

-   Crie um arquivo chamado **additem.php**.

-   Adicione o seguinte ao arquivo:

        <?php       
        require_once "init.php";        
        use WindowsAzure\Table\Models\Entity;
        use WindowsAzure\Table\Models\EdmType;      

-   A primeira etapa para inserir uma entidade é instanciar um objeto `Entity` e configurar as propriedades nele:

        $entity = new Entity();
        $entity->setPartitionKey('p1');
        $entity->setRowKey((string) microtime(true));
        $entity->addProperty('name', EdmType::STRING, $_POST['itemname']);
        $entity->addProperty('category', EdmType::STRING, $_POST['category']);
        $entity->addProperty('date', EdmType::STRING, $_POST['date']);
        $entity->addProperty('complete', EdmType::BOOLEAN, false);

-   Em seguida, você pode passar a `$entity` que acabou de criar ao método `insertEntity`:

        try{
            $tableRestProxy->insertEntity('tasks', $entity);
        }
        catch(ServiceException $e){
            $code = $e->getCode();
            $error_message = $e->getMessage();
            echo $code.": ".$error_message."<br />";
        }

-   Por último, para fazer a página retornar à home page depois de inserir a entidade:

        header('Location: index.php');      
        ?>

## Atualizando uma entidade

O aplicativo de lista de tarefa tem a capacidade de marcar um item como concluído, bem como desmarcá-lo. A home page passa o *RowKey* e *PartitionKey* de uma entidade e o estado de destino (marcado==1, desmarcado==0).

-   Crie um arquivo chamado **markitem.php** e adicione a parte de inicialização:

        <?php       
        require_once "init.php";

-   O primeiro passo para atualização de uma entidade é buscá-la da tabela:

        $result = $tableRestProxy->queryEntities('tasks', 'PartitionKey eq \''.$_GET['pk'].'\' and RowKey eq \''.$_GET['rk'].'\'');     
        $entities = $result->getEntities();     
        $entity = $entities[0];

    Como você pode ver no filtro da consulta aprovado, o formato é `Key eq 'Value'`. Uma descrição completa da sintaxe de consulta está disponível [aqui][aqui].

-   Em seguida, você pode alterar quaisquer propriedades:

        $entity->setPropertyValue('complete', ($_GET['complete'] == 'true') ? true : false);

-   E o método `updateEntity` executa a atualização:

        try{
            $result = $tableRestProxy->updateEntity('tasks', $entity);
        }
        catch(ServiceException $e){
            $code = $e->getCode();
            $error_message = $e->getMessage();
            echo $code.": ".$error_message."<br />";
        }

-   Para fazer a página retornar à home page depois de inserir a entidade:

        header('Location: index.php');      
        ?>

## Excluir uma entidade

A exclusão de um item é realizada por uma única chamada para `deleteItem`. Os valores passados são **PartitionKey** e **RowKey**, que juntos formam a chave primária da entidade. Crie um arquivo chamado **deleteitem.php** e insira o seguinte código:

        <?php
        
        require_once "init.php";        
        $tableRestProxy->deleteEntity('tasks', $_GET['pk'], $_GET['rk']);       
        header('Location: index.php');
        
        ?>

## Criar uma conta de armazenamento do Azure

Para fazer seu aplicativo armazenar dados na nuvem, você precisa primeiro criar uma conta de armazenamento no Azure e, em seguida, passar as informações de autenticação adequadas para a classe *Configuração*.

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].

2.  Clique no ícone **+ Novo** na parte inferior esquerda do portal.

    ![Criar um novo site do Azure][Criar um novo site do Azure]

3.  Clique em **Serviços de Dados**, **Armazenamento**, depois em **Criação Rápida**.

    ![Criação personalizada de um novo site][Criação personalizada de um novo site]

    Insira um valor para **URL** e selecione o centro de dados para seu site no menu suspenso **REGIÃO**. Clique no botão **Criar conta de armazenamento** na parte inferior da caixa de diálogo.

    ![Preencha os detalhes do site][Preencha os detalhes do site]

    Quando a conta de armazenamento tiver sido criada, você verá o texto **Criação da conta de armazenamento '[NOME]' criada com êxito**.

4.  Verifique se a guia **Armazenamento** está selecionada e, em seguida, selecione a conta de armazenamento que você recém criou na lista.

5.  Clique em **Gerenciar Chaves** na barra de aplicativos na parte inferior.

    ![Selecione Gerenciar chaves][Selecione Gerenciar chaves]

6.  Anote o nome da conta de armazenamento que você criou e da chave primária.

    ![Selecione Gerenciar chaves][1]

7.  Abra **init.php** e substitua `[YOUR_STORAGE_ACCOUNT_NAME]` e `[YOUR_STORAGE_ACCOUNT_KEY]` com o nome da conta e a chave você tirou nota na última etapa. Salve o arquivo.

## Criar um site do Azure e configurar a publicação Git

Siga estas etapas para criar um site do Azure:

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  Clique no ícone **+ Novo** na parte inferior esquerda do portal.

    ![Criar um novo site do Azure][Criar um novo site do Azure]

3.  Clique em **Computação**, **Site**, em seguida, em **Criação rápida**.

    ![Criação personalizada de um novo site][2]

    Insira um valor para **URL** e selecione o centro de dados para seu site no menu suspenso **REGIÃO**. Clique no botão **Criar novo site da web** na parte inferior da caixa de diálogo.

    ![Preencha os detalhes do site][3]

    Quando o site for criado, você verá o texto **Criação do site '[NOME DO SITE]' concluído com êxito**. Agora, você pode habilitar a publicação do Git.

4.  Clique no nome do site exibido na lista de sites para abrir o painel **INÍCIO RÁPIDO**.

    ![Abrir o painel do site][Abrir o painel do site]

5.  Na parte inferior direita da página Quickstart, selecione **Configurar a implantação a partir do controle de origem**.

    ![Configurar a publicação do Git][Configurar a publicação do Git]

6.  Quando receber a pergunta "Onde está seu código-fonte?" selecionar **Repositório Git Local** e, em seguida, clique na seta.

    ![onde está o código-fonte][onde está o código-fonte]

7.  Para habilitar a publicação do Git, você deve fornecer um nome de usuário e uma senha. Anote o nome do usuário e a senha que você criou. (Se você tiver configurado um repositório de Git antes, esta etapa será ignorada.)

    ![Criar credenciais de publicação][Criar credenciais de publicação]

    Levará alguns segundos para configurar seu repositório.

8.  Quando o repositório Git estiver pronto, você receberá instruções sobre os comandos Git a serem usados para configurar um repositório local e enviar os arquivos ao Azure.

    ![Instruções da implantação do Git retornadas após a criação de um repositório para o site.][Instruções da implantação do Git retornadas após a criação de um repositório para o site.]

    Observe as instruções, pois elas serão usadas na próxima seção para publicar o aplicativo.

## Publicar seu aplicativo

Para publicar o aplicativo com Git, siga as etapas abaixo.

1.  Abra a pasta **vendor/microsoft/windowsazure** sob a raiz do aplicativo e exclua os seguintes arquivos e pastas:

    -   .git
    -   .gitattributes
    -   .gitignore

    Quando o Gerenciador de pacotes do Composer baixa as bibliotecas de cliente Azure e suas dependências, ele faz isso por meio da clonagem de repositório GitHub em que reside. Na próxima etapa, o aplicativo será implantado por meio do Git através da criação de um repositório fora da pasta raiz do aplicativo. O Git irá ignorar o repositório sub onde as bibliotecas de cliente residem, a menos que os arquivos específicos do repositório são removidos.

2.  Abra GitBash (ou um terminal, se o Git estiver em seu `PATH`), altere para o diretório raiz de seu aplicativo e execute os seguintes comandos (**Observação:**) estas são as mesmas etapas observadas no final da seção **Criar um Site do Azure e Configurar a publicação do Git**):

        git init
        git add .
        git commit -m "initial commit"
        git remote add azure [URL for remote repository]
        git push azure master

    Será solicitada a senha que você criou anteriormente.

3.  Vá para **http://[o domínio do seu site]/createtable.php** para criar a tabela para o aplicativo.
4.  Vá para **http://[o domínio do seu site]/index.php** para começar a usar o aplicativo.

Depois de ter publicado seu aplicativo, você pode começar a fazer alterações nele e usar o Git para publicá-lo.

## Publicar alterações em seu aplicativo

Para publicar alterações no aplicativo, siga estas etapas:

1.  Faça alterações em seu aplicativo localmente.
2.  Abra GitBash (ou um terminal, se o Git estiver em seu `PATH`), altere para o diretório raiz de seu aplicativo e execute os seguintes comandos:

        git add .
        git commit -m "comment describing changes"
        git push azure master

    Será solicitada a senha que você criou anteriormente.

3.  Vá para **http://[o domínio do seu site]/index.php** para ver as alterações.

  [PHP]: http://www.php.net/manual/en/install.php
  [Site PHP do Azure]: ./media/web-sites-php-storage/ws-storage-app.png
  [Instalar o Git.]: http://git-scm.com/book/en/Getting-Started-Installing-Git
  [composer.phar]: http://getcomposer.org/composer.phar
  []: http://msdn.microsoft.com/pt-BR/library/windowsazure/dd179438.aspx
  [aqui]: http://msdn.microsoft.com/pt-BR/library/windowsazure/dd894031.aspx
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com
  [Criar um novo site do Azure]: ./media/web-sites-php-storage/new_website.jpg
  [Criação personalizada de um novo site]: ./media/web-sites-php-storage/storage-quick-create.png
  [Preencha os detalhes do site]: ./media/web-sites-php-storage/storage-quick-create-details.png
  [Selecione Gerenciar chaves]: ./media/web-sites-php-storage/storage-manage-keys.png
  [1]: ./media/web-sites-php-storage/storage-access-keys.png
  [2]: ./media/web-sites-php-storage/website-quick-create.png
  [3]: ./media/web-sites-php-storage/website-quick-create-details.png
  [Abrir o painel do site]: ./media/web-sites-php-storage/go_to_dashboard.png
  [Configurar a publicação do Git]: ./media/web-sites-php-storage/setup_git_publishing.png
  [onde está o código-fonte]: ./media/web-sites-php-storage/where_is_code.png
  [Criar credenciais de publicação]: ./media/web-sites-php-storage/git-deployment-credentials.png
  [Instruções da implantação do Git retornadas após a criação de um repositório para o site.]: ./media/web-sites-php-storage/git-instructions.png
