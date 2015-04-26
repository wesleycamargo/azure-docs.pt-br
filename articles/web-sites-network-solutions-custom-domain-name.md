<properties 
	pageTitle="Configurar um nome de domínio da Network Solutions para um WebSite do Azure" 
	description="Saiba como usar um nome de domínio da Network Solutions com WebSites do Azure" 
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

#Configurando um nome de domínio personalizado para um site do Azure (Network Solutions)

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Domínio personalizado</a><a href="/pt-br/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/pt-br/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions" class="current">soluções de rede</a><a href="/pt-br/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/pt-br/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/pt-br/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/pt-br/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/pt-br/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/pt-br/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/web-sites-network-solutions-custom-domain-name/" title="Websites" class="current">Site</a> | <a href="/pt-br/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Site usando o Gerenciador de Tráfego</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Este artigo fornece instruções sobre como usar o nome de domínio personalizado comprado da [Network Solutions](https://www.networksolutions.com) com sites do Azure.

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Neste artigo:

-   [Compreendendo os registros DNS](#understanding-records)
-   [Configurar seus sites para modo básico, compartilhado ou padrão](#bkmk_configsharedmode)
-   [Adicionar um registro DNS a seu domínio personalizado](#bkmk_configurecname)
-   [Habilitar o domínio no site](#enabledomain)

<h2><a name="understanding-records"></a>Compreendendo os registros DNS</h2>

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>Configurar seus sites para modo básico, compartilhado ou padrão</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>Adicionar um registro DNS a seu domínio personalizado</h2>

Para associar seu domínio personalizado a um site do Azure, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pela Network Solutions. Usar as seguintes etapas para localizar e usar as ferramentas DNS para networksolutions.com

1. Faça logon na sua conta em networksolutions.com e selecione **Minha Conta** no canto superior direito.

3. Na guia **Meus Produtos e Serviços**, selecione **Editar DNS**.

	![edit dns page](./media/web-sites-custom-domain-name/ns-editdns.png)

2. Na seção **Gerenciar <yourdomainname>** da página **Nomes de Domínio**, selecione **Editar Registros DNS Avançados**.

	![domain names page with edit advanced dns records highlighted](./media/web-sites-custom-domain-name/ns-editadvanced.png)

4. A página **Atualizar DNS Avançado** contém uma seção para cada tipo de registro, com um botão **Editar** abaixo de cada seção.
	
	* Para registros A, use a seção **Endereço IP (Registros)**.
	* Para registros CNAME, use a seção **Alias de Host (Registros CNAME)**.

	![update advanced dns page](./media/web-sites-custom-domain-name/ns-updateadvanced.png)

5. Ao clicar no botão **Editar**, você verá um formulário que pode usar para modificar registros existentes ou adicionar novos. 

	> [AZURE.NOTE] Antes de adicionar novas entradas, observe que a Network Solutions já criou alguns registros DNS padrão para coisas como o domínio raiz (' @') e um registro curinga ('*') para subdomínios. Se o registro que você deseja usar já existir, modifique-o em vez de criar um novo.

	* Ao adicionar um registro CNAME, você deve definir o campo **Alias** como o subdomínio que deseja usar. Por exemplo, **www**. Você deve selecionar o campo circular ao lado do campo **Outro host** e definir **Outro host** como o nome de domínio **.azurewebsites.net** do Website do Azure. Por exemplo, **contoso.azurwebsites.net**. Deixe o **Refere-se ao Nome do Host** como **Selecionar**, pois esse campo não é obrigatório durante a criação de um registro CNAME a ser usado com sites do Azure.
	
		![cname form](./media/web-sites-custom-domain-name/ns-cname.png)

		> [AZURE.NOTE] Se estiver usando um registro A, você também deverá adicionar um registro CNAME com uma das seguintes configurações:
		> 
		> * Um valor **Alias** do **www** com um valor de **outro host** do**&lt;yourwebsitename&gt;.azurewebsites.net**.
		> 
		> OU
		> 
		> * Um valor **Alias** do**awverify.www** com um valor de **outro host** de **awverify.&lt;yourwebsitename&gt;.azurewebsites.net**.
		> 
		> Esse registro CNAME é usado pelo Azure para validar que você possui o domínio descrito pelo registro A.

	* Ao adicionar um registro A, você deve definir o campo **Host** como **@** (isso representa o nome do domínio raiz, como **contoso.com**) * (um caractere curinga para corresponder a vários subdomínios) ou o subdomínio que você deseja usar (por exemplo, **www**). Você deve definir o campo **IP numérico** como o endereço IP do seu site do Azure.

		![a record form](./media/web-sites-custom-domain-name/ns-arecord.png)

5. Ao concluir a adição ou a modificação dos registros, clique em **Continuar** para revisar as alterações. Selecione **Salvar apenas as alterações** para salvar as alterações.

<h2><a name="enabledomain"></a>Habilitar o nome do domínio no seu site</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]



<!--HONumber=42-->
