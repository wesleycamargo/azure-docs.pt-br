<properties title="Learn how to configure an Azure website to use a domain name registered with DomainDiscover - TierraNet" pageTitle="Configure a DomainDiscover domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, DomainDiscover, TierraNet" description="Learn how to configure an Azure website to use a domain name registered with DomainDiscover - TierraNet" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth"></tags>

# Configurando um nome de domínio personalizado para um site do Azure (DomainDiscover / TierraNet)

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-BR/documentation/articles/web-sites-custom-domain-name" title="Dom&iacute;nio personalizado">Dom&iacute;nio personalizado</a><a href="/pt-BR/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/pt-BR/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Solu&ccedil;&otilde;es de rede">Solu&ccedil;&otilde;es de rede</a><a href="/pt-BR/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/pt-BR/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/pt-BR/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/pt-BR/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/pt-BR/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/pt-BR/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/pt-BR/documentation/articles/web-sites-domaindiscover-custom-domain-name/" title="Sites" class="current">Sites</a> | <a href="/pt-BR/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/" title="Site usando o Gerenciador de Tr&aacute;fego">Site usando o Gerenciador de Tr&aacute;fego</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][websites-cloud-services-css-guided-walkthrough]]

[WACOM.INCLUDE [intro][intro]]

Este artigo fornece instruções sobre como usar um nome de domínio personalizado adquirido do [DomainDiscover.com][DomainDiscover.com] com os Sites do Azure. O DomainDiscover.com faz parte do TierraNet.

[WACOM.INCLUDE [introfooter][introfooter]]

Neste artigo:

-   [Compreendendo os registros DNS][Compreendendo os registros DNS]
-   [Configurar seus sites para modo básico, compartilhado ou padrão][Configurar seus sites para modo básico, compartilhado ou padrão]
-   [Adicionar um registro DNS a seu domínio personalizado][Adicionar um registro DNS a seu domínio personalizado]
-   [Habilitar o domínio no site][Habilitar o domínio no site]

## <a name="understanding-records"></a>Compreendendo os registros DNS

[WACOM.INCLUDE [understandingdns][understandingdns]]

## <a name="bkmk_configsharedmode"></a>Configurar seus sites para modo básico, compartilhado ou padrão

[WACOM.INCLUDE [modes][modes]]

<a name="bkmk_configurecname"></a>

## Adicionar um registro DNS a seu domínio personalizado

Para associar seu domínio personalizado a um Site do Azure, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo DomainDiscover. Use as seguintes etapas para localizar as ferramentas DNS para o DomainDiscover.com

1.  Faça logon em sua conta do DomainDiscover.com (TierraNet) selecionando **Painel de Controle** no menu **Logon**.

    ![Menu de logon do DomainDiscover][Menu de logon do DomainDiscover]

2.  Na página **Serviços de Domínio**, selecione o domínio que você deseja usar para o site do Azure.

    ![Página Gerenciamento de domínio][Página Gerenciamento de domínio]

3.  Em Configurações de domínio, clique no botão **Editar** do **Serviço DNS**.

    ![Botão Editar DNS][Botão Editar DNS]

4.  Na janela **Gerenciar DNS**, selecione o tipo de registro DNS a ser adicionado na lista **Adicionar Registros**. Em seguida, clique no botão **Adicionar**.

    ![Botão Editar DNS][1]

5.  Na página seguinte, digite os valores dos registros DNS. Em seguida, clique no botão **Adicionar**.

    ![Botão Editar DNS][2]

    -   Ao adicionar um registro CNAME, você deve primeiro selecionar **CNAME (Alias)** na página **Gerenciar DNS**. Em seguida, defina o campo **Host** como o subdomínio que deseja usar. Por exemplo, **www**. Você deve definir o campo **Nome do Host do Alias** como o nome do domínio **.azurewebsites.net** do Site do Azure. Por exemplo, **contoso.azurwebsites.net**. Em seguida, forneça um valor de TTL (vida útil), como 1800 segundos.

    -   Ao adicionar um registro A, você deve primeiro selecionar **A** na página **Gerenciar DNS**. Em seguida, defina o campo **Host** como **@** (isso representa o nome do domínio raiz, como **contoso.com**) ou o subdomínio que deseja usar (por exemplo, **www**). Você deve definir o campo **Endereço IP** como o endereço IP do seu Site do Azure. Em seguida, forneça um valor de TTL (vida útil), como 1800 segundos.

        > [WACOM.NOTE] Se estiver usando um registro A, você também deverá adicionar um registro CNAME com uma das seguintes configurações:
        >
        > -   Um valor **Nome** de **www** com um valor **Nome de Host do Alias** de **\<yourwebsitename\>.azurewebsites.net**.
        >
        > OU
        >
        > -   Um valor **Nome** de **awverify.www** com um valor **Nome de Host do Alias** de **awverify.\<yourwebsitename\>.azurewebsites.net**.
        >
        > Esse registro CNAME é usado pelo Azure para validar que você possui o domínio descrito pelo registro A

6.  Se tiver adicionado um registro A, você poderá obter um aviso de que o registro A existente de seu domínio não está inativo. Ele usa o registro alterado mais recentemente, e o DomainDirect já tem um registro A padrão para o nome do domínio raiz. Você pode contar com essa precedência, ou remover o registro A padrão selecionando o botão **EXCLUIR**.

## <a name="enabledomain"></a>Habilitar o nome do domínio no seu site

[WACOM.INCLUDE [modes][3]]

  [Domínio personalizado]: /pt-BR/documentation/articles/web-sites-custom-domain-name "Domínio personalizado"
  [GoDaddy]: /pt-BR/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy"
  [Soluções de rede]: /pt-BR/documentation/articles/web-sites-network-solutions-custom-domain-name "Soluções de rede"
  [Register.com]: /pt-BR/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [Enom]: /pt-BR/documentation/articles/web-sites-enom-custom-domain-name "Enom"
  [Moniker]: /pt-BR/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [Dotster]: /pt-BR/documentation/articles/web-sites-dotster-custom-domain-name "Dotster"
  [DomainDiscover]: /pt-BR/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover"
  [Directnic]: /pt-BR/documentation/articles/web-sites-directnic-custom-domain-name "Directnic"
  [Sites]: /pt-BR/documentation/articles/web-sites-domaindiscover-custom-domain-name/ "Sites"
  [Site usando o Gerenciador de Tráfego]: /pt-BR/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/ "Site usando o Gerenciador de Tráfego"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro.md
  [DomainDiscover.com]: https://domaindiscover.com
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Compreendendo os registros DNS]: #understanding-records
  [Configurar seus sites para modo básico, compartilhado ou padrão]: #bkmk_configsharedmode
  [Adicionar um registro DNS a seu domínio personalizado]: #bkmk_configurecname
  [Habilitar o domínio no site]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-raw.md
  [modes]: ../includes/custom-dns-web-site-modes.md
  [Menu de logon do DomainDiscover]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_LoginMenu.png
  [Página Gerenciamento de domínio]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DomainManagement.png
  [Botão Editar DNS]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSEditButton.png
  [1]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSAddRecords.png
  [2]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSRecords.png
  [3]: ../includes/custom-dns-web-site-enable-on-web-site.md
