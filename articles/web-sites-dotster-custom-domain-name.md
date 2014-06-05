<properties title="Saiba como configurar um site do Azure para usar um nome de domínio registrado com o Dotster" pageTitle="Configurar um nome de domínio Dotster para um site do Azure" metaKeywords="Azure, Sites do Azure, Dotster" description="Saiba como configurar um site do Azure para usar um nome de domínio registrado com o Dotster" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

#Configurando um nome de domínio personalizado para um site do Azure (Dotster)

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/web-sites-custom-domain-name" title="Domínio personalizado">Domínio personalizado</a><a href="/pt-br/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/pt-br/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Soluções de rede">Soluções de rede</a><a href="/pt-br/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/pt-br/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/pt-br/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/pt-br/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/pt-br/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/pt-br/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/web-sites-dotster-custom-domain-name/" title="Sites" class="current">Site</a> | <a href="/pt-br/documentation/articles/web-sites-dotster-traffic-manager-custom-domain-name/" title="Site usando o Gerenciador de Tráfego">Site usando o Gerenciador de Tráfego</a></div>

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Este artigo fornece instruções sobre como usar um nome de domínio personalizado adquirido do [Dotster.com](https://dotster.com) com os Sites do Azure.

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

Para associar seu domínio personalizado a um Site do Azure, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo Dotster. Use as seguintes etapas para localizar as ferramentas DNS para o Dotster.com

1. Faça logon em sua conta do Dotster.com. No menu **Domínio**, selecione **DomainCentral**.

    ![Menu Central de Domínios do Dotster](.\media\web-sites-dotster-custom-domain-name\Dotster_DomainCentralMenu.png)

2. Selecione seu domínio para abrir uma lista de configurações. Em seguida, selecione o link **Nameservers**.

    ![Opções de configuração de domínio do Dotster](.\media\web-sites-dotster-custom-domain-name\Dotster_DomainMenu.png)

3. Selecione **Usar diferentes servidores de nome**. Para tirar proveito dos serviços DNS no Dotster, você deve especificar os seguintes servidores de nome: ns1.nameresolve.com, ns2.nameresolve.com, ns3.nameresolve.com e ns4.nameresolve.com.

    ![Opções de configuração de domínio do Dotster](.\media\web-sites-dotster-custom-domain-name\Dotster_Nameservers.png)

    > [WACOM.NOTE] Pode levar de 24 a 48 horas para que a alteração de servidores de nome tenha efeito. As demais etapas deste artigo não funcionarão até o fim desse período esse período de tempo.

4. Na DomainCentral, selecione o domínio e, em seguida, selecione **DNS**. Na lista **Modificar**, selecione o tipo de registro DNS a ser adicionado (**Alias do CNAME** ou **Registro A**). 

    ![Opções de configuração de domínio do Dotster](.\media\web-sites-dotster-custom-domain-name\Dotster_DNS.png)

5. Em seguida, especifique os campos **Host** e **Aponta para** do registro. Ao concluir, clique no botão **Adicionar**.

    ![Opções de configuração de domínio do Dotster](.\media\web-sites-dotster-custom-domain-name\Dotster_DNS_CNAME.png)
 
    * Ao adicionar um registro CNAME, você deve definir o campo **Host** como o subdomínio que deseja usar. Por exemplo, **www**. Você deve definir o campo **Aponta para** como o nome do domínio **.azurewebsites.net** do Site do Azure. Por exemplo, **contoso.azurwebsites.net**.

    * Ao adicionar um registro A, você deve definir o campo **Host** como **@** (isso representa o nome do domínio raiz, como **contoso.com**) ou o subdomínio que você deseja usar (por exemplo, **www**). Você deve definir o campo **Aponta para** como o endereço IP do seu Site do Azure.

		> [WACOM.NOTE] Se estiver usando um registro A, você também deverá adicionar um registro CNAME com uma das seguintes configurações:
		> 
		> * Um valor de **Host** de **www** que **Aponta para** um valor de **&lt;yourwebsitename&gt;.azurewebsites.net**.
		> 
		> OU
		> 
		> * Um valor de **Host** de **awverify.www** que **Aponta para** um valor de **awverify&lt;yourwebsitename&gt;.azurewebsites.net**.
		> 
		> Esse registro CNAME é usado pelo Azure para validar que você possui o domínio descrito pelo registro A


<h2><a name="enabledomain"></a>Habilitar o nome do domínio no seu site</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

