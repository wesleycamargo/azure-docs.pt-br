<properties title="Aprenda a configurar um site do Azure para usar um nome de domínio registrado com o DomainDiscover - TierraNet" pageTitle="Configurar um nome de domínio DomainDiscover para um site do Azure" metaKeywords="Azure, Sites do Azure, DomainDiscover, TierraNet" description="Saiba como configurar um site do Azure para usar um nome de domínio registrado com o DomainDiscover - TierraNet" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

#Configurando um nome de domínio personalizado para um site do Azure (DomainDiscover / TierraNet)

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/web-sites-custom-domain-name" title="Domínio personalizado">Domínio personalizado</a><a href="/pt-br/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/pt-br/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Soluções de rede">Soluções de rede</a><a href="/pt-br/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/pt-br/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/pt-br/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/pt-br/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/pt-br/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/pt-br/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/web-sites-domaindiscover-custom-domain-name/" title="Sites" class="current">Site</a> | <a href="/pt-br/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/" title="Site usando o Gerenciador de Tráfego">Site usando o Gerenciador de Tráfego</a></div>

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Este artigo fornece instruções sobre como usar um nome de domínio personalizado adquirido do [DomainDiscover.com](https://domaindiscover.com) com os Sites do Azure. O DomainDiscover.com faz parte do TierraNet.

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

Para associar seu domínio personalizado a um Site do Azure, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo DomainDiscover. Use as seguintes etapas para localizar as ferramentas DNS para o DomainDiscover.com

1. Faça logon em sua conta do DomainDiscover.com (TierraNet) selecionando **Painel de Controle** no menu **Logon**.

    ![Menu de logon do DomainDiscover](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_LoginMenu.png)

2. Na página **Serviços de Domínio**, selecione o domínio que você deseja usar para o site do Azure.

    ![Página Gerenciamento de domínio](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DomainManagement.png)

3. Em Configurações de domínio, clique no botão **Editar** do **Serviço DNS**.

    ![Botão Editar DNS](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSEditButton.png)

4. Na janela **Gerenciar DNS**, selecione o tipo de registro DNS a ser adicionado na lista **Adicionar Registros**. Em seguida, clique no botão **Adicionar**.

    ![Botão Editar DNS](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSAddRecords.png)

5. Na página seguinte, digite os valores dos registros DNS. Em seguida, clique no botão **Adicionar**.

    ![Botão Editar DNS](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSRecords.png)

    * Ao adicionar um registro CNAME, você deve primeiro selecionar **CNAME (Alias)** na página **Gerenciar DNS**. Em seguida, defina o campo **Host** como o subdomínio que deseja usar. Por exemplo, **www**. Você deve definir o campo **Nome do Host do Alias** como o nome do domínio **.azurewebsites.net** do Site do Azure. Por exemplo, **contoso.azurwebsites.net**. Em seguida, forneça um valor de TTL (vida útil), como 1800 segundos.

    * Ao adicionar um registro A, você deve primeiro selecionar **A** na página **Gerenciar DNS**. Em seguida, defina o campo **Host** como **@** (isso representa o nome do domínio raiz, como **contoso.com**) ou o subdomínio que deseja usar (por exemplo, **www**). Você deve definir o campo **Endereço IP** como o endereço IP do seu Site do Azure. Em seguida, forneça um valor de TTL (vida útil), como 1800 segundos.

		> [WACOM.NOTE] Se estiver usando um registro A, você também deverá adicionar um registro CNAME com uma das seguintes configurações:
		> 
		> * Um valor de **Host** de **www** com um valor de **Nome de Host do Alias** de **&lt;yourwebsitename&gt;.azurewebsites.net**.
		> 
		> OU
		> 
		> * Um valor de **Host** de **awverify.www** com um valor de **Nome de Host do Alias** de **awverify.&lt;yourwebsitename&gt;.azurewebsites.net**.
		> 
		> Esse registro CNAME é usado pelo Azure para validar que você possui o domínio descrito pelo registro A


5. Se tiver adicionado um registro A, você poderá obter um aviso de que o registro A existente de seu domínio não está inativo. Ele usa o registro alterado mais recentemente, e o DomainDirect já tem um registro A padrão para o nome do domínio raiz. Você pode contar com essa precedência, ou remover o registro A padrão selecionando o botão **EXCLUIR**.

<h2><a name="enabledomain"></a>Habilitar o nome do domínio no seu site</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

