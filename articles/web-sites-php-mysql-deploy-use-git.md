<properties linkid="develop-php-website-with-mysql-and-git" urlDisplayName="Web w/ MySQL + Git" pageTitle="PHP website with MySQL and Git - Azure tutorial" metaKeywords="" description="A tutorial that demonstrates how to create a PHP website that stores data in MySQL and use Git deployment to Azure." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Create a PHP-MySQL Azure website and deploy using Git" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

# Criar um Site do Azure PHP-MySQL e implantar usando o Git

Este tutorial mostra como criar um Site do Azure PHP-MySQL e como implantá-lo usando o Git. Você usará o [PHP][PHP], a Ferramenta de Linha de Comando do MySQL (parte do [MySQL][install-mysql]), um servidor web e o [Git][Git] instalados no seu computador. As instruções deste tutorial podem ser seguidas em qualquer sistema operacional, incluindo o Windows, o Mac e o Linux. Após a conclusão deste guia, você terá um site do PHP/MySQL em execução no Azure.

Você aprenderá a:

-   Como criar um Site do Azure e um banco de dados MySQL usando o Portal de Gerenciamento do Azure. Como, por padrão, o PHP está habilitado no sites do Azure, nada de especial é necessário para executar seu código PHP.
-   Como publicar e publicar novamente o aplicativo no Azure usando o Git.

Seguindo este tutorial, você irá criar um aplicativo da web de registro simples no PHP. O aplicativo será hospedado em um Site do Azure. A seguinte é uma captura de tela do aplicativo concluído:

![Site PHP do Azure][Site PHP do Azure]

<div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> <p>Para concluir este tutorial, voc&ecirc; precisa de uma conta do Azure com o recurso Sites do Azure habilitado. Se voc&ecirc; n&atilde;o tiver uma conta, poder&aacute; criar uma conta de avalia&ccedil;&atilde;o gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A74E0F923" target="_blank">Avalia&ccedil;&atilde;o gratuita do Azure</a>.</p> </div>

## Configurar o ambiente de desenvolvimento

Este tutorial pressupõe que você tem o [PHP][PHP], a Ferramenta de Linha de Comando do MySQL (parte do [MySQL][install-mysql]), um servidor web e o [Git][Git] instalados em seu computador.

> [WACOM.NOTE]
> Se você estiver executando este tutorial no Windows, poderá configurar sua máquina para o PHP e configurar automaticamente o IIS (o servidor Web interno do Windows) instalando o [SDK do Azure para PHP][SDK do Azure para PHP].

## <span id="create-web-site-and-set-up-git"></span></a>Criar um Site do Azure e configurar a publicação do Git

Siga estas etapas para criar um Site do Azure e um banco de dados MySQL:

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  Clique no ícone **Novo** na parte inferior esquerda do portal.

    ![Criar um novo site do Azure][Criar um novo site do Azure]

3.  Clique em **Site**, em seguida clique em **Criação personalizada**.

    ![Criação personalizada de um novo site][Criação personalizada de um novo site]

    Insira um valor para a **URL**, selecione **Criar Novo Banco de Dados MySQL** a partir da lista suspensa **Base de dados** e selecione o datacenter para seu site na lista suspensa **Região**. Clique na seta na parte inferior da caixa de diálogo.

    ![Preencha os detalhes do site][Preencha os detalhes do site]

4.  Insira um valor para o **Nome** do banco de dados, selecione o data center para seu banco de dados no menu suspenso **Região** e marque a caixa que indica que você concorda com os termos legais. Clique na marca de seleção na parte inferior da caixa de diálogo.

    ![Criar novo banco de dados MySQL][Criar novo banco de dados MySQL]

    Quando o site tiver sido criado, você verá o texto **Criação de site “[NOMEDOSITE]” concluída com êxito**. Agora, você pode habilitar a publicação do Git.

5.  Clique no nome do site exibido na lista de sites para abrir o painel **QuickStart** do site.

    ![Abrir o painel do site][Abrir o painel do site]

6.  Na parte inferior da página **QuickStart**, clique em **Configurar a publicação do Git**.

    ![Configurar a publicação do Git][Configurar a publicação do Git]

7.  Para habilitar a publicação do Git, você deve fornecer um nome de usuário e uma senha. Anote o nome do usuário e a senha que você criou. (Se você tiver configurado um repositório de Git antes, esta etapa será ignorada.)

    ![Criar credenciais de publicação][Criar credenciais de publicação]

    Levará alguns segundos para configurar seu repositório.

8.  Quando o repositório estiver pronto, você verá instruções para enviar os arquivos do aplicativo por push ao repositório. Tome nota destas instruções - elas serão necessárias mais tarde.

    ![Instruções do Git][Instruções do Git]

## Obter informações da conexão MySQL remota

Para conectar-se ao banco de dados MySQL que está em execução no Sites do Azure, você precisará de informações sobre a conexão. Para obter informações sobre a conexão MySQL, siga estas etapas:

1.  No painel do site, clique no link **Exibir cadeias de conexão** no lado direito da página:

    ![Obter informações da conexão do banco de dados][Obter informações da conexão do banco de dados]

2.  Anote os valores de `Database`, `Data Source`, `User Id` e `Password`.

## Criar e testar o aplicativo localmente

Agora que criou um Site do Azure, você pode desenvolver seu aplicativo localmente e implantá-lo após o teste.

O aplicativo Registro é um aplicativo simples do PHP que permite que você se registre em um evento fornecendo seu nome e endereço de email. As informações sobre inscritos anteriores são exibidas em uma tabela. As informações de registro são armazenadas em um banco de dados MySQL. O aplicativo consiste em um arquivo (copie/cole o código disponível abaixo):

-   **index.php**: Exibe um formulário de registro e uma tabela que contém informações de registro.

Para criar e executar o aplicativo localmente, siga as etapas abaixo. Observe que essas etapas presumem que o PHP, a ferramenta de linha de comando do MySQL (parte do MySQL) e um servidor Web estão configurados no seu computador local e que você habilitou a [extensão PDO do MySQL][extensão PDO do MySQL].

1.  Conecte-se ao servidor MySQL remoto usando os valores de `Data Source`, `User Id`, `Password` e `Database` que você recuperou anteriormente:

        mysql -h{Data Source] -u[User Id] -p[Password] -D[Database]

2.  O prompt de comando do MySQL será exibido:

        mysql>

3.  Cole no seguinte comando `CREATE TABLE` para criar a tabela `registration_tbl` em seu banco de dados:

        mysql> CREATE TABLE registration_tbl(id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id), name VARCHAR(30), email VARCHAR(30), date DATE);

4.  No diretório raiz do servidor web, crie uma pasta chamada `registration` e crie um arquivo chamado `index.php`.

5.  Abra o arquivo **index.php** em um editor de texto ou IDE, adicione o código a seguir e conclua as alterações necessárias marcadas com os comentários `//TODO:`.

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
            // DB connection info
            //TODO: Update the values for $host, $user, $pwd, and $db
            //using the values you retrieved earlier from the portal.
            $host = "value of Data Source";
            $user = "value of User Id";
            $pwd = "value of Password";
            $db = "value of Database";
            // Connect to database.
            try {
                $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
                $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
            }
            catch(Exception $e){
                die(var_dump($e));
            }
            // Insert registration info
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
            // Retrieve data
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
        ?>
        </body>
        </html>

Agora você pode navegar para **http://localhost/registration/index.php** para testar o aplicativo.

## Publicar seu aplicativo

Depois de testar o aplicativo localmente, você poderá publicá-lo no Site do Azure usando o Git. Você inicializara seu repositório local do Git e publicará o aplicativo.

> [WACOM.NOTE]
> Estas são as mesmas etapas mostradas no portal no final da seção Criar um Site do Azure e Configurar a publicação do Git acima.

1.  (Opcional) Se você tiver esquecido a URL de seu repositório remoto do Git, navegue até a guia Implantação no portal.

    ![Obter URL do Git][Instruções do Git]

2.  Abra o GitBash (ou um terminal, se o Git estiver em seu `PATH`), altere para o diretório raiz de seu aplicativo e execute os seguintes comandos:

        git init
        git add .
        git commit -m "initial commit"
        git remote add azure [URL for remote repository]
        git push azure master

    Será solicitada a senha que você criou anteriormente.

    ![Envio por push inicial ao Azure via Git][Envio por push inicial ao Azure via Git]

3.  Navegue até **http://[nome do site].azurewebsites.net/index.php** para começar a usar o aplicativo (essas informações serão armazenadas no painel de sua conta):

    ![Site PHP do Azure][Site PHP do Azure]

Depois de ter publicado seu aplicativo, você pode começar a fazer alterações nele e usar o Git para publicá-lo.

## Publicar alterações em seu aplicativo

Para publicar alterações no aplicativo, siga estas etapas:

1.  Faça alterações em seu aplicativo localmente.
2.  Abra o GitBash (ou um terminal, se o Git estiver em seu `PATH`), altere para o diretório raiz de seu aplicativo e execute os seguintes comandos:

        git add .
        git commit -m "comment describing changes"
        git push azure master

    Será solicitada a senha que você criou anteriormente.

    ![Enviando alterações por push ao Site do Azure via Git][Enviando alterações por push ao Site do Azure via Git]

3.  Navegue até **http://[nome do site]azurewebsites.net/index.php** para ver seu aplicativo e todas as alterações feitas:

    ![Site PHP do Azure][Site PHP do Azure]

4.  Você também pode ver a nova implantação na guia 'Implantações' no Portal de Gerenciamento do Azure:

    ![Lista das implantações de site][Lista das implantações de site]

  [PHP]: http://www.php.net/manual/en/install.php
  [Git]: http://git-scm.com/
  [Site PHP do Azure]: ./media/web-sites-php-mysql-deploy-use-git/running_app_2.png
  [SDK do Azure para PHP]: http://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/azurephpsdk.appids
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com
  [Criar um novo site do Azure]: ./media/web-sites-php-mysql-deploy-use-git/new_website.jpg
  [Criação personalizada de um novo site]: ./media/web-sites-php-mysql-deploy-use-git/custom_create.png
  [Preencha os detalhes do site]: ./media/web-sites-php-mysql-deploy-use-git/website_details.jpg
  [Criar novo banco de dados MySQL]: ./media/web-sites-php-mysql-deploy-use-git/new_mysql_db.jpg
  [Abrir o painel do site]: ./media/web-sites-php-mysql-deploy-use-git/go_to_dashboard.png
  [Configurar a publicação do Git]: ./media/web-sites-php-mysql-deploy-use-git/setup_git_publishing.png
  [Criar credenciais de publicação]: ./media/web-sites-php-mysql-deploy-use-git/git-deployment-credentials.png
  [Instruções do Git]: ./media/web-sites-php-mysql-deploy-use-git/git-instructions.png
  [Obter informações da conexão do banco de dados]: ./media/web-sites-php-mysql-deploy-use-git/connection_string_info.png
  [extensão PDO do MySQL]: http://www.php.net/manual/en/ref.pdo-mysql.php
  [Envio por push inicial ao Azure via Git]: ./media/web-sites-php-mysql-deploy-use-git/php-git-initial-push.png
  [Enviando alterações por push ao Site do Azure via Git]: ./media/web-sites-php-mysql-deploy-use-git/php-git-change-push.png
  [Lista das implantações de site]: ./media/web-sites-php-mysql-deploy-use-git/php-deployments-list.png
