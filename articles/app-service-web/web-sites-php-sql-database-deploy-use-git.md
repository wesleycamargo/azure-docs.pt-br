---
title: "Criar um aplicativo Web PHP-SQL e implantá-lo no Serviço de Aplicativo do Azure usando Git"
description: "Um tutorial que demonstra como criar um aplicativo Web do PHP que armazena dados no Banco de Dados SQL do Azure e usa a implantação do Git para o Serviço de Aplicativo do Azure."
services: app-service\web, sql-database
documentationcenter: php
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 6b090bf6-31d8-4b74-81eb-050ef95929ca
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: c7dbb3ab55607e377abc3c91c7ee3e938248a676


---
# <a name="create-a-php-sql-web-app-and-deploy-to-azure-app-service-using-git"></a>Criar um aplicativo Web PHP-SQL e implantá-lo no Serviço de Aplicativo do Azure usando Git
Este tutorial mostra como criar um aplicativo web do PHP no [serviço de aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) que se conecta ao banco de dados SQL do Azure e como implantá-lo usando o Git. Este tutorial presume que você tenha [PHP][install-php], [SQL Server Express][install-SQLExpress], [Drivers da Microsoft para SQL Server para PHP](http://www.microsoft.com/download/en/details.aspx?id=20098) e [Git][install-git] instalados em seu computador. Após a conclusão deste guia, você terá um aplicativo Web PHP/SQL em execução no Azure.

> [!NOTE]
> É possível instalar e configurar PHP, SQL Server Express, os Drivers da Microsoft para SQL Server para PHP usando o [Microsoft Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx).
> 
> 

Você aprenderá:

* Como criar um aplicativo Web do Azure e um banco de dados SQL usando o [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Já que o PHP está habilitado nos Aplicativos Web do Serviço de Aplicativo por padrão, não é necessário nada de especial para executar seu código PHP.
* Como publicar e publicar novamente o aplicativo no Azure usando o Git.

Seguindo este tutorial, você irá criar um aplicativo da web de registro simples no PHP. O aplicativo será hospedado em um Site do Azure. Abaixo, uma captura de tela do aplicativo concluído:

![Site PHP do Azure](./media/web-sites-php-sql-database-deploy-use-git/running_app_3.png)

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

> [!NOTE]
> Se desejar começar a usar o Serviço de Aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo](https://azure.microsoft.com/try/app-service/), onde você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.
> 
> 

## <a name="create-an-azure-web-app-and-set-up-git-publishing"></a>Criar um aplicativo Web do Azure e configurar a publicação Git
Siga estas etapas para criar um aplicativo Web do Azure e um banco de dados SQL:

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Abra o Azure Marketplace clicando no ícone **Novo** no lado superior esquerdo do painel, clique em **Selecionar Tudo** próximo a Marketplace e selecione **Web + Móvel**.
3. No Marketplace, selecione **Web + Móvel**.
4. Clique no ícone **Aplicativo Web + SQL** .
5. Depois de ler a descrição do aplicativo Web + aplicativo SQL, selecione **Criar**.
6. Clique em cada parte (**Grupo de Recursos**, **Aplicativo Web**, **Banco de Dados** e **Assinatura**) e insira ou selecione valores para os campos obrigatórios:
   
   * Insira um nome de URL de sua escolha    
   * Configurar credenciais de servidor de banco de dados
   * Selecione a região mais próxima de você
     
     ![configurar o aplicativo](./media/web-sites-php-sql-database-deploy-use-git/configure-db-settings.png)
7. Quando terminar de definir o aplicativo Web, clique em **Criar**.
   
    Quando o aplicativo Web tiver sido criado, o botão **Notificações** piscará **SUCESSO** em verde e abrirá a folha do grupo de recursos para exibir o aplicativo Web e o banco de dados SQL no grupo.
8. Clique no ícone do aplicativo Web na folha do grupo de recursos para abrir a folha do aplicativo Web.
   
    ![grupo de recursos do aplicativo Web](./media/web-sites-php-sql-database-deploy-use-git/resource-group-blade.png)
9. Em **Configurações**, clique em **Implantação contínua** > **Definir configurações necessárias**. Selecione **Repositório Git local** e clique em **OK**.
   
    ![onde está o código-fonte](./media/web-sites-php-sql-database-deploy-use-git/setup-local-git.png)
   
    Se não tiver configurado um repositório Git antes, você deverá fornecer um nome de usuário e senha. Para fazer isso, clique em **Configurações** > **Credenciais de implantação** na folha do aplicativo Web.
   
    ![](./media/web-sites-php-sql-database-deploy-use-git/deployment-credentials.png)
10. Em **Configurações**, clique em **Propriedades** para ver a URL remota do Git que você precisa usar para implantar seu aplicativo PHP posteriormente.

## <a name="get-sql-database-connection-information"></a>Obter informações da conexão do Banco de Dados SQL
Para se conectar à instância do banco de dados SQL que está vinculada a seu aplicativo Web, você precisa das informações de conexão, que especificou ao criar o banco de dados. Para obter informações sobre a conexão do Banco de Dados SQL, siga estas etapas:

1. Na folha do grupo de recursos, clique no ícone do banco de dados SQL.
2. Na folha do banco de dados SQL, clique em **Configurações** > **Propriedades** e clique em **Mostrar cadeias de conexão de banco de dados**. 
   
    ![Exibir propriedades do banco de dados](./media/web-sites-php-sql-database-deploy-use-git/view-database-properties.png)
3. Na seção **PHP** da caixa de diálogo resultante, anote os valores de `Server`, `SQL Database` e `User Name`. Você usará posteriormente esses valores ao publicar seu aplicativo Web do PHP para o Serviço de Aplicativo do Azure.

## <a name="build-and-test-your-application-locally"></a>Criar e testar o aplicativo localmente
O aplicativo Registro é um aplicativo simples do PHP que permite que você se registre em um evento fornecendo seu nome e endereço de email. As informações sobre inscritos anteriores são exibidas em uma tabela. As informações de registro são armazenadas em uma instância do Banco de Dados SQL. O aplicativo consiste em dois arquivos (copie/cole o código disponível abaixo):

* **index.php**: exibe um formulário de registro e uma tabela contendo informações sobre o inscrito.
* **createtable.php**: cria a tabela de Banco de Dados SQL para o aplicativo. Este arquivo será usado apenas uma vez.

Para executar o aplicativo localmente, siga as etapas abaixo. Observe que essas etapas pressupõem que você tem PHP e SQL Server Express definidos em sua máquina local, e que você habilitou a [Extensão PDO para SQL Server][pdo-sqlsrv].

1. Crie um Banco de Dados SQL chamado `registration`. Você pode fazer isso a partir do comando `sqlcmd` com estes comandos:
   
        >sqlcmd -S localhost\sqlexpress -U <local user name> -P <local password>
        1> create database registration
        2> GO    
2. No diretório raiz de seu aplicativo, crie dois arquivos: um chamado `createtable.php` e outro chamado `index.php`.
3. Abra o arquivo `createtable.php` em um editor de texto ou IDE e adicione o código abaixo. Esse código será usado para criar a tabela `registration_tbl` no banco de dados `registration`.
   
        <?php
        // DB connection info
        $host = "localhost\sqlexpress";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        try{
            $conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
            $sql = "CREATE TABLE registration_tbl(
            id INT NOT NULL IDENTITY(1,1) 
            PRIMARY KEY(id),
            name VARCHAR(30),
            email VARCHAR(30),
            date DATE)";
            $conn->query($sql);
        }
        catch(Exception $e){
            die(print_r($e));
        }
        echo "<h3>Table created.</h3>";
        ?>
   
    Observe que você precisará atualizar os valores de <code>$user</code> e <code>$pwd</code> com seu nome de usuário e senha SQL Server local.
4. Em um terminal no diretório raiz do aplicativo, digite o seguinte comando:
   
        php -S localhost:8000
5. Abra um navegador da Web e navegue para **http://localhost:8000/createtable.php**. Isso criará a tabela `registration_tbl` no banco de dados.
6. Abra o arquivo **index.php** em um editor de texto ou IDE e adicione o código básico de HTML e CSS para a página (o código PHP será adicionado em várias etapas).
   
        <html>
        <head>
        <Title>Registration Form</Title>
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
        <h1>Register here!</h1>
        <p>Fill in your name and email address, then click <strong>Submit</strong> to register.</p>
        <form method="post" action="index.php" enctype="multipart/form-data" >
              Name  <input type="text" name="name" id="name"/></br>
              Email <input type="text" name="email" id="email"/></br>
              <input type="submit" name="submit" value="Submit" />
        </form>
        <?php
   
        ?>
        </body>
        </html>
7. Nas marcas de PHP, adicione o código PHP para conectar ao banco de dados.
   
        // DB connection info
        $host = "localhost\sqlexpress";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        // Connect to database.
        try {
            $conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
        }
        catch(Exception $e){
            die(var_dump($e));
        }
   
    Mais uma vez, será necessário atualizar os valores de <code>$user</code> e <code>$pwd</code> com seu nome de usuário e senha do MySQL local.
8. Após o código de conexão do banco de dados, adicione código para inserir informações de registro no banco de dados.
   
        if(!empty($_POST)) {
        try {
            $name = $_POST['name'];
            $email = $_POST['email'];
            $date = date("Y-m-d");
            // Insert data
            $sql_insert = "INSERT INTO registration_tbl (name, email, date) 
                           VALUES (?,?,?)";
            $stmt = $conn->prepare($sql_insert);
            $stmt->bindValue(1, $name);
            $stmt->bindValue(2, $email);
            $stmt->bindValue(3, $date);
            $stmt->execute();
        }
        catch(Exception $e) {
            die(var_dump($e));
        }
        echo "<h3>Your're registered!</h3>";
        }
9. Finalmente, após o código acima, adicione código para recuperar dados do banco de dados.
   
        $sql_select = "SELECT * FROM registration_tbl";
        $stmt = $conn->query($sql_select);
        $registrants = $stmt->fetchAll(); 
        if(count($registrants) > 0) {
            echo "<h2>People who are registered:</h2>";
            echo "<table>";
            echo "<tr><th>Name</th>";
            echo "<th>Email</th>";
            echo "<th>Date</th></tr>";
            foreach($registrants as $registrant) {
                echo "<tr><td>".$registrant['name']."</td>";
                echo "<td>".$registrant['email']."</td>";
                echo "<td>".$registrant['date']."</td></tr>";
            }
             echo "</table>";
        } else {
            echo "<h3>No one is currently registered.</h3>";
        }

Agora você pode navegar para **http://localhost:8000/index.php** para testar o aplicativo.

## <a name="publish-your-application"></a>Publicar seu aplicativo
Depois de testar o aplicativo localmente, você poderá publicá-lo nos Aplicativos Web do Serviço de Aplicativo do Azure usando o Git. Entretanto, você precisará atualizar a conexão do banco de dados no aplicativo. Usando as informações de conexão de banco de dados obtidas previamente (na seção **Obter informações de conexão de banco de dados SQL**), atualize as seguintes informações nos **dois** arquivos `createdatabase.php` e `index.php` com os valores apropriados:

    // DB connection info
    $host = "tcp:<value of Server>";
    $user = "<value of User Name>";
    $pwd = "<your password>";
    $db = "<value of SQL Database>";

> [!NOTE]
> Em <code>$host</code>, o valor do Servidor deve ser precedido com <code>tcp:</code>.
> 
> 

Agora, você está pronto para configurar a publicação Git e publicar o aplicativo.

> [!NOTE]
> Essas são as mesmas etapas indicadas no final da seção **Criar um aplicativo web do Azure e configurar a publicação do Git** , acima.
> 
> 

1. Abra GitBash (ou um terminal, se o Git estiver em seu `PATH`), altere os diretórios para o diretório raiz de seu aplicativo (o diretório de **registro** ) e execute os seguintes comandos:
   
        git init
        git add .
        git commit -m "initial commit"
        git remote add azure [URL for remote repository]
        git push azure master
   
    Será solicitada a senha que você criou anteriormente.
2. Navegue até **http://[nome do aplicativo Web].azurewebsites.net/createtable.php** para criar a tabela do banco de dados SQL para o aplicativo.
3. Navegue até **http://[nome do aplicativo Web].azurewebsites.net/index.php** para começar a usar o aplicativo.

Depois de ter publicado seu aplicativo, você pode começar a fazer alterações nele e usar o Git para publicá-lo. 

## <a name="publish-changes-to-your-application"></a>Publicar alterações em seu aplicativo
Para publicar alterações no aplicativo, siga estas etapas:

1. Faça alterações em seu aplicativo localmente.
2. Abra GitBash (ou um terminal, se o Git estiver em seu `PATH`), altere os diretórios para o diretório raiz de seu aplicativo e execute os seguintes comandos:
   
        git add .
        git commit -m "comment describing changes"
        git push azure master
   
    Será solicitada a senha que você criou anteriormente.
3. Navegue até **http://[nome do aplicativo Web].azurewebsites.net/index.php** para ver suas alterações.

## <a name="whats-changed"></a>O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/download/details.aspx?id=20098
[install-git]: http://git-scm.com/
[pdo-sqlsrv]: http://php.net/pdo_sqlsrv




<!--HONumber=Jan17_HO3-->


