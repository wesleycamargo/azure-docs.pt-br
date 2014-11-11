<properties linkid="develop-php-website-with-sql-database-and-webmatrix" urlDisplayName="Web w/ SQL + WebMatrix" pageTitle="PHP website with SQL Database and WebMatrix - Azure" metaKeywords="" description="A tutorial that demonstrates how to use the free WebMatrix IDE to create and deploy a PHP website that stores data in SQL Database." metaCanonical="" services="" documentationCenter="" title="Create and Deploy a PHP Website and SQL Database using WebMatrix" authors="cephalin" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />





# Criar e implantar um Site do PHP e um Banco de Dados SQL usando o WebMatrix

Este tutorial mostra como usar o WebMatrix para desenvolver e implantar um aplicativo PHP que utilize um Banco de Dados SQL Azure para um Site do Azure. O WebMatrix é uma ferramenta gratuita de desenvolvimento da Web da Microsoft que inclui tudo que você precisa para o desenvolvimento de sites. O WebMatrix oferece suporte ao PHP e inclui o IntelliSense para desenvolvimento de PHP.

Este tutorial presume que você tenha o [SQL Server Express][SQL Server Express] instalado em seu computador para que possa testar um aplicativo localmente. No entanto, você pode concluir o tutorial sem ter o SQL Server Express instalado. Em vez disto, você pode implantar seu aplicativo diretamente com os Sites do Azure.

Após a conclusão deste guia, você terá um Banco de Dados SQL do PHP em execução no Azure.

Você aprenderá a:

-   Como criar um Site do Azure e um banco de dados SQL usando o Portal de Gerenciamento. Como, por padrão, o PHP está habilitado no sites do Azure, nada de especial é necessário para executar seu código PHP.
-   Como desenvolver um aplicativo PHP usando o WebMatrix.
-   Como publicar e publicar novamente o aplicativo no Azure usando o WebMatrix.

Seguindo este tutorial, você criará um simples aplicativo Lista de Tarefas em PHP. O aplicativo será hospedado em um Site do Azure. Uma captura de tela do aplicativo em execução está exibida a seguir:

![Site PHP do Azure][Site PHP do Azure]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Pré-requisitos

1.  [Baixe][Baixe] os arquivos do aplicativo Lista de Tarefas. O Lista de Tarefas é um simples aplicativo PHP que permite que você adicione, marque como concluídos e exclua itens de uma lista de tarefas. Itens de lista de tarefas são armazenadas em um Banco de Dados SQL (SQL Server Express para locais de teste). O aplicativo consiste destes arquivos:

-   **index.php**: exibe tarefas e fornece um formulário para adicionar um item à lista.
-   **additem.php**: adiciona um item à lista.
-   **getitems.php**: obtém todos os itens no banco de dados.
-   **markitemcomplete.php**: altera o status de um item para concluir.
-   **deleteitem.php**: exclui um item.
-   **taskmodel.php**: contém funções que adicionam, obtém, atualizam e excluem itens do banco de dados.
-   **createtable.php**: Cria a tabela de banco de dados SQL para o aplicativo. Este arquivo será chamado apenas uma vez.

1.  Crie um Banco de Dados SQL chamado `tasklist`. Você pode fazer isso a partir do comando `sqlcmd` com estes comandos:

        >sqlcmd -S <server name>\sqlexpress -U <user name> -P <password>
        1> create database tasklist
        2> GO

    Esta etapa será necessária somente se você quiser testar o seu aplicativo localmente.

## Criar um site e banco de dados SQL

1.  Logon no [Portal de Gerenciamento][Portal de Gerenciamento].
2.  Clique no ícone **+ Novo** na parte inferior esquerda do portal.

    ![Criar um novo site do Azure][Criar um novo site do Azure]

3.  Clique em **SITE**, em seguida clique em **CRIAÇÃO PERSONALIZADA**.

    ![Criação personalizada de um novo site][Criação personalizada de um novo site]

    Insira um valor para a **URL**, selecione **Criar Novo Banco de Dados SQL** a partir da lista suspensa **BASE DE DADOS** e selecione o datacenter para seu site na lista suspensa **REGIÃO**. Clique na seta na parte inferior da caixa de diálogo.

    ![Preencha os detalhes do site][Preencha os detalhes do site]

4.  Insira um valor para o **NOME** dO banco de dados e selecione **servidor do novo banco de dados do SQL**. Digite um nome de login do servidor e a senha (e confirme a senha).Escolha a região em que o novo servidor de Banco de Dados SQL será criado.

    ![Preencher as configurações do banco de dados SQL][Preencher as configurações do banco de dados SQL]

    Quando o site for criado, você verá o texto **Site '[NOME DO SITE]' criado com êxito**. Em seguida, você deverá obter as informações para conexão do banco de dados.

5.  Clique em **recursos vinculados**, em seguida, o nome do banco de dados.

    ![Recursos Vinculados][Recursos Vinculados]

6.  Clique em **Exibir Cadeias de Conexão**.

    ![Cadeia de conexão][Cadeia de conexão]

Na seção **PHP** da caixa de diálogo resultante, anote os valores de `UID`, `PWD`, `Database` e`$serverName`. Você usará essas informações posteriormente.

## Instalar o WebMatrix

Você pode instalar o WebMatrix a partir do [Portal de Gerenciamento][Portal de Gerenciamento].

1.  Depois de fazer logon, navegue na página Início Rápido do seu site e clique no ícone do WebMatrix na parte inferior da página:

    ![Instalar o WebMatrix][Instalar o WebMatrix]

    Siga os prompts para instalar o WebMatrix.

2.  Depois que o WebMatrix estiver instalado, este tentará abrir o seu site como um projeto WebMatrix. Você pode escolher editar diretamente o seu site ativo ou baixar uma cópia local. Para este tutorial, selecione 'Editar cópia local'.

3.  Ao ser solicitado a baixar seu site, escolha **Sim, instalar a partir da Galeria de Modelos**.

    ![Baixe o site][Baixe o site]

4.  A partir dos modelos disponíveis, escolha **PHP**.

    ![Site a partir de modelo][Site a partir de modelo]

5.  Selecione o modelo **Site Vazio**. Forneça um nome para o site e clique em **PRÓXIMO**.

    ![Fornecer nome para site][Fornecer nome para site]

Seu site será aberto no WebMatrix com alguns arquivos padrão estabelecidos.

## Implantar o seu aplicativo

Nas próximas etapas, você desenvolverá o aplicativo Lista de Tarefas ao adicionar os arquivos baixados anteriormente, fazendo algumas modificações. Você pode, no entanto, adicionar seus arquivos existentes ou criar novos arquivos.

1.  Com seu site aberto no WebMatrix, adicione os arquivos do aplicativo, clicando em **Add Existing**:

    ![WebMatrix - adicionar arquivos existentes][WebMatrix - adicionar arquivos existentes]

    Na caixa de diálogos resultante, navegue para os arquivos que você baixou anteriormente, selecione todos eles e clique em Abrir. Quando solicitado, escolha substituir o arquivo `index.php`.

2.  Em seguida, você deverá adicionar as informações para conexão do seu banco de dados SQL Server local ao arquivo `taskmodel.php`. Abra o arquivo `taskmodel.php` clicando nele duas vezes e atualize as informações para conexão do banco de dados na função `connect`. (**Observação**: vá para [Publicar seu aplicativo][Publicar seu aplicativo] se você não quiser testar seu aplicativo localmente e, em vez disto, quiser publicá-lo diretamente nos Sites do Azure.)

        // DB connection info
        $host = "localhost\sqlexpress";
        $user = "your user name";
        $pwd = "your password";
        $db = "tasklist";

    Salve o arquivo `taskmodel.php`.

3.  Para que o aplicativo seja executado, a tabela `items` deverá ser criada. Clique no arquivo `createtable.php` com o botão direito do mouse e selecione **Iniciar no navegador**. Isto iniciará o `createtable.php` no seu navegador e executará o código que cria a tabela `items` no banco de dados `tasklist`.

    ![WebMatrix - iniciar o createtable.php no navegador][WebMatrix - iniciar o createtable.php no navegador]

4.  Agora você pode testar o aplicativo localmente. Clique no arquivo `index.php` com o botão direito do mouse e selecione **Iniciar no navegador**. Teste o aplicativo ao adicionar, marcar como concluídos e excluir itens.

## <span id="Publish"></span></a>Publicar o aplicativo

Antes de publicar seu aplicativo nos Sites do Azure, as informações para conexão do banco de dados no `taskmodel.php` precisam ser atualizadas com as informações para conexão que você obteve anteriormente (na seção [Criar Site do Azure e Banco de dados SQL][Criar Site do Azure e Banco de dados SQL]).

1.  Abra o arquivo `taskmodel.php` clicando nele duas vezes e atualize as informações para conexão do banco de dados na função `connect`.

        // DB connection info
        $host = "value of $serverName";
        $user = "value of UID";
        $pwd = "the SQL password you created when creating the website";
        $db = "value of Database";

    Salve o arquivo `taskmodel.php`.

2.  Clique em **Publicar** em WebMatrix, então clique em **Continuar** na caixa de diálogo **Publicar Visualização**.

    ![WebMatrix - publicar][WebMatrix - publicar]

3.  Navegue para http://[nome do seu site].azurewebsites.net/createtable.php para criar a tabela `items`.

4.  Por fim, navegue para http://[nome do seu site].azurewebsites.net/index.php para iniciar o aplicativo.

## Modificar e republicar seu aplicativo

Você pode facilmente modificar seu aplicativo editando a cópia local do site que você baixou anteriormente e republicar ou pode editar diretamente em modo Remoto. Aqui você fará uma simples alteração no cabeçalho do arquivo `index.php` e o salvará diretamente no site ativo.

1.  Clique na guia Remoto do seu site no WebMatrix e selecione **Abrir Exibição Remota**. Isto abrirá seu site remoto para edição direta.
     ![WebMatrix - abrir exibição remota][WebMatrix - abrir exibição remota]

2.  Abra o arquivo `index.php` clicando nele duas vezes.
    ![WebMatrix - abrir índice de arquivo][WebMatrix - abrir índice de arquivo]

3.  Altere **Minha Lista de Tarefas Pendentes** para **Minha Lista de Tarefas** nas tags **título** e **h1** e salve o arquivo.

4.  Quando o arquivo estiver salvo, clique no botão Executar para visualizar as alterações no site ativo.
    ![WebMatrix - iniciar o site em modo remoto][WebMatrix - iniciar o site em modo remoto]

## Próximas etapas

Você viu como criar e implantar um site a partir do WebMatrix para o Azure. Para saber mais sobre o WebMatrix, verifique estes recursos:

-   [WebMatrix para Azure][WebMatrix para Azure]

-   [Site do WebMatrix][Site do WebMatrix]

  [SQL Server Express]: http://www.microsoft.com/pt-br/download/details.aspx?id=29062
  [Site PHP do Azure]: ./media/web-sites-php-sql-database-use-webmatrix/tasklist_app_windows.png
  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [Baixe]: http://go.microsoft.com/fwlink/?LinkId=252504
  [Portal de Gerenciamento]: https://manage.windowsazure.com
  [Criar um novo site do Azure]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite1.jpg
  [Criação personalizada de um novo site]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite2.png
  [Preencha os detalhes do site]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite3_SQL.png
  [Preencher as configurações do banco de dados SQL]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite4_SQL.png
  [Recursos Vinculados]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite6_SQL.png
  [Cadeia de conexão]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite7.png
  [Instalar o WebMatrix]: ./media/web-sites-php-sql-database-use-webmatrix/InstallWebMatrix.png
  [Baixe o site]: ./media/web-sites-php-sql-database-use-webmatrix/download-site-1.png
  [Site a partir de modelo]: ./media/web-sites-php-sql-database-use-webmatrix/site-from-template.png
  [Fornecer nome para site]: ./media/web-sites-php-sql-database-use-webmatrix/site-from-template-2.png
  [WebMatrix - adicionar arquivos existentes]: ./media/web-sites-php-sql-database-use-webmatrix/edit_addexisting.png
  [Publicar seu aplicativo]: #Publish
  [WebMatrix - iniciar o createtable.php no navegador]: ./media/web-sites-php-sql-database-use-webmatrix/edit_run.png
  [Criar Site do Azure e Banco de dados SQL]: #CreateWebsite
  [WebMatrix - publicar]: ./media/web-sites-php-sql-database-use-webmatrix/edit_publish.png
  [WebMatrix - abrir exibição remota]: ./media/web-sites-php-sql-database-use-webmatrix/OpenRemoteView.png
  [WebMatrix - abrir índice de arquivo]: ./media/web-sites-php-sql-database-use-webmatrix/Remote_editIndex.png
  [WebMatrix - iniciar o site em modo remoto]: ./media/web-sites-php-sql-database-use-webmatrix/Remote_run.png
  [WebMatrix para Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409
  [Site do WebMatrix]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
