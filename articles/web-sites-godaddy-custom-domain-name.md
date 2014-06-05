<properties title="Saiba como configurar um site do Azure para usar um nome de domínio registrado com o GoDaddy" pageTitle="Configurar um nome de domínio do GoDaddy para um site do Azure" metaKeywords="Windows Azure, Sites do Windows, nome do domínio" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

#Configurando um nome de domínio personalizado para um site do Azure (GoDaddy)

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/web-sites-custom-domain-name" title="Domínio personalizado">Domínio personalizado</a><a href="/pt-br/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy" class="current">GoDaddy</a><a href="/pt-br/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Soluções de rede">Soluções de rede</a><a href="/pt-br/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/pt-br/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/pt-br/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/pt-br/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/pt-br/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/pt-br/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/web-sites-godaddy-custom-domain-name/" title="Sites" class="current">Site</a> | <a href="/pt-br/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/" title="Site usando o Gerenciador de Tráfego">Site usando o Gerenciador de Tráfego</a></div>

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Este artigo fornece instruções sobre como usar um nome de domínio personalizado adquirido do [GoDaddy](https://godaddy.com) com os Sites do Azure.

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

<h2><a name="bkmk_configurecname"></a>Adicionar um registro DNS a seu domínio personalizado</h2>

Para associar seu domínio personalizado a um Site do Azure, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo GoDaddy. Use as seguintes etapas para localizar as ferramentas DNS para o GoDaddy.com

1. Faça logon na sua conta do GoDaddy.com e selecione **Minha Conta** e, em seguida, **Gerenciar seus domínios**. Finalmente, selecione o nome do domínio que você deseja usar com o Site do Azure.

	![página de domínio personalizado para GoDaddy](./media/web-sites-custom-domain-name/godaddy-customdomain.png)

2. Na página **Detalhes do domínio**, selecione a guia **Arquivo de zona DNS**. Esta é a seção usada para adicionar e modificar registros DNS para o nome do domínio. Selecione o botão **Editar** para exibir o **Editor de Arquivo de Zona**.

	![Guia Arquivo de Zona DNS](./media/web-sites-custom-domain-name/godaddy-zonetab.png)

4. O **Editor de Arquivo de Zona** é dividido em seções para cada tipo de registro, começando com os registros A (listados como **A (Host)** como a primeira seção, seguida por registros CNAME (listados como **CNAME (Alias)**.) Para adicionar uma nova entrada, use o botão **Adição Rápida** abaixo da seção correspondente. Para editar uma entrada existente, selecione essa entrada e modifique as informações existentes.

	> [WACOM.NOTE] Antes de adicionar as entradas ao arquivo de zona, observe que o GoDaddy já criou os registros DNS para subdomínios populares (chamados **Host** no editor), como **email**, **arquivos**, **mail** e outros. Se o nome que você deseja usar já existir, modifique o registro existente em vez de criar um novo.

	* Ao adicionar um registro CNAME, você deve definir o campo **Host** como o subdomínio que deseja usar. Por exemplo, **www**. Você deve definir o campo **Aponta para** como o nome do domínio **.azurewebsites.net** do Site do Azure. Por exemplo, **contoso.azurwebsites.net**.

		![editor de arquivo de zona](./media/web-sites-custom-domain-name/godaddy-quickaddcname.png)
	
		> [WACOM.NOTE] Se estiver usando um registro A, você também deverá adicionar um registro CNAME com uma das seguintes configurações:
		> 
		> * Um valor de **Host** de **www** que **Aponta para** um valor de **&lt;yourwebsitename&gt;.azurewebsites.net**.
		> 
		> OU
		> 
		> * Um valor de **Host** de **awverify.www** que **Aponta para** um valor de **awverify&lt;yourwebsitename&gt;.azurewebsites.net**.
		> 
		> Esse registro CNAME é usado pelo Azure para validar que você possui o domínio descrito pelo registro A

	* Ao adicionar um registro A, você deve definir o campo **Host** como **@** (isso representa o nome do domínio raiz, como **contoso.com**) * (um caractere curinga para corresponder a vários subdomínios) ou o subdomínio que você deseja usar (por exemplo, **www**). Você deve definir o campo **Aponta para** como o endereço IP do seu Site do Azure.

		![editor de arquivo de zona para um registro](./media/web-sites-custom-domain-name/godaddy-quickaddarecord.png)

5. Ao concluir a adição ou a modificação dos registros, clique em **Salvar Arquivo de Zona** para salvar as alterações.

<h2><a name="enabledomain"></a>Habilitar o nome do domínio no seu site</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

