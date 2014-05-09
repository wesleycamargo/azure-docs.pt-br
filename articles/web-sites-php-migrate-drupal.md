<properties linkid="migrating-drupal-to-azure-websites" urlDisplayName="Migrando Drupal para Sites do Azure" pageTitle="Migrando Drupal para Sites do Azure" metaKeywords="Drupal, PHP, sites" description="Migre um site PHP Drupal para Sites do Azure." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Migrando Drupal para Sites do Azure" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />


# Migrando Drupal para Sites do Azure

Como os Sites do Azure dão suporte a PHP e MySQL, é relativamente fácil de migrar um site Drupal para Sites do Azure. E, como Drupal e PHP são executados em qualquer plataforma, o processo deve funcionar para mover Drupal para Sites do Azure, independentemente da plataforma atual. Assim, como as instalações de Drupal podem variar muito, talvez haja algumas etapas de migração exclusiva não abordadas no material a seguir. Observe que a ferramenta Drush não é usada, porque ela não é compatível com Sites do Azure.

> [WACOM.NOTE]
> Se você estiver movendo um aplicativo Drupal grande e complexo, outra opção será usar os Serviços de Nuvem do Azure. Para obter mais informações sobre as diferenças entre Sites e Serviços de Nuvem, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=310123">Sites, Serviços de Nuvem e VMs do Azure: quando usar o quê?</a>. Para obter ajuda sobre como mover Drupal para Serviços de Nuvem, consulte <a href="http://blogs.msdn.com/b/brian_swan/archive/2012/03/19/azure-real-world-migrating-drupal-from-lamp-to-windows-azure.aspx">Migrando um site Drupal de LAMP para Azure</a>.

## Sumário

- [Criar o site do Azure][]
- [Copiar o banco de dados][]
- [Modificar Settings.php][]
- [Implantar o código Drupal][]
- [Informações relacionadas][]
 
##<a name="create-siteanddb"></a><span class="short-header">Criar um Site do Azure e um banco de dados MySQL</span>1. Criar um Site do Azure e um banco de dados MySQL

Primeiro, leia o tutorial passo a passo para saber como criar um novo site com MySQL: [Criar um site do Azure PHP-MySQL e implantar usando Git][]. Se você quiser usar Git para publicar o site Drupal, siga as etapas no tutorial que explicam como configurar um repositório Git. Não se esqueça de seguir as instruções na seção **Obter informações de conexão MySQL remota** porque você precisará dessas informações depois. Você pode ignorar o restante do tutorial para fins de implantação do site Drupal, mas se estiver começando nos Sites do Azure (e Git), você poderá achar a leitura adicional informativa.

Depois de instalar um novo site com um banco de dados MySQL, agora você tem as informações de conexão do banco de dados MySQL e um repositório Git (opcional). A próxima etapa é copiar o banco de dados para MySQL em Sites do Azure.

##<a name="copy-database"></a><span class="short-header">Copiar banco de dados para MySQL em Sites do Azure</span>2. Copiar banco de dados para MySQL em Sites do Azure

Existem várias maneiras de migrar um banco de dados para o Azure. Uma maneira que funciona bem com bancos de dados MySQL é usar a ferramenta [MySqlDump][]. O seguinte comando fornece um exemplo de como copiar de uma máquina local para os Sites do Azure:

    mysqldump -u local_username --password=local_password  drupal | mysql -h remote_host -u remote_username --password=remote_password remote_db_name

Obviamente, você precisa fornecer o nome de usuário e a senha do banco de dados Drupal. Você também deve fornecer o nome de host, o nome de usuário, a senha e o nome do banco de dados MySQL criado na primeira etapa. Essas informações estão disponíveis nas informações da cadeia de conexão coletadas anteriormente. A cadeia de conexão deve ter um formato semelhante ao seguinte:

    Database=remote_db_name;Data Source=remote_host;User Id=remote_username;Password=remote_password

Dependendo do tamanho do banco de dados, o processo de cópia pode demorar vários minutos.

Agora o banco de dados Drupal está online nos Sites do Azure. Antes de implantar o código Drupal, você precisa modificá-lo para que ele possa se conectar ao novo banco de dados.

##<a name="modify-settingsphp"></a><span class="short-header">Modificar informações de conexão do banco de dados em settings.php</span>3. Modificar informações de conexão do banco de dados em settings.php

Aqui, você precisa novamente das novas informações de conexão do banco de dados. Abra o arquivo **/drupal/sites/default/setting.php** em um editor de texto e substitua os valores ‘database’, ‘username’, ‘password’ e ‘host’ na matriz **$databases** pelos valores corretos para o novo banco de dados. Quando você terminar, você deverá ter algo semelhante a:

    $databases = array (
       'default' => 
       array (
         'default' => 
         array (
           'database' => 'remote_db_name',
           'username' => 'remote_username',
           'password' => 'remote_password',
           'host' => 'remote_host',
           'port' => '',
           'driver' => 'mysql',
           'prefix' => '',
         ),
       ),
     );

Salve o arquivo **settings.php**. Agora você está pronto para implantar.

##<a name="deploy-drupalcode"></a><span class="short-header">Implantar código Drupal usando Git ou FTP</span>4. Implantar código Drupal usando Git ou FTP

A última etapa é implantar o código em Sites do Azure usando Git ou FTP.

Se você estiver usando FTP, obtenha nome de host do FTP e o nome de usuário no painel do site. Em seguida, use qualquer cliente FTP para carregar os arquivos do Drupal na pasta **/site/wwwroot** do site remoto.

Se estiver usando Git, você deverá ter configurado um repositório Git nas etapas anteriores. Você deve instalar Git na máquina local. Depois, siga as instruções fornecidas após a criação do repositório.

> [WACOM.NOTE]
> Dependendo das configurações de Git, você talvez tenha que editar o arquivo .gitignore (um arquivo oculto e um irmão da pasta .git criada no diretório raiz local depois de executar a confirmação de git). Isso especifica arquivos no aplicativo Drupal que podem ser ignorados. Se ele contiver arquivos que devam ser implantados, remova essas entradas para que esses arquivos não sejam ignorados.

Depois que tiver implantado o Drupal em Sites do Azure, você poderá continuar implantando atualizações via Git ou FTP.

##<a name="related-information"></a><span class="short-header">Informações relacionadas</span>Informações relacionadas

Para obter mais informações, consulte as postagens e os tópicos a seguir:

- [Sites do Azure, um ponto de vista do PHP][]
- [Sites, Serviços de Nuvem e VMs do Azure: quando usar o quê?][]
- [Configurando PHP em Sites do Azure com arquivos .user.ini][]
- [Módulo de integração do Azure](https://drupal.org/project/azure_auth)
- [Módulo de armazenamento de blob do Azure](https://drupal.org/project/azure_blob)

  [Criar o site do Azure]: #create-siteanddb
  [Copiar o banco de dados]: #copy-database
  [Modificar Settings.php]: #modify-settingsphp
  [Implantar o código Drupal]: #deploy-drupalcode
  [Informações relacionadas]: #related-information
  [Criar um site do Azure PHP-MySQL e implantar usando Git]: http://www.windowsazure.com/pt-br/develop/php/tutorials/website-w-mysql-and-git/
  
  [Sites do Azure, um ponto de vista do PHP]: http://blogs.msdn.com/b/silverlining/archive/2012/06/12/windows-azure-websites-a-php-perspective.aspx
  [Sites, Serviços de Nuvem e VMs do Azure: quando usar o quê?]: http://go.microsoft.com/fwlink/?LinkId=310123
  [Configurando PHP em Sites do Azure com arquivos .user.ini]: http://blogs.msdn.com/b/silverlining/archive/2012/07/10/configuring-php-in-windows-azure-websites-with-user-ini-files.aspx
  [Módulo de integração do Azure]: http://drupal.org/project/azure

