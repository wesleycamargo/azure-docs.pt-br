<properties title="Saiba como configurar um site do Azure que usa o Gerenciador de Tráfego para usar um nome de domínio registrado junto a Register.com" pageTitle="Configurar um nome de domínio de Register.com para um site do Azure que usa o Gerenciador de Tráfego" metaKeywords="Windows Azure, sites do Windows Azure, nome do domínio" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

#Configurando um nome de domínio personalizado para um site do Azure usando do Gerenciador de Tráfego (Register.com)

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/web-sites-custom-domain-name" title="Domínio personalizado">Domínio personalizado</a><a href="/pt-br/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/pt-br/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Soluções de rede">Soluções de rede</a><a href="/pt-br/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/pt-br/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/pt-br/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/pt-br/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/pt-br/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/pt-br/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/web-sites-registerdotcom-custom-domain-name/" title="Sites">Site</a> | <a href="/pt-br/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/" title="Site usando o Gerenciador de Tráfego" class="current">Site usando o Gerenciador de Tráfego</a></div>


[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Este artigo fornece instruções sobre como usar o nome de domínio personalizado comprado junto a [Register.com](https://register.com) com sites do Azure.

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

Para associar o domínio personalizado a um site do Azure, você deve adicionar uma nova entrada na tabela DNS para o domínio personalizado usando as ferramentas fornecidas por Register.com. Use as seguintes etapas para localizar e usar as ferramentas DNS.

1. Faça logon na conta em register.com e selecione **Sua Conta** no canto superior direito para exibir seus domínios e selecione seu nome de domínio personalizado.

	![a página minha conta](./media/web-sites-custom-domain-name/rdotcom-myaccount.png)

3. Role a página até ver **Configurações Técnicas Avançadas**. Os links nesta seção permitem gerenciar os registros do domínio. Para registros CNAME, use o link **Editar Registros de Aliases de Domínio**.

	![Configurações técnicas avançadas](./media/web-sites-custom-domain-name/rdotcom-advancedsettingstm.png)

5. Ao clicar no botão **Editar**, você verá um formulário que pode usar para modificar registros existentes ou adicionar novos. O formulário é semelhante para registros CNAME e A.

	* Ao adicionar um registro CNAME, você deve definir o campo **.mydomainname.com** como o subdomínio que deseja usar. Por exemplo, **www**. Você deve selecionar o valor **aponta para** como o nome de domínio **.trafficmanager.net** do perfil do Gerenciador de Tráfego que você está usando com o site do Azure. Por exemplo, **contoso.trafficmanager.net**. Deixe o **Refere-se ao Nome do Host** como **Selecionar**, pois esse campo não é obrigatório durante a criação de um registro CNAME a ser usado com sites do Azure.
	
		![formulário cname](./media/web-sites-custom-domain-name/rdotcom-editcnamerecordtm.png)


5. Ao concluir a adição ou a modificação dos registros, clique em **Continuar** para revisar as alterações. Selecione **Continuar** novamente para salvar as alterações.

<h2><a name="enabledomain"></a>Habilitar o site do Gerenciador de Tráfego</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

