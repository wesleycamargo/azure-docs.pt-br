<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Directnic" pageTitle="Configure a Directnic domain name for an Azure website using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, Directnic, Traffic Manager" description="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Directnic" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth"></tags>

# Configurando um nome de domínio personalizado para um site do Windows Azure usando o Gerenciador de Tráfego (Directnic)

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/web-sites-custom-domain-name" title="Dom&iacute;nio personalizado">Dom&iacute;nio personalizado</a><a href="/pt-br/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/pt-br/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Solu&ccedil;&otilde;es de rede">Solu&ccedil;&otilde;es de rede</a><a href="/pt-br/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/pt-br/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/pt-br/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/pt-br/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/pt-br/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/pt-br/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/web-sites-directnic-custom-domain-name/" title="Sites">Sites</a> | <a href="/pt-br/documentation/articles/web-sites-directnic-traffic-manager-custom-domain-name/" title="Site usando o Gerenciador de Tr&aacute;fego" class="current">Site usando o Gerenciador de Tr&aacute;fego</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][websites-cloud-services-css-guided-walkthrough]]

[WACOM.INCLUDE [intro][intro]]

Este artigo fornece instruções sobre como usar um nome de domínio personalizado adquirido do [Directnic.com][Directnic.com] com os Sites do Azure.

[WACOM.INCLUDE [tmwebsitefooter][tmwebsitefooter]]

[WACOM.INCLUDE [introfooter][introfooter]]

Neste artigo:

-   [Compreendendo os registros DNS][Compreendendo os registros DNS]
-   [Configurar seus sites para modo padrão][Configurar seus sites para modo padrão]
-   [Adicionar um registro DNS a seu domínio personalizado][Adicionar um registro DNS a seu domínio personalizado]
-   [Habilitar o Gerenciador de Tráfego para o seu site][Habilitar o Gerenciador de Tráfego para o seu site]

## <a name="understanding-records"></a>Compreendendo os registros DNS

[WACOM.INCLUDE [understandingdns][understandingdns]]

## <a name="bkmk_configsharedmode"></a>Configurar seus sites para modo padrão

[WACOM.INCLUDE [modes][modes]]

<a name="bkmk_configurecname"></a>

## Adicionar um registro DNS a seu domínio personalizado

</p>
Para associar seu domínio personalizado a um Site do Azure, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo Directnic. Use as seguintes etapas para localizar as ferramentas DNS para o Directnic.com

1.  Faça logon na sua conta do Directnic.com e selecione **Meus Serviços** e, em seguida, **Domínios**.

    ![Menu de serviços do Directnic][Menu de serviços do Directnic]

2.  Clique no nome do domínio que você deseja usar com o Site do Azure.

3.  Na página de gerenciamento de seu domínio, clique no botão **Gerenciar** do **DNS** no painel **Serviços**.

    ![Painel Serviços][Painel Serviços]

4.  Adicione registros DNS preenchendo os campos **Tipo**, **Nome** e **Dados**. Ao concluir, clique no botão **Adicionar Registro**.

    ![][]

    -   Ao adicionar um registro CNAME, você deve definir o campo **Nome** como o subdomínio que deseja usar. Por exemplo, **www**. Você deve definir o campo **Dados** como o nome do domínio **.trafficmanager.net** do perfil do Gerenciador de Tráfego que você está usando com o seu Site do Azure. Por exemplo, **contoso.trafficmanager.net**.

        > [WACOM.NOTE] Você só deve usar registros CNAME ao associar o seu nome de domínio personalizado a um site que cuja carga é balanceada com o Gerenciador de Tráfego.

## <a name="enabledomain"></a>Habilitar o site do Gerenciador de Tráfego

[WACOM.INCLUDE [modes][1]]

  [Domínio personalizado]: /pt-br/documentation/articles/web-sites-custom-domain-name "Domínio personalizado"
  [GoDaddy]: /pt-br/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy"
  [Soluções de rede]: /pt-br/documentation/articles/web-sites-network-solutions-custom-domain-name "Soluções de rede"
  [Register.com]: /pt-br/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [Enom]: /pt-br/documentation/articles/web-sites-enom-custom-domain-name "Enom"
  [Moniker]: /pt-br/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [Dotster]: /pt-br/documentation/articles/web-sites-dotster-custom-domain-name "Dotster"
  [DomainDiscover]: /pt-br/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover"
  [Directnic]: /pt-br/documentation/articles/web-sites-directnic-custom-domain-name "Directnic"
  [Sites]: /pt-br/documentation/articles/web-sites-directnic-custom-domain-name/ "Sites"
  [Site usando o Gerenciador de Tráfego]: /pt-br/documentation/articles/web-sites-directnic-traffic-manager-custom-domain-name/ "Site usando o Gerenciador de Tráfego"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro-traffic-manager.md
  [Directnic.com]: https://directnic.com
  [tmwebsitefooter]: ../includes/custom-dns-web-site-traffic-manager-notes.md
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Compreendendo os registros DNS]: #understanding-records
  [Configurar seus sites para modo padrão]: #bkmk_configsharedmode
  [Adicionar um registro DNS a seu domínio personalizado]: #bkmk_configurecname
  [Habilitar o Gerenciador de Tráfego para o seu site]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-traffic-manager.md
  [modes]: ../includes/custom-dns-web-site-modes-traffic-manager.md
  [Menu de serviços do Directnic]: .\media\web-sites-directnic-custom-domain-name\Directnic_DomainMenu.png
  [Painel Serviços]: .\media\web-sites-directnic-custom-domain-name\Directnic_DomainManagement.png
  []: .\media\web-sites-directnic-custom-domain-name\Directnic_DNS_TM.png
  [1]: ../includes/custom-dns-web-site-enable-on-traffic-manager.md
