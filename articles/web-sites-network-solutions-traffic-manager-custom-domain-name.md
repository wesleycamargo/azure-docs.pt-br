<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Network Solutions" pageTitle="Configure a Network Solutions domain name for an Azure website that uses Traffic Manager" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth"></tags>

# Configurando um nome de domínio personalizado para um site do Azure usando o Gerenciador de Tráfego (Soluções de Rede)

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-BR/documentation/articles/web-sites-custom-domain-name" title="Dom&iacute;nio personalizado">Dom&iacute;nio personalizado</a><a href="/pt-BR/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/pt-BR/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Solu&ccedil;&otilde;es de rede" class="current">Solu&ccedil;&otilde;es de rede</a><a href="/pt-BR/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/pt-BR/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/pt-BR/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/pt-BR/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/pt-BR/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/pt-BR/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/pt-BR/documentation/articles/web-sites-network-solutions-custom-domain-name/" title="Sites">Sites</a> | <a href="/pt-BR/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/" title="Site usando o Gerenciador de Tr&aacute;fego" class="current">Site usando o Gerenciador de Tr&aacute;fego</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][websites-cloud-services-css-guided-walkthrough]]

[WACOM.INCLUDE [intro][intro]]

Este artigo fornece instruções sobre como usar o nome de domínio personalizado comprado junto a [Network Solutions][Network Solutions] com sites do Azure.

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

Para associar seu domínio personalizado a um site do Azure, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pela Network Solutions. Use as seguintes etapas para localizar e usar as ferramentas DNS.

1.  Faça logon na sua conta em networksolutions.com e selecione **Minha Conta** no canto superior direito.

2.  Na guia **Meus Produtos e Serviços**, selecione **Editar DNS**.

    ![editar página dns][editar página dns]

3.  Na seção **Gerenciar <yourdomainname>** da página **Nomes de Domínio**, selecione **Editar Registros DNS Avançados**.

    ![página de nomes de domínio com editar registros dns avançados realçado][página de nomes de domínio com editar registros dns avançados realçado]

4.  A página **Atualizar DNS Avançado** contém uma seção para cada tipo de registro, com um botão **Editar** abaixo de cada seção. Para registros CNAME, use a seção **Alias de Host (Registros CNAME)**.

    ![atualizar página dns avançado][atualizar página dns avançado]

5.  Ao clicar no botão **Editar**, você verá um formulário que pode usar para modificar registros existentes ou adicionar novos.

    Ao adicionar um registro CNAME, você deve definir o campo **Alias** como o subdomínio que deseja usar. Por exemplo, **www**. Você deve selecionar o campo circular ao lado do campo **Outro host** e definir **Outro host** como o nome de domínio **.trafficmanager.net** do perfil do Gerenciador de Tráfego usado com o site do Azure. Por exemplo, **contoso.trafficmanager.net**. Deixe o **Refere-se ao Nome do Host** como **Selecionar**, pois esse campo não é obrigatório durante a criação de um registro CNAME a ser usado com sites do Azure.

    ![formulário cname][formulário cname]

6.  Ao concluir a adição ou a modificação dos registros, clique em **Continuar** para revisar as alterações. Selecione **Salvar apenas as alterações** para salvar as alterações.

## <a name="enabledomain"></a>Habilitar o site do Gerenciador de Tráfego

[WACOM.INCLUDE [modes][1]]

  [Domínio personalizado]: /pt-BR/documentation/articles/web-sites-custom-domain-name "Domínio personalizado"
  [GoDaddy]: /pt-BR/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy"
  [Soluções de rede]: /pt-BR/documentation/articles/web-sites-network-solutions-custom-domain-name "Soluções de rede"
  [Register.com]: /pt-BR/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [Enom]: /pt-BR/documentation/articles/web-sites-enom-custom-domain-name "Enom"
  [Moniker]: /pt-BR/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [Dotster]: /pt-BR/documentation/articles/web-sites-dotster-custom-domain-name "Dotster"
  [DomainDiscover]: /pt-BR/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover"
  [Directnic]: /pt-BR/documentation/articles/web-sites-directnic-custom-domain-name "Directnic"
  [Sites]: /pt-BR/documentation/articles/web-sites-network-solutions-custom-domain-name/ "Sites"
  [Site usando o Gerenciador de Tráfego]: /pt-BR/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/ "Site usando o Gerenciador de Tráfego"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro-traffic-manager.md
  [Network Solutions]: https://www.networksolutions.com
  [tmwebsitefooter]: ../includes/custom-dns-web-site-traffic-manager-notes.md
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Compreendendo os registros DNS]: #understanding-records
  [Configurar seus sites para modo padrão]: #bkmk_configsharedmode
  [Adicionar um registro DNS a seu domínio personalizado]: #bkmk_configurecname
  [Habilitar o Gerenciador de Tráfego para o seu site]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-traffic-manager.md
  [modes]: ../includes/custom-dns-web-site-modes-traffic-manager.md
  [editar página dns]: ./media/web-sites-custom-domain-name/ns-editdns.png
  [página de nomes de domínio com editar registros dns avançados realçado]: ./media/web-sites-custom-domain-name/ns-editadvanced.png
  [atualizar página dns avançado]: ./media/web-sites-custom-domain-name/ns-updatecnameadvanced.png
  [formulário cname]: ./media/web-sites-custom-domain-name/ns-cnametm.png
  [1]: ../includes/custom-dns-web-site-enable-on-traffic-manager.md
