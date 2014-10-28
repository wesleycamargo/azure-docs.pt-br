# Como criar um banco de dados MySQL no Azure

Este guia mostrará como usar o [ClearDB][ClearDB] para criar um banco de dados MySQL na [Azure Store][Azure Store] e como criar um banco de dados MySQL como um recurso vinculado, quando você cria um [Site do Azure][Site do Azure]. O [ClearDB][ClearDB] é um provedor de banco de dados como serviço tolerante a falhas que permite que você execute e gerencie bancos de dados MySQL em datacenters do Azure e conecte-os de qualquer aplicativo.

## Sumário

-   [Como: criar um banco de dados MySQL na Azure Store][Como: criar um banco de dados MySQL na Azure Store]
-   [Como: criar um banco de dados MySQL como um recurso vinculado para o Site do Azure][Como: criar um banco de dados MySQL como um recurso vinculado para o Site do Azure]

<div class="dev-callout"> 
<b>Observa&ccedil;&atilde;o</b> 
<p>Quando voc&ecirc; cria um banco de dados MySQL como parte do processo de cria&ccedil;&atilde;o de Sites, voc&ecirc; s&oacute; pode criar um banco de dados gratuito. Criar um banco de dados MySQL na Azure Store permite que voc&ecirc; crie um banco de dados gratuito ou escolha uma das op&ccedil;&otilde;es pagas.</p> 
</div>

## <span id="CreateFromStore"></span></a>Como: criar um banco de dados MySQL na Azure Store

Para criar um banco de dados MySQL na [Azure Store][Azure Store], faça o seguinte:

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  Clique em **+NOVO** na parte inferior da página e selecione **STORE**.

    ![Selecione o complemento na store][Selecione o complemento na store]

3.  Selecione **Banco de Dados ClearDB MySQL** e, em seguida, clique na seta na parte inferior do quadro.

    ![Selecione o banco de dados ClearDB MySQL][Selecione o banco de dados ClearDB MySQL]

4.  Selecione um plano, digite um nome de banco de dados e selecione uma região. Em seguida, clique na seta na parte inferior do quadro.

    ![Compre o banco de dados MySQL na store][Compre o banco de dados MySQL na store]

5.  Clique na marca de seleção para concluir a compra.

    ![Revise e conclua a compra][Revise e conclua a compra]

6.  Depois que o banco de dados tiver sido criado, você pode gerenciá-lo na guia **COMPLEMENTOS** no portal de gerenciamento.

    ![Gerencie o banco de dados MySQL no portal do Azure][Gerencie o banco de dados MySQL no portal do Azure]

7.  Você pode obter as informações de conexão do banco de dados clicando em **INFORMAÇÕES DE CONEXÃO** na parte inferior da página (mostrada acima).

    ![Informações de conexão do MySql][Informações de conexão do MySql]

## <span id="CreateForWebSite"></span></a>Como: criar um banco de dados MySQL como um recurso vinculado para o Site do Azure

Para criar um banco de dados MySQL como um recurso vinculado ao criar um [Site do Azure][Site do Azure], faça o seguinte:

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  Clique em **+NOVO** na parte inferior da página, selecione **COMPUTAÇÃO**, **SITE** e **CRIAR COM BANCO DE DADOS**.

    ![Criar site com banco de dados][Criar site com banco de dados]

3.  Forneça um **URL** para o seu site, selecione a **REGIÃO** para seu site e escolha **Criar um novo banco de dados MySQL** na lista suspensa **BANCO DE DADOS**. Opcionalmente, você pode substituir o nome padrão pela cadeia de conexão. Clique na seta na parte inferior da página.

    ![Forneça detalhes do site][Forneça detalhes do site]

4.  Forneça um **NOME** de banco de dados, selecione a **REGIÃO** do banco de dados (esta deve ser a mesma região do seu site), concorde com os termos legais do ClearDB e, em seguida, clique em marca de seleção na parte inferior do quadro.

    ![Forneça detalhes do MySQL][Forneça detalhes do MySQL]

5.  Depois de criar seu site, clique no nome do seu site para ir para o painel do site.

    ![Vá para o painel do site][Vá para o painel do site]

6.  Clique em **CONFIGURAR**.

    ![Vá para a guia configurar][Vá para a guia configurar]

7.  Role para baixo até a seção **cadeias de conexão** e clique em **Mostrar cadeias de conexão**

    ![Mostrar cadeia de conexão][Mostrar cadeia de conexão]

8.  Copie a cadeia de conexão para usar em seu aplicativo.

    ![Cadeia de conexão exibida][Cadeia de conexão exibida]

> [WACOM.NOTE] As cadeias de conexão são acessíveis para o aplicativo do seu site pelo nome da cadeia de conexão. Em aplicativos .NET, cadeias de conexão estão disponíveis no objeto **connectionStrings**. Em outras linguagens de programação, cadeias de conexão são acessíveis como variáveis de ambiente. Para obter mais informações, consulte [Como configurar sites][Como configurar sites].

  [ClearDB]: http://www.cleardb.com/
  [Azure Store]: /pt-br/gallery/store/
  [Site do Azure]: /pt-br/documentation/services/web-sites/
  [Como: criar um banco de dados MySQL na Azure Store]: #CreateFromStore
  [Como: criar um banco de dados MySQL como um recurso vinculado para o Site do Azure]: #CreateForWebSite
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [Selecione o complemento na store]: ./media/create-mysql-db/select-store.png
  [Selecione o banco de dados ClearDB MySQL]: ./media/create-mysql-db/select-cleardb-mysql.png
  [Compre o banco de dados MySQL na store]: ./media/create-mysql-db/purchase-mysql.png
  [Revise e conclua a compra]: ./media/create-mysql-db/complete-mysql-purchase.png
  [Gerencie o banco de dados MySQL no portal do Azure]: ./media/create-mysql-db/manage-mysql-add-on.png
  [Informações de conexão do MySql]: ./media/create-mysql-db/mysql-conn-info.png
  [Criar site com banco de dados]: ./media/create-mysql-db/custom_create.png
  [Forneça detalhes do site]: ./media/create-mysql-db/provide-website-details.png
  [Forneça detalhes do MySQL]: ./media/create-mysql-db/provide-mysql-details.png
  [Vá para o painel do site]: ./media/create-mysql-db/go-to-website-dashboard.png
  [Vá para a guia configurar]: ./media/create-mysql-db/go-to-configure-tab.png
  [Mostrar cadeia de conexão]: ./media/create-mysql-db/show-conn-string.png
  [Cadeia de conexão exibida]: ./media/create-mysql-db/shown-conn-string.png
  [Como configurar sites]: ../web-sites-configure/
