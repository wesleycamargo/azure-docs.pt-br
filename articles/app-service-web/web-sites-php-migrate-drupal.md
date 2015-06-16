<properties 
	pageTitle="Migrar o Drupal para o Serviço de Aplicativo do Azure" 
	description="Migre um site PHP do Drupal para o Serviço de Aplicativo do Azure." 
	services="app-service\web" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="tomfitz"/>


# Migrar o Drupal para o Serviço de Aplicativo do Azure

Como o Serviço de Aplicativo do Azure dá suporte a PHP e MySQL, é relativamente fácil migrar um site dp Drupal para Aplicativos Web do Serviço de Aplicativo do Azure. Como o Drupal e o PHP são executados em qualquer plataforma, o processo deve funcionar para mover do Drupal para Aplicativos Web do Serviço de Aplicativo do Azure, independentemente da plataforma atual. Assim, como as instalações de Drupal podem variar muito, talvez haja algumas etapas de migração exclusiva não abordadas no material a seguir. Observe que a ferramenta Drush não é usada, porque ela não é compatível com o Serviço de Aplicativo do Azure.

Se você estiver movendo um aplicativo do Drupal grande e complexo, outra opção será usar os Serviços de Nuvem do Azure. Para obter mais informações sobre as diferenças entre o Serviço de Aplicativo e os Serviços de Nuvem, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=310123">Serviço de Aplicativo do Azure, Serviços de Nuvem e VMs: Quando usar qual opção?</a>. Para obter ajuda sobre como mover do Drupal para os Serviços de Nuvem, consulte <a href="http://blogs.msdn.com/b/brian_swan/archive/2012/03/19/azure-real-world-migrating-drupal-from-lamp-to-windows-azure.aspx">Migrando um site do Drupal de LAMP para o Azure</a>.
 
## Criar um aplicativo Web e banco de dados MySQL

Primeiro, acesse o tutorial passo a passo para saber como criar um novo aplicativo Web com MySQL: [Criar um aplicativo Web PHP-MySQL no Serviço de Aplicativo do Azure e implantá-lo usando o Git][]. Se você quiser usar Git para publicar o site Drupal, siga as etapas no tutorial que explicam como configurar um repositório Git. Não se esqueça de seguir as instruções na seção **Obter informações de conexão MySQL remota** porque você precisará dessas informações depois. Você pode ignorar o restante do tutorial para fins de implantação do site do Drupal, mas se você for iniciante no Serviço de Aplicativo do Azure (e no Git), a leitura adicional poderá ser informativa.

Depois de instalar um novo aplicativo Web com um banco de dados MySQL, agora você tem as informações de conexão do banco de dados MySQL e um repositório Git (opcional). A próxima etapa é copiar seu banco de dados para o banco de dados MySQL do aplicativo Web.

## Copiar o banco de dados para o banco de dados MySQL do aplicativo Web

Existem várias maneiras de migrar um banco de dados para o Azure. Uma maneira que funciona bem com bancos de dados MySQL é usar a ferramenta [MySqlDump][]. O seguinte comando fornece um exemplo de como copiar de uma máquina local para o Azure:

    mysqldump -u local_username --password=local_password  drupal | mysql -h remote_host -u remote_username --password=remote_password remote_db_name

Obviamente, você precisa fornecer o nome de usuário e a senha do banco de dados Drupal. Você também deve fornecer o nome de host, o nome de usuário, a senha e o nome do banco de dados MySQL criado na primeira etapa. Essas informações estão disponíveis nas informações da cadeia de conexão coletadas anteriormente. A cadeia de conexão deve ter um formato semelhante ao seguinte:

    Database=remote_db_name;Data Source=remote_host;User Id=remote_username;Password=remote_password

Dependendo do tamanho do banco de dados, o processo de cópia pode demorar vários minutos.

Agora o banco de dados Drupal está online no Azure. Antes de implantar o código Drupal, você precisa modificá-lo para que ele possa se conectar ao novo banco de dados.

## Modificar informações de conexão de banco de dados em settings.php

Aqui, você precisa novamente das novas informações de conexão de banco de dados. Abra o arquivo **/drupal/sites/default/setting.php** em um editor de texto e substitua os valores  `database`, `username`, `password` e `host` na matriz **$databases** pelos valores corretos para o novo banco de dados. Quando você terminar, você deverá ter algo semelhante a:

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

## Implantar código do Drupal usando Git ou FTP

A última etapa é implantar o código em aplicativos Web usando Git ou FTP.

Se você estiver usando FTP, obtenha o nome de host e o nome de usuário do FTP na folha do aplicativo Web no [Portal do Azure](https://portal.azure.com). Em seguida, use qualquer cliente FTP para carregar os arquivos do Drupal na pasta **/site/wwwroot** do site remoto.

Se estiver usando Git, você deverá ter configurado um repositório Git nas etapas anteriores. Você deve instalar Git na máquina local. Depois, siga as instruções fornecidas após a criação do repositório.

> [AZURE.NOTE]
> Dependendo das configurações de Git, talvez você tenha que editar o arquivo .gitignore (um arquivo oculto e um irmão da pasta .git criada no diretório raiz local depois que você executou a confirmação de git). Isso especifica arquivos no aplicativo Drupal que podem ser ignorados. Se ele contiver arquivos que devam ser implantados, remova essas entradas para que esses arquivos não sejam ignorados.

Depois que tiver implantado o Drupal em aplicativos Web, você poderá continuar implantando atualizações via Git ou FTP.

## Informações relacionadas

Para obter mais informações, consulte as postagens e os tópicos a seguir:

- [Aplicativos Web do Serviço de Aplicativo do Azure, uma perspectiva do PHP][]
- [Serviço de Aplicativo do Azure, Serviços de Nuvem e VMs: Quando usar qual opção?][]
- [Configurando PHP em Aplicativos Web do Serviço de Aplicativo do Azure com arquivos .user.ini][]
- [Módulo de integração do Azure](https://drupal.org/project/azure_auth)
- [Módulo de armazenamento de blob do Azure](https://drupal.org/project/azure_blob)

>[AZURE.NOTE] Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Não é necessário nenhum cartão de crédito; não há compromissos.

## O que mudou
* Para obter um guia para a alteração de sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia para a alteração do portal antigo para o novo portal, consulte: [Referência à navegação pelo portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)

  [Criar um aplicativo Web PHP-MySQL no Serviço de Aplicativo do Azure e implantá-lo usando o Git]: /develop/php/tutorials/website-w-mysql-and-git/
  
  [Aplicativos Web do Serviço de Aplicativo do Azure, uma perspectiva do PHP]: http://blogs.msdn.com/b/silverlining/archive/2012/06/12/windows-azure-websites-a-php-perspective.aspx
  [Serviço de Aplicativo do Azure, Serviços de Nuvem e VMs: quando usar qual opção?]: http://go.microsoft.com/fwlink/?LinkId=310123
  [Configurando PHP em Aplicativos Web do Serviço de Aplicativo do Azure com arquivos .user.ini]: http://blogs.msdn.com/b/silverlining/archive/2012/07/10/configuring-php-in-windows-azure-websites-with-user-ini-files.aspx
  [Módulo de integração do Azure]: http://drupal.org/project/azure


<!--HONumber=52--> 