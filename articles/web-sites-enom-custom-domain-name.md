<properties title="Learn how to configure an Azure website to use a domain name registered with eNom" pageTitle="Configurar um nome de domínio eNom para um site do Azure" metaKeywords="Microsoft Azure, sites do Microsoft Azure, nome de domínio" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

#Configurando um nome de domínio personalizado para um Site do Azure (eNom)

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Domínio personalizado</a><a href="/pt-br/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/pt-br/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Soluções de rede</a><a href="/pt-br/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/pt-br/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/pt-br/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/pt-br/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/pt-br/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/pt-br/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/web-sites-enom-custom-domain-name/" title="Websites" class="current">Site</a> | <a href="/pt-br/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Site usando o Gerenciador de Tráfego</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Este artigo fornece instruções sobre como usar um nome de domínio personalizado adquirido do [eNom](https://enom.com) com os sites do Azure.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Neste artigo:

-   [Compreendendo os registros DNS](#understanding-records)
-   [Configurar seus sites para modo básico, compartilhado ou padrão](#bkmk_configsharedmode)
-   [Adicionar um registro DNS a seu domínio personalizado](#bkmk_configurecname)
-   [Habilitar o domínio no site](#enabledomain)

<h2><a name="understanding-records"></a>Compreendendo os registros DNS</h2>

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>Configurar seus sites para modo básico, compartilhado ou padrão</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>Adicionar um registro DNS a seu domínio personalizado</h2>

Para associar seu domínio personalizado a um site do Azure, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo eNom. Use as seguintes etapas para localizar as ferramentas DNS para o enom.com

1. Faça logon na sua conta do eNom e selecione **Domínios** e, em seguida, **Meus Domínios**. Isso exibirá os nomes de seus domínios.

2. Na página **Meus Domínios**, use o campo **Gerenciar Domínio** para selecionar **Registros do Host**. Isso exibirá os campos de registros de host.

	![DNS Zone File tab](./media/web-sites-custom-domain-name/e-hostrecords.png)

4. O editor de Registros do Host permite que você selecione o tipo de registro específico usando o campo **Tipo de Registro**. Para sites do Azure, você deve usar somente o **CNAME (Alias)** ou uma seleção **A (Endereço)**.

	![zone file editor](./media/web-sites-custom-domain-name/e-editrecords.png)

	> [WACOM.NOTE] Antes de adicionar as entradas ao arquivo de zona, observe que o eNom já criou os registros de DNS para o domínio raiz ('@') e um caractere curinga para subdomínios ('\*'). Se desejar redirecionar o domínio raiz para o seu site, ou usar um caractere curinga de registro A, você deverá modificar essas entradas em vez de criar novas.

	*  Ao adicionar um registro CNAME, você deve definir o campo **Nome do Host** como o subdomínio que deseja usar. Por exemplo, **www**. Você deve definir o campo **Endereço** como o nome do domínio **.azurewebsites.net** do Site do Azure. Por exemplo, **contoso.azurwebsites.net**.

		> [WACOM.NOTE] Se estiver usando um registro A, você também deverá adicionar um registro CNAME com uma das seguintes configurações:
		> 
		> * Um valor de **Alias** de **www** com um valor de **outro host** de **&lt;yourwebsitename&gt;.azurewebsites.net**.
		> 
		> OU
		> 
		> * Um valor de **Alias** de **awverify.www** com um valor de **outro host** de **awverify.&lt;yourwebsitename&gt;.azurewebsites.net**.
		> 
		> Esse registro CNAME é usado pelo Azure para validar que você possui o domínio descrito pelo registro A.

	* Ao adicionar um registro A, você deve definir o campo **Nome do Host** como **@** (isso representa o nome do domínio raiz, como **contoso.com**,) * (um caractere curinga para corresponder a vários subdomínios) ou o subdomínio específico que você deseja usar (por exemplo, **www**). Você deve definir o campo **Endereço** como o endereço IP do seu site do Azure.

		> [WACOM.NOTE] Ao adicionar um registro A, você também deve adicionar um registro CNAME com um host de **awverify** e um **Aponta para** de **awverify.&lt;yourwebsitename&gt;.azurewebsites.net.

5. Ao concluir a adição ou a modificação dos registros, clique em **Salvar** para salvar as alterações.

<h2><a name="enabledomain"></a>Habilitar o nome do domínio no seu site</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]
