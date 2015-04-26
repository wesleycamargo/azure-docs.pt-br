<properties 
	pageTitle="Configurar um nome de domínio DomainDiscover para um site do Azure usando o Gerenciador de tráfego" 
	description="Saiba como configurar um site do Azure que usa o Gerenciador de Tráfego para usar um nome de domínio registrado com o DomainDiscover - TierraNet" 
	services="web-sites" 
	documentationCenter="" 
	authors="blackmist" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr,jroth"/>

#Configurando um nome de domínio personalizado para um site do Microsoft Azure usando o Gerenciador de Tráfego (DomainDiscover / TierraNet)

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Domínio personalizado</a><a href="/pt-br/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/pt-br/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">soluções de rede</a><a href="/pt-br/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/pt-br/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/pt-br/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/pt-br/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/pt-br/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/pt-br/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/web-sites-domaindiscover-custom-domain-name/" title="Websites">Site</a> |<a href="/pt-br/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">site usando o Gerenciador de tráfego</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [Introdução](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Este artigo fornece instruções sobre como utilizar um nome de domínio personalizado adquirido do [DomainDiscover.com](https://domaindiscover.com) com os Sites do Azure. O DomainDiscover.com faz parte do [TierraNet](https://www.tierra.net/).

[AZURE.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Neste artigo:

-   [Compreendendo os registros DNS](#understanding-records)
-   [Configurar seus sites para modo padrão](#bkmk_configsharedmode)
-   [Adicionar um registro DNS a seu domínio personalizado](#bkmk_configurecname)
-   [Habilitar o Gerenciador de Tráfego para o seu site](#enabledomain)

<h2><a name="understanding-records"></a>Compreendendo os registros DNS</h2>

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<h2><a name="bkmk_configsharedmode"></a>Configurar seus sites para modo padrão</h2>

[AZURE.INCLUDE [modos](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a><h2>Adicionar um registro DNS a seu domínio personalizado</h2>

Para associar seu domínio personalizado a um Site do Azure, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado utilizando as ferramentas fornecidas pelo DomainDiscover. Use as seguintes etapas para localizar as ferramentas DNS para o DomainDiscover.com

1. Faça logon em sua conta do DomainDiscover.com (TierraNet) selecionando **Painel de Controle** no menu **Logon**.

    ![Menu de logon do DomainDiscover](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_LoginMenu.png)

2. Na página **Serviços de domínio**, selecione o domínio que você deseja utilizar para o site do Azure.

    ![Página Gerenciamento de domínio](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DomainManagement.png)

3. Em Configurações de domínio, clique no botão **Editar** do **Serviço DNS**.

    ![Botão Editar DNS](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSEditButton.png)

4. Na janela **Gerenciar DNS**, selecione o tipo de registro DNS a ser adicionado na lista **Adicionar Registros**. Em seguida, clique no botão **Adicionar**.

    ![Botão Editar DNS](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSAddRecords.png)

5. Na página seguinte, digite os valores dos registros DNS. Em seguida, clique no botão **Adicionar**.

    ![Botão Editar DNS](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSRecords_TM.png)

    * Ao adicionar um registro CNAME, você deve primeiro selecionar **CNAME (Alias)** na página **Gerenciar DNS**. Em seguida, defina o campo **Host** como o subdomínio que deseja usar. Por exemplo, **www**. Você deve definir o campo **Nome do Host do Alias** como o nome do domínio **.trafficmanager.net** do perfil do Gerenciador de Tráfego que você está usando com o seu Site do Azure. Por exemplo, **contoso.trafficmanager.net**. Em seguida, forneça um valor de (TTL) (vida útil), como 1800 segundos.

	    > [AZURE.NOTE] Você só deve usar registros CNAME ao associar o seu nome de domínio personalizado a um site cuja carga é balanceada com o Gerenciador de Tráfego.

<h2><a name="enabledomain"></a>Habilitar o site do Gerenciador de Tráfego</h2>

[AZURE.INCLUDE [modos](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]



<!--HONumber=42-->
