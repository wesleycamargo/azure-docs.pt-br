<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with GoDaddy" pageTitle="Configure a GoDaddy domain name for an Azure website that uses Traffic Manager" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth"></tags>

# Configurando um nome de domínio personalizado para um site do Azure usando o Gerenciador de Tráfego (GoDaddy)

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-BR/documentation/articles/web-sites-custom-domain-name" title="Dom&iacute;nio personalizado">Dom&iacute;nio personalizado</a><a href="/pt-BR/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy" class="current">GoDaddy</a><a href="/pt-BR/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Solu&ccedil;&otilde;es de rede" class="current">Solu&ccedil;&otilde;es de rede</a><a href="/pt-BR/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/pt-BR/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/pt-BR/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/pt-BR/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/pt-BR/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/pt-BR/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/pt-BR/documentation/articles/web-sites-godaddy-custom-domain-name/" title="Sites">Sites</a> | <a href="/pt-BR/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/" title="Site usando o Gerenciador de Tr&aacute;fego" class="current">Site usando o Gerenciador de Tr&aacute;fego</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][websites-cloud-services-css-guided-walkthrough]]

[WACOM.INCLUDE [intro][intro]]

Este artigo fornece instruções sobre como usar um nome de domínio personalizado adquirido do [GoDaddy][1] com os Sites do Azure.

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

Para associar seu domínio personalizado a um Site do Azure, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo GoDaddy. Use as seguintes etapas para localizar as ferramentas DNS para o GoDaddy.com

1.  Faça logon na sua conta do GoDaddy.com e selecione **Minha Conta** e, em seguida, **Gerenciar seus domínios**. Finalmente, selecione o nome do domínio que você deseja usar com o Site do Azure.

    ![página de domínio personalizado para GoDaddy][página de domínio personalizado para GoDaddy]

2.  Na página **Detalhes do domínio**, selecione a guia **Arquivo de zona DNS**. Esta é a seção usada para adicionar e modificar registros DNS para o nome do domínio. Selecione o botão **Editar** para exibir o **Editor de Arquivo de Zona**.

    ![Guia Arquivo de Zona DNS][Guia Arquivo de Zona DNS]

3.  O **Editor de Arquivo de Zona** é dividido em seções para cada tipo de registro, começando com os registros A (listados como **A (Host)** como a primeira seção, seguida por registros CNAME (listados como **CNAME (Alias)**). Para adicionar uma nova entrada, use o botão **Adição Rápida** abaixo da seção correspondente. Para editar uma entrada existente, selecione essa entrada e modifique as informações existentes.

    ![editor de arquivo de zona][editor de arquivo de zona]

    > [WACOM.NOTE] Antes de adicionar as entradas ao arquivo de zona, observe que o GoDaddy já criou os registros DNS para subdomínios populares (chamados **Host** no editor), como **email**, **arquivos**, **mail** e outros. Se o nome que você deseja usar já existir, modifique o registro existente em vez de criar um novo.

    Ao adicionar um registro CNAME, você deve definir o campo **Host** como o subdomínio que deseja usar. Por exemplo, **www**. Você deve definir o campo **Aponta para** como o nome do domínio **.trafficmanager.net** do perfil do Gerenciador de Tráfego que você está usando com o seu Site do Azure. Por exemplo, **contoso.trafficmanager.net**.

    > [WACOM.NOTE] Você só deve usar registros CNAME ao associar o seu nome de domínio personalizado a um site que cuja carga é balanceada com o Gerenciador de Tráfego.

4.  Ao concluir a adição ou a modificação dos registros, clique em **Salvar Arquivo de Zona** para salvar as alterações.

## <a name="enabledomain"></a>Habilitar o site do Gerenciador de Tráfego

[WACOM.INCLUDE [modes][2]]

  [Domínio personalizado]: /pt-BR/documentation/articles/web-sites-custom-domain-name "Domínio personalizado"
  [GoDaddy]: /pt-BR/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy"
  [Soluções de rede]: /pt-BR/documentation/articles/web-sites-network-solutions-custom-domain-name "Soluções de rede"
  [Register.com]: /pt-BR/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [Enom]: /pt-BR/documentation/articles/web-sites-enom-custom-domain-name "Enom"
  [Moniker]: /pt-BR/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [Dotster]: /pt-BR/documentation/articles/web-sites-dotster-custom-domain-name "Dotster"
  [DomainDiscover]: /pt-BR/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover"
  [Directnic]: /pt-BR/documentation/articles/web-sites-directnic-custom-domain-name "Directnic"
  [Sites]: /pt-BR/documentation/articles/web-sites-godaddy-custom-domain-name/ "Sites"
  [Site usando o Gerenciador de Tráfego]: /pt-BR/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/ "Site usando o Gerenciador de Tráfego"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro-traffic-manager.md
  [1]: https://godaddy.com
  [tmwebsitefooter]: ../includes/custom-dns-web-site-traffic-manager-notes.md
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Compreendendo os registros DNS]: #understanding-records
  [Configurar seus sites para modo padrão]: #bkmk_configsharedmode
  [Adicionar um registro DNS a seu domínio personalizado]: #bkmk_configurecname
  [Habilitar o Gerenciador de Tráfego para o seu site]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-traffic-manager.md
  [modes]: ../includes/custom-dns-web-site-modes-traffic-manager.md
  [página de domínio personalizado para GoDaddy]: ./media/web-sites-custom-domain-name/godaddy-customdomain.png
  [Guia Arquivo de Zona DNS]: ./media/web-sites-custom-domain-name/godaddy-zonetab.png
  [editor de arquivo de zona]: ./media/web-sites-custom-domain-name/godaddy-quickaddcname.png
  [2]: ../includes/custom-dns-web-site-enable-on-traffic-manager.md
