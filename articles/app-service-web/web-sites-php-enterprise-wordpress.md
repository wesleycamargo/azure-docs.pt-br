<properties
	pageTitle="WordPress de classe empresarial no Serviço de Aplicativo do Azure"
	description="Saiba como hospedar um site do WordPress de classe empresarial no Serviço de Aplicativo do Azure"
	services="app-service\web"
	documentationCenter=""
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.devlang="php"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="web"
	ms.date="08/03/2015"
	ms.author="tomfitz"/>

#WordPress de classe empresarial no Serviço de Aplicativo do Azure

O Serviço de Aplicativo do Azure oferece um ambiente escalonável, seguro e fácil de usar para sites do [WordPress][wordpress] críticos e de grande escala. A própria Microsoft executa sites de edição corporativa como os blogs do [Office][officeblog] e do [Bing][bingblog]. Este documento mostra como você pode usar os Aplicativos Web do Serviço de Aplicativo do Azure para estabelecer e manter um site do WordPress de classe empresarial baseado na nuvem que pode lidar com um grande volume de visitantes.

## Arquitetura e planejamento

Uma instalação básica do WordPress tem apenas dois requisitos.

* **Banco de Dados MySQL** - disponível por meio do [ClearDB no Azure Marketplace][cdbnstore] ou é possível gerenciar a própria instalação do MySQL nas Máquinas Virtuais do Azure que usam [Windows][mysqlwindows] ou [Linux][mysqllinux].

    > [AZURE.NOTE]O ClearDB fornece diversas configurações do MySQL, com características de desempenho diferentes para cada configuração. Consulte a [Azure Store][cdbnstore] for para obter informações sobre ofertas oferecidas por meio da Azure Store ou o [preço do ClearDB](http://www.cleardb.com/pricing.view) para ofertas diretamente no ClearDB.

* **PHP 5.2.4 ou posterior** - o Serviço de Aplicativo do Azure atualmente fornece as versões do [PHP 5.3, 5.4 e 5.5][phpwebsite].

	> [AZURE.NOTE]Recomendamos sempre executar a versão mais recente do PHP para garantir que você tenha as correções de segurança mais recentes.

###Implantação básica

Usando apenas os requisitos básicos, você poderia criar uma solução básica dentro de uma região do Azure.

![Um aplicativo Web do Azure e um Banco de Dados MySQL hospedados em uma única região do Azure][basic-diagram]

Embora isso permita que você escale horizontalmente o aplicativo criando várias instâncias de aplicativos Web do site, tudo é hospedado dentro dos data centers em uma região geográfica específica. Os visitantes de fora dessa região podem notar tempos de resposta longos ao usar o site e, se os datacenters nessa região desligarem, o aplicativo desligará também.


###Implantação em várias regiões

Usando-se o [Gerenciador de Tráfego][trafficmanager] do Azure, é possível dimensionar o site do WordPress. Todos os visitantes chegam por meio do Gerenciador de Tráfego e são encaminhados para uma região com base na configuração do balanceamento de carga.

![Um aplicativo Web do Azure, hospedado em várias regiões, usando o roteador de Alta Disponibilidade do CDBR para rotear ao MySQL em várias regiões][multi-region-diagram]

Dentro de cada região, o site do WordPress ainda seria dimensionado em várias instâncias dos aplicativos Web, mas essa colocação em escala é específica da região. As regiões de alto tráfego podem ser dimensionadas de maneira diferente do que as de baixo tráfego.

A replicação e o roteamento para vários Bancos de Dados MySQL podem ser feitos usando-se o [CDBR High Availability Router][cleardbscale] do ClearDB (mostrado à esquerda) ou o [MySQL Cluster CGE][cge].

###Implantação em várias regiões com armazenamento e cache de mídia

Se o site aceitar uploads ou hospedar arquivos de mídia, use o armazenamento de Blob do Azure. Se você precisar de cache, leve em consideração [Redis cache][rediscache], [Memcache Cloud](http://azure.microsoft.com/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/gallery/store/memcachier/memcachier/) ou uma das outras ofertas de cache na [Azure Store](http://azure.microsoft.com/gallery/store/).

![Um aplicativo Web do Azure, hospedado em várias regiões, usando-se o roteador de Alta Disponibilidade do CDBR para MySQL, com Cache Gerenciado, armazenamento de Blob e CDN][performance-diagram]

Como o armazenamento de blob é distribuído geograficamente entre várias regiões por padrão, você não precisa se preocupar com a replicação de arquivos em todos os sites. Também é possível habilitar a [Rede de Distribuição de Conteúdo][cdn] (CDN) do Azure para armazenamento de Blob, que distribui arquivos para nós finais mais próximos dos visitantes.

###Planejamento

####Requisitos adicionais

Para fazer isto... | Use isto...
------------------------|-----------
**Carregar ou armazenar arquivos grandes** | [Plug-in do WordPress para usar o armazenamento de Blob][storageplugin]
**Enviar email** | [SendGrid][storesendgrid] e o [plug-in do WordPress para usar o SendGrid][sendgridplugin]
**Nomes de domínio personalizados** | [Configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure][customdomain]
**HTTPS** | [Habilitar HTTPS para um aplicativo Web no Serviço de Aplicativo do Azure][httpscustomdomain]
**Validação de pré-produção** | [Configurar ambientes para aplicativos Web no Serviço de Aplicativo do Azure temporários][staging] <p>Observe que a alternância de um aplicativo Web para a produção também migra a configuração do WordPress. Você deverá garantir que todas as configurações sejam atualizadas segundo os requisitos do aplicativo de produção antes de alternar o site de teste para a produção.</p>
**Monitoramento e solução de problemas** | [Habilitar o log de diagnóstico para aplicativos Web no Serviço de Aplicativo do Azure][log] e [Monitorar aplicativos Web no Serviço de Aplicativo do Azure][monitor]
**Implantar o site** | [Implantar um aplicativo Web no Serviço de Aplicativo do Azure][deploy]

####Disponibilidade e recuperação de desastre

Para fazer isto... | Use isto...
------------------------|-----------
**Balanceamento de carga dos sites** ou **sites distribuídos geograficamente** | [Encaminhar tráfego com o Gerenciador de Tráfego do Azure.][trafficmanager]
**Backup e restauração** | [Fazer backup de um aplicativo Web no Serviço de Aplicativo do Azure][backup] e [Restaurar um aplicativo Web no Serviço de Aplicativo do Azure][restore]

####Desempenho

O desempenho na nuvem é obtido principalmente por meio do cache e da escala horizontal, mas a memória, a largura de banda e outros atributos da hospedagem de Aplicativos Web também devem ser levados em consideração.

Para fazer isto... | Use isto...
------------------------|-----------
**Compreender recursos de instância do Site** | [Detalhes do preço, inclusive recursos de tamanhos e modos do Site][websitepricing]
**Recursos do cache** | [Redis cache][rediscache], [Memcache Cloud](/gallery/store/garantiadata/memcached/), [MemCachier](/gallery/store/memcachier/memcachier/) ou uma das outras opções de cache na [Azure Store](/gallery/store/)
**Dimensionar o aplicativo** | [Dimensionar um aplicativo Web no Serviço de Aplicativo do Azure][websitescale] e [Roteamento de alta disponibilidade do ClearDB][cleardbscale]. Se optar por hospedar e gerenciar a própria instalação do MySQL, você deverá levar em consideração o [MySQL Cluster CGE][cge] na expansão

####Migração

Existem dois métodos de migrar um site do WordPress existente para o Serviço de Aplicativo do Azure.

* **[Exportação do WordPress][export]** - isso exporta o conteúdo de seu blog, que pode então ser importado para um novo site do WordPress no Serviço de Aplicativo do Azure usando o [plug-in do WordPress importer][import].

	> [AZURE.NOTE]Embora permita migrar o conteúdo, esse processo não migra nenhum plug-in, tema ou outra personalização. Eles devem ser reinstalados manualmente.

* **Migração manual** - [faça backup do site][wordpressbackup] e do [banco de dados][wordpressdbbackup] e restaure-o manualmente em um aplicativo Web no Serviço de Aplicativo do Azure e no banco de dados MySQL para migrar sites altamente personalizados e evitar o tédio de instalar manualmente plug-ins, temas e outras personalizações.

## Instruções passo a passo

### Cria um novo site do WordPress

1. Use o [Azure Marketplace][cdbnstore] para criar um banco de dados MySQL do tamanho identificado na seção [Arquitetura e planejamento](#planning) nas regiões em que você hospedará o site.

2. Siga as etapas em [Criar um aplicativo Web do WordPress no Serviço de Aplicativo do Azure][createwordpress] para criar um novo aplicativo Web do WordPress. Ao criar o aplicativo Web, selecione **Usar um banco de dados MySQL existente** e o banco de dados criado na etapa 1.

Se você estiver migrando um site do WordPress existente, consulte [Migrar um site do WordPress existente para o Azure](#Migrate-an-existing-WordPress-site-to-Azure) depois de criar um novo aplicativo Web.

### Migrar um site do WordPress existente para o Azure

Conforme mencionado na seção [Arquitetura e planejamento](#planning), existem duas maneiras de migrar um site do WordPress.

* **exportar e importar** - para sites sem muita personalização ou onde você apenas deseja mover o conteúdo.

* **fazer backup e restaurar** - para sites com muita personalização onde você deseja mover tudo.

Use uma das seções a seguir para migrar o site.

####O método exportar e importar

1. Use a [exportação do WordPress][export] para exportar o site existente.

2. Crie um novo aplicativo Web usando as etapas na seção [Criar um novo site do WordPress](#Create-a-new-WordPress-site).

3. Faça logon em seu site do WordPress aos aplicativos da Web e clique em **Plug-ins** -> **Adicionar Novo**. Procure e instale o plug-in **WordPress Importer**.

4. Depois que o plug-in de importação tiver sido instalado, clique em **Ferramentas** -> **Importar** e selecione **WordPress** para usar o plug-in de importação do WordPress.

5. Na página **Importar WordPress**, clique em **Escolher Arquivo**. Procure o arquivo WXR exportado do site do WordPress existente e escolha **Carregar arquivo e importar**.

6. Clique em **Enviar**. Você será avisado de que a importação foi bem-sucedida.

8. Depois de ter concluído todas essas etapas, reinicie o site de sua folha de aplicativo Web no [Portal de visualização do Azure][mgmtportal].

Depois de importar o site, você precisará realizar as etapas a seguir para habilitar as configurações não contidas no arquivo de importação.

Se você estava usando isto... | Faça isso...
------------------ | ----------
**Permalinks** | No painel do WordPress do novo site, clique em **Configurações** -> **Permalinks** e atualize a estrutura Permalinks
**links de imagem/mídia** | Para atualizar links para o novo local, use o [plug-in Velvet Blues Update URLs][velvet], uma ferramenta de pesquisa e substituição, ou faça isso manualmente no banco de dados
**Temas** | Vá para **Aparência** -> **Tema** e atualize o tema do site conforme necessário
**Menus** | Se o tema permitir menus, os links para a home page ainda poderão ter o subdiretório anterior inserido. Vá até **Aparência** -> **Menus** e os atualize

####O método fazer backup e restaurar

1. Faça backup do site do WordPress usando as informações nos [backups do WordPress][wordpressbackup].

2. Faça backup do banco de dados existente usando as informações em [Fazendo backup do banco de dados][wordpressdbbackup].

3. Crie um novo banco de dados e restaure o backup.

	1. Compre um novo banco de dados no [Azure Marketplace][cdbnstore] ou configure o banco de dados MySQL em uma VM [Windows][mysqlwindows] ou [Linux][mysqllinux].

	2. Usando um cliente do MySQL como [MySQL Workbench][workbench], se conecte ao novo banco de dados e importe o banco de dados do WordPress.

	3. Atualize o banco de dados para alterar as entradas de domínio para o novo domínio do Serviço de Aplicativo do Azure. Por exemplo, mywordpress.azurewebsites.net. Use [Localizar e Substituir o Script dos Bancos de Dados do WordPress][searchandreplace] para alterar todas as instâncias com segurança.

4. Crie um novo aplicativo Web no Portal do Azure e publique o backup do WordPress.

	1. Criar um novo aplicativo Web no[ Portal de visualização do Azure][mgmtportal] com um banco de dados usando **Novo** -> **Web + Móvel** -> **Azure Marketplace** -> **Aplicativos Web** -> **Aplicativo Web + SQL** (ou **Aplicativo Web + MySQL**) -> **Criar**. Defina todas as configurações necessárias para criar um aplicativo Web vazio.

	2. No backup do WordPress, localize o arquivo **wp-config.php** e o abra em um editor. Substitua as entradas a seguir pelas informações do novo banco de dados MySQL.

		* **DB\_NAME** - o nome do usuário do banco de dados

		* **DB\_USER** - o nome do usuário usado para acessar o banco de dados

		* **DB\_PASSWORD** - a senha do usuário

		Depois de alterar essas entradas, salve e feche o arquivo **wp-config.php**.

	3. Use as informações de [Implantar um aplicativo Web no Serviço de Aplicativo do Azure][deploy] para habilitar o método de implantação que você deseja usar e, em seguida, implante seu backup do WordPress para seu aplicativo Web no Serviço de Aplicativo do Azure.

5. Depois que o site do WordPress for implantado, você deverá ser capaz de acessar o novo site (como um aplicativo Web do Serviço de Aplicativo) usando a URL *.azurewebsite.net do site.

###Configurar o site

Depois que o site do WordPress for criado ou migrado, use as informações a seguir para melhorar o desempenho ou habilitar a funcionalidade adicional.

Para fazer isto... | Use isto...
------------- | -----------
**Definir o modo do plano de Serviço de Aplicativo e o tamanho e habilitar o dimensionamento** | [Dimensionar um aplicativo Web no Serviço de Aplicativo do Azure][websitescale]
**Habilitar conexões de banco de dados persistentes** <p>Por padrão, o WordPress não usa conexões de banco de dados persistente, que podem fazer a conexão com o banco de dados se tornar limitada após várias conexões.</p> | <ol><li><p>Edite o arquivo <strong>wp-includes/wp-db.php</strong>.</p></li><li><p>Localize esta linha.</p><code>$this->dbh = mysql\_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new\_link, $client\_flags );</code></li><li><p>Substitua a linha anterior pela seguinte.</p><code>$this->dbh = mysql\_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword, $client\_flags ); <br/>if ( false !== $error\_reporting ) { /br/>&nbsp;&nbsp;error\_reporting( $error\_reporting ); <br/>} </code></li><li><p>Localize esta linha.</p><code>$this->dbh = @mysql\_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new\_link, $client\_flags ); </code></li><li><p>Substitua a linha anterior pela seguinte.</p><code>$this->dbh = @mysql\_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword, $client\_flags ); </code></li><li><p>Salve o arquivo <strong>wp-includes/wp-db.php</strong> e reimplante o site.</p></li></ol><div class="wa-note"><span class="wa-icon-bulb"></span><p>Essas alterações poderão ser substituídas quando WordPress for atualizado.</p><p>O WordPress usa como padrão as atualizações automáticas, que podem ser desabilitadas com a edição do arquivo <strong>wp-config.php</strong> e a adição de <code>define ( 'WP\_AUTO\_UPDATE\_CORE', false );</code></p><p>Outra maneira de abordar atualizações seria a utilização de um WebJob que monita o arquivo <strong>wp-db.php</strong> e executa as modificações acima sempre que o arquivo é atualizado. Consulte <a href="http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx">Introdução a Trabalhos Web</a> para obter mais informações.</p></div>
**Melhorar o desempenho** | <ul><li><p><a href="http://ppe.blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">Desabilitar o cookie ARR</a> - pode melhorar o desempenho durante a execução do WordPress em várias instâncias do aplicativo Web</p></li><li><p>Habilitar cache. O <a href="http://msdn.microsoft.com/library/azure/dn690470.aspx">Cache redis</a> (visualização) pode ser usada com o <a href="https://wordpress.org/plugins/redis-object-cache/">plug-in do WordPress de cache de objeto Redis</a>, ou use uma das outras ofertas de cache da <a href="/gallery/store/">Azure Store</a></p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">como tornar o WordPress mais rápido com Wincache</a> - Wincache é habilitado por padrão para aplicativos Web</p></li><li><p><a href="../web-sites-scale/">Dimensione um aplicativo Web no Serviço de Aplicativo do Azure</a> e use o <a href="http://www.cleardb.com/developers/cdbr/introduction">Roteamento de alta disponibilidade do ClearDB</a> ou <a href="http://www.mysql.com/products/cluster/">MySQL Cluster CGE</a></p></li></ul>
**Usar blobs para armazenamento** | <ol><li><p><a href="../storage-create-storage-account/">Criar uma conta de armazenamento do Azure</a></p></li><li><p>Saiba como <a href="../cdn-how-to-use/">usar a rede de distribuição de conteúdo (CDN)</a> para geodistribuir dados armazenados em blobs.</p></li><li><p>Instale e configure o <a href="https://wordpress.org/plugins/windows-azure-storage/">Armazenamento do Azure para o plug-in do WordPress</a>.</p><p>Para obter informações detalhadas de configuração e instalação para o plug-in, consulte o <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">guia do usuário</a>.</p> </li></ol>
**Habilitar o email** | <ol><li><p><a href="/gallery/store/sendgrid/sendgrid-azure/">Habilitar SendGrid usando o Armazenamento do Azure</a></p></li><li><p><a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">Instalar o plug-in do SendGrid para WordPress</a></p></li></ol>
**Configurar um nome de domínio personalizado** | [Configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure][customdomain]
**Habilitar protocolo HTTPS para um nome de domínio personalizado** | [Habilitar HTTPS para um aplicativo Web no Serviço de Aplicativo do Azure][httpscustomdomain]
**Fazer balanceamento de carga ou distribuir geograficamente o site** | [Encaminhar tráfego com o Gerenciador de Tráfego do Azure][trafficmanager]. Se você estiver usando um domínio personalizado, consulte [Configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure][customdomain] para obter informações sobre como usar o Gerenciador de Tráfego com nomes de domínio personalizados
**Habilitar backups automatizados** | [Fazer backup de um aplicativo Web no Serviço de Aplicativo do Azure][backup]
**Habilitar registro em log de diagnóstico** | [Habilitar o registro em log de diagnóstico para aplicativos Web no Serviço de Aplicativo do Azure][log]

## Próximas etapas

* [Otimização do WordPress](http://codex.wordpress.org/WordPress_Optimization)

* [Converter WordPress em Multissite no Serviço de Aplicativo do Azure](web-sites-php-convert-wordpress-multisite.md)

* [Assistente de atualização do ClearDB para o Azure](http://www.cleardb.com/store/azure/upgrade)

* [Hospedando o WordPress em uma subpasta do seu aplicativo Web no Serviço de Aplicativo do Azure](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx)

* [Passo a passo: criar um site do WordPress usando o Azure](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx)

* [Hospedar o blog do WordPress existente no Azure](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx)

* [Habilitando formatação de permalinks no WordPress](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress)

* [Como migrar e executar seu blog do WordPress no Serviço de Aplicativo do Azure](http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/)

* [Como executar o WordPress no Serviço de Aplicativo do Azure gratuitamente](http://architects.dzone.com/articles/how-run-wordpress-azure)

* [WordPress no Azure em dois minutos ou menos](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/)

* [Migrando um blog do WordPress para o Azure - parte 1: criar um blog do WordPress no Azure](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1)

* [Migrando um blog do WordPress para o Azure - Parte 2:](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content)

* [Migrando um blog do WordPress para o Azure - parte 3: configurar seu domínio personalizado](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain)

* [Migrando um blog do WordPress para o Azure - parte 4: permalinks e regras de reconfiguração de URL](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules)

* [Migrando um blog do WordPress para o Azure - parte 5: mover de uma subpasta para a raiz](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root)

* [Como configurar um aplicativo Web do WordPress em sua conta do Azure](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/)

* [Preparando o WordPress no Azure](http://www.johnpapa.net/wordpress-on-azure/)

* [Dicas para o WordPress no Azure](http://www.johnpapa.net/azurecleardbmysql/)

>[AZURE.NOTE]Se você deseja começar a usar o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, confira: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia sobre a alteração do portal para o portal de visualização, confira: [Referência para navegar no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)

[performance-diagram]: ./media/web-sites-php-enterprise-wordpress/performance-diagram.png
[basic-diagram]: ./media/web-sites-php-enterprise-wordpress/basic-diagram.png
[multi-region-diagram]: ./media/web-sites-php-enterprise-wordpress/multi-region-diagram.png
[wordpress]: http://www.microsoft.com/web/wordpress
[officeblog]: http://blogs.office.com/
[bingblog]: http://blogs.bing.com/
[cdbnstore]: http://www.cleardb.com/store/azure
[storageplugin]: https://wordpress.org/plugins/windows-azure-storage/
[sendgridplugin]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
[phpwebsite]: web-sites-php-configure.md
[customdomain]: web-sites-custom-domain-name.md
[trafficmanager]: /blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/
[backup]: web-sites-backup.md
[restore]: web-sites-restore.md
[rediscache]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[managedcache]: http://msdn.microsoft.com/library/azure/dn386122.aspx
[websitescale]: web-sites-scale.md
[managedcachescale]: http://msdn.microsoft.com/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: web-sites-staged-publishing.md
[monitor]: web-sites-monitor.md
[log]: web-sites-enable-diagnostic-log.md
[httpscustomdomain]: web-sites-configure-ssl-certificate.md
[mysqlwindows]: ../virtual-machines-mysql-windows-server-2008r2.md
[mysqllinux]: ../virtual-machines-linux-mysql-use-opensuse.md
[cge]: http://www.mysql.com/products/cluster/
[websitepricing]: /pricing/details/app-service/
[export]: http://en.support.wordpress.com/export/
[import]: http://wordpress.org/plugins/wordpress-importer/
[wordpressbackup]: http://wordpress.org/plugins/wordpress-importer/
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[createwordpress]: web-sites-php-web-site-gallery.md
[velvet]: https://wordpress.org/plugins/velvet-blues-update-urls/
[mgmtportal]: https://portal.azure.com/
[wordpressbackup]: http://codex.wordpress.org/WordPress_Backups
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[workbench]: http://www.mysql.com/products/workbench/
[searchandreplace]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
[deploy]: web-sites-deploy.md
[posh]: ../install-configure-powershell.md
[Azure CLI]: ../xplat-cli-install.md
[storesendgrid]: /gallery/store/sendgrid/sendgrid-azure/
[cdn]: ../cdn-how-to-use.md
 

<!---HONumber=Oct15_HO3-->