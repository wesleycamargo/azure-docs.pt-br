---
title: Criar um aplicativo Web PHP-MySQL no Serviço de Aplicativo do Azure e implantá-lo usando o Git
description: Um tutorial que demonstra como criar um aplicativo Web PHP que armazena dados no MySQL e como usar implantação Git no Azure.
services: app-service\web
documentationcenter: php
author: rmcmurray
manager: wpickett
editor: ''
tags: mysql

ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 08/11/2016
ms.author: robmcm

---
# Criar um aplicativo Web PHP-MySQL no Serviço de Aplicativo do Azure e implantá-lo usando o Git
Este tutorial mostra como criar um aplicativo Web PHP-MySQL e como implantá-lo no [Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714) usando o Git. Você usará o [PHP][install-php], a Ferramenta de Linha de Comando do MySQL (parte do [MySQL][install-mysql]), e [Git][install-git] instalados em seu computador. As instruções deste tutorial podem ser seguidas em qualquer sistema operacional, incluindo o Windows, o Mac e o Linux. Após a conclusão deste guia, você terá um aplicativo Web PHP/MySQL em execução no Azure.

Você aprenderá:

* Como criar um aplicativo Web e um banco de dados MySQL usando o [Portal do Azure][management-portal]. Já que o PHP está habilitado nos [Aplicativos Web do Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714) por padrão, não é necessário nada de especial para executar seu código PHP.
* Como publicar e publicar novamente o aplicativo no Azure usando o Git.
* Como habilitar a extensão do Compositor para automatizar tarefas do Compositor em cada `git push`.

Seguindo este tutorial, você compilará um aplicativo Web de registro simples em PHP. O aplicativo será hospedado em aplicativos Web. Abaixo, uma captura de tela do aplicativo concluído:

![Site PHP do Azure][running-app]

## Configurar o ambiente de desenvolvimento
O tutorial presume que você usa o [PHP][install-php], a Ferramenta de Linha de Comando do MySQL (parte do [MySQL][install-mysql]), e [Git][install-git] instalados em seu computador.

<a id="create-web-site-and-set-up-git"></a>

## Criar um aplicativo Web e configurar a publicação Git
Siga estas etapas para criar um aplicativo Web e um Banco de Dados MySQL:

1. Faça logon no [Portal do Azure][management-portal].
2. Clique no ícone **Novo**.
3. Clique em **Ver tudo** ao lado de **Marketplace**.
4. Clique em **Web + Móvel** e, em seguida, em **Aplicativo Web + MySQL**. Em seguida, clique em **Criar**.
5. Insira um nome válido para o grupo de recursos.
   
    ![Definir nome do grupo de recursos][resource-group]
6. Insira valores para seu novo aplicativo Web.
   
    ![Criar um aplicativo Web][new-web-app]
7. Insira valores para seu novo banco de dados, incluindo concordar com os termos legais.
   
    ![Criar novo banco de dados MySQL][new-mysql-db]
8. Quando o aplicativo web tiver sido criado, você verá a nova folha do aplicativo web.
9. Em **configurações**, clique em **Implantação Contínua**. Em seguida, clique em *Configurar configurações exigidas*.
   
    ![Configurar a publicação do Git][setup-publishing]
10. Selecione **Repositório Git local** como a fonte.
    
     ![Configurar o repositório Git][setup-repository]
11. Para habilitar a publicação do Git, você deve fornecer um nome de usuário e uma senha. Anote o nome do usuário e a senha que você criou. (Se você tiver configurado um repositório de Git antes, esta etapa será ignorada.)
    
     ![Criar credenciais de publicação][credentials]

## Obter informações da conexão MySQL remota
Para conectar-se ao Banco de Dados MySQL que está em execução nos Aplicativos Web, você precisará das informações da conexão. Para obter informações sobre a conexão MySQL, siga estas etapas:

1. No seu grupo de recursos, verifique o banco de dados:
   
    ![Selecionar um banco de dados][select-database]
2. Em **Configurações** do banco de dados, selecione **Propriedades**.
   
    ![Selecionar propriedades][select-properties]
3. Anote os valores de `Database`, `Host`, `User Id` e `Password`.
   
    ![Anotar propriedades][note-properties]

## Compilar e testar o aplicativo localmente
Agora que criou um aplicativo Web, você pode desenvolvê-lo localmente e então implantá-lo após o teste.

O aplicativo Registro é um aplicativo simples do PHP que permite que você se registre em um evento fornecendo seu nome e endereço de email. As informações sobre inscritos anteriores são exibidas em uma tabela. As informações de registro são armazenadas em um banco de dados MySQL. O aplicativo consiste em um arquivo (copie/cole o código disponível abaixo):

* **index.php**: exibe um formulário de registro e uma tabela contendo informações sobre o inscrito.

Para criar e executar o aplicativo localmente, siga as etapas abaixo. Observe que essas etapas pressupõem que o PHP e a ferramenta de linha de comando do MySQL (parte do MySQL) estão configurados no computador local e que a [extensão PDO para MySQL][pdo-mysql] foi habilitada.

1. Conecte-se ao servidor MySQL remoto usando os valores de `Data Source`, `User Id`, `Password` e `Database` que você recuperou anteriormente:
   
        mysql -h{Data Source] -u[User Id] -p[Password] -D[Database]
2. O prompt de comando do MySQL será exibido:
   
        mysql>
3. Cole no seguinte comando `CREATE TABLE` para criar a tabela `registration_tbl` em seu banco de dados:
   
        CREATE TABLE registration_tbl(id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id), name VARCHAR(30), email VARCHAR(30), date DATE);
4. Na raiz da pasta do aplicativo local, crie o arquivo **index.php**.
5. Abra o arquivo **index.php** em um editor de texto ou IDE, adicione o código a seguir e conclua as alterações necessárias marcadas com os comentários `//TODO:`.

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
            //using the values you retrieved earlier from the Azure Portal.
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

1. Em um terminal, abra a sua pasta do aplicativo e digite o seguinte comando:
   
       php -S localhost:8000

Agora você pode navegar até **http://localhost:8000/** para testar o aplicativo.

## Publicar seu aplicativo
Depois de testar seu aplicativo localmente, você poderá publicá-lo para aplicativos Web usando Git. Você inicializara seu repositório local do Git e publicará o aplicativo.

> [!NOTE]
> Estas são as mesmas etapas mostradas no Portal do Azure no final da seção Criar um aplicativo Web e Configurar a publicação Git, apresentada acima.
> 
> 

1. (Opcional) Se você tiver esquecido a URL de seu repositório Git remoto, navegue até as propriedades do aplicativo Web no Portal do Azure.
2. Abra GitBash (ou um terminal, se o Git estiver em seu `PATH`), altere para o diretório raiz de seu aplicativo e execute os seguintes comandos:
   
        git init
        git add .
        git commit -m "initial commit"
        git remote add azure [URL for remote repository]
        git push azure master
   
    Será solicitada a senha que você criou anteriormente.
   
    ![Envio por push inicial ao Azure via Git][git-initial-push]
3. Navegue até **http://[site nome].azurewebsites.net/index.php** para começar a usar o aplicativo (essas informações serão armazenadas no painel de sua conta):
   
    ![Site PHP do Azure][running-app]

Depois de ter publicado seu aplicativo, você pode começar a fazer alterações nele e usar o Git para publicá-lo.

## Publicar alterações em seu aplicativo
Para publicar alterações em seu aplicativo, siga essas etapas:

1. Faça alterações em seu aplicativo localmente.
2. Abra GitBash (ou um terminal, se o Git estiver em seu `PATH`), altere os diretórios para o diretório raiz de seu aplicativo e execute os seguintes comandos:
   
        git add .
        git commit -m "comment describing changes"
        git push azure master
   
    Será solicitada a senha que você criou anteriormente.
   
    ![Enviando alterações por push ao Site do Azure via Git][git-change-push]
3. Navegue até **http://[site nome].azurewebsites.net/index.php** para ver seu aplicativo e todas as alterações feitas:
   
    ![Site PHP do Azure][running-app]

> [!NOTE]
> Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.
> 
> 

<a name="composer"></a>

## Habilitar a automação do Compositor com a extensão do Compositor
Por padrão, o processo de implantação do git no Serviço de Aplicativo não faz nada com o composer.json, se você tiver um em seu projeto PHP. Você pode habilitar o processamento composer.json durante `git push` habilitando a Extensão do compositor.

1. Na folha do aplicativo Web PHP no [Portal do Azure][management-portal], clique em **Ferramentas** > **Extensões**.
   
    ![Configurações de extensão do Composer][composer-extension-settings]
2. Clique em **Adicionar** e em **Criador**.
   
    ![Suplemento da extensão do Composer][composer-extension-add]
3. Clique em **OK** para aceitar os termos legais. Clique em **OK** novamente para adicionar a extensão.
   
    Agora, a folha **Extensões instaladas** mostrará a Extensão do criador. ![Visualização da extensão do Composer][composer-extension-view]
4. Agora, execute `git add`, `git commit` e `git push` como na seção anterior. Agora, você verá que o Compositor está instalando dependências definidas no composer.json.
   
    ![Êxito da extensão do Composer][composer-extension-success]

## Próximas etapas
Para obter mais informações, consulte o [Centro de Desenvolvimento PHP](/develop/php/).

<!-- URL List -->

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/download/details.aspx?id=20098
[install-git]: http://git-scm.com/
[install-mysql]: http://dev.mysql.com/downloads/mysql/
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[management-portal]: https://portal.azure.com
[sql-database-editions]: http://msdn.microsoft.com/library/windowsazure/ee621788.aspx

<!-- IMG List -->

[running-app]: ./media/web-sites-php-mysql-deploy-use-git/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-git/new_website2.png
[custom-create]: ./media/web-sites-php-mysql-deploy-use-git/create_web_mysql.png
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-git/create_db.png
[go-to-webapp]: ./media/web-sites-php-mysql-deploy-use-git/select_webapp.png
[setup-git-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-mysql-deploy-use-git/save_credentials.png
[resource-group]: ./media/web-sites-php-mysql-deploy-use-git/set_group.png
[new-web-app]: ./media/web-sites-php-mysql-deploy-use-git/create_wa.png
[setup-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_deploy.png
[setup-repository]: ./media/web-sites-php-mysql-deploy-use-git/select_local_git.png
[select-database]: ./media/web-sites-php-mysql-deploy-use-git/select_database.png
[select-properties]: ./media/web-sites-php-mysql-deploy-use-git/select_properties.png
[note-properties]: ./media/web-sites-php-mysql-deploy-use-git/note-properties.png

[git-instructions]: ./media/web-sites-php-mysql-deploy-use-git/git-instructions.png
[git-change-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-change-push.png
[git-initial-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-initial-push.png

[composer-extension-settings]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-settings.png
[composer-extension-add]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-add.png
[composer-extension-view]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-view.png
[composer-extension-success]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-success.png

<!---HONumber=AcomDC_0817_2016-->