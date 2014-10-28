<properties title="How to create a PHP website in Azure Websites" pageTitle="How to create a PHP website in Azure Websites" metaKeywords="PHP Azure Web Sites" description="Learn how to create a PHP website in Azure Websites" documentationCenter="PHP" services="Web Sites" editor="mollybos" manager="wpickett" authors="cephalin" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# Como criar um site PHP nos sites do Azure

Este artigo mostrará como criar um site do PHP nos [sites do Azure][sites do Azure] usando o [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], as [Ferramentas de Linha de Comando do Azure para Mac e Linux][Ferramentas de Linha de Comando do Azure para Mac e Linux] ou os cmdlets [PowerShell do Azure][PowerShell do Azure].

Em geral, criando um site do PHP não é diferente que criando *as* site da web em Sites do Azure. Por padrão, o PHP está habilitado para todos os sites. Para obter informações sobre configurar PHP (ou fornecendo seu próprio tempo de execução do PHP personalizado), consulte [como configurar PHP em Sites do Azure][como configurar PHP em Sites do Azure]

Cada opção descrita abaixo mostra como criar um site da web em um ambiente de hospedagem compartilhado sem custo adicional, mas com algumas limitações no uso da CPU e o uso de largura de banda. Para obter mais informações, consulte [Sites de Preço do Azure][Sites de Preço do Azure]. Para obter informações adicionais sobre como um atualizar e dimensionar um site, consulte [Como dimensionar sites][Como dimensionar sites].

## Sumário

-   [Criar um site usando o Portal de Gerenciamento do Azure][Criar um site usando o Portal de Gerenciamento do Azure]
-   [Criar um site usando as ferramentas de linha de comando do Azure para Mac e Linux.][Criar um site usando as ferramentas de linha de comando do Azure para Mac e Linux.]
-   [Criar um site usando o PowerShell cmdlets do Azure][Criar um site usando o PowerShell cmdlets do Azure]

## <a name="portal"></a>Criar um site do PHP usando o Portal de Gerenciamento do Azure

Quando você cria um site da web no Portal de Gerenciamento do Azure, você tem três opções: **criação rápida**, **criar com banco de dados**, e **da galeria**. As instruções a seguir abordará a **criação rápida** opção. Para obter informações sobre as duas opções, consulte [criar um site do Azure PHP MySQL e implantar usando gito][criar um site do Azure PHP MySQL e implantar usando gito] e [criar um site do WordPress da galeria no Azure][criar um site do WordPress da galeria no Azure]

Para criar um Site do PHP usando o Portal de Gerenciamento do Azure, faça o seguinte:

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  Clique em **NOVO** na parte inferior da página, selecione **Computação**, **SITE** e **Criação Rápida**. Fornecer um **URL** para seu site e selecione o **região** para seu site da web. Por fim, clique em **Criar Site**.

    ![Selecione Criação Rápida de site.][Selecione Criação Rápida de site.]

## <a name="XplatTools"></a>Criar um site do PHP usando as ferramentas de linha de comando do Azure para Mac e Linux

Criar um site do PHP usando as ferramentas de linha de comando do Azure para Mac e Linux, faça o seguinte:

1.  Instalar as ferramentas de linha de comando do Azure, seguindo as instruções aqui: [como instalar as ferramentas de linha de comando do Azure para Mac e Linux][como instalar as ferramentas de linha de comando do Azure para Mac e Linux].

2.  Faça o download e importar a publicar o arquivo de configurações, seguindo as instruções aqui: [como fazer o download e importar as configurações de publicação][como fazer o download e importar as configurações de publicação]

3.  Execute o seguinte comando do prompt de comando:

        azure site create MySiteName

A URL para o site criado recém-criado será `http://MySiteName.azurewebsites.net`.

Observe que você pode executar o comando `azure site create` com qualquer uma das seguintes opções:

-   `--location [location name]`. Esta opção permite que você especifique o local do datacenter em que o site será criado (por exemplo, “Oeste dos EUA”). Se você omitir esta opção, será solicitado que escolha um local.
-   `--hostname [custom host name]`. Esta opção permite que você especifique um nome de host personalizado para o site.
-   `--git`. Esta opção permite que você use o git para publicar o site, criando repositórios git tanto no diretório do aplicativo local quanto no datacenter do site. Observe que, se a sua pasta local já for um repositório git, o comando adicionará um novo controle remoto ao repositório existente, apontando para o repositório no datacenter do site.

Para obter informações sobre opções adicionais, consulte [como criar e gerenciar um Site do Azure][como criar e gerenciar um Site do Azure]

## <a name="PowerShell"></a>Criar um site do PHP usando o Portal do Azure PowerShell

Para criar um Site do PHP usando o PowerShell cmdlets do Azure, faça o seguinte:

1.  Instalar os cmdlets do PowerShell do Azure, seguindo as instruções aqui: [começar com o PowerShell Azure][começar com o PowerShell Azure]

2.  Baixar e importar os arquivos de configurações de publicação seguindo as instruções aqui:[Como: Importar configurações de publicação][Como: Importar configurações de publicação].

3.  Abra um prompt de comando do PowerShell e execute o seguinte comando:

        New-AzureWebSite MySiteName

A URL para o site criado recém-criado será `http://MySiteName.azurewebsites.net`.

Observe que você pode executar o comando `New-AzureWebSite` com qualquer uma das seguintes opções:

-   `-Location [location name]`. Esta opção permite que você especifique o local do datacenter em que o site será criado (por exemplo, “Oeste dos EUA”). Se você omitir esta opção, será solicitado que escolha um local.
-   `-Hostname [custom host name]`. Esta opção permite que você especifique um nome de host personalizado para o site.
-   `-Git`. Esta opção permite que você use o git para publicar o site, criando repositórios git tanto no diretório do aplicativo local quanto no datacenter do site. Observe que, se a sua pasta local já for um repositório git, o comando adicionará um novo controle remoto ao repositório existente, apontando para o repositório no datacenter do site.

Para obter informações sobre opções adicionais, consulte [Como: criar e gerenciar um Site do Azure][Como: criar e gerenciar um Site do Azure].

## <a name="NextSteps"></a>Próximas etapas

Agora que você criou um site do PHP em Sites do Azure, você pode gerenciar, configurar, monitorar, implantar e dimensionar seu site. Para obter mais informações, consulte os links a seguir:

-   [Como configurar sites][Como configurar sites]
-   [Como configurar o PHP nos Sites do Azure][como configurar PHP em Sites do Azure]
-   [Como gerenciar Sites][Como gerenciar Sites]
-   [Como monitorar Sites][Como monitorar Sites]
-   [Como dimensionar sites][Como dimensionar sites]
-   [Publicando com o Git][Publicando com o Git]

Para tutoriais de ponta a ponta, visite o [PHP Developer Center - tutoriais][PHP Developer Center - tutoriais] página.

  [sites do Azure]: /pt-br/manage/services/web-sites/
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com/
  [Ferramentas de Linha de Comando do Azure para Mac e Linux]: /pt-br/develop/php/how-to-guides/command-line-tools/
  [PowerShell do Azure]: /pt-br/develop/php/how-to-guides/powershell-cmdlets/
  [como configurar PHP em Sites do Azure]: /pt-br/develop/php/common-tasks/configure-php-web-site/
  [Sites de Preço do Azure]: http://www.windowsazure.com/pt-br/pricing/details/#header-1
  [Como dimensionar sites]: /pt-br/manage/services/web-sites/how-to-scale-websites/
  [Criar um site usando o Portal de Gerenciamento do Azure]: #portal
  [Criar um site usando as ferramentas de linha de comando do Azure para Mac e Linux.]: #XplatTools
  [Criar um site usando o PowerShell cmdlets do Azure]: #PowerShell
  [criar um site do Azure PHP MySQL e implantar usando gito]: /pt-br/develop/php/tutorials/website-w-mysql-and-git/
  [criar um site do WordPress da galeria no Azure]: /pt-br/develop/php/tutorials/website-from-gallery/
  [Selecione Criação Rápida de site.]: ./media/web-sites-php-create-web-sites/select-quickcreate-website.png
  [como instalar as ferramentas de linha de comando do Azure para Mac e Linux]: /pt-br/develop/php/how-to-guides/command-line-tools/#Download
  [como fazer o download e importar as configurações de publicação]: /pt-br/develop/php/how-to-guides/command-line-tools/#Account
  [como criar e gerenciar um Site do Azure]: /pt-br/develop/php/how-to-guides/command-line-tools/#WebSites
  [começar com o PowerShell Azure]: /pt-br/develop/php/how-to-guides/powershell-cmdlets/#GetStarted
  [Como: Importar configurações de publicação]: /pt-br/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings
  [Como: criar e gerenciar um Site do Azure]: /pt-br/develop/php/how-to-guides/powershell-cmdlets/#WebSite
  [Como configurar sites]: /pt-br/manage/services/web-sites/how-to-configure-websites/
  [Como gerenciar Sites]: /pt-br/manage/services/web-sites/how-to-manage-websites/
  [Como monitorar Sites]: /pt-br/manage/services/web-sites/how-to-monitor-websites/
  [Publicando com o Git]: /pt-br/develop/php/common-tasks/publishing-with-git/
  [PHP Developer Center - tutoriais]: /pt-br/develop/php/tutorials/
