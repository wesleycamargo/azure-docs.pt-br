<properties 
	pageTitle="Configurar um nome de domínio de Register.com para um Website do Azure que usa o Gerenciador de tráfego" 
	description="Saiba como usar um nome de domínio de Register.com com Websites do Azure" 
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
	ms.author="larryfr, jroth"/>

#Configurando um nome de domínio personalizado para um site do Azure usando o Gerenciador de Tráfego (Register.com)

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Domínio personalizado</a><a href="/pt-br/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/pt-br/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">soluções de rede</a><a href="/pt-br/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/pt-br/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/pt-br/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/pt-br/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/pt-br/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/pt-br/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/web-sites-registerdotcom-custom-domain-name/" title="Websites">Site</a> | <a href="/pt-br/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">Site usando o Gerenciador de Tráfego</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Este artigo fornece instruções sobre como usar um nome de domínio personalizado adquirido do [Register.com](https://www.register.com) com os sites do Azure.

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

Para associar seu domínio personalizado a um Website do Azure, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo Register.com. Use as seguintes etapas para localizar e usar as ferramentas DNS.

1. Faça logon na conta em register.com e selecione **Sua Conta** no canto superior direito para exibir seus domínios e selecione seu nome de domínio personalizado.

	![the my account page](./media/web-sites-custom-domain-name/rdotcom-myaccount.png)

3. Role a página até ver **Configurações Técnicas Avançadas**. Os links nesta seção permitem gerenciar os registros do domínio. Para registros CNAME, use o link **Editar Registros de Aliases de Domínio**.

	![Advanced technical settings](./media/web-sites-custom-domain-name/rdotcom-advancedsettingstm.png)

5. Ao clicar no botão **Editar**, você verá um formulário que pode usar para modificar registros existentes ou adicionar novos. O formulário é semelhante para registros CNAME e A.

	* Ao adicionar um registro CNAME, você deve definir o campo **.mydomainname.com** como o subdomínio que deseja usar. Por exemplo, **www**. Você deve selecionar o valor **aponta para** como o nome de domínio **.trafficmanager.net** do perfil do Gerenciador de Tráfego que você está usando com o site do Azure. Por exemplo, **contoso.trafficmanager.net**. Deixe o **Refere-se ao Nome do Host** como **Selecionar**, pois esse campo não é obrigatório durante a criação de um registro CNAME a ser usado com sites do Azure.
	
		![cname form](./media/web-sites-custom-domain-name/rdotcom-editcnamerecordtm.png)


5. Ao concluir a adição ou a modificação dos registros, clique em **Continuar** para revisar as alterações. Selecione **Continuar** novamente para salvar as alterações.

<h2><a name="enabledomain"></a>Habilitar o site do Gerenciador de Tráfego</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]



<!--HONumber=42-->
