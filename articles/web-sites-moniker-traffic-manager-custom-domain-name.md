<properties title="Saiba como configurar um site do Azure que usa o Gerenciador de Tráfego para usar um nome de domínio registrado junto a Moniker" pageTitle="Configurar um nome de domínio Moniker para um site do Azure usando o Gerenciador de Tráfego" metaKeywords="Windows Azure, sites do Windows Azure, Moniker, Gerenciador de Tráfego" description="Saiba como configurar um site do Azure que usa o Gerenciador de Tráfego para usar um nome de domínio registrado junto a Moniker" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

#Configurando um nome de domínio personalizado para um site do Azure usando o Gerenciador de Tráfego (Moniker)

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/web-sites-custom-domain-name" title="Domínio personalizado">Domínio personalizado</a><a href="/pt-br/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/pt-br/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Soluções de rede">Soluções de rede</a><a href="/pt-br/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/pt-br/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/pt-br/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/pt-br/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/pt-br/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/pt-br/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/web-sites-moniker-custom-domain-name/" title="Sites">Site</a> | <a href="/pt-br/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/" title="Site usando o Gerenciador de Tráfego" class="current">Site usando o Gerenciador de Tráfego</a></div>


[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Este artigo fornece instruções sobre como usar um nome de domínio personalizado comprado junto a [Moniker](https://moniker.com) com sites do Azure.

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

Para associar seu domínio personalizado a um site do Azure, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pela Moniker. Use as seguintes etapas para localizar as ferramentas DNS para Moniker.com

1. Faça logon na sua conta de Moniker.com e selecione **Meus Domínios** e, em seguida, clique em **Gerenciar Modelos**.

    ![página Meus Domínios para Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_MyDomains.png)

2. Na página **Gerenciamento do Modelo de Zona**, selecione **Criar Novo Modelo**.

    ![Gerenciamento do modelo de zona de Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneManager.png)

3. Preencha o **Nome do Modelo**. 

5. Em seguida, crie um registro DNS selecionando primeiro o **Tipo de Registro**. Em seguida, preencha o **Nome do Host** e o **Endereço**. 

    ![Criar modelo de zona de Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_CreateZoneTemplate_TM.png)

    * Ao adicionar um registro CNAME, você deve definir o campo **Nome do Host** como o subdomínio que deseja usar. Por exemplo, **www**. Você deve definir o campo **Endereço** como o nome do domínio **.trafficmanager.net** do perfil do Gerenciador de Tráfego que você está usando com o seu Site do Azure. Por exemplo, **contoso.trafficmanager.net**.

	    > [WACOM.NOTE] Você só deve usar registros CNAME ao associar o seu nome de domínio personalizado a um site que cuja carga é balanceada com o Gerenciador de Tráfego.
	 
7. Clique no botão **Adicionar** para adicionar a entrada.

8. Depois que todas as entradas tiverem sido adicionadas, clique no **Salvar**.

5. Selecione **Gerenciador de Domínios** para voltar à lista de domínios.

6. Marque a caixa de seleção do domínio de destino e clique em **Gerenciar Modelos** novamente.

7. Localize o novo modelo que você criou nas etapas anteriores. Em seguida, clique no link **colocar domínios selecionados (1) neste Modelo**.

    ![Criar modelo de zona de Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneAssignment.png)

<h2><a name="enabledomain"></a>Habilitar o site do Gerenciador de Tráfego</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

