<properties 
	pageTitle="Configurar um nome de domínio Moniker para um site do Azure usando o Gerenciador de tráfego" 
	description="Saiba como configurar um site do Azure que usa o Gerenciador de Tráfego para usar um nome de domínio registrado com o Moniker" 
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

#Configurando um nome de domínio personalizado para um site do Azure usando o Gerenciador de Tráfego (Moniker)

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Domínio personalizado</a><a href="/pt-br/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/pt-br/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">soluções de rede</a><a href="/pt-br/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/pt-br/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/pt-br/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/pt-br/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/pt-br/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/pt-br/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/web-sites-moniker-custom-domain-name/" title="Websites">Site</a> | <a href="/pt-br/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">Site usando o Gerenciador de Tráfego</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Este artigo fornece instruções sobre como usar um nome de domínio personalizado adquirido do [Moniker](https://moniker.com) com os sites do Azure.

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

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a><h2>Adicionar um registro DNS a seu domínio personalizado</h2>

Para associar seu domínio personalizado a um site do Azure, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo Moniker. Use as seguintes etapas para localizar as ferramentas DNS para Moniker.com

1. Faça logon na sua conta de Moniker.com e selecione **Meus Domínios** e, em seguida, clique em **Gerenciar Modelos**.

    ![My Domains page for Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_MyDomains.png)

2. Na página **Gerenciamento do Modelo de Zona**, selecione **Criar Novo Modelo**.

    ![Moniker Zone Template Management](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneManager.png)

3. Preencha o **Nome do Modelo**. 

5. Em seguida, crie um registro DNS selecionando primeiro o **Tipo de Registro**. Em seguida, preencha o **Nome do Host** e o **Endereço**. 

    ![Moniker Create Zone Template](.\media\web-sites-moniker-custom-domain-name\Moniker_CreateZoneTemplate_TM.png)

    * Ao adicionar um registro CNAME, você deve definir o campo **Nome do Host** como o subdomínio que deseja usar. Por exemplo, **www**. Você deve definir o campo **Endereço** como o nome do domínio **.trafficmanager.net**  do perfil do Gerenciador de Tráfego que você está usando com o seu Website do Azure. Por exemplo, **contoso.trafficmanager.net**.

	    > [AZURE.NOTE] Você só deve usar registros CNAME ao associar o seu nome de domínio personalizado a um site cuja carga é balanceada com o Gerenciador de Tráfego.
	 
7. Clique no botão **Adicionar** para adicionar a entrada.

8. Depois que todas as entradas tiverem sido adicionadas, clique no **Salvar**.

5. Selecione **Gerenciador de Domínios** para voltar à lista de domínios.

6. Marque a caixa de seleção do domínio de destino e clique em **Gerenciar Modelos** novamente.

7. Localize o novo modelo que você criou nas etapas anteriores. Em seguida, clique no link **colocar domínios selecionados (1) neste Modelo**.

    ![Moniker Create Zone Template](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneAssignment.png)

<h2><a name="enabledomain"></a>Habilitar o site do Gerenciador de Tráfego</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]


<!--HONumber=42-->
