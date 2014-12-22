<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Network Solutions" pageTitle="Configurar um nome de domínio do Network Solutions para um site do Azure que usa o Gerenciador de Tráfego" metaKeywords="Azure, sites do Azure, nome de domínio" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

#Configurando um nome de domínio personalizado para um site do Azure usando o Gerenciador de Tráfego (Soluções de Rede)

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Domínio personalizado</a><a href="/pt-br/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/pt-br/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions" class="current">Soluções de rede</a><a href="/pt-br/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/pt-br/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/pt-br/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/pt-br/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/pt-br/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/pt-br/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/web-sites-network-solutions-custom-domain-name/" title="Websites">Site</a> | <a href="/pt-br/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">Site usando o Gerenciador de Tráfego</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Este artigo fornece instruções sobre como usar o nome de domínio personalizado comprado da [Network Solutions](https://www.networksolutions.com) com sites do Azure.

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

Para associar seu domínio personalizado a um site do Azure, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pela Network Solutions. Use as seguintes etapas para localizar e usar as ferramentas DNS.

1. Faça logon na sua conta em networksolutions.com e selecione **Minha Conta** no canto superior direito.

3. Na guia **Meus Produtos e Serviços**, selecione **Editar DNS**.

	![edit dns page](./media/web-sites-custom-domain-name/ns-editdns.png)

2. Na seção **Gerenciar <yourdomainname>** da página **Nomes de Domínio**, selecione **Editar Registros DNS Avançados**.

	![domain names page with edit advanced dns records highlighted](./media/web-sites-custom-domain-name/ns-editadvanced.png)

4. A página **Atualizar DNS Avançado** contém uma seção para cada tipo de registro, com um botão **Editar** abaixo de cada seção. Para registros CNAME, use a seção **Alias de Host (Registros CNAME)**.

	![update advanced dns page](./media/web-sites-custom-domain-name/ns-updatecnameadvanced.png)

5. Ao clicar no botão **Editar**, você verá um formulário que pode usar para modificar registros existentes ou adicionar novos. 

 Ao adicionar um registro CNAME, você deve definir o campo **Alias** como o subdomínio que deseja usar. Por exemplo, **www**. Você deve selecionar o campo circular ao lado do campo **Outro host** e definir **Outro host** como o nome de domínio **.trafficmanager.net** do perfil do Gerenciador de Tráfego usado com o site do Azure. Por exemplo, **contoso.trafficmanager.net**. Deixe o **Refere-se ao Nome do Host** como **Selecionar**, pois esse campo não é obrigatório durante a criação de um registro CNAME a ser usado com sites do Azure.
	
	![cname form](./media/web-sites-custom-domain-name/ns-cnametm.png)

5. Ao concluir a adição ou a modificação dos registros, clique em **Continuar** para revisar as alterações. Selecione **Salvar apenas as alterações** para salvar as alterações.

<h2><a name="enabledomain"></a>Habilitar o site do Gerenciador de Tráfego</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]
