<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with GoDaddy" pageTitle="Configurar um nome de domínio GoDaddy para um site do Azure usando o Gerenciador de Tráfego" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

#Configurando um nome de domínio personalizado para um site do Azure usando o Gerenciador de Tráfego (GoDaddy)

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Domínio personalizado</a><a href="/pt-br/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy" class="current">GoDaddy</a><a href="/pt-br/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Soluções de rede</a><a href="/pt-br/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/pt-br/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/pt-br/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/pt-br/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/pt-br/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/pt-br/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/web-sites-godaddy-custom-domain-name/" title="Websites">Site</a> | <a href="/pt-br/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">Site usando o Gerenciador de Tráfego</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [Introdução](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Este artigo fornece instruções sobre como usar um nome de domínio personalizado adquirido do [Go Daddy](https://godaddy.com) com os sites do Azure.

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Neste artigo:

-   [Compreendendo os registros DNS](#understanding-records)
-   [Configurar seus sites para modo padrão](#bkmk_configsharedmode)
-   [Adicionar um registro DNS a seu domínio personalizado](#bkmk_configurecname)
-   [Habilitar o Gerenciador de Tráfego para o seu site](#enabledomain)

<h2><a name="understanding-records"></a>Compreendendo os registros DNS</h2>

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<h2><a name="bkmk_configsharedmode"></a>Configurar seus sites para modo padrão</h2>

[WACOM.INCLUDE [modos](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a><h2>Adicionar um registro DNS a seu domínio personalizado</h2>

Para associar seu domínio personalizado a um site do Azure, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo GoDaddy. Use as seguintes etapas para localizar as ferramentas DNS para o GoDaddy.com

1. Faça logon na sua conta do GoDaddy.com e selecione **Minha Conta** e, em seguida, **Gerenciar seus domínios**. Finalmente, selecione o nome do domínio que você deseja usar com o site do Azure.

	![custom domain page for GoDaddy](./media/web-sites-custom-domain-name/godaddy-customdomain.png)

2. Na página **Detalhes do domínio**, selecione a guia **Arquivo de zona DNS**. Esta é a seção usada para adicionar e modificar registros DNS para o nome do domínio. Selecione o botão **Editar** para exibir o **Editor de Arquivo de Zona**.

	![DNS Zone File tab](./media/web-sites-custom-domain-name/godaddy-zonetab.png)

4. O **Editor de Arquivo de Zona** é dividido em seções para cada tipo de registro, começando com os registros A (listados como **A (Host)** como a primeira seção, seguida por registros CNAME (listados como **CNAME (Alias)**.) Para adicionar uma nova entrada, use o botão **Adição Rápida** abaixo da seção correspondente. Para editar uma entrada existente, selecione essa entrada e modifique as informações existentes.

	![zone file editor](./media/web-sites-custom-domain-name/godaddy-quickaddcname.png)

	> [WACOM.NOTE] Antes de adicionar as entradas ao arquivo de zona, observe que o GoDaddy já criou os registros DNS para subdomínios populares (chamados **Host** no editor), como **email**, **arquivos**, **mail** e outros. Se o nome que você deseja usar já existir, modifique o registro existente em vez de criar um novo.

Ao adicionar um registro CNAME, você deve definir o campo **host** como o subdomínio que deseja usar. Por exemplo, **www**. Você deve definir o campo **Aponta para** como o nome do domínio **.trafficmanager.net** do perfil do Gerenciador de Tráfego que você está usando com o seu Site do Azure. Por exemplo, **contoso.trafficmanager.net**.

	> [WACOM.NOTE] Você só deve usar registros CNAME ao associar o seu nome de domínio personalizado a um site cuja carga é balanceada com o Gerenciador de Tráfego.

5. Ao concluir a adição ou a modificação dos registros, clique em **Salvar Arquivo de Zona** para salvar as alterações.

<h2><a name="enabledomain"></a>Habilitar o site do Gerenciador de Tráfego</h2>

[WACOM.INCLUDE [modos](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]
