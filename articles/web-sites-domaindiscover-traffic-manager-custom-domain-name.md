<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with DomainDiscover - TierraNet" pageTitle="Configure a DomainDiscover domain name for an Azure website using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, DomainDiscover, TierraNet, Traffic Manager" description="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with DomainDiscover - TierraNet" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth" />

# Configurando um nome de domínio personalizado para um site do Windows Azure usando o Gerenciador de Tráfego (DomainDiscover / TierraNet)

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-BR/documentation/articles/web-sites-custom-domain-name" title="Dom&iacute;nio personalizado">Dom&iacute;nio personalizado</a><a href="/pt-BR/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/pt-BR/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Solu&ccedil;&otilde;es de rede">Solu&ccedil;&otilde;es de rede</a><a href="/pt-BR/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/pt-BR/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/pt-BR/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/pt-BR/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/pt-BR/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/pt-BR/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/pt-BR/documentation/articles/web-sites-domaindiscover-custom-domain-name/" title="Sites">Sites</a> | <a href="/pt-BR/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/" title="Site usando o Gerenciador de Tr&aacute;fego" class="current">Site usando o Gerenciador de Tr&aacute;fego</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Este artigo fornece instruções sobre como usar um nome de domínio personalizado adquirido do [DomainDiscover.com][DomainDiscover.com] com os Sites do Azure. O DomainDiscover.com faz parte do [TierraNet][TierraNet].

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Neste artigo:

-   [Compreendendo os registros DNS][Compreendendo os registros DNS]
-   [Configurar seus sites para modo padrão][Configurar seus sites para modo padrão]
-   [Adicionar um registro DNS a seu domínio personalizado][Adicionar um registro DNS a seu domínio personalizado]
-   [Habilitar o Gerenciador de Tráfego para o seu site][Habilitar o Gerenciador de Tráfego para o seu site]

## <a name="understanding-records"></a>Compreendendo os registros DNS

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

## <a name="bkmk_configsharedmode"></a>Configurar seus sites para modo padrão

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

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

    -   Ao adicionar um registro CNAME, você deve primeiro selecionar **CNAME (Alias)** na página **Gerenciar DNS**. Em seguida, defina o campo **Host** como o subdomínio que deseja usar. Por exemplo, **www**. Você deve definir o campo **Nome do Host do Alias** como o nome do domínio **.trafficmanager.net** do perfil do Gerenciador de Tráfego que você está usando com o seu Site do Azure. Por exemplo, **contoso.trafficmanager.net**. Em seguida, forneça um valor de TTL (vida útil), como 1800 segundos.

        > [WACOM.NOTE] Você só deve usar registros CNAME ao associar o seu nome de domínio personalizado a um site que cuja carga é balanceada com o Gerenciador de Tráfego.

## <a name="enabledomain"></a>Habilitar o site do Gerenciador de Tráfego

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

  [DomainDiscover.com]: https://domaindiscover.com
  [TierraNet]: https://www.tierra.net/
  [Compreendendo os registros DNS]: #understanding-records
  [Configurar seus sites para modo padrão]: #bkmk_configsharedmode
  [Adicionar um registro DNS a seu domínio personalizado]: #bkmk_configurecname
  [Habilitar o Gerenciador de Tráfego para o seu site]: #enabledomain
  [Menu de logon do DomainDiscover]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_LoginMenu.png
  [Página Gerenciamento de domínio]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DomainManagement.png
  [Botão Editar DNS]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSEditButton.png
  [1]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSAddRecords.png
  [2]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSRecords_TM.png
