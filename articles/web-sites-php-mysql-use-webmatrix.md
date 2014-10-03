<properties linkid="develop-php-website-with-mysql-and-webmatrix" urlDisplayName="Web w/ WebMatrix" pageTitle="PHP website with MySQL and WebMatrix - Azure tutorial" metaKeywords="" description="A tutorial that demonstrates how to use the free WebMatrix IDE to create and deploy a PHP website that stores data in MySQL." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Create and deploy a PHP-MySQL Azure Website using WebMatrix" authors="" solutions="" manager="" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# Criar e implantar um Site do Azure PHP-MySQL usando o WebMatrix

Este tutorial mostra como usar o WebMatrix para desenvolver e implantar um aplicativo PHP-MySQL para um Site do Azure. O WebMatrix é uma ferramenta gratuita de desenvolvimento da Web da Microsoft que inclui tudo que você precisa para o desenvolvimento de sites. O WebMatrix oferece suporte ao PHP e inclui o IntelliSense para desenvolvimento de PHP.

Este tutorial presume que você tenha o [MySQL][] instalado em seu computador para que possa testar um aplicativo localmente. No entanto, você pode concluir o tutorial sem ter o MySQL instalado. Em vez disto, você pode implantar seu aplicativo diretamente com os Sites do Azure.

Após a conclusão deste guia, você terá um site do PHP-MySQL em execução no Azure.

Você aprenderá a:

-   Como criar um Site do Azure e um banco de dados MySQL usando o Portal de Gerenciamento. Como, por padrão, o PHP está habilitado no sites do Azure, nada de especial é necessário para executar seu código PHP.
-   Como desenvolver um aplicativo PHP usando o WebMatrix.
-   Como publicar e publicar novamente o aplicativo no Azure usando o WebMatrix.

Seguindo este tutorial, você criará um simples aplicativo Lista de Tarefas em PHP. O aplicativo será hospedado em um Site do Azure. Uma captura de tela do aplicativo em execução está exibida a seguir:

![Site PHP do Azure][]

[WACOM.INCLUDE [create-account-and-websites-note][]]

## Pré-requisitos

1.  [Baixe][] os arquivos do aplicativo Lista de Tarefas. O Lista de Tarefas é um simples aplicativo PHP que permite que você adicione, marque como concluídos e exclua itens de uma lista de tarefas. Os itens da Lista de Tarefas são armazenados em um banco de dados MySQL. O aplicativo consiste destes arquivos:

    -   **additem.php**: adiciona um item à lista.
    -   **createtable.php**: cria a tabela MySQL para o aplicativo. Este arquivo será chamado apenas uma vez.
    -   **deleteitem.php**: exclui um item.
    -   **getitems.php**: obtém todos os itens no banco de dados.
    -   **index.php**: exibe tarefas e fornece um formulário para adicionar um item à lista.
    -   **markitemcomplete.php**: altera o status de um item para concluir.
    -   **taskmodel.php**: contém funções que adicionam, obtém, atualizam e excluem itens do banco de dados.

2.  Crie um banco de dados MySQL local chamado `tasklist`. Isto pode ser feito tanto a partir do espaço de trabalho do Banco de Dados no WebMatrix (depois de ser instalado abaixo no tutorial), como a partir do prompt de comando MySQL, com o seguinte comando:

        mysql> create database tasklist;

    Esta etapa será necessária somente se você quiser testar o seu aplicativo localmente.

## <span id="CreateWebsite"></span></a>Criar um Site do Azure e banco de dados do MySQL

1.  Logon no [Portal de Gerenciamento][].
2.  Clique no ícone **+ Novo** na parte inferior esquerda do portal.

    ![Criar um novo site do Azure][]

3.  Clique em **SITE**, em seguida clique em **CRIAÇÃO PERSONALIZADA**.

    ![Criação personalizada de um novo site][]

    > [WACOM.NOTE]
    > Não é possível criar um Banco de Dados MySQL para um site depois de criá-lo. Você deve criar um site e um banco de dados MySQL conforme descrito nas etapas abaixo.

4.  Insira um valor para a **URL**, selecione **Criar Novo Banco de Dados MySQL** a partir da lista suspensa **BASE DE DADOS** e selecione o datacenter para seu site na lista suspensa **REGIÃO**. Clique na seta na parte inferior da caixa de diálogo.

    ![Preencha os detalhes do site][]

5.  Insira um valor para o **Nome** do banco de dados, selecione o datacenter para seu banco de dados no menu suspenso **Região** e marque a caixa que indica que você concorda com os termos legais. Clique na marca de seleção na parte inferior da caixa de diálogo.

    ![Criar novo banco de dados MySQL][]

    Quando o site for criado, você verá o texto **Site '[NOME DO SITE]' criado com êxito**.

    Em seguida, você deverá obter as informações para conexão do MySQL.

6.  Clique no nome do site exibido na lista de sites para abrir a página Início Rápido.

    ![Abrir o painel do site][]

7.  Clique na guia **CONFIGURAR**:

    ![Guia configurar][]

8.  Role para baixo até a seção **cadeias de conexão**. Os valores para `Database`, `Data Source`, `User Id` e `Password` são (respectivamente) o nome do banco de dados, nome do servidor e senha do usuário. Anote as informações para conexão do banco de dados, pois estas serão necessárias posteriormente.

    ![Cadeia de conexão][]

## Instale o WebMatrix e desenvolva seu aplicativo

Você pode instalar o WebMatrix a partir do [Portal de Gerenciamento][].

1.  Depois de fazer logon, navegue na página Início Rápido do seu site e clique no ícone do WebMatrix na parte inferior da página:

    ![Instalar o WebMatrix][]

    Siga os prompts para instalar o WebMatrix.

2.  Depois que o WebMatrix estiver instalado, este tentará abrir o seu site como um projeto WebMatrix. Você pode escolher editar diretamente o seu site ativo ou baixar uma cópia local. Para este tutorial, selecione 'Editar cópia local'.

3.  Ao ser solicitado a baixar seu site, escolha **Sim, instalar a partir da Galeria de Modelos**.

    ![Baixe o site][]

4.  A partir dos modelos disponíveis, escolha **PHP**.

    ![Site a partir de modelo][]

5.  Selecione o modelo **Site Vazio**. Forneça um nome para o site e clique em **PRÓXIMO**.

    ![Fornecer nome para site][]

    Seu site será aberto no WebMatrix com alguns arquivos padrão estabelecidos.

    Nas próximas etapas, você desenvolverá o aplicativo Lista de Tarefas ao adicionar os arquivos baixados anteriormente, fazendo algumas modificações. Você pode, no entanto, adicionar seus arquivos existentes ou criar novos arquivos.

6.  Adicione seus arquivos de aplicativo ao clicar em **Adicionar Existente**:

    ![WebMatrix - adicionar arquivos existentes][]

    Na caixa de diálogos resultante, navegue para os arquivos que você baixou anteriormente, selecione todos eles e clique em Abrir. Quando solicitado, escolha substituir o arquivo `index.php`.

7.  Em seguida, você deverá adicionar as informações para conexão do seu banco de dados MySQL local ao arquivo `taskmodel.php`. Abra o arquivo `taskmodel.php` clicando nele duas vezes e atualize as informações para conexão do banco de dados na função `connect`. (**Observação**: vá para [Publicar seu aplicativo][] se você não quiser testar seu aplicativo localmente e, em vez disto, quiser publicá-lo diretamente nos Sites do Azure.)

        // DB connection info
        $host = "localhost";
        $user = "your user name";
        $pwd = "your password";
        $db = "tasklist";

    Salve o arquivo `taskmodel.php`.

8.  Para que o aplicativo seja executado, a tabela `items` deverá ser criada. Clique no arquivo `createtable.php` com o botão direito do mouse e selecione **Iniciar no navegador**. Isto iniciará o `createtable.php` no seu navegador e executará o código que cria a tabela `items` no banco de dados `tasklist`.

    ![WebMatrix - iniciar o createtable.php no navegador][]

9.  Agora você pode testar o aplicativo localmente. Clique no arquivo `index.php` com o botão direito do mouse e selecione **Iniciar no navegador**. Teste o aplicativo ao adicionar, marcar como concluídos e excluir itens.

## <span id="Publish"></span></a>Publicar o aplicativo

Antes de publicar seu aplicativo nos Sites do Azure, as informações para conexão do banco de dados no `taskmodel.php` precisam ser atualizadas com as informações para conexão que você obteve anteriormente (na seção [Criar Site do Azure e Banco de dados MySQL][]).

1.  Abra o arquivo `taskmodel.php` clicando nele duas vezes e atualize as informações para conexão do banco de dados na função `connect`.

        // DB connection info
        $host = "value of Data Source";
        $user = "value of User Id";
        $pwd = "value of Password";
        $db = "value of Database";

    Salve o arquivo `taskmodel.php`.

2.  Clique em **Publicar** em WebMatrix, então clique em **Continuar** na caixa de diálogo **Publicar Visualização**.

    ![WebMatrix - publicar][]

3.  Navegue para [http://[nome do][] seu site].azurewebsites.net/createtable.php para criar a tabela `items`.

4.  Por fim, navegue para [http://[nome do][] seu site].azurewebsites.net/index.php para usar o aplicativo.

## Modificar e republicar seu aplicativo

Você pode facilmente modificar seu aplicativo editando a cópia local do site que você baixou anteriormente e republicar ou pode editar diretamente em modo Remoto. Aqui você fará uma simples alteração no cabeçalho do arquivo `index.php` e o salvará diretamente no site ativo.

1.  Clique na guia Remoto do seu site no WebMatrix e selecione **Abrir Exibição Remota**. Isto abrirá seu site remoto para edição direta.
     ![WebMatrix - abrir exibição remota][]

2.  Abra o arquivo `index.php` clicando nele duas vezes.
    ![WebMatrix - abrir índice de arquivo][]

3.  Altere **Minha Lista de Tarefas Pendentes** para **Minha Lista de Tarefas** nas tags **título** e **h1** e salve o arquivo.

4.  Quando o arquivo estiver salvo, clique no botão Executar para visualizar as alterações no site ativo.
    ![WebMatrix - iniciar o site em modo remoto][]

# Próximas etapas

Você viu como criar e implantar um site a partir do WebMatrix para o Azure. Para saber mais sobre o WebMatrix, verifique estes recursos:

-   [WebMatrix para Azure][]

-   [Site do WebMatrix][]

  [MySQL]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
  [Site PHP do Azure]: ./media/web-sites-php-mysql-use-webmatrix/tasklist_app_windows.png
  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [Baixe]: http://go.microsoft.com/fwlink/?LinkId=252506
  [Portal de Gerenciamento]: https://manage.windowsazure.com
  [Criar um novo site do Azure]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite1.jpg
  [Criação personalizada de um novo site]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite2.png
  [Preencha os detalhes do site]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite3.png
  [Criar novo banco de dados MySQL]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite4.png
  [Abrir o painel do site]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite5.png
  [Guia configurar]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite6.png
  [Cadeia de conexão]: ./media/web-sites-php-mysql-use-webmatrix/ConnectionString.png
  [Instalar o WebMatrix]: ./media/web-sites-php-mysql-use-webmatrix/InstallWebMatrix.png
  [Baixe o site]: ./media/web-sites-php-mysql-use-webmatrix/download-site-1.png
  [Site a partir de modelo]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template.png
  [Fornecer nome para site]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template-2.png
  [WebMatrix - adicionar arquivos existentes]: ./media/web-sites-php-mysql-use-webmatrix/edit_addexisting.png
  [Publicar seu aplicativo]: #Publish
  [WebMatrix - iniciar o createtable.php no navegador]: ./media/web-sites-php-mysql-use-webmatrix/edit_run.png
  [Criar Site do Azure e Banco de dados MySQL]: #CreateWebsite
  [WebMatrix - publicar]: ./media/web-sites-php-mysql-use-webmatrix/edit_publish.png
  [http://[nome do]: http://[your
  [WebMatrix - abrir exibição remota]: ./media/web-sites-php-mysql-use-webmatrix/OpenRemoteView.png
  [WebMatrix - abrir índice de arquivo]: ./media/web-sites-php-mysql-use-webmatrix/Remote_editIndex.png
  [WebMatrix - iniciar o site em modo remoto]: ./media/web-sites-php-mysql-use-webmatrix/Remote_run.png
  [WebMatrix para Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409
  [Site do WebMatrix]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
