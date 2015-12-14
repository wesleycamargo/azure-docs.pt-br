<properties
   pageTitle="Usar ambientes de Operações de Desenvolvimento com eficiência em seu aplicativo Web"
   description="Saiba como usar os slots de implantação para configurar e gerenciar vários ambientes de desenvolvimento do seu aplicativo"
   services="app-service\web"
   documentationCenter=""
   authors="sunbuild"
   manager="yochayk"
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="web"
   ms.date="09/24/2015"
   ms.author="sumuth"/>

# Usar ambientes de Operações de Desenvolvimento com eficiência em seus aplicativos Web

Este artigo mostra como configurar e gerenciar implantações de aplicativo Web para várias versões do seu aplicativo, como desenvolvimento, teste, controle de qualidade e de produção. Cada versão do seu aplicativo pode ser considerada como ambiente de desenvolvimento para necessidades específicas de seu processo de implantação, por exemplo, o ambiente de Controle de Qualidade pode ser usado por sua equipe de desenvolvedores para testar a qualidade do aplicativo antes de aplicar as alterações à produção. A configuração de vários ambientes de desenvolvimento pode ser uma tarefa desafiadora, já que você precisa controlar e gerenciar os recursos (computação, aplicativo Web, banco de dados, cache, etc.) Entre esses ambientes e implantar o conteúdo de um ambiente para outro.

## Configurando um ambiente de não produção (estágio, desenvolvimento, controle de qualidade)
Quando você tiver um aplicativo Web de produção em funcionamento, a próxima etapa é criar um ambiente de não produção. Para usar slots de implantação, verifique se está executando no modo do plano de Serviço de Aplicativo **Standard** ou **Premium**. Slots de implantação são, na verdade, aplicativos Web online com seus próprios nomes de host. Os elementos de configurações e conteúdo de aplicativo Web podem ser permutados entre dois slots de implantação, incluindo o slot de produção. Implantar o seu aplicativo em um slot de implantação tem os seguintes benefícios:

1. É possível validar as alterações no aplicativo Web em um slot de implantação de preparo antes de permutá-lo pelo slot de produção.
2. Implantar um aplicativo Web em um slot inicial e depois permutá-lo, enviando-o para produção, garante que todas as instâncias do slot estejam prontas antes dessa permuta. Isso elimina o tempo de inatividade quando você for implantar seu aplicativo Web. O redirecionamento do tráfego é contínuo e nenhuma solicitação é descartada como resultado de operações de permuta. Todo esse fluxo de trabalho pode ser automatizado por meio da configuração de [Permuta Automática](web-sites-staged-publishing/#configure-auto-swap-for-your-web-app) quando a validação de pré-permuta não é necessária.
3. Após uma permuta, o slot com o aplicativo Web preparado anteriormente tem agora o aplicativo Web de produção anterior. Se as modificações alternadas no slot de produção não forem o que você esperava, é possível fazer a mesma alternação imediatamente para ter o “último aplicativo Web bom” de volta.

Para configurar um slot de implantação de preparo, confira [Configurar ambientes de preparo para aplicativos Web no Serviço de Aplicativo do Azure](web-sites-staged-publishing) . Cada ambiente deve incluir seu próprio conjunto de recursos, por exemplo, se seu aplicativo Web usa um banco de dados, tanto os aplicativos Web de produção quanto de preparo devem usar bancos de dados diferentes. Adicione recursos de ambiente de desenvolvimento de preparo, como banco de dados, armazenamento ou cache, para definir o ambiente de desenvolvimento de preparo.

## Exemplos de uso de vários ambientes de desenvolvimento

Todos os projetos devem seguir um gerenciamento de código-fonte com pelo menos dois ambientes, um ambiente de desenvolvimento e um de produção, mas ao usar sistemas de Gerenciamento de conteúdo, Estruturas de aplicativo e afins, podemos encontrar problemas para os quais o aplicativo não dê suporte em cenário imprevisto. Isso é verdadeiro para algumas das estruturas populares discutidas a seguir. Muitas perguntas vêm à tona ao trabalhar com CMS/estruturas como

1. Como dividi-las em ambientes diferentes
2. Que arquivos posso mudar sem afetar as atualizações de versão da estrutura
3. Como gerenciar a configuração por ambiente
4. Como gerenciar atualizações de versão de módulos/plug-ins, atualizações de versão da estrutura principal

Há várias maneiras de configurar um ambiente múltiplo para seu projeto e os exemplos a seguir são um apenas um dos métodos dos respectivos aplicativos.

### WordPress
Nesta seção, você aprenderá como configurar um fluxo de trabalho de implantação usando slots para WordPress. O WordPress, como a maioria das soluções CMS, não dá suporte ao trabalho com vários ambientes de desenvolvimento prontos. Aplicativos Web do Serviço de Aplicativo têm alguns recursos que facilitam o armazenamento de definições de configuração fora do seu código.

Antes de criar um slot de preparo, configure o código do aplicativo para dar suporte a vários ambientes. Para dar suporte a vários ambientes no WordPress, você precisa editar `wp-config.php` em seu aplicativo Web de desenvolvimento local adicionando o código a seguir no início do arquivo. Isso permitirá que seu aplicativo obtenha a configuração correta com base no ambiente selecionado.

```
// Support multiple environments
// set the config file based on current environment
if (strpos($_SERVER['HTTP_HOST'],'localhost') !== false) {
    // local development
    $config_file = 'config/wp-config.local.php';
}
elseif  ((strpos(getenv('WP_ENV'),'stage') !== false) ||  (strpos(getenv('WP_ENV'),'prod' )!== false )){
      //single file for all azure development environments
      $config_file = 'config/wp-config.azure.php';
}
$path = dirname(__FILE__) . '/';
if (file_exists($path . $config_file)) {
// include the config file if it exists, otherwise WP is going to fail
require_once $path . $config_file;
```

Crie uma pasta na raiz do aplicativo Web chamada `config` e adicione dois arquivos: `wp-config.azure.php` e `wp-config.local.php`, que representam o ambiente local e do Azure respectivamente.

Copie o seguinte em `wp-config.local.php` :

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
define('AUTH_KEY',         'put your unique phrase here');
define('SECURE_AUTH_KEY',  'put your unique phrase here');
define('LOGGED_IN_KEY',    'put your unique phrase here');
define('NONCE_KEY',        'put your unique phrase here');
define('AUTH_SALT',        'put your unique phrase here');
define('SECURE_AUTH_SALT', 'put your unique phrase here');
define('LOGGED_IN_SALT',   'put your unique phrase here');
define('NONCE_SALT',       'put your unique phrase here');

/**
 * WordPress Database Table prefix.
 *
 * You can have multiple installations in one database if you give each a unique
 * prefix. Only numbers, letters, and underscores please!
 */
$table_prefix  = 'wp_';
```

A definição das chaves de segurança acima pode ajudar impedindo que seu aplicativo Web seja invadido; portanto, use valores exclusivos. Se você precisar gerar a cadeia de caracteres para as chaves de segurança mencionadas acima, pode ir ao gerador automático para criar novas chaves/valores usando este [link](https://api.wordpress.org/secret-key/1.1/salt)

Copie o seguinte código em `wp-config.azure.php`:


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
define('AUTH_KEY' ,getenv('DB_AUTH_KEY'));
define('SECURE_AUTH_KEY',  getenv('DB_SECURE_AUTH_KEY'));
define('LOGGED_IN_KEY', getenv('DB_LOGGED_IN_KEY'));
define('NONCE_KEY', getenv('DB_NONCE_KEY'));
define('AUTH_SALT',  getenv('DB_AUTH_SALT'));
define('SECURE_AUTH_SALT', getenv('DB_SECURE_AUTH_SALT'));
define('LOGGED_IN_SALT',   getenv('DB_LOGGED_IN_SALT'));
define('NONCE_SALT',   getenv('DB_NONCE_SALT'));

/**
* WordPress Database Table prefix.
*
* You can have multiple installations in one database if you give each a unique
* prefix. Only numbers, letters, and underscores please!
*/
$table_prefix  = getenv('DB_PREFIX');
```

#### Use caminhos relativos
Por fim, permita ao aplicativo WordPress usar caminhos relativos. O WordPress armazena informações de URL no banco de dados. Isso tornar a movimentação de conteúdo de um ambiente para outro mais difícil à medida que você precisa atualizar o banco de dados toda vez que move de local para estágio ou de estágio para ambiente de produção. Para reduzir o risco de problemas que podem ocorrer com a implantação de banco de dados sempre que você implantar de um ambiente para outro, use o [plug-in de links Relative Root](https://wordpress.org/plugins/root-relative-urls/) que pode ser instalado usando o painel de administrador do WordPress ou baixado manualmente [aqui](https://downloads.wordpress.org/plugin/root-relative-urls.zip).

Adicione as seguintes entradas ao arquivo `wp-config.php` antes do comentário `That's all, stop editing!`:

```
    define('WP_HOME', 'http://' . $_SERVER ['HTTP_HOST']);
    define('WP_SITEURL', 'http://' . $_SERVER ['HTTP_HOST']);
    define('WP_CONTENT_URL', '/wp-content');
    define('DOMAIN_CURRENT_SITE', $_SERVER['HTTP_HOST']);
```
Ative o plug-in no menu `Plugins` no painel do Administrador do WordPress. Salve as configurações de link permanente para o aplicativo WordPress.

#### O último arquivo `wp-config.php`
As atualizações principais do WordPress não afetarão seus arquivos `wp-config.php` , `wp-config.azure.php` e `wp-config.local.php`. No fim, o arquivo `wp-config.php` terá esta aparência

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
elseif  ((strpos(getenv('WP_ENV'),'stage') !== false) ||  (strpos(getenv('WP_ENV'),'prod' )!== false )){
    $config_file = 'config/wp-config.azure.php';
}


$path = dirname(__FILE__) . '/';
if (file_exists($path . $config_file)) {
    // include the config file if it exists, otherwise WP is going to fail
    require_once $path . $config_file;
}

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');


/* That's all, stop editing! Happy blogging. */

define('WP_HOME', 'http://' . $_SERVER['HTTP_HOST']);
define('WP_SITEURL', 'http://' . $_SERVER['HTTP_HOST']);
define('WP_CONTENT_URL', '/wp-content');
define('DOMAIN_CURRENT_SITE', $_SERVER['HTTP_HOST']);

/** Absolute path to the WordPress directory. */
if ( !defined('ABSPATH') )
	define('ABSPATH', dirname(__FILE__) . '/');

/** Sets up WordPress vars and included files. */
require_once(ABSPATH . 'wp-settings.php');
```

#### Configurar um Ambiente de Preparo
Supondo que você já tenha um aplicativo Web do WordPress em execução no Azure Web, faça logon no [Portal do Azure](http://portal.azure.com) e vá para seu aplicativo Web WordPress. Do contrário, você pode criar um no marketplace. Para saber mais, clique [aqui](web-sites-php-web-site-gallery). Clique em Configurações -> Slots de implantação-> Adicionar para criar um slot de implantação com estágio de nome. Um slot de implantação é outro aplicativo Web que compartilha os mesmos recursos que o aplicativo Web principal criado anteriormente.

![Criar um slot de implantação de estágio](./media/app-service-web-staged-publishing-realworld-scenarios/1setupstage.png)

Adicione outro banco de dados MySQL, digamos `wordpress-stage-db`, ao seu grupo de recursos `wordpressapp-group`.

 ![Adicionar banco de dados MySQL ao grupo de recursos](./media/app-service-web-staged-publishing-realworld-scenarios/2addmysql.png)

Atualize as Cadeias de conexão para o slot de implantação de estágio para apontar para o banco de dados recém-criado, `wordpress-stage-db`. Observe que o aplicativo Web de produção, `wordpressapp-group` e o aplicativo Web de preparo `wordpressprodapp-stage` têm que apontar para bancos de dados diferentes.

#### Definir configurações específicas do ambiente de aplicativo
Os desenvolvedores podem armazenar pares de cadeia chave-valor no Azure como parte das informações de configuração associadas a um aplicativo Web chamado Configurações do Aplicativo. No tempo de execução, os Aplicativos Web do Serviço de Aplicativo recuperam esses valores automaticamente para você e os disponibilizam na execução do código em seu aplicativo Web. Do ponto de vista da segurança, é um bom benefício indireto, já que informações confidenciais, como cadeias de conexão de banco de dados com senhas, nunca aparecem como texto não criptografado em um arquivo como o `wp-config.php`.

O processo definido abaixo é útil quando você executa, pois inclui alterações no arquivo e alterações no banco de dados para o aplicativo WordPress:

- Atualização de versão do WordPress
- Adicionar novo, editar ou atualizar plug-ins
- Adicionar novo, editar ou atualizar temas

Definir configurações do aplicativo para:

- informações de banco de dados
- habilitar/desabilitar o registro em log do WordPress
- Configurações de segurança do WordPress

![Configurações de Aplicativo para aplicativo Web Wordpress](./media/app-service-web-staged-publishing-realworld-scenarios/3configure.png)

Não deixe de adicionar as seguintes configurações de aplicativo para os slots do aplicativo Web de produção e de estágio. Observe que os aplicativos Web de produção e de preparo usam bancos de dados diferentes. Desmarque a caixa de seleção **Configuração do Slot** para todos os parâmetros de configurações, exceto WP\_ENV. Isso alternará a configuração do seu aplicativo Web, além do conteúdo do arquivo e do banco de dados. Se a **Configuração de Slot** for **Marcada**, as configurações de aplicativo do aplicativo Web e as configurações de cadeias de conexão NÃO se moverão entre ambientes durante uma operação de ALTERNAR e, se houver qualquer alteração no banco de dados, isso não poderá ser visto, o que danifica seu aplicativo Web de produção.

Implante um aplicativo Web do ambiente de desenvolvimento local no aplicativo Web de estágio e no banco de dados usando o WebMatrix ou ferramentas de sua escolha, como FTP, Git, PhpMyAdmin.

![Caixa de Diálogo Publicação do Web Matrix no aplicativo Web WordPress](./media/app-service-web-staged-publishing-realworld-scenarios/4wmpublish.png)

Procurar e testar seu aplicativo Web de preparo. Considerando um cenário em que o tema do aplicativo Web deva ser atualizado, eis o aplicativo Web de preparo.

![Procurar aplicativo Web de preparo antes de alternar slots](./media/app-service-web-staged-publishing-realworld-scenarios/5wpstage.png)


 Se estiver tudo correto, clique no botão **Alternar** nas suas configurações de aplicativo Web de preparo para mover o conteúdo para o seu ambiente de produção. Neste caso, alterne o aplicativo Web e o banco de dados entre ambientes durante cada operação **Alternar**.

![Alternar alterações de visualização do WordPress](./media/app-service-web-staged-publishing-realworld-scenarios/6swaps1.png)

 >[AZURE.NOTE]>Se você tiver um cenário em que precisa somente enviar arquivos (nenhuma atualização de banco de dados), **Verifique** a **Configuração de Slot** para todas as *configurações do aplicativo* e *configurações de cadeias de conexão* relacionadas ao banco de dados na folha de configuração do aplicativo Web no Portal do Azure antes de ALTERNAR. Neste caso, DB\_NAME, DB\_HOST, DB\_PASSWORD, DB\_USER, as configurações de cadeia de conexão padrão devem aparecer nas alterações de visualização ao **Alternar**. Neste momento, quando você concluir a operação **Alternar**, o aplicativo Web WordPress terá **SOMENTE** os arquivos atualizados.

Antes de ALTERNAR, aqui está o aplicativo Web de produção do WordPress ![Aplicativo Web de produção antes de alternar slots](./media/app-service-web-staged-publishing-realworld-scenarios/7bfswap.png)

Após a operação ALTERNAR, o tema foi atualizado no seu aplicativo Web de produção.

![Aplicativo Web de produção após alternar slots](./media/app-service-web-staged-publishing-realworld-scenarios/8afswap.png)

Em uma situação em que você precise **reverter**, pode ir para as configurações de aplicativo Web de produção e clicar no botão **Alternar** para alternar o aplicativo Web e o banco de dados de produção para o slot de preparo. É importante lembrar que, se as alterações do banco de dados estiverem incluídas em algum momento na operação de **Alternar**, da próxima vez que você implantar novamente ao seu aplicativo Web de preparo, precisará implantar as alterações no banco de dados atual apontado pelo seu aplicativo Web de preparo, que pode ser um banco de dados de produção anterior ou de preparo.

#### Resumo
Para generalizar o processo para qualquer aplicativo com um banco de dados

1. Instale o aplicativo em seu ambiente local
2. Inclua configurações de ambiente específicas (locais e Aplicativo Web do Azure)
3. Configure seus ambientes em Aplicativos Web do Serviço de Aplicativo - Preparo, Produção
4. Se você tiver um aplicativo de produção já em execução no Azure, sincronize o conteúdo de produção (arquivos/código + banco de dados) para os ambientes de preparo e local.
5. Desenvolva seu aplicativo no ambiente local
6. Coloque seu aplicativo Web de produção em manutenção ou em modo bloqueado e sincronize o conteúdo do banco de dados da produção para ambientes de teste e desenvolvimento
7. Implante no ambiente de Preparo e de Teste
8. Implante no ambiente de Produção
9. Repita as etapas 4 a 6

### Umbraco
Nesta seção, você aprenderá como o Umbraco CMS usa módulos personalizados para implantar entre vários ambientes de Operações de Desenvolvimento. Este exemplo fornece uma abordagem diferente para o gerenciamento de vários ambientes de desenvolvimento.

[Umbraco CMS](http://umbraco.com/) é uma das soluções .NET CMS populares usadas por muitos desenvolvedores que fornece módulo [Courier2](http://umbraco.com/products/more-add-ons/courier-2) para implantar de ambientes de desenvolvimento para preparação para ambientes de produção. Você pode criar facilmente um ambiente de desenvolvimento local para um aplicativo Web Umbraco CMS usando o Visual Studio ou o WebMatrix.

1. Para criar um aplicativo Web Umbraco com o Visual Studio, clique [aqui](https://our.umbraco.org/documentation/Installation/install-umbraco-with-nuget) .
2. Para criar um aplicativo Web Umbraco com o WebMatrix, clique [aqui](http://umbraco.com/help-and-support/video-tutorials/getting-started/working-with-webmatrix).

Lembre-se sempre de remover a pasta `install` em seu aplicativo e nunca carregá-la em aplicativos Web em estágio ou produção. Para este tutorial, usarei o WebMatrix

#### Configurar um ambiente de preparo
Crie um slot de implantação, conforme mencionado acima, para o aplicativo Web Umbraco CMS, supondo que você já tenha um aplicativo Web Umbraco CMS em funcionamento. Do contrário, você pode criar um no marketplace.

Atualize as Cadeias de conexão do slot de implantação de estágio para apontarem para o banco de dados recém-criado, **umbraco-stage-db**. O aplicativo Web de produção (umbraositecms-1) e o aplicativo Web de preparo (umbracositecms-1-stage) **DEVEM** apontar para bancos de dados diferentes.

![Atualizar a Cadeia de conexão para aplicativos Web de preparo com o novo banco de dados de preparo](./media/app-service-web-staged-publishing-realworld-scenarios/9umbconnstr.png)

Clique em **Obter configurações de publicação** para o **estágio** do slot de implantação. Isso irá baixar um arquivo de configurações de publicação que armazena todas as informações necessárias ao Visual Studio ou ao Web Matrix para publicar seu aplicativo do aplicativo Web de desenvolvimento local para o aplicativo Web do Azure.

 ![Obter configurações de publicação para o aplicativo Web de preparo](./media/app-service-web-staged-publishing-realworld-scenarios/10getpsetting.png)

- Abra seu aplicativo Web de desenvolvimento local no **WebMatrix** ou no **Visual Studio**. Neste tutorial, estou usando o Web Matrix e primeiro você precisa importar o arquivo de configurações de publicação do seu aplicativo Web de preparo

![Importar Configurações de publicação para o Umbraco usando Web Matrix](./media/app-service-web-staged-publishing-realworld-scenarios/11import.png)

- Examine as alterações feitas na caixa de diálogo e implante seu aplicativo Web local em seu aplicativo Web do Azure, *umbracositecms-1-stage*. Ao implantar arquivos diretamente no seu aplicativo Web de preparo, você omitirá os arquivos na pasta `~/app_data/TEMP/`, já que eles serão regenerados quando o aplicativo Web de preparo for iniciado pela primeira vez. Você também deverá omitir o arquivo `~/app_data/umbraco.config`, já que ele também será regenerado.

![Examinar as alterações de publicação no Web Matrix](./media/app-service-web-staged-publishing-realworld-scenarios/12umbpublish.png)

- Após a publicação bem-sucedida do aplicativo Web Umbraco local no aplicativo Web de preparo, procure seu aplicativo Web de preparo e execute alguns testes para eliminar problemas.

#### Configurar módulo de implantação Courier2
Com o módulo [Courier2](http://umbraco.com/products/more-add-ons/courier-2), você pode enviar conteúdo por push, folhas de estilo, módulos de desenvolvimento e muito mais com um simples clique com o botão direito do mouse em um aplicativo Web de preparo para implantações sem problemas e reduzindo o risco de danificar seu aplicativo Web de produção ao implantar uma atualização. Compre uma licença para o Courier2 para o domínio `*.azurewebsites.net` e seu domínio personalizado (digamos http://abc.com) Depois que você adquirir a licença, coloque a licença baixada (arquivo .LIC) na pasta `bin`.

![Soltar o arquivo de licença na pasta bin](./media/app-service-web-staged-publishing-realworld-scenarios/13droplic.png)

Baixe o pacote Courier2 [aqui](https://our.umbraco.org/projects/umbraco-pro/umbraco-courier-2/). Faça logon no seu aplicativo Web de preparo, por exemplo http://umbracocms-site-stage.azurewebsites.net/umbraco, clique no menu **Desenvolvedor** e selecione **Pacotes**. Clique no pacote local **Instalar**

![Instalador do pacote Umbraco](./media/app-service-web-staged-publishing-realworld-scenarios/14umbpkg.png)

Carregue o pacote courier2 usando o instalador.

![Carregar pacote para módulo courier](./media/app-service-web-staged-publishing-realworld-scenarios/15umbloadpkg.png)

Para configurar, você precisa atualizar o arquivo courier.config na pasta **Config** do seu aplicativo Web.

```xml
<!-- Repository connection settings -->
  <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
  <repositories>
        <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear:  -->
        <repository name="production web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
            <url>http://umbracositecms-1.azurewebsites.net</url>
            <user>0</user>
            <!--<login>user@email.com</login> -->
            <!-- <password>user_password</password>-->
           <!-- <passwordEncoding>Clear</passwordEncoding>-->
           </repository>
  </repositories>
 ```

Under `<repositories>`, enter the production site URL and user information. If you are using default Umbraco Membership provider, then add the ID for the Administration user in <user> section . If you are using a custom Umbraco membership provider, use `<login>`,`<password>` to Courier2 module know how to connect to the production site. For more details, review the [documentation](http://umbraco.com/help-and-support/customer-area/courier-2-support-and-download/developer-documentation) for Courier module.

Similarly, install Courier module on your production site and configure it point to stage web app in its respective courier.config file as shown here

```xml
  <!-- Repository connection settings -->
  <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
  <repositories>
        <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear:  -->
        <repository name="Stage web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
            <url>http://umbracositecms-1-stage.azurewebsites.net</url>
            <user>0</user>
           </repository>
  </repositories>
```

Clique na guia Courier2 no painel do aplicativo Web Umbraco CMS e selecione os locais. Você deve ver o nome do repositório, como mencionado em `courier.config`. Faça isso tanto nos aplicativos Web de produção quanto nos de preparo.

![Exibir repositório de aplicativo Web de destino](./media/app-service-web-staged-publishing-realworld-scenarios/16courierloc.png)

Agora vamos implantar algum conteúdo do site de preparo para o site de produção. Vá até Conteúdo e selecione uma página existente ou crie uma nova página. Selecionarei uma página existente do meu aplicativo Web cujo título da página é alterado para **Introdução – novo** e agora clicarei em **Salvar e Publicar**.

![Alterar o título da página e publicar](./media/app-service-web-staged-publishing-realworld-scenarios/17changepg.png)

Agora, selecione a página modificada e *clique com o botão direito do mouse* para exibir todas as opções. Clique em **Courier** e para exibir o diálogo Implantação. Clique em **Implantar** para iniciar a implantação

![Diálogo de implantação de módulo Courier](./media/app-service-web-staged-publishing-realworld-scenarios/18dialog1.png)

Analise as alterações e clique em Continuar.

![Alterações de análise do diálogo de implantação de módulo Courier](./media/app-service-web-staged-publishing-realworld-scenarios/19dialog2.png)

O log de implantação mostra se a implantação foi bem-sucedida.

 ![Exibir Logs de implantação do módulo Courier](./media/app-service-web-staged-publishing-realworld-scenarios/20successdlg.png)

Procure seu aplicativo Web de produção para ver se as alterações foram reproduzidas.

 ![Procurar o aplicativo Web de produção](./media/app-service-web-staged-publishing-realworld-scenarios/21umbpg.png)

Para saber mais sobre como usar o Courier, leia a documentação.

#### Como atualizar a versão CMS do Umbraco

O Courier não implantará ajuda na atualização de uma versão do Umbraco CMS para outra. Ao atualizar a versão do Umbraco CMS, você deve verificar as incompatibilidades entre seus módulos personalizados ou módulos de terceiros e as bibliotecas principais do Umbraco. Como prática recomendada

1. SEMPRE faça backup de seu aplicativo Web e do banco de dados antes de fazer uma atualização. No aplicativo Web do Azure, você pode configurar backups automáticos para recursos de seus sites usando o recurso backup e restaurando seu site, se necessário, usando o recurso restaurar. Para obter mais detalhes, veja [Como fazer backup de seu aplicativo Web](web-sites-backup) e [Como restaurar seu aplicativo Web](web-sites-restore).

2. Verifique se os pacotes de terceiros que estiver usando são compatíveis com a versão para a qual você está atualizando. Na página de download do pacote, examine a Compatibilidade de projeto com a versão do Umbraco CMS.

Para obter mais detalhes sobre como atualizar seu aplicativo Web localmente, siga as orientações mencionadas [aqui](https://our.umbraco.org/documentation/getting-started/setup/upgrading/general).

Após a atualização do site de desenvolvimento local, publique as alterações no aplicativo Web de preparo. Teste seu aplicativo e, se estiver tudo certo, use o botão **Alternar** para **Alternar** seu site de preparo para o aplicativo Web de produção. Ao executar a operação **Alternar**, você pode exibir as alterações que serão afetadas na configuração de seu aplicativo Web. Com essa operação **Alternar**, estamos alternando os bancos de dados e aplicativos Web. Isso significa que, após ALTERNAR, o aplicativo Web de produção apontará para o banco de dados umbraco-stage-db e o aplicativo Web de preparo apontará para o banco de dados umbraco-prod-db.

![Alternar visualização para implantar o Umbraco CMS](./media/app-service-web-staged-publishing-realworld-scenarios/22umbswap.png)

A vantagem de alternar o aplicativo Web e o banco de dados: 1. Fornece a capacidade de reverter para a versão anterior do seu aplicativo Web com outra **Alternância** se houver algum problema com o aplicativo. 2. Para uma atualização, você precisa implantar arquivos e banco de dados do aplicativo Web de preparo para o aplicativo Web de produção e o banco de dados. Há muitas coisas que podem dar errado durante a implantação de banco de dados e de arquivos. Usando o recurso **Alternância** dos slots, podemos reduzir o tempo de inatividade durante uma atualização e reduzir o risco de falhas que podem ocorrer na implantação das alterações. 3. Fornece a capacidade de fazer **testes A/B** usando o recurso [Teste em produção](http://azure.microsoft.com/documentation/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)

Este exemplo mostra a flexibilidade da plataforma, onde você pode criar módulos personalizados semelhantes ao módulo Umbraco Courier para gerenciar a implantação entre ambientes.

## Referências
[Desenvolvimento de software Agile com o Serviço de Aplicativo do Azure](app-service-agile-software-development)

[Configurar ambientes de preparo para aplicativos Web no Serviço de Aplicativo do Azure](web-sites-staged-publishing)

[Como bloquear acesso via Web a slots de implantação de não produção](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

<!---HONumber=AcomDC_1203_2015-->