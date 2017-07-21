---
title: WordPress de classe empresarial no Azure | Microsoft Docs
description: "Saiba como hospedar um site do WordPress de classe empresarial no Serviço de Aplicativo do Azure"
services: app-service\web
documentationcenter: 
author: sunbuild
manager: yochayk
editor: 
ms.assetid: 22d68588-2511-4600-8527-c518fede8978
ms.service: app-service-web
ms.devlang: php
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 10/24/2016
ms.author: sumuth
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: b562e8cbc84fc3a1e7e6dab1845022dfcce692a3
ms.contentlocale: pt-br
ms.lasthandoff: 05/26/2017


---
# <a name="enterprise-class-wordpress-on-azure"></a>WordPress de classe empresarial no Azure
O Serviço de Aplicativo do Azure oferece um ambiente escalonável, seguro e fácil de usar para sites do [WordPress][wordpress] críticos e de grande escala. A própria Microsoft executa sites de edição corporativa, como os blogs do [Office ][officeblog]e do [Bing][bingblog]. Este artigo mostra como usar o recurso Aplicativos Web do Serviço de Aplicativo do Microsoft Azure para estabelecer e manter um site do WordPress de classe empresarial baseado em nuvem que pode lidar com um grande volume de visitantes.

## <a name="architecture-and-planning"></a>Arquitetura e planejamento
Uma instalação básica do WordPress tem apenas dois requisitos:

* **Banco de dados MySQL**: esse requisito está disponível por meio de [ClearDB no Azure Marketplace][cdbnstore]. Como alternativa, é possível gerenciar sua própria instalação do MySQL nas Máquinas Virtuais do Azure usando o [Windows][mysqlwindows] ou o [Linux][mysqllinux].

  > [!NOTE]
  > O ClearDB fornece diversas configurações de MySQL. Cada configuração tem características de desempenho diferentes. Veja a [Azure Store][cdbnstore] para saber mais sobre ofertas fornecidas por meio da Azure Store ou diretamente, como visto no [site do ClearDB](http://www.cleardb.com/pricing.view).
  >
  >
* **PHP 5.2.4 ou posterior**: atualmente, o Serviço de Aplicativo do Azure fornece as [versões 5.4, 5.5 e 5.6 do PHP][phpwebsite].

  > [!NOTE]
  > Recomendamos sempre a execução da versão mais recente do PHP, para garantir que você tenha as correções de segurança mais recentes.
  >
  >

### <a name="basic-deployment"></a>Implantação básica
Se você usar apenas os requisitos básicos, poderá criar uma solução básica dentro de uma região do Azure.

![Um aplicativo Web do Azure e um Banco de Dados MySQL hospedados em uma única região do Azure][basic-diagram]

Embora isso permita a criação de várias instâncias de aplicativos Web do site a fim de escalar horizontalmente o aplicativo, tudo é hospedado dentro dos datacenters em uma região geográfica específica. Os visitantes de fora dessa região podem enfrentar tempos de resposta mais lentos no site. Se os datacenters nessa região ficarem inativos, seu aplicativo também ficará.

### <a name="multi-region-deployment"></a>Implantação em várias regiões
Com o [Gerenciador de Tráfego][trafficmanager] do Azure, é possível dimensionar seu site do WordPress por várias regiões geográficas e fornecer a mesma URL para todos os visitantes. Todos os visitantes chegam por meio do Gerenciador de Tráfego e são encaminhados para uma região com base na configuração de balanceamento de carga.

![Um aplicativo Web do Azure, hospedado em várias regiões, usando o roteador de Alta Disponibilidade do CDBR para rotear ao MySQL em várias regiões][multi-region-diagram]

Dentro de cada região, o site do WordPress ainda seria dimensionado por várias instâncias dos Aplicativos Web, mas esse dimensionamento é específico a uma região. As regiões de tráfego alto podem ser dimensionadas de maneira diferente do que as de tráfego baixo.

Para replicar e rotear o tráfego para vários bancos de dados MySQL, você pode usar [roteadores de alta disponibilidade do ClearDB (CDBRs)][cleardbscale] (à esquerda) ou o [CGE (Carrier Grade Edition) de Cluster do MySQL][cge].

### <a name="multi-region-deployment-with-media-storage-and-caching"></a>Implantação em várias regiões com armazenamento e cache de mídia
Se o site aceitar uploads ou hospedar arquivos de mídia, use o Armazenamento de Blobs do Azure. Se você precisar de cache, leve em consideração o [Cache Redis][rediscache], o [Memcache Cloud](http://azure.microsoft.com/gallery/store/garantiadata/memcached/), o [MemCachier](http://azure.microsoft.com/gallery/store/memcachier/memcachier/) ou uma das outras ofertas de cache na [Azure Store](http://azure.microsoft.com/gallery/store/).

![Um aplicativo Web do Azure, hospedado em várias regiões, usando o roteador de Alta Disponibilidade do CDBR para MySQL, com o Cache Gerenciado, Armazenamento de Blobs e Rede de Distribuição de Conteúdo][performance-diagram]

Como o armazenamento de blob é distribuído geograficamente entre várias regiões por padrão, você não precisa se preocupar com a replicação de arquivos em todos os sites. Também é possível habilitar a [Rede de Distribuição de Conteúdo][cdn] do Azure para Armazenamento de Blobs, que distribui arquivos para nós finais mais próximos dos visitantes.

### <a name="planning"></a>Planejamento
#### <a name="additional-requirements"></a>Requisitos adicionais
| Para fazer isto... | Use isto... |
| --- | --- |
| **Carregar ou armazenar arquivos grandes** |[Plug-in do WordPress para usar o Armazenamento de Blobs][storageplugin] |
| **Enviar email** |[SendGrid][storesendgrid] e o [plug-in do WordPress para usar o SendGrid][sendgridplugin] |
| **Nomes de domínio personalizados** |[Configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure][customdomain] |
| **HTTPS** |[Habilitar HTTPS para um aplicativo Web no Serviço de Aplicativo do Azure][httpscustomdomain] |
| **Validação de pré-produção** |[Configurar ambientes de preparo para aplicativos Web no Serviço de Aplicativo do Azure][staging] <p>Ao transferir um aplicativo Web de preparo para a produção, mova também a configuração do WordPress. Verifique se todas as configurações estão atualizadas de acordo com os requisitos do aplicativo de produção antes de mover o aplicativo do preparo para a produção.</p> |
| **Monitoramento e solução de problemas** |[Habilitar o log de diagnóstico para aplicativos Web no Serviço de Aplicativo do Azure][log] e [Monitorar aplicativos Web no Serviço de Aplicativo do Azure][monitor] |
| **Implantar o site** |[Implantar um aplicativo Web no Serviço de Aplicativo do Azure][deploy] |

#### <a name="availability-and-disaster-recovery"></a>Disponibilidade e recuperação de desastre
| Para fazer isto... | Use isto... |
| --- | --- |
| **Sites de balanceamento de carga** ou **sites distribuídos geograficamente** |[Encaminhar tráfego com o Gerenciador de Tráfego do Azure][trafficmanager] |
| **Backup e restauração** |[Fazer backup de um aplicativo Web no Serviço de Aplicativo do Azure][backup] e [Restaurar um aplicativo Web no Serviço de Aplicativo do Azure][restore] |

#### <a name="performance"></a>Desempenho
O desempenho na nuvem é obtido principalmente por meio de cache e escalonamento. No entanto, a memória, a largura de banda e outros atributos da hospedagem de Aplicativos da Web devem ser consideradas.

| Para fazer isto... | Use isto... |
| --- | --- |
| **Compreender recursos de instância do Site** |[Detalhes do preço, incluindo recursos de camadas do Serviço de Aplicativo][websitepricing] |
| **Recursos do cache** |[Cache Redis][rediscache], [Memcache Cloud](/gallery/store/garantiadata/memcached/), [MemCachier](/gallery/store/memcachier/memcachier/) ou uma das outras opções de cache na [Azure Store](/gallery/store/) |
| **Dimensionar o aplicativo** |[Dimensionar um aplicativo Web no Serviço de Aplicativo do Azure][websitescale] e [Roteamento de alta disponibilidade do ClearDB][cleardbscale]. Se você optar por hospedar e gerenciar a própria instalação do MySQL, considere o [CGE de Cluster do MySQL][cge] para escalar horizontalmente. |

#### <a name="migration"></a>Migração
Há dois métodos para migrar um site do WordPress existente para o Serviço de Aplicativo do Azure:

* **[Exportação do WordPress][export]**: esse método exporta o conteúdo de seu blog. Em seguida, você pode importar o conteúdo para um novo site do WordPress no Serviço de Aplicativo do Azure usando o [plug-in Importador do WordPress][import].

  > [!NOTE]
  > Embora permita migrar o conteúdo, esse processo não migra qualquer plug-in, tema ou outra personalização. Instale novamente esses componentes de forma manual.
  >
  >
* **Migração manual**: [faça backup de seu site][wordpressbackup] e do [banco de dados][wordpressdbbackup] e, em seguida, restaure-os manualmente para um aplicativo Web no Serviço de Aplicativo do Azure e no banco de dados MySQL. Esse método é útil para migrar sites altamente personalizados, pois evita o tédio da instalação manual de plug-ins, temas e outras personalizações.

## <a name="step-by-step-instructions"></a>Instruções passo a passo
### <a name="create-a-wordpress-site"></a>Criar um site do WordPress
1. Use o [Azure Marketplace][cdbnstore] para criar um banco de dados MySQL do tamanho identificado na seção [Arquitetura e planejamento](#planning) na região, ou regiões, em que você hospedará o site.
2. Execute as etapas em [Criar um aplicativo Web do WordPress no Serviço de Aplicativo do Azure][createwordpress] para criar um novo aplicativo Web do WordPress. Ao criar o aplicativo Web, selecione **Usar um banco de dados MySQL existente** e selecione o banco de dados criado na etapa 1.

Se você estiver migrando um site do WordPress existente, consulte [Migrar um site do WordPress existente para o Azure](#Migrate-an-existing-WordPress-site-to-Azure) após a criação de um novo aplicativo Web.

### <a name="migrate-an-existing-wordpress-site-to-azure"></a>Migrar um site do WordPress existente para o Azure
Conforme mencionado na seção [Arquitetura e planejamento](#planning), existem duas maneiras de migrar um site do WordPress:

* **Use exportar e importar** para sites sem muita personalização ou nos quais você apenas deseja mover o conteúdo.
* **Use fazer backup e restaurar** para sites com muita personalização onde você deseja mover tudo.

Use uma das seções a seguir para migrar o site.

#### <a name="the-export-and-import-method"></a>O método exportar e importar
1. Use a [exportação do WordPress][export] para exportar o site existente.
2. Crie um aplicativo Web usando as etapas na seção [Criar um site do WordPress](#Create-a-new-WordPress-site).
3. Entre no site do WordPress no [Portal do Azure][mgmtportal]e, em seguida, clique em **Plug-ins** > **Adicionar Novo**. Procure e instale o plug-in **Importador do WordPress**.
4. Após a instalação do plug-in do Importador do WordPress, clique em **Ferramentas** > **Importar** e clique em **WordPress** para usar o plug-in Importador do WordPress.
5. Na página **Importar WordPress**, clique em **Escolher Arquivo**. Localize o arquivo WXR exportado do site do WordPress existente e clique em **Carregar arquivo e importar**.
6. Clique em **Enviar**. Você será avisado se a importação for bem-sucedida.
7. Após a conclusão de todas essas etapas, reinicie o site de sua folha **Serviços de Aplicativo** no [Portal do Azure][mgmtportal].

Depois de importar o site, talvez seja necessário executar as etapas a seguir para habilitar as configurações não contidas no arquivo de importação.

| Se você estava usando isto... | Faça isso... |
| --- | --- |
| **Permalinks** |No painel do WordPress do novo site, clique em **Configurações** > **Permalinks** e atualize a estrutura Permalinks. |
| **links de imagem/mídia** |Para atualizar links para o novo local, use o [plug-in Velvet Blues Update URLs][velvet], uma ferramenta de pesquisa e substituição, ou atualize os links manualmente no banco de dados. |
| **Temas** |Acesse **Aparência** > **Tema** e atualize o tema do site conforme necessário. |
| **Menus** |Se o tema permitir menus, os links para a home page ainda poderão ter o subdiretório anterior inserido. Acesse **Aparência** > **Menus** e os atualize. |

#### <a name="the-backup-and-restore-method"></a>O método fazer backup e restaurar
1. Faça backup do site do WordPress existente usando as informações nos [backups do WordPress][wordpressbackup].
2. Faça backup do banco de dados existente usando as informações em [Fazer backup do banco de dados][wordpressdbbackup].
3. Crie um novo banco de dados e restaure o backup.

   1. Compre um novo banco de dados no [Azure Marketplace][cdbnstore] ou configure o banco de dados MySQL em uma máquina virtual [Windows][mysqlwindows] ou [Linux][mysqllinux].
   2. Use um cliente do MySQL como [MySQL Workbench][workbench] para se conectar ao novo banco de dados e importar o banco de dados do WordPress.
   3. Atualize o banco de dados para alterar as entradas de domínio para o novo domínio do Serviço de Aplicativo do Azure, por exemplo, mywordpress.azurewebsites.net. Use [Localizar e Substituir o Script dos Bancos de Dados do WordPress][searchandreplace] para alterar todas as instâncias com segurança.
4. Crie um aplicativo Web no Portal do Azure e publique o backup do WordPress.

   1. Para criar um aplicativo Web que tenha um banco de dados, no [Portal do Azure][mgmtportal], clique em **Novo** > **Web + Móvel** > **Azure Marketplace** > **Aplicativos Web** > **Aplicativo Web + SQL** (ou **Aplicativo Web + MySQL**) > **Criar**. Defina todas as configurações necessárias para criar um aplicativo Web vazio.
   2. No backup do WordPress, localize o arquivo **wp-config.php** e o abra em um editor. Substitua as entradas a seguir pelas informações do novo banco de dados MySQL:

      * **DB_NAME**: o nome de usuário do banco de dados.
      * **DB_USER**: o nome de usuário usado para acessar o banco de dados.
      * **DB_PASSWORD**: a senha do usuário.

        Depois de alterar essas entradas, salve e feche o arquivo **wp-config.php**.
   3. Use as informações em [Implantar um aplicativo Web no Serviço de Aplicativo do Azure][deploy] para habilitar o método de implantação que você deseja usar e, em seguida, implante seu backup do WordPress para seu aplicativo Web no Serviço de Aplicativo do Azure.
5. Depois que o site do WordPress for implantado, você deverá ser capaz de acessar o novo site (como um aplicativo Web do Serviço de Aplicativo) usando a URL *.azurewebsite.net do site.

### <a name="configure-your-site"></a>Configurar o site
Depois que o site do WordPress for criado ou migrado, use as informações a seguir para melhorar o desempenho ou habilitar a funcionalidade adicional.

| Para fazer isto... | Use isto... |
| --- | --- |
| **Definir o modo do plano de Serviço de Aplicativo e o tamanho e habilitar o dimensionamento** |[Dimensionar um aplicativo Web no Serviço de Aplicativo do Azure][websitescale]. |
| **Habilitar conexões de banco de dados persistentes** |Por padrão, o WordPress não usa conexões de banco de dados persistentes, que podem reduzir a conexão com o banco de dados depois de várias conexões. Para habilitar conexões persistentes, instale o [plug-in do adaptador de conexões persistentes](https://wordpress.org/plugins/persistent-database-connection-updater/installation/). |
| **Melhorar o desempenho** |<ul><li><p><a href="https://azure.microsoft.com/en-us/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/">Desabilite o cookie ARR</a>, o que pode melhorar o desempenho durante a execução do WordPress em várias instâncias de Aplicativos Web.</p></li><li><p>Habilitar caching. Use o <a href="http://msdn.microsoft.com/library/azure/dn690470.aspx">Cache Redis</a> (visualização) com o <a href="https://wordpress.org/plugins/redis-object-cache/">Plug-in do WordPress de objeto de cache Redis</a>, ou use uma das outras opções de cache da <a href="/gallery/store/">Azure Store</a>.</p></li><li><p>[Tornar o WordPress mais rápido com Wincache](https://wordpress.org/plugins/w3-total-cache/). O Wincache está habilitado por padrão para aplicativos Web. Ao utilizar WinCache e Dynamic Cache em conjunto, desative o cache de arquivo do WinCache, mas deixe o usuário e o cache da sessão ativados. Para desativar o cache de arquivo, em um arquivo .ini no nível do sistema, defina o seguinte valor:<br/><code>wincache.fcenabled = 0</code></p></li><li><p>[Dimensione um aplicativo Web no Serviço de Aplicativo do Azure][websitescale] e use o <a href="http://www.cleardb.com/developers/cdbr/introduction">Roteamento de Alta Disponibilidade do ClearDB</a> ou o <a href="http://www.mysql.com/products/cluster/">CGE de Cluster do MySQL</a>.</p></li></ul> |
| **Usar blobs para armazenamento** |<ol><li><p>[Criar uma conta de Armazenamento do Azure](../storage/storage-create-storage-account.md).</p></li><li><p>Saiba como [Usar a Rede de Distribuição de Conteúdo](../cdn/cdn-create-new-endpoint.md) para distribuir geograficamente os dados armazenados em blobs.</p></li><li><p>Instale e configure o <a href="https://wordpress.org/plugins/windows-azure-storage/">Plug-in Azure Storage for WordPress</a>.</p><p>Para obter informações detalhadas sobre a instalação e a configuração do plug-in, consulte o <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">guia do usuário</a>.</p> </li></ol> |
| **Habilitar o email** |Habilitar o <a href="https://azure.microsoft.com/en-us/marketplace/partners/sendgrid/sendgrid-azure/">SendGrid</a> usando a Azure Store. Instalar o <a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified">plug-in SendGrid</a> para o WordPress. |
| **Configurar um nome de domínio personalizado** |[Configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure][customdomain]. |
| **Habilitar protocolo HTTPS para um nome de domínio personalizado** |[Habilitar HTTPS para um aplicativo Web no Serviço de Aplicativo do Azure][httpscustomdomain]. |
| **Fazer balanceamento de carga ou distribuir geograficamente o site** |[Encaminhar tráfego com o Gerenciador de Tráfego do Azure][trafficmanager]. Se você estiver usando um domínio personalizado, consulte [Configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure][customdomain] para obter informações sobre como usar o Gerenciador de Tráfego com nomes de domínio personalizados. |
| **Habilitar backups automatizados** |[Fazer backup de um aplicativo Web no Serviço de Aplicativo do Azure][backup]. |
| **Habilitar registro em log de diagnóstico** |[Habilitar o registro em log de diagnóstico para aplicativos Web no Serviço de Aplicativo do Azure][log]. |

## <a name="next-steps"></a>Próximas etapas
* [Otimização do WordPress](http://codex.wordpress.org/WordPress_Optimization)
* [Converter WordPress em multissite no Serviço de Aplicativo do Azure](web-sites-php-convert-wordpress-multisite.md)
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

> [!NOTE]
> Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](https://azure.microsoft.com/try/app-service/) , em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido e não há compromissos.
>
>

## <a name="whats-changed"></a>O que mudou
Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, confira: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714).

<!-- URL List -->

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
[customdomain]: app-service-web-tutorial-custom-domain.md
[trafficmanager]: ../traffic-manager/traffic-manager-overview.md
[backup]: web-sites-backup.md
[restore]: web-sites-restore.md
[rediscache]: https://azure.microsoft.com/documentation/services/redis-cache/
[managedcache]: http://msdn.microsoft.com/library/azure/dn386122.aspx
[websitescale]: web-sites-scale.md
[managedcachescale]: http://msdn.microsoft.com/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: web-sites-staged-publishing.md
[monitor]: web-sites-monitor.md
[log]: web-sites-enable-diagnostic-log.md
[httpscustomdomain]: app-service-web-tutorial-custom-ssl.md
[mysqlwindows]:../virtual-machines/windows/classic/mysql-2008r2.md
[mysqllinux]:../virtual-machines/linux/classic/mysql-on-opensuse.md
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
[posh]: /powershell/azureps-cmdlets-docs
[Azure CLI]:../cli-install-nodejs.md
[storesendgrid]: https://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/
[cdn]: ../cdn/cdn-overview.md

