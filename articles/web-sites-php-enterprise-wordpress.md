<properties title="Enterprise class WordPress on Azure Websites" pageTitle="Enterprise class WordPress on Azure Websites" description="Learn how to host an enterprise class WordPress site on Azure Websites" metaKeywords="wordpress azure, wordpress website, wordpress azure website" services="web-sites" solutions="web" documentationCenter="" authors="larryfr" videoId="" scriptId="" />

## WordPress edição corporativa nos Sites do Azure

Os Sites do Azure oferecem um ambiente escalonável, seguro e fácil de usar para sites do [WordPress][WordPress] críticos e de larga escala. A própria Microsoft executa sites de edição corporativa como os blogs do [Office][Office] e do [Bing][Bing]. Este documento mostra como é possível usar os Sites do Azure para estabelecer e manter um site do WordPress de edição corporativa e com base em nuvem que possa identificar um grande volume de visitantes.

## Neste artigo

-   [Arquitetura e planejamento][Arquitetura e planejamento] - Conhecer arquiteturas, requisitos e considerações de desempenho antes de criar o site

-   [Como][Como] - Criar, implantar e configurar o site

-   [Mais recursos][Mais recursos] - Recursos adicionais e informações

## <span id="plan"></span></a>Arquitetura e planejamento

Uma instalação básica do WordPress tem apenas dois requisitos.

-   **Banco de Dados MySQL** - disponível por meio do [ClearDB na Azure Store][ClearDB na Azure Store] ou é possível gerenciar a própria instalação do MySQL nas Máquinas Virtuais do Azure que usam [Windows][Windows] ou [Linux][Linux].

    > [WACOM.NOTE] O ClearDB fornece diversas configurações do MySQL, com características de desempenho diferentes para cada configuração. Consulte a [Azure Store][ClearDB na Azure Store] for para obter informações sobre ofertas oferecidas por meio da Azure Store ou o [preço do ClearDB][preço do ClearDB] para ofertas diretamente no ClearDB.

-   **PHP 5.2.4 ou superior** - os Sites do Azure atualmente fornecem [PHP versões 5.3, 5.4 e 5.5][PHP versões 5.3, 5.4 e 5.5].

    > [WACOM.NOTE] Recomendamos sempre executar a versão mais recente do PHP para garantir que você tenha as correções de segurança mais recentes.

### Implantação básica

Usando apenas os requisitos básicos, você poderia criar uma solução básica dentro de uma região do Azure.

![Um Site do Azure e um Banco de Dados MySQL hospedados em uma única região do Azure][Um Site do Azure e um Banco de Dados MySQL hospedados em uma única região do Azure]

Embora isso permita que você dimensione o aplicativo criando várias instâncias do Site, tudo é hospedado dentro dos datacenters em uma determinada região geográfica. Os visitantes de fora dessa região podem notar tempos de resposta longos ao usar o site e, se os datacenters nessa região desligarem, o aplicativo desligará também.

### Implantação em várias regiões

Usando-se o [Gerenciador de Tráfego][Gerenciador de Tráfego] do Azure, é possível dimensionar o site do WordPress. Todos os visitantes chegam por meio do Gerenciador de Tráfego e são encaminhados para uma região com base na configuração do balanceamento de carga.

![Um Site do Azure, hospedado em várias regiões, usando o roteador CDBR High Availability para encaminhar ao MySQL em várias regiões][Um Site do Azure, hospedado em várias regiões, usando o roteador CDBR High Availability para encaminhar ao MySQL em várias regiões]

Dentro de cada região, o site do WordPress ainda seria dimensionado em várias instâncias do Site, mas essa colocação em escala é específica da região. As regiões de alto tráfego podem ser dimensionadas de maneira diferente do que as de baixo tráfego.

A replicação e o roteamento para vários Bancos de Dados MySQL podem ser feitos usando-se o [CDBR High Availability Router][CDBR High Availability Router] do ClearDB (mostrado à esquerda) ou o [MySQL Cluster CGE][MySQL Cluster CGE].

### Implantação em várias regiões com armazenamento e cache de mídia

Se o site aceitar uploads ou hospedar arquivos de mídia, use o armazenamento de Blob do Azure. Se você precisar de cache, leve em consideração [Redis cache][Redis cache], [Memcache Cloud][Memcache Cloud], [MemCachier][MemCachier] ou uma das outras ofertas de cache na [Azure Store][Azure Store].

![Um Site do Azure, hospedado em várias regiões, usando-se o roteador CDBR High Availability para MySQL, com Cache Gerenciado, armazenamento de Blob e CDN][Um Site do Azure, hospedado em várias regiões, usando-se o roteador CDBR High Availability para MySQL, com Cache Gerenciado, armazenamento de Blob e CDN]

Como o armazenamento de blob é distribuído geograficamente entre várias regiões por padrão, você não precisa se preocupar com a replicação de arquivos em todos os sites. Também é possível habilitar a [Rede de Distribuição de Conteúdo][Rede de Distribuição de Conteúdo] (CDN) do Azure para armazenamento de Blob, que distribui arquivos para nós finais mais próximos dos visitantes.

### Planejamento

#### Requisitos adicionais

Para fazer isto... | Use isto...
------------------------|-----------
**Carregar ou armazenar arquivos grandes** | <a href="https://wordpress.org/plugins/windows-azure-storage/">Plug-in do WordPress para usar o armazenamento de Blob</a></td>
**Enviar email** | <a href="http://azure.microsoft.com/pt-br/gallery/store/sendgrid/sendgrid-azure/">SendGrid</a> e o <a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">plug-in do WordPress para usar o SendGrid</a></td>
**Nomes de domínio personalizados** | <a href="http://azure.microsoft.com/pt-br/documentation/articles/web-sites-custom-domain-name/">Nomes de domínio personalizados com Sites do Azure</a></td>
**HTTPS** | <a href="http://azure.microsoft.com/pt-br/documentation/articles/web-sites-configure-ssl-certificate/">Suporte ao protocolo HTTPS em Sites do Azure</a></td>
**Validação de pré-produção** | <a href="http://azure.microsoft.com/pt-br/documentation/articles/web-sites-staged-publishing/">Suporte à publicação em etapas para Sites do Azure</a><p>A alternância de um site de etapas para em produção também migra a configuração do WordPress. Você deverá verificar se todas as configurações estão atualizadas segundo os requisitos do site em produção antes de alternar o site em etapas para em produção.</p></td>
**Monitoramento e solução de problemas** | <a href="http://azure.microsoft.com/pt-br/documentation/articles/web-sites-enable-diagnostic-log/">Registro em log de diagnóstico com Sites do Azure</a> e <a href="http://azure.microsoft.com/pt-br/documentation/articles/web-sites-monitor/">Monitorando Sites do Azure</a></td>
**Implantar o site** | <a href="http://azure.microsoft.com/pt-br/documentation/articles/web-sites-deploy/">Implantar um Site do Azure</a></td>

#### Disponibilidade e recuperação de desastre

| Para fazer isto...                                                             | Use isto...                                                                        |
|--------------------------------------------------------------------------------|------------------------------------------------------------------------------------|
| **Balanceamento de carga dos sites** ou **sites distribuídos geograficamente** | [Encaminhar tráfego com o Gerenciador de Tráfego do Azure][Gerenciador de Tráfego] |
| **Backup e restauração**                                                       | [Fazer backup dos Sites do Azure][Fazer backup dos Sites do Azure] e [Restaurar um Site do Azure][Restaurar um Site do Azure]               |

#### Desempenho

O desempenho na nuvem é obtido principalmente por meio do cache e da expansão, mas a memória, a largura de banda e outros atributos da hospedagem do Site também devem ser levados em consideração.

| Para fazer isto...                            | Use isto...                                                                                                                                                                                                                                         |
|-----------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Compreender recursos de instância do Site** | [Detalhes do preço, inclusive recursos de tamanhos e modos do Site][Detalhes do preço, inclusive recursos de tamanhos e modos do Site]                                                                                                                                                                               |
| **Recursos do cache**                         | [Redis cache][Redis cache], [Memcache Cloud][Memcache Cloud], [MemCachier][MemCachier] ou uma das outras opções de cache na [Azure Store][Azure Store]                                                                                                                                            |
| **Dimensionar o aplicativo**                  | [Dimensionar um Site do Azure][Dimensionar um Site do Azure] e [Roteamento do ClearDB High Availability][CDBR High Availability Router]. Se optar por hospedar e gerenciar a própria instalação do MySQL, você deverá levar em consideração o [MySQL Cluster CGE][MySQL Cluster CGE] na expansão |

#### Migração

Existem dois métodos de migrar um site do WordPress existente para Sites do Azure.

-   **[Exportação do WordPress][Exportação do WordPress]** - Exporta o conteúdo do blog, que poderá ser importado em um novo site do [WordPress no Azure usando-se o plug-in de importação do WordPress][WordPress no Azure usando-se o plug-in de importação do WordPress].

    > [WACOM.NOTE] Embora permita migrar o conteúdo, esse processo não migra nenhum plug-in, tema ou outra personalização. Eles devem ser reinstalados manualmente.

-   **Migração manual** - [Faça backup do site][Faça backup do site] e do [banco de dados][banco de dados] e restaure-o manualmente em um Site do Azure e no banco de dados MySQL para migrar sites altamente personalizados e evitar o tédio de instalar manualmente plug-ins, temas e outras personalizações.

## Como

### <span id="create"></span></a>Cria um novo site do WordPress

1.  Use a [Azure Store][ClearDB na Azure Store] para criar um banco de dados MySQL do tamanho identificado na seção [Arquitetura e planejamento][Arquitetura e planejamento] nas regiões em que você hospedará o site.

2.  Siga as etapas em [Criar um site do WordPress da galeria na Azure][Criar um site do WordPress da galeria na Azure] para criar um novo site do WordPress. Ao criar o site, selecione **Usar um banco de dados MySQL existente** e o banco de dados criado na etapa 1.

Se você estiver migrando um site do WordPress existente, consulte [Migrar um site do WordPress existente][Migrar um site do WordPress existente] depois de criar um novo site.

### <span id="migrate"></span></a>Migrar um site do WordPress existente para o Azure

Conforme mencionado na seção [Arquitetura e planejamento][Arquitetura e planejamento], existem duas maneiras de migrar um site do WordPress.

-   **exportar e importar** - para sites sem muita personalização ou onde você apenas deseja mover o conteúdo.

-   **fazer backup e restaurar** - para sites com muita personalização onde você deseja mover tudo.

Use uma das seções a seguir para migrar o site.

#### O método exportar e importar

1.  Use a [exportação do WordPress][Exportação do WordPress] para exportar o site existente.

2.  Crie um novo Site usando as etapas na seção [Cria um novo site do WordPress][Cria um novo site do WordPress].

3.  Faça logon no site do WordPress nos Sites do Azure e clique em **Plug-ins** -\> **Adicionar Novo**. Procure e instale o plug-in **WordPress Importer**.

4.  Depois que o plug-in de importação tiver sido instalado, clique em **Ferramentas** -\> **Importar** e selecione **WordPress** para usar o plug-in de importação do WordPress.

5.  Na página **Importar WordPress**, clique em **Escolher Arquivo**. Procure o arquivo WXR exportado do site do WordPress existente e escolha **Carregar arquivo e importar**.

6.  Clique em **Enviar**. Você será avisado de que a importação foi bem-sucedida.

7.  Depois de concluir todas essas etapas, reinicie o site no painel do site no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].

Depois de importar o site, você precisará realizar as etapas a seguir para habilitar as configurações não contidas no arquivo de importação.

| Se você estava usando isto... | Faça isso...                                                                                                                                                              |
|-------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Permalinks**                | No painel do WordPress do novo site, clique em **Configurações** -\> **Permalinks** e atualize a estrutura Permalinks                                                     |
| **links de imagem/mídia**     | Para atualizar links para o novo local, use o [plug-in Velvet Blues Update URLs][plug-in Velvet Blues Update URLs], uma ferramenta de pesquisa e substituição, ou faça isso manualmente no banco de dados |
| **Temas**                     | Vá até **Aparência** -\> **Tema** e atualize o tema do site conforme necessário                                                                                           |
| **Menus**                     | Se o tema permitir menus, os links para a home page ainda poderão ter o subdiretório anterior inserido. Vá até **Aparência** -\> **Menus** e os atualize                  |

#### O método fazer backup e restaurar

1.  Faça backup do site do WordPress usando as informações nos [backups do WordPress][Faça backup do site].

2.  Faça backup do banco de dados existente usando as informações em [Fazendo backup do banco de dados][banco de dados].

3.  Crie um novo banco de dados e restaure o backup.

    1.  Compre um novo banco de dados na [Azure Store][ClearDB na Azure Store] ou configure o banco de dados MySQL em uma VM [Windows][Windows] ou [Linux][Linux].

    2.  Usando um cliente do MySQL como [MySQL Workbench][MySQL Workbench], se conecte ao novo banco de dados e importe o banco de dados do WordPress.

    3.  Atualize o banco de dados para alterar as entradas de domínio para o novo domínio do site do Azure. Por exemplo, mywordpress.azurewebsites.net. Use [Localizar e Substituir o Script dos Bancos de Dados do WordPress][Localizar e Substituir o Script dos Bancos de Dados do WordPress] para alterar todas as instâncias com segurança.

4.  Crie um novo site e publique o backup do WordPress.

    1.  Crie um novo Site no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure] com um banco de dados usando **Novo** -\> **Site** -\> **Criação Personalizada**. Isso criará um site vazio

    2.  No backup do WordPress, localize o arquivo **wp-config.php** e o abra em um editor. Substitua as entradas a seguir pelas informações do novo banco de dados MySQL.

        -   **DB\_NAME** - o nome do usuário do banco de dados

        -   **DB\_USER** - o nome do usuário usado para acessar o banco de dados

        -   **DB\_PASSWORD** - a senha do usuário

        Depois de alterar essas entradas, salve e feche o arquivo **wp-config.php**.

    3.  Use as informações em [Como implantar um Site do Azure][Implantar um Site do Azure] para habilitar o método de implantação que você deseja usar e implante o backup do WordPress no Site do Azure.

5.  Depois que o site do WordPress for implantado, você deverá ser capaz de acessar o novo site usando a URL \*.azurewebsite.net do site.

### Configurar o site

Depois que o site do WordPress for criado ou migrado, use as informações a seguir para melhorar o desempenho ou habilitar a funcionalidade adicional.

Para fazer isto... | Use isto...
------------------------|-----------
**Definir modo, trabalho e habilitar colocação em escala do Site** | [Como dimensionar Sites](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-scale/)
**Habilitar conexões de banco de dados persistentes** <p>Por padrão, o WordPress não usa conexões de banco de dados persistentes, que podem reduzir a conexão com o banco de dados depois de várias conexões.</p>  | <ol><li><p>Edite o arquivo <strong>wp-includes/wp-db.php</strong>.</p></li><li><p>Encontre a linha a seguir.</p><code>$this-&gt;dbh = mysql_connect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $new_link, $client_flags );</code></p></li><li><p>Substitua a linha anterior pela linha a seguir.</p><code>$this-&gt;dbh = mysql_pconnect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $client_flags ); <br/>if ( false !== $error_reporting ) { /br/&gt;  error_reporting( $error_reporting ); } </code></p></li><li><p>Encontre a linha a seguir.</p><code>$this-&gt;dbh = @mysql_connect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $new_link, $client_flags ); </code></p></li><li><p>Substitua a linha acima pela linha a seguir.</p><code>$this-&gt;dbh = @mysql_pconnect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $client_flags ); </code></p></li><li><p>Salve o arquivo <strong>wp-includes/wp-db.php</strong> e reimplante o site.</p></li></ol><div class="wa-note"><span class="wa-icon-bulb"></span><h5><a name="note"></a>OBSERVA&Ccedil;&Atilde;O:</h5><p>Estas altera&ccedil;&otilde;es podem ser substitu&iacute;das quando o WordPress &eacute; atualizado.</p><p>O WordPress assume como padr&atilde;o as atualiza&ccedil;&otilde;es autom&aacute;ticas, que podem ser desabilitadas com a edi&ccedil;&atilde;o do arquivo <strong>wp-config.php</strong> e a adi&ccedil;&atilde;o do <code>define ( 'WP_AUTO_UPDATE_CORE', false );</code></p><p>Outra maneira de resolver as atualiza&ccedil;&otilde;es seria usar um Trabalho Web que monitora o arquivo <strong>wp-db.php</strong> e realiza as modifica&ccedil;&otilde;es acima sempre que o arquivo &eacute; atualizado. Consulte <a href="http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx">Introdu&ccedil;&atilde;o a Trabalhos Web</a> para obter mais informa&ccedil;&otilde;es.</p></div>
**Melhorar desempenho** | <ul><li><p><a href="http://blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">Desabilitar o cookie ARR</a> - pode melhorar o desempenho durante a execução do WordPress em várias instâncias do Site</p></li><li><p>Habilite o cache. <a href="http://msdn.microsoft.com/pt-br/library/azure/dn690470.aspx">Cache Redis</a> (visualização) pode ser usado com o <a href="https://wordpress.org/plugins/redis-object-cache/">Plug-in do WordPress Redis object cache</a> ou usar uma das outras opções de cache da <a href="http://azure.microsoft.com/pt-br/gallery/store/">Azure Store</a></p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">Como deixar o WordPress mais rápido com Wincache</a> - Wincache permanece habilitado por padrão para os Sites</p></li><li><p><a href="http://azure.microsoft.com/pt-br/documentation/articles/web-sites-scale/">Dimensionar o Site do Azure</a> e usar <a href="http://www.cleardb.com/developers/cdbr/introduction">ClearDB High Availability Routing</a> ou <a href="http://www.mysql.com/products/cluster/">MySQL Cluster CGE</a></p></li></ul>
**Usar blobs para armazenamento** | <ol><li><p><a href="http://azure.microsoft.com/pt-br/documentation/articles/storage-create-storage-account/">Criar uma conta de Armazenamento do Azure</a></p></li><li><p>Saiba como <a href="http://azure.microsoft.com/pt-br/documentation/articles/cdn-how-to-use/">Usar a Rede de Distribuição de Conteúdo (CDN)</a> para distribuir geograficamente os dados armazenados em blobs.</p></li><li><p>Instale e configure o <a href="https://wordpress.org/plugins/windows-azure-storage/">Plug-in Azure Storage for WordPress</a>.</p><p>Para obter informações detalhadas sobre a instalação e a configuração do plug-in, consulte o <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">guia do usuário</a>.</p> </li></ol>
**Habilitar email** | <ol><li><p><a href="http://azure.microsoft.com/pt-br/gallery/store/sendgrid/sendgrid-azure/">Habilitar SendGrid usando a Azure Store</a></p></li><li><p><a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">Instalar o plug-in SendGrid para o WordPress</a></p></li></ol>
**Configurar um nome de domínio personalizado** | [Usar nome de domínio personalizado com um Site do Azure](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-custom-domain-name/)
**Habilitar protocolo HTTPS para um nome de domínio personalizado** | [Usar o protocolo HTTPS com um Site do Azure](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-configure-ssl-certificate/)
**Fazer balanceamento de carga ou distribuir geograficamente o site** | [Encaminhar tráfego com o Gerenciador de Tráfego do Azure](http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/). Se você estiver usando um domínio personalizado, consulte [Usar nome de domínio personalizado com um Site do Azure](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-custom-domain-name/) para obter informações sobre como usar o Gerenciador de Tráfego com nomes de domínio personalizado
**Habilitar backups de site automatizados** | [Fazer backup dos Sites do Azure](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-backup/)
**Habilitar registro em log de diagnóstico** | [Habilitar registro em log de diagnóstico para Sites](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-enable-diagnostic-log/)

## [][]Recursos adicionais

-   [Otimização do WordPress][Otimização do WordPress]

-   [Converter um site do WordPress em um multissite][Converter um site do WordPress em um multissite]

-   [Assistente de atualização do ClearDB para o Azure][Assistente de atualização do ClearDB para o Azure]

-   [Hospedando o WordPress em uma subpasta do Site do Azure][Hospedando o WordPress em uma subpasta do Site do Azure]

-   [Passo a passo: Criar um site do WordPress usando o Azure][Passo a passo: Criar um site do WordPress usando o Azure]

-   [Hospedar o blog do WordPress existente no Azure][Hospedar o blog do WordPress existente no Azure]

-   [Habilitando formatação de permalinks no WordPress][Habilitando formatação de permalinks no WordPress]

-   [Como migrar e executar o blog do WordPress nos Sites do Azure][Como migrar e executar o blog do WordPress nos Sites do Azure]

-   [Como executar o WordPress nos Sites do Azure gratuitamente][Como executar o WordPress nos Sites do Azure gratuitamente]

-   [WordPress no Azure em 2 minutos ou menos][WordPress no Azure em 2 minutos ou menos]

-   [Migrando um blog do WordPress para o Azure - Parte 1: Criando um blog do WordPress no Azure][Migrando um blog do WordPress para o Azure - Parte 1: Criando um blog do WordPress no Azure]

-   [Migrando um blog do WordPress para o Azure - Parte 2: Transferindo o conteúdo][Migrando um blog do WordPress para o Azure - Parte 2: Transferindo o conteúdo]

-   [Migrando um blog do WordPress para o Azure - Parte 3: Configurando o domínio personalizado][Migrando um blog do WordPress para o Azure - Parte 3: Configurando o domínio personalizado]

-   [Migrando um blog do WordPress para o Azure - Parte 4: Formatação de permalinks e regras para reescrever URLs][Migrando um blog do WordPress para o Azure - Parte 4: Formatação de permalinks e regras para reescrever URLs]

-   [Migrando um blog do WordPress para o Azure - Parte 5: Migrando de uma subpasta para a raiz][Migrando um blog do WordPress para o Azure - Parte 5: Migrando de uma subpasta para a raiz]

-   [Como configurar um site do WordPress na conta do Azure][Como configurar um site do WordPress na conta do Azure]

-   [Preparando o WordPress no Azure][Preparando o WordPress no Azure]

-   [Dicas para o WordPress no Azure][Dicas para o WordPress no Azure]

  [WordPress]: http://www.microsoft.com/web/wordpress
  [Office]: http://blogs.office.com/
  [Bing]: http://blogs.bing.com/
  [Arquitetura e planejamento]: #planning
  [Como]: #howto
  [Mais recursos]: #resources
  [ClearDB na Azure Store]: http://www.cleardb.com/store/azure
  [Windows]: http://azure.microsoft.com/pt-br/documentation/articles/virtual-machines-mysql-windows-server-2008r2/
  [Linux]: http://azure.microsoft.com/pt-br/documentation/articles/virtual-machines-linux-mysql-use-opensuse/
  [preço do ClearDB]: http://www.cleardb.com/pricing.view
  [PHP versões 5.3, 5.4 e 5.5]: http://azure.microsoft.com/pt-br/documentation/articles/web-sites-php-configure/
  [Um Site do Azure e um Banco de Dados MySQL hospedados em uma única região do Azure]: ./media/web-sites-enterprise-wordpress/basic-diagram.png
  [Gerenciador de Tráfego]: http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/
  [Um Site do Azure, hospedado em várias regiões, usando o roteador CDBR High Availability para encaminhar ao MySQL em várias regiões]: ./media/web-sites-enterprise-wordpress/multi-region-diagram.png
  [CDBR High Availability Router]: http://www.cleardb.com/developers/cdbr/introduction
  [MySQL Cluster CGE]: http://www.mysql.com/products/cluster/
  [Redis cache]: http://msdn.microsoft.com/pt-br/library/azure/dn690470.aspx
  [Memcache Cloud]: http://azure.microsoft.com/pt-br/gallery/store/garantiadata/memcached/
  [MemCachier]: http://azure.microsoft.com/pt-br/gallery/store/memcachier/memcachier/
  [Azure Store]: http://azure.microsoft.com/pt-br/gallery/store/
  [Um Site do Azure, hospedado em várias regiões, usando-se o roteador CDBR High Availability para MySQL, com Cache Gerenciado, armazenamento de Blob e CDN]: ./media/web-sites-enterprise-wordpress/performance-diagram.png
  [Rede de Distribuição de Conteúdo]: http://azure.microsoft.com/pt-br/documentation/articles/cdn-how-to-use/
  [Plug-in do WordPress para usar o armazenamento de Blob]: https://wordpress.org/plugins/windows-azure-storage/
  [SendGrid]: http://azure.microsoft.com/pt-br/gallery/store/sendgrid/sendgrid-azure/
  [plug-in do WordPress para usar o SendGrid]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
  [Nomes de domínio personalizados com Sites do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/web-sites-custom-domain-name/
  [Suporte ao protocolo HTTPS em Sites do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/web-sites-configure-ssl-certificate/
  [Suporte à publicação em etapas para Sites do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/web-sites-staged-publishing/
  [Registro em log de diagnóstico com Sites do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/web-sites-enable-diagnostic-log/
  [Monitorando Sites do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/web-sites-monitor/
  [Implantar um Site do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/web-sites-deploy/
  [Fazer backup dos Sites do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/web-sites-backup/
  [Restaurar um Site do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/web-sites-restore/
  [Detalhes do preço, inclusive recursos de tamanhos e modos do Site]: https://azure.microsoft.com/pt-br/pricing/details/web-sites/
  [Dimensionar um Site do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/web-sites-scale/
  [Exportação do WordPress]: http://en.support.wordpress.com/export/
  [WordPress no Azure usando-se o plug-in de importação do WordPress]: http://wordpress.org/plugins/wordpress-importer/
  [Faça backup do site]: http://codex.wordpress.org/WordPress_Backups
  [banco de dados]: http://codex.wordpress.org/Backing_Up_Your_Database
  [Criar um site do WordPress da galeria na Azure]:  "http://azure.microsoft.com/pt-br/documentation/articles/web-sites-php-web-site-gallery/"
  [Migrar um site do WordPress existente]: #migrate
  [Cria um novo site do WordPress]: #create
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [plug-in Velvet Blues Update URLs]: https://wordpress.org/plugins/velvet-blues-update-urls/
  [MySQL Workbench]: http://www.mysql.com/products/workbench/
  [Localizar e Substituir o Script dos Bancos de Dados do WordPress]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
  [Introdução a Trabalhos Web]: http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx
  [Desabilitar o cookie ARR]: http://blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx
  [Plug-in do WordPress Redis object cache]: https://wordpress.org/plugins/redis-object-cache/
  [Como deixar o WordPress mais rápido com Wincache]: http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/
  [Criar uma conta de Armazenamento do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/storage-create-storage-account/
  [guia do usuário]: http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx
  []: resources
  [Otimização do WordPress]: http://codex.wordpress.org/WordPress_Optimization
  [Converter um site do WordPress em um multissite]: http://azure.microsoft.com/pt-br/documentation/articles/web-sites-php-convert-wordpress-multisite/
  [Assistente de atualização do ClearDB para o Azure]: http://www.cleardb.com/store/azure/upgrade
  [Hospedando o WordPress em uma subpasta do Site do Azure]: http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx
  [Passo a passo: Criar um site do WordPress usando o Azure]: http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx
  [Hospedar o blog do WordPress existente no Azure]: http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx
  [Habilitando formatação de permalinks no WordPress]: http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress
  [Como migrar e executar o blog do WordPress nos Sites do Azure]: http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/
  [Como executar o WordPress nos Sites do Azure gratuitamente]: http://architects.dzone.com/articles/how-run-wordpress-azure
  [WordPress no Azure em 2 minutos ou menos]: http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/
  [Migrando um blog do WordPress para o Azure - Parte 1: Criando um blog do WordPress no Azure]: http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1
  [Migrando um blog do WordPress para o Azure - Parte 2: Transferindo o conteúdo]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content
  [Migrando um blog do WordPress para o Azure - Parte 3: Configurando o domínio personalizado]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain
  [Migrando um blog do WordPress para o Azure - Parte 4: Formatação de permalinks e regras para reescrever URLs]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules
  [Migrando um blog do WordPress para o Azure - Parte 5: Migrando de uma subpasta para a raiz]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root
  [Como configurar um site do WordPress na conta do Azure]: http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/
  [Preparando o WordPress no Azure]: http://www.johnpapa.net/wordpress-on-azure/
  [Dicas para o WordPress no Azure]: http://www.johnpapa.net/azurecleardbmysql/
