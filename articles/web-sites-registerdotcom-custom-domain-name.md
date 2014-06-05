<properties title="Saiba como configurar um site do Azure para usar um nome de domínio registrado junto a Register.com" pageTitle="Configurar um nome de domínio de Register.com para um site do Azure" metaKeywords="Azure, sites do Azure, nome do domínio" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

#Configurando um nome de domínio personalizado para um site do Azure (Register.com)

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/web-sites-custom-domain-name" title="Domínio personalizado">Domínio personalizado</a><a href="/pt-br/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/pt-br/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Soluções de rede">Soluções de rede</a><a href="/pt-br/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/pt-br/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/pt-br/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/pt-br/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/pt-br/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/pt-br/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/web-sites-registerdotcom-custom-domain-name/" title="Sites" class="current">Site</a> | <a href="/pt-br/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/" title="Site usando o Gerenciador de Tráfego">Site usando o Gerenciador de Tráfego</a></div>

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Este artigo fornece instruções sobre como usar o nome de domínio personalizado comprado junto a [Register.com](https://register.com) com sites do Azure.

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

Para associar o domínio personalizado a um site do Azure, você deve adicionar uma nova entrada na tabela DNS para o domínio personalizado usando as ferramentas fornecidas por Register.com. Use as seguintes etapas para localizar e usar as ferramentas DNS.

1. Faça logon na conta em register.com e selecione **Sua Conta** no canto superior direito para exibir seus domínios e selecione seu nome de domínio personalizado.

	![a página minha conta](./media/web-sites-custom-domain-name/rdotcom-myaccount.png)

3. Role a página até ver **Configurações Técnicas Avançadas**. Os links nesta seção permitem gerenciar os registros do domínio.

	* Para registros A, use o link **Editar Registros de Endereço IP**.
	* Para registros CNAME, use o link **Editar Registros de Aliases de Domínio**.

	![Configurações técnicas avançadas](./media/web-sites-custom-domain-name/rdotcom-advancedsettings.png)

5. Ao clicar no botão **Editar**, você verá um formulário que pode usar para modificar registros existentes ou adicionar novos. O formulário é semelhante para registros CNAME e A.

	* Ao adicionar um registro CNAME, você deve definir o campo **.mydomainname.com** como o subdomínio que deseja usar. Por exemplo, **www**. Você deve selecionar o valor **aponta para** como o nome do domínio **.azurewebsites.net** do site do Azure. Por exemplo, **contoso.azurwebsites.net**. Deixe o **Refere-se ao Nome do Host** como **Selecionar**, pois esse campo não é obrigatório durante a criação de um registro CNAME a ser usado com sites do Azure.
	
		![formulário cname](./media/web-sites-custom-domain-name/rdotcom-editcnamerecord.png)

		> [WACOM.NOTE] Se estiver usando um registro A, você também deverá adicionar um registro CNAME com uma das seguintes configurações:
		> 
		> * Um valor de **Alias** de **www** com um valor de **Outro host** de **&lt;yourwebsitename&gt;.azurewebsites.net**.
		> 
		> OU
		> 
		> * Um valor de **Alias** de **awverify.www** com um valor de **Outro host** de **awverify.&lt;yourwebsitename&gt;.azurewebsites.net**.
		> 
		> Esse registro CNAME é usado pelo Azure para validar que você possui o domínio descrito pelo registro A.

	* Ao adicionar um registro A, você deve definir o campo **.mydomainname.com** como o subdomínio que deseja usar (por exemplo, **www**). Deixe o campo em branco para definir o domínio raiz ou use **\** para criar um mapeamento de caractere curinga. Você deve definir o campo **Aponta para** como o endereço IP do seu Site do Azure.

		![formulário de registro a](./media/web-sites-custom-domain-name/rdotcom-editarecord.png)

5. Ao concluir a adição ou a modificação dos registros, clique em **Continuar** para revisar as alterações. Selecione **Continuar** novamente para salvar as alterações.

<h2><a name="enabledomain"></a>Habilitar o nome do domínio no seu site</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

