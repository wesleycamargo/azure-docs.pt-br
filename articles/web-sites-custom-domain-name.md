<properties title="Saiba como configurar um site do Azure para usar um nome de domínio personalizado" pageTitle="Configurar um nome de domínio personalizado para um site do Azure" metaKeywords="Azure, Sites do Azure, nome de domínio" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

#Configurando um nome de domínio personalizado para um Site do Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/web-sites-custom-domain-name" title="Domínio personalizado" class="current">Domínio personalizado</a><a href="/pt-br/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/pt-br/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Soluções de rede">Soluções de rede</a><a href="/pt-br/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/pt-br/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/pt-br/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/pt-br/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/pt-br/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/pt-br/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/web-sites-custom-domain-name/" title="Sites" class="current">Site</a> | <a href="/pt-br/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Site usando o Gerenciador de Tráfego">Site usando o Gerenciador de Tráfego</a></div>

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Este artigo fornece instruções genéricas para usar um nome de domínio personalizado com os Sites do Azure. Marque as guias na parte superior deste artigo para ver se o registrador de domínio está listado. Em caso afirmativo, selecione essa guia para etapas específicas do registrador.

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

Para associar seu domínio personalizado a um site do Azure, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo registrador de domínio do qual comprou seu nome de domínio. Use as seguintes etapas para localizar e usar as ferramentas DNS.

1. Faça logon na sua conta em seu registrador de domínio e procure uma página de gerenciamento de registros DNS. Procure links ou áreas do site rotuladas como **Nome de domínio**, **DNS**, ou **Gerenciamento de servidor de nome**. Normalmente, um link para essa página pode ser encontrado exibindo-se as informações de conta e procurando-se um link como **Meus domínios**.

4. Depois de localizar a página de gerenciamento para seu nome de domínio, procure um link que permita editar os registros DNS. Ele pode estar listado como um **Arquivo de zona**, **Registros DNS** ou como um link de configuração **Avançado**.

	* A página deverá ter alguns poucos registros já criados, como uma associação de entrada '**@**' ou ' \*' com uma página de "estacionamento de domínio". Ela também pode conter registros para subdomínios comuns como **www**.
	* A página mencionará **registros A** e **registros CNAME**, ou fornecerá uma lista suspensa para selecionar um tipo de registro. Ela também pode mencionar outros registros, como **registros MX**. Em alguns casos, serão chamados por outros nomes como **registros de Endereço IP** em vez de registros A, ou **Registros de alias** em vez de registros CNAME.
	* A página também terá campos que permitem **mapear** de um **Nome do host** ou **Nome do domínio** para um **Endereço IP** ou outro nome de domínio.

5. Embora as especificidades de cada registrador variem, em geral, você mapeia *do* nome de domínio personalizado (como **contoso.com**) *para* o nome de domínio do Site do Azure (**contoso.azurewebsites.net**) ou o endereço IP virtual do Site do Azure.

	* Os registros CNAME sempre mapearão o domínio de Sites do Azure - **contoso.azurewebsites.net**. Portanto, você estará mapeando *de* um domínio como **www** *para* seu endereço **&lt;yourwebsitename&gt;. azurewebsites.net**.
	
		> [WACOM.NOTE] Se estiver usando um registro A, você também deverá adicionar um registro CNAME com uma das seguintes configurações:
		> 
		> * Mapeia *de* **www** *para* seu **&lt;yourwebsitename&gt;.azurewebsites.net**.
		> 
		> OU
		> 
		> * Mapeia *de* **awverify.www** *para* **awverify.&lt;yourwebsitename&gt;.azurewebsites.net**.
		> 
		> Esse registro CNAME é usado pelo Azure para validar que você possui o domínio descrito pelo registro A.

	* Um registro sempre mapeará para o endereço IP virtual dos Sites do Azure. Portanto, você está mapeando *de* um domínio como **www** *para* o endereço IP virtual do site.
	
		> [WACOM.NOTE] Para mapear um domínio raiz (como **contoso.com**) para um Site do Azure, você geralmente mapeará de '**@**', ou de uma entrada em branco para o endereço IP virtual. Para criar um mapeamento de caractere curinga que mapeia todos os subdomínios para o endereço IP virtual, você geralmente mapeará de '*' para o endereço IP virtual.
		> 
		> As especificidades de mapeamento de uma raiz ou um caractere curinga variam entre os registradores. Consulte a documentação fornecida pelo seu registrador para obter orientações mais específicas.

6. Depois de terminar a adição ou a modificação de registros DNS no registrador, salve as alterações.

<h2><a name="enabledomain"></a>Habilitar o nome do domínio no seu site</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

