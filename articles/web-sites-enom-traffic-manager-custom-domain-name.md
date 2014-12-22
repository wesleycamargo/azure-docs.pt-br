<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with eNom" pageTitle="Configurar um nome de domínio eNom para um site do Azure que usa o Gerenciador de Tráfego" metaKeywords="Microsoft Azure, sites do Microsoft Azure, nome de domínio" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

#Configurando um nome de domínio personalizado para um site do Azure usando o Gerenciador de Tráfego (eNom)

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Domínio personalizado</a><a href="/pt-br/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/pt-br/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Soluções de rede</a><a href="/pt-br/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/pt-br/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/pt-br/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/pt-br/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/pt-br/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/pt-br/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/web-sites-enom-custom-domain-name/" title="Websites">Site</a> | <a href="/pt-br/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">Site usando o Gerenciador de Tráfego</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Este artigo fornece instruções sobre como usar um nome de domínio personalizado adquirido do [eNom](https://enom.com) com os sites do Azure.

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

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a><h2>Adicionar um registro DNS a seu domínio personalizado</h2>

Para associar seu domínio personalizado a um site do Azure, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo eNom. Use as seguintes etapas para localizar as ferramentas DNS para o enom.com

1. Faça logon na sua conta do eNom e selecione **Domínios** e, em seguida, **Meus Domínios**. Isso exibirá os nomes de seus domínios.

2. Na página **Meus Domínios**, use o campo **Gerenciar Domínio** para selecionar **Registros do Host**. Isso exibirá os campos de registros de host.

	![DNS Zone File tab](./media/web-sites-custom-domain-name/e-hostrecords.png)

4. O editor de Registros do Host permite que você selecione o tipo de registro específico usando o campo **Tipo de Registro**. Para sites do Azure que usam o Gerenciador de Tráfego, você deve usar somente a seleção **CNAME (Alias)**, pois o Gerenciador de Tráfego só funciona com registros CNAME.

	![zone file editor](./media/web-sites-custom-domain-name/e-editrecordstm.png)

	*  Ao adicionar um registro CNAME, você deve definir o campo **Nome do Host** como o subdomínio que deseja usar. Por exemplo, **www**. Você deve definir o campo **Endereço** como o nome do domínio **.trafficmanager.net** do perfil do Gerenciador de Tráfego usado com o seu Site do Azure. Por exemplo, **contoso.trafficmanager.net**.

5. Ao concluir a adição ou a modificação dos registros, clique em **Salvar** para salvar as alterações.

<h2><a name="enabledomain"></a>Habilitar o site do Gerenciador de Tráfego</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]
