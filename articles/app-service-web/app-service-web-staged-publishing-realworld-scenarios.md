---
title: "Usar ambientes de DevOps com eficiência para seu aplicativo Web | Microsoft Docs"
description: "Saiba como usar os slots de implantação para configurar e gerenciar vários ambientes de desenvolvimento do aplicativo"
services: app-service\web
documentationcenter: 
author: sunbuild
manager: yochayk
editor: 
ms.assetid: 16a594dc-61f5-4984-b5ca-9d5abc39fb1e
ms.service: app-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 10/24/2016
ms.author: sumuth
translationtype: Human Translation
ms.sourcegitcommit: 385eb87ec32f5f605b28cc8c76b1c89c7e90bfec
ms.openlocfilehash: 5284022ea473db893800b0f64b5bf4f811d994aa
ms.lasthandoff: 02/11/2017


---
# <a name="use-devops-environments-effectively-for-your-web-apps"></a>Usar ambientes de Operações de Desenvolvimento com eficiência em seus aplicativos Web
Este artigo mostra como configurar e gerenciar implantações de aplicativo Web quando houver várias versões de seu aplicativo em vários ambientes, como desenvolvimento, preparo, QA (controle de qualidade) e produção. Cada versão do aplicativo pode ser considerada um ambiente de desenvolvimento para a finalidade específica de seu processo de implantação. Por exemplo os desenvolvedores podem usar o ambiente de QA para testar a qualidade do aplicativo antes de aplicar as alterações em produção.
Ter vários ambientes de desenvolvimento pode ser algo desafiador, pois você precisa controlar o código, gerenciar os recursos (computação, aplicativo Web, banco de dados, cache, etc.) e implantar o código nos ambientes.

## <a name="set-up-a-non-production-environment-stage-dev-qa"></a>Configurar um ambiente de não produção (estágio, desenvolvimento, controle de qualidade)
Quando um aplicativo Web de produção estiver em funcionamento, a próxima etapa será criar um ambiente de não produção. Para usar slots de implantação, verifique se você está executando no modo do plano do Serviço de Aplicativo do Azure Standard ou Premium. Os slots de implantação são aplicativos Web dinâmicos com seus próprios nomes de host. Os elementos de configuração e conteúdo de aplicativo Web podem ser permutados entre dois slots de implantação, incluindo o slot de produção. Quando você implanta seu aplicativo em um slot de implantação, obtém os seguintes benefícios:

- É possível validar as alterações no aplicativo Web em um slot de implantação de preparo antes de trocá-lo pelo slot de produção.
- Quando você implanta primeiro um aplicativo Web em um slot e depois trocá-o para produção, todas as instâncias do slot são preparadas antes dessa troca. Esse processo elimina o tempo de inatividade quando você for implantar seu aplicativo Web. O redirecionamento do tráfego é contínuo e nenhuma solicitação é descartada devido a operações de permuta. Para automatizar todo esse fluxo de trabalho, configure a [Troca Automática](web-sites-staged-publishing.md#configure-auto-swap) quando a validação pré-troca não for necessária.
- Após uma troca, o slot com o aplicativo Web preparado anteriormente terá o aplicativo Web de produção anterior. Se as modificações alternadas no slot de produção não forem o que você esperava, é possível fazer a mesma alternação imediatamente para ter o “último aplicativo Web bom” de volta.

Para configurar um slot de implantação de preparo, confira [Configurar ambientes de preparo para aplicativos Web no Serviço de Aplicativo do Azure](web-sites-staged-publishing.md). Cada ambiente deve incluir seu próprio conjunto de recursos. Por exemplo, se o seu aplicativo Web usar um banco de dados, os aplicativos Web de preparo e produção deverão usar bancos de dados diferentes. Adicione recursos de ambiente de desenvolvimento de preparo, como banco de dados, armazenamento ou cache, para definir o ambiente de desenvolvimento de preparo.

## <a name="examples-of-using-multiple-development-environments"></a>Exemplos de uso de vários ambientes de desenvolvimento
Qualquer projeto deve seguir o gerenciamento de código fonte com pelo menos dois ambientes: desenvolvimento e produção. Se você usar CMSs (sistemas de gerenciamento de conteúdo), estruturas de aplicativo etc., talvez o aplicativo não ofereça suporte a esse cenário sem personalização. Essa eventualidade é real para algumas das estruturas populares discutidas nas seções a seguir. Muitas perguntas vêm à tona ao trabalhar com CMS/estruturas, como:

- Como dividir o conteúdo em ambientes diferentes?
- Quais arquivos você pode alterar sem afetar as atualizações de versão da estrutura?
- Como gerenciar as configurações por ambiente?
- Como você gerencia atualizações de versão para módulos, plug-ins e estrutura principal?

Há várias maneiras de configurar vários ambientes para seu projeto. Os exemplos a seguir mostram um método para cada aplicativo respectivo.

### <a name="wordpress"></a>WordPress
Nesta seção, você aprenderá como configurar um fluxo de trabalho de implantação usando slots para WordPress. O WordPress, como a maioria das soluções de CMS, não dá suporte a vários ambientes de desenvolvimento sem personalização. O recurso Aplicativos Web do Serviço de Aplicativo do Azure tem alguns recursos que facilitam o armazenamento de definições de configuração fora de seu código.

1. Antes de criar um slot de preparo, configure o código do aplicativo para dar suporte a vários ambientes. Para dar suporte a vários ambientes no WordPress, você precisa editar `wp-config.php` em seu aplicativo Web de desenvolvimento local e adicionar o código a seguir no início do arquivo. Esse processo permitirá que seu aplicativo obtenha a configuração correta com base no ambiente selecionado.

    ```
    // Support multiple environments
    // set the config file based on current environment
    if (strpos($_SERVER['HTTP_HOST'],'localhost') !== false) {
    // local development
     $config_file = 'config/wp-config.local.php';
    }
    elseif ((strpos(getenv('WP_ENV'),'stage') !== false) || (strpos(getenv('WP_ENV'),'prod' )!== false ))
    //single file for all azure development environments
     $config_file = 'config/wp-config.azure.php';
    }
    $path = dirname(__FILE__). '/';
    if (file_exists($path. $config_file)) {
    // include the config file if it exists, otherwise WP is going to fail
    require_once $path. $config_file;
    ```

2. Crie uma pasta na raiz do aplicativo Web chamada `config` e adicione os arquivos `wp-config.azure.php` e `wp-config.local.php`, que representam o ambiente do Azure e o ambiente local respectivamente.

3. Copie o seguinte em `wp-config.local.php`:

    ```
    <?php
    // MySQL settings
    /** The name of the database for WordPress */

    define('DB_NAME', 'yourdatabasename');

    /** MySQL database username */
    define('DB_USER', 'yourdbuser');

    /** MySQL database password */
    define('DB_PASSWORD', 'yourpassword');

    /** MySQL hostname */
    define('DB_HOST', 'localhost');
    /**
     * For developers: WordPress debugging mode.
     * * Change this to true to enable the display of notices during development.
     * It is strongly recommended that plugin and theme developers use WP_DEBUG
     * in their development environments.
     */
    define('WP_DEBUG', true);

    //Security key settings
    define('AUTH_KEY', 'put your unique phrase here');
    define('SECURE_AUTH_KEY','put your unique phrase here');
    define('LOGGED_IN_KEY','put your unique phrase here');
    define('NONCE_KEY', 'put your unique phrase here');
    define('AUTH_SALT', 'put your unique phrase here');
    define('SECURE_AUTH_SALT', 'put your unique phrase here');
    define('LOGGED_IN_SALT', 'put your unique phrase here');
    define('NONCE_SALT', 'put your unique phrase here');

    /**
     * WordPress Database Table prefix.
     *
     * You can have multiple installations in one database if you give each a unique
     * prefix. Only numbers, letters, and underscores please!
     */
    $table_prefix = 'wp_';
    ```

    A definição das chaves de segurança, conforme ilustrado acima, pode ajudar a impedir que seu aplicativo Web seja invadido; portanto, use valores exclusivos. Se você precisar gerar a cadeia de caracteres para as chaves de segurança mencionadas no código, [acesse o gerador automático](https://api.wordpress.org/secret-key/1.1/salt) para criar novos pares de chave/valor.

4. Copie o seguinte código em `wp-config.azure.php`:

    ```    
    <?php
    // MySQL settings
    /** The name of the database for WordPress */

    define('DB_NAME', getenv('DB_NAME'));

    /** MySQL database username */
    define('DB_USER', getenv('DB_USER'));

    /** MySQL database password */
    define('DB_PASSWORD', getenv('DB_PASSWORD'));

    /** MySQL hostname */
    define('DB_HOST', getenv('DB_HOST'));

    /**
    * For developers: WordPress debugging mode.
    *
    * Change this to true to enable the display of notices during development.
    * It is strongly recommended that plugin and theme developers use WP_DEBUG
    * in their development environments.
    * Turn on debug logging to investigate issues without displaying to end user. For WP_DEBUG_LOG to
    * do anything, WP_DEBUG must be enabled (true). WP_DEBUG_DISPLAY should be used in conjunction
    * with WP_DEBUG_LOG so that errors are not displayed on the page */

    */
    define('WP_DEBUG', getenv('WP_DEBUG'));
    define('WP_DEBUG_LOG', getenv('TURN_ON_DEBUG_LOG'));
    define('WP_DEBUG_DISPLAY',false);

    //Security key settings
    /** If you need to generate the string for security keys mentioned above, you can go the automatic generator to create new keys/values: https://api.wordpress.org/secret-key/1.1/salt **/
    define('AUTH_KEY',getenv('DB_AUTH_KEY'));
    define('SECURE_AUTH_KEY', getenv('DB_SECURE_AUTH_KEY'));
    define('LOGGED_IN_KEY', getenv('DB_LOGGED_IN_KEY'));
    define('NONCE_KEY', getenv('DB_NONCE_KEY'));
    define('AUTH_SALT', getenv('DB_AUTH_SALT'));
    define('SECURE_AUTH_SALT', getenv('DB_SECURE_AUTH_SALT'));
    define('LOGGED_IN_SALT',  getenv('DB_LOGGED_IN_SALT'));
    define('NONCE_SALT',  getenv('DB_NONCE_SALT'));

    /**
    * WordPress Database Table prefix.
    *
    * You can have multiple installations in one database if you give each a unique
    * prefix. Only numbers, letters, and underscores please!
    */
    $table_prefix = getenv('DB_PREFIX');
    ```

#### <a name="use-relative-paths"></a>Use caminhos relativos
Por fim, configure caminhos relativos no aplicativo WordPress. O WordPress armazena informações de URL no banco de dados. Esse armazenamento dificulta mais a movimentação de conteúdo de um ambiente para outro. Você precisa atualizar o banco de dados sempre que mudar do ambiente local para o de preparo ou do ambiente de preparo para de produção. Para reduzir o risco de problemas que podem ocorrer com a implantação de banco de dados sempre que você implantar de um ambiente para outro, use o [plug-in de links Relative Root](https://wordpress.org/plugins/root-relative-urls/) que pode ser instalado usando o painel de administrador do WordPress.

Adicione as seguintes entradas ao arquivo `wp-config.php` antes do comentário `That's all, stop editing!`:

```

  define('WP_HOME', 'http://'. filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
    define('WP_SITEURL', 'http://'. filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
    define('WP_CONTENT_URL', '/wp-content');
    define('DOMAIN_CURRENT_SITE', filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
```

Ative o plug-in no menu `Plugins` no painel de administrador do WordPress. Salve as configurações de link permanente para o aplicativo WordPress.

#### <a name="the-final-wp-configphp-file"></a>O último arquivo `wp-config.php`
As atualizações principais do WordPress não afetarão seus arquivos `wp-config.php`, `wp-config.azure.php` e `wp-config.local.php`. Aqui está uma versão final do arquivo `wp-config.php`:

```
<?php
/**
 * The base configurations of the WordPress.
 *
 * This file has the following configurations: MySQL settings, Table Prefix,
 * Secret Keys, and ABSPATH. You can find more information by visiting
 *
 * Codex page. You can get the MySQL settings from your web host.
 *
 * This file is used by the wp-config.php creation script during the
 * installation. You don't have to use the web web app, you can just copy this file
 * to "wp-config.php" and fill in the values.
 *
 * @package WordPress
 */

// Support multiple environments
// set the config file based on current environment
if (strpos($_SERVER['HTTP_HOST'],'localhost') !== false) { // local development
  $config_file = 'config/wp-config.local.php';
}
elseif ((strpos(getenv('WP_ENV'),'stage') !== false) ||(strpos(getenv('WP_ENV'),'prod' )!== false )){
  $config_file = 'config/wp-config.azure.php';
}


$path = dirname(__FILE__). '/';
if (file_exists($path. $config_file)) {
  // include the config file if it exists, otherwise WP is going to fail
  require_once $path. $config_file;
}

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');


/* That's all, stop editing! Happy blogging. */

define('WP_HOME', 'http://'. $_SERVER['HTTP_HOST']);
define('WP_SITEURL', 'http://'. $_SERVER['HTTP_HOST']);
define('WP_CONTENT_URL', '/wp-content');
define('DOMAIN_CURRENT_SITE', $_SERVER['HTTP_HOST']);

/** Absolute path to the WordPress directory. */
if ( !defined('ABSPATH') )
    define('ABSPATH', dirname(__FILE__). '/');

/** Sets up WordPress vars and included files. */
require_once(ABSPATH. 'wp-settings.php');
```

#### <a name="set-up-a-staging-environment"></a>Configurar um ambiente de preparo
1. Se você já tiver um aplicativo Web do WordPress em execução em sua assinatura do Azure, entre no [Portal do Azure](http://portal.azure.com) e acesse seu aplicativo Web do WordPress. Se você não tiver um aplicativo Web do WordPress, crie um no Azure Marketplace. Para saber mais, consulte [Criar um aplicativo Web do WordPress no Serviço de Aplicativo do Azure](web-sites-php-web-site-gallery.md).
Clique em **Configurações** > **Slots de implantação** > **Adicionar** para criar um slot de implantação com o nome *preparo*. Um slot de implantação é outro aplicativo Web que compartilha os mesmos recursos que o aplicativo Web principal criado anteriormente.

    ![Criar um slot de implantação de estágio](./media/app-service-web-staged-publishing-realworld-scenarios/1setupstage.png)

2. Adicione outro banco de dados MySQL, digamos `wordpress-stage-db`, ao seu grupo de recursos, `wordpressapp-group`.

    ![Adicionar banco de dados MySQL ao grupo de recursos](./media/app-service-web-staged-publishing-realworld-scenarios/2addmysql.png)

3. Atualize as cadeias de conexão de seu slot de implantação de preparo para apontar para o banco de dados, `wordpress-stage-db`. Seu aplicativo Web de produção, `wordpressprodapp` e o aplicativo Web de preparo `wordpressprodapp-stage`, têm que apontar para bancos de dados diferentes.

#### <a name="configure-environment-specific-app-settings"></a>Definir configurações específicas do ambiente de aplicativo
Os desenvolvedores podem armazenar pares de cadeia de chave/valor no Azure como parte das informações de configuração, chamadas de **Configurações do Aplicativo**, associadas a um aplicativo Web. No tempo de execução, os aplicativos Web recuperam esses valores automaticamente para você e os disponibilizam na execução do código em seu aplicativo Web. Do ponto de vista da segurança, é um bom benefício indireto, pois informações confidenciais, como cadeias de conexão de banco de dados que incluem senhas, nunca aparecem como texto não criptografado em um arquivo como o `wp-config.php`.

Esse processo, que é explicado nos parágrafos a seguir, é útil porque inclui alterações de arquivo e alterações do banco de dados para o aplicativo do WordPress:

* Atualização de versão do WordPress
* Adicionar novo, editar ou atualizar plug-ins
* Adicionar novo, editar ou atualizar temas

Definir configurações do aplicativo para:

* Informações de banco de dados
* Habilitar/desabilitar o registro em log do WordPress
* Configurações de segurança do WordPress

![Configurações de Aplicativo para aplicativo Web Wordpress](./media/app-service-web-staged-publishing-realworld-scenarios/3configure.png)

Não deixe de adicionar as seguintes configurações de aplicativo para os slots do aplicativo Web de produção e de preparo. Observe que os aplicativos Web de produção e de preparo usam bancos de dados diferentes.

1. Desmarque a caixa de seleção **Configuração do Slot** para todos os parâmetros de configurações, exceto WP_ENV. Esse processo trocará a configuração de seu aplicativo Web, conteúdo do arquivo e banco de dados. Se a **Configuração de Slot** estiver marcada, as configurações de aplicativo e as configurações de cadeia de conexão do aplicativo Web *não* moverão entre ambientes durante uma operação de **Troca**. As alterações de banco de dados presentes não interromperão o aplicativo Web de produção.

2. Implante um aplicativo Web do ambiente de desenvolvimento local no aplicativo Web de preparo e no banco de dados usando o WebMatrix, ou ferramentas de sua escolha, como FTP, Git ou PhpMyAdmin.

    ![Caixa de Diálogo Publicação do Web Matrix no aplicativo Web WordPress](./media/app-service-web-staged-publishing-realworld-scenarios/4wmpublish.png)

3. Procurar e testar seu aplicativo Web de preparo. Considerando um cenário em que o tema do aplicativo Web deva ser atualizado, eis o aplicativo Web de preparo.

    ![Procurar aplicativo Web de preparo antes de alternar slots](./media/app-service-web-staged-publishing-realworld-scenarios/5wpstage.png)

4. Se estiver tudo correto, clique no botão **Alternar** no seu aplicativo Web de preparo para mover o conteúdo para o seu ambiente de produção. Neste caso, alterne o aplicativo Web e o banco de dados entre ambientes durante cada operação **Alternar** .

    ![Alternar alterações de visualização do WordPress](./media/app-service-web-staged-publishing-realworld-scenarios/6swaps1.png)

    > [!NOTE]
    > Se o seu cenário precisar apenas enviar arquivos por push (sem atualizações de banco de dados), verifique a **Configuração do Slot** de todas as *configurações de aplicativo* e *configurações de cadeias de conexão* relacionadas ao banco de dados na folha **Configurações do Aplicativo Web** no Portal do Azure antes de realizar a **Troca**. Neste caso, DB_NAME, DB_HOST, DB_PASSWORD, DB_USER, as configurações de cadeia de conexão padrão devem aparecer nas alterações de visualização ao realizar a **Troca**. Neste momento, quando você concluir a operação de **Troca**, o aplicativo Web do WordPress terá somente os arquivos atualizados.
    >
    >

    Antes de realizar uma **Troca**, este é o aplicativo Web de produção do WordPress.
    ![Aplicativo Web de produção antes de trocar slots](./media/app-service-web-staged-publishing-realworld-scenarios/7bfswap.png)

    Após a operação de **Troca**, o tema terá sido atualizado em seu aplicativo Web de produção.

    ![Aplicativo Web de produção após alternar slots](./media/app-service-web-staged-publishing-realworld-scenarios/8afswap.png)

5. Quando você precisar reverter, vá até as **Configurações de Aplicativo** Web de produção e clique no botão **Trocar** para trocar o aplicativo Web e o banco de dados do slot de produção para o de preparo. Lembre-se de que se as alterações do banco de dados estiverem incluídas em uma operação de **Troca**, na próxima vez que você implantar em seu aplicativo Web de preparo, será necessário implantar as alterações do banco de dados no banco de dados atual para seu aplicativo Web de preparo. O banco de dados atual pode ser o banco de dados de produção anterior ou o banco de dados de preparo.

#### <a name="summary"></a>Resumo
Veja a seguir um processo generalizado para qualquer aplicativo que tenha um banco de dados:

1. Instale o aplicativo em seu ambiente local.
2. Inclua configurações específicas de ambiente (locais e Aplicativos Web do Azure).
3. Configure seus ambientes de produção e preparo para aplicativos Web.
4. Se você tiver um aplicativo de produção já em execução no Azure, sincronize o conteúdo de produção (arquivos/código e banco de dados) para os ambientes de preparo e local.
5. Desenvolva seu aplicativo no ambiente local.
6. Coloque seu aplicativo Web de produção em manutenção ou em modo bloqueado e sincronize o conteúdo do banco de dados da produção para ambientes de teste e desenvolvimento.
7. Implante no ambiente de preparo e teste.
8. Implante no ambiente de produção.
9. Repita as etapas quatro a seis.

### <a name="umbraco"></a>Umbraco
Nesta seção, você aprenderá como o Umbraco CMS usa um módulo personalizado para implantar em vários ambientes DevOps. Este exemplo fornece uma abordagem diferente para gerenciar vários ambientes de desenvolvimento.

[Umbraco CMS](http://umbraco.com/) é uma solução de .NET CMS popular usada por muitos desenvolvedores. Ela fornece o módulo [Courier2](http://umbraco.com/products/more-add-ons/courier-2) para implantar dos ambientes de desenvolvimento, passando pelo preparo até chegar à produção. Você pode criar facilmente um ambiente de desenvolvimento local para um aplicativo Web Umbraco CMS usando o Visual Studio ou o WebMatrix.

- [Criar um aplicativo Web Umbraco com o Visual Studio](https://our.umbraco.org/documentation/Installation/install-umbraco-with-nuget)
- [Criar um aplicativo Web Umbraco com o WebMatrix](http://umbraco.tv/videos/umbraco-v7/implementor/fundamentals/installation/creating-umbraco-site-from-webmatrix-web-gallery/)

Lembre-se sempre de remover a pasta `install` em seu aplicativo e nunca carregá-la em aplicativos Web em estágio ou produção. Este tutorial usa o WebMatrix.

#### <a name="set-up-a-staging-environment"></a>Configurar um ambiente de preparo
1. Crie um slot de implantação, conforme mencionado anteriormente, para o aplicativo Web Umbraco CMS, supondo que você já tenha um aplicativo Web Umbraco CMS em funcionamento. Se não tiver, você poderá criar um no Marketplace.
2. Atualize a cadeia de conexão do slot de implantação de preparo para apontar para o novo banco de dados **umbraco-stage-db**. O aplicativo Web de produção (umbraositecms-1) e o aplicativo Web de preparo (umbracositecms-1-stage) *devem* apontar para bancos de dados diferentes.

    ![Atualizar a Cadeia de conexão para aplicativos Web de preparo com o novo banco de dados de preparo](./media/app-service-web-staged-publishing-realworld-scenarios/9umbconnstr.png)

3. Clique em **Obter configurações de publicação** para o **preparo** do slot de implantação. Esse processo baixará um arquivo de configurações de publicação que armazena todas as informações exigidas pelo Visual Studio ou WebMatrix para publicar seu aplicativo do aplicativo Web de desenvolvimento local para o aplicativo Web do Azure.

    ![Obter configurações de publicação para o aplicativo Web de preparo](./media/app-service-web-staged-publishing-realworld-scenarios/10getpsetting.png)
4. Abra seu aplicativo Web de desenvolvimento local no WebMatrix ou Visual Studio. Este tutorial usa o WebMatrix. Primeiro, você precisa importar o arquivo de configurações de publicação de seu aplicativo Web de preparo.

    ![Importar Configurações de publicação para o Umbraco usando Web Matrix](./media/app-service-web-staged-publishing-realworld-scenarios/11import.png)

5. Examine as alterações feitas na caixa de diálogo e implante seu aplicativo Web local em seu aplicativo Web do Azure, *umbracositecms-1-stage*. Ao implantar arquivos diretamente em seu aplicativo Web de preparo, você omitirá os arquivos na pasta `~/app_data/TEMP/`, pois eles serão regenerados quando o aplicativo Web de preparo for iniciado pela primeira vez. Você também deverá omitir o arquivo `~/app_data/umbraco.config`, que também será regenerado.

    ![Examinar as alterações de publicação no Web Matrix](./media/app-service-web-staged-publishing-realworld-scenarios/12umbpublish.png)

6. Após a publicação bem-sucedida do aplicativo Web Umbraco local no aplicativo Web de preparo, procure seu aplicativo Web de preparo e execute alguns testes para eliminar problemas.

#### <a name="set-up-the-courier2-deployment-module"></a>Configurar módulo de implantação Courier2
Com o módulo [Courier2](http://umbraco.com/products/more-add-ons/courier-2), você pode simplesmente clicar com o botão direito para enviar conteúdo por push, folhas de estilo e módulos de desenvolvimento de um aplicativo Web de preparo para um aplicativo Web de produção. Esse processo reduz o risco de interromper o aplicativo Web de produção ao implantar uma atualização.
Adquira uma licença para Courier2 para o domínio `*.azurewebsites.net` e seu domínio personalizado (vamos supor http://abc.com). Após adquirir a licença, coloque a licença baixada (arquivo .LIC.) na pasta `bin`.

![Soltar o arquivo de licença na pasta bin](./media/app-service-web-staged-publishing-realworld-scenarios/13droplic.png)

1. [Baixe o pacote Courier2](https://our.umbraco.org/projects/umbraco-pro/umbraco-courier-2/). Entre no aplicativo Web de preparo, por exemplo, http://umbracocms-site-stage.azurewebsites.net/umbraco, clique no menu **Desenvolvedor** e clique em **Pacotes** > **Instalar pacote local**.

    ![Instalador do pacote Umbraco](./media/app-service-web-staged-publishing-realworld-scenarios/14umbpkg.png)

2. Carregue o pacote Courier2 usando o instalador.

    ![Carregar pacote para módulo courier](./media/app-service-web-staged-publishing-realworld-scenarios/15umbloadpkg.png)

3. Para configurar o pacote, você precisa atualizar o arquivo courier.config na pasta **Config** do seu aplicativo Web.

    ```xml
    <!-- Repository connection settings -->
     <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
     <repositories>
        <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear: -->
        <repository name="production web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
          <url>http://umbracositecms-1.azurewebsites.net</url>
          <user>0</user>
          <!--<login>user@email.com</login> -->
          <!-- <password>user_password</password>-->
          <!-- <passwordEncoding>Clear</passwordEncoding>-->
          </repository>
     </repositories>
     ```

4. Em `<repositories>`, insira a URL do site de produção e as informações do usuário.
    Se você estiver usando o provedor de associação Umbraco padrão, adicione a ID do usuário Administração na seção &lt;user&gt;.
    Se você estiver usando um provedor de associação do Umbraco personalizado, use `<login>` e `<password>` no módulo Courier2 para conectar-se ao site de produção.
    Para obter mais detalhes, [confira a documentação do módulo Courier2](http://umbraco.com/help-and-support/customer-area/courier-2-support-and-download/developer-documentation).

5. De maneira semelhante, instale o módulo Courier2 em seu site de produção e configure-o para apontar para o aplicativo Web de estágio em seu respectivo arquivo courier.config, conforme mostrado aqui.

    ```xml
     <!-- Repository connection settings -->
     <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
     <repositories>
        <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear: -->
        <repository name="Stage web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
          <url>http://umbracositecms-1-stage.azurewebsites.net</url>
          <user>0</user>
          </repository>
     </repositories>
    ```

6. Clique na guia **Courier2** no painel do aplicativo Web Umbraco CMS e clique em **Locais**. Você deve ver o nome do repositório, como mencionado em `courier.config`. Faça esse processo em seus aplicativos Web de preparo e de produção.

    ![Exibir repositório de aplicativo Web de destino](./media/app-service-web-staged-publishing-realworld-scenarios/16courierloc.png)

7. Para implantar o conteúdo do site de preparo para o site de produção, acesse **Conteúdo** e selecione uma página existente ou crie uma nova página. Selecionarei uma página existente de meu aplicativo Web cujo título da página é **Introdução – novo** e clicarei em **Salvar e Publicar**.

    ![Alterar o título da página e publicar](./media/app-service-web-staged-publishing-realworld-scenarios/17changepg.png)

8. Clique com o botão direito do mouse na página modificada para exibir todas as opções. Clique em **Courier** para abrir a caixa de diálogo **Implantação**. Clique em **Implantar** para iniciar a implantação.

    ![Diálogo de implantação de módulo Courier](./media/app-service-web-staged-publishing-realworld-scenarios/18dialog1.png)

9. Analise as alterações e clique em **Continuar**.

    ![Alterações de análise do diálogo de implantação de módulo Courier](./media/app-service-web-staged-publishing-realworld-scenarios/19dialog2.png)

    O log de implantação mostra se a implantação foi bem-sucedida.

     ![Exibir Logs de implantação do módulo Courier](./media/app-service-web-staged-publishing-realworld-scenarios/20successdlg.png)

10. Procure seu aplicativo Web de produção para ver se as alterações foram reproduzidas.

     ![Procurar o aplicativo Web de produção](./media/app-service-web-staged-publishing-realworld-scenarios/21umbpg.png)

Para saber mais sobre como usar o Courier, leia a documentação.

#### <a name="how-to-upgrade-the-umbraco-cms-version"></a>Como atualizar a versão CMS do Umbraco
O Courier não ajudará com a atualização de uma versão do Umbraco CMS para outra. Ao atualizar a versão CMS do Umbraco, você deve verificar as incompatibilidades entre seus módulos personalizados ou módulos de parceiros e as bibliotecas principais do Umbraco. Estas são as práticas recomendadas:

* Sempre faça backup de seu aplicativo Web e do banco de dados antes de fazer uma atualização. Em aplicativos Web no Azure, você pode configurar backups automáticos para seus sites usando o recurso de backup e restaurando seu site, se for necessário, usando o recurso restaurar. Para obter mais detalhes, veja [Como fazer backup de seu aplicativo Web](web-sites-backup.md) e [Como restaurar seu aplicativo Web](web-sites-restore.md).
* Verifique se os pacotes de parceiros são compatíveis com a versão para a qual você está atualizando. Na página de download do pacote, examine a compatibilidade de projeto com a versão do Umbraco CMS.

Para obter mais detalhes sobre como atualizar seu aplicativo Web localmente, [consulte o guia de atualização geral](https://our.umbraco.org/documentation/getting-started/setup/upgrading/general).

Após a atualização do site de desenvolvimento local, publique as alterações no aplicativo Web de preparo. Teste seu aplicativo. Se estiver tudo certo, use o botão **Trocar** para trocar seu site de preparo para o aplicativo Web de produção. Ao usar a operação **Trocar**, você poderá exibir as alterações que serão afetadas na configuração de seu aplicativo Web. Essa operação de **Troca** alterna os bancos de dados e aplicativos Web. Após a **Troca**, o aplicativo Web de produção apontará para o banco de dados umbraco-stage-db, e o aplicativo Web de preparo apontará para o banco de dados umbraco-prod-db.

![Alternar visualização para implantar o Umbraco CMS](./media/app-service-web-staged-publishing-realworld-scenarios/22umbswap.png)

Estas são as vantagens de trocar o aplicativo Web e o banco de dados:

* Você pode reverter para a versão anterior do seu aplicativo Web com outra **Troca** se houver algum problema com o aplicativo.
* Para uma atualização, você precisa implantar arquivos e banco de dados do aplicativo Web de preparo para o aplicativo Web de produção e o banco de dados. Muitas coisas que podem dar errado quando você implanta arquivos e bancos de dados. Usando o recurso **Troca** dos slots, podemos reduzir o tempo de inatividade durante uma atualização e reduzir o risco de falhas que podem ocorrer na implantação das alterações.
* Você pode fazer o **Teste A/B** usando o recurso [Teste em produção](https://azure.microsoft.com/documentation/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/).

Este exemplo mostra a flexibilidade da plataforma, onde você pode criar módulos personalizados semelhantes ao módulo Umbraco Courier para gerenciar a implantação entre ambientes.

## <a name="references"></a>Referências
[Desenvolvimento de software Agile com o Serviço de Aplicativo do Azure](app-service-agile-software-development.md)

[Configurar ambientes de preparo para aplicativos Web no Serviço de Aplicativo do Azure](web-sites-staged-publishing.md)

[Como bloquear acesso via Web a slots de implantação de não produção](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

