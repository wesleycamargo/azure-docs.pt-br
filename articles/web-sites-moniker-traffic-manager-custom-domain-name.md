<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Moniker" pageTitle="Configure a Moniker domain name for an Azure website using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Websites, Moniker, Traffic Manager" description="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Moniker" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth" />

# Configurando um nome de domínio personalizado para um site do Azure usando o Gerenciador de Tráfego (Moniker)

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-BR/documentation/articles/web-sites-custom-domain-name" title="Dom&iacute;nio personalizado">Dom&iacute;nio personalizado</a><a href="/pt-BR/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/pt-BR/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Solu&ccedil;&otilde;es de rede">Solu&ccedil;&otilde;es de rede</a><a href="/pt-BR/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/pt-BR/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/pt-BR/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/pt-BR/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/pt-BR/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/pt-BR/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/pt-BR/documentation/articles/web-sites-moniker-custom-domain-name/" title="Sites">Sites</a> | <a href="/pt-BR/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/" title="Site usando o Gerenciador de Tr&aacute;fego" class="current">Site usando o Gerenciador de Tr&aacute;fego</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Este artigo fornece instruções sobre como usar um nome de domínio personalizado adquirido do [Moniker][1] com os Sites do Azure.

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Neste artigo:

-   [Compreendendo os registros DNS][Compreendendo os registros DNS]
-   [Configurar seus sites para modo padrão][Configurar seus sites para modo padrão]
-   [Adicionar um registro DNS a seu domínio personalizado][Adicionar um registro DNS a seu domínio personalizado]
-   [Habilitar o Gerenciador de Tráfego para o seu site][Habilitar o Gerenciador de Tráfego para o seu site]

## <a name="understanding-records"></a>Compreendendo os registros DNS

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

## <a name="bkmk_configsharedmode"></a>Configurar seus sites para modo padrão

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## Adicionar um registro DNS a seu domínio personalizado

Para associar seu domínio personalizado a um Site do Azure, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo Moniker. Use as seguintes etapas para localizar as ferramentas DNS para Moniker.com

1.  Faça logon na sua conta de Moniker.com e selecione **Meus Domínios** e, em seguida, clique em **Gerenciar Modelos**.

    ![página Meus Domínios para Moniker][página Meus Domínios para Moniker]

2.  Na página **Gerenciamento do Modelo de Zona**, selecione **Criar Novo Modelo**.

    ![Gerenciamento do modelo de zona de Moniker][Gerenciamento do modelo de zona de Moniker]

3.  Preencha o **Nome do Modelo**.

4.  Em seguida, crie um registro DNS selecionando primeiro o **Tipo de Registro**. Em seguida, preencha o **Nome do Host** e o **Endereço**.

    ![Criar modelo de zona de Moniker][Criar modelo de zona de Moniker]

    -   Ao adicionar um registro CNAME, você deve definir o campo **Nome do Host** como o subdomínio que deseja usar. Por exemplo, **www**. Você deve definir o campo **Endereço** como o nome do domínio **.trafficmanager.net** do perfil do Gerenciador de Tráfego que você está usando com o seu Site do Azure. Por exemplo, **contoso.trafficmanager.net**.

        > [WACOM.NOTE] Você só deve usar registros CNAME ao associar o seu nome de domínio personalizado a um site que cuja carga é balanceada com o Gerenciador de Tráfego.

5.  Clique no botão **Adicionar** para adicionar a entrada.

6.  Depois que todas as entradas tiverem sido adicionadas, clique no **Salvar**.

7.  Selecione **Gerenciador de Domínios** para voltar à lista de domínios.

8.  Marque a caixa de seleção do domínio de destino e clique em **Gerenciar Modelos** novamente.

9.  Localize o novo modelo que você criou nas etapas anteriores. Em seguida, clique no link **colocar domínios selecionados (1) neste Modelo**.

    ![Criar modelo de zona de Moniker][2]

## <a name="enabledomain"></a>Habilitar o site do Gerenciador de Tráfego

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

  [Moniker]: /pt-BR/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [1]: https://moniker.com
  [Compreendendo os registros DNS]: #understanding-records
  [Configurar seus sites para modo padrão]: #bkmk_configsharedmode
  [Adicionar um registro DNS a seu domínio personalizado]: #bkmk_configurecname
  [Habilitar o Gerenciador de Tráfego para o seu site]: #enabledomain
  [página Meus Domínios para Moniker]: .\media\web-sites-moniker-custom-domain-name\Moniker_MyDomains.png
  [Gerenciamento do modelo de zona de Moniker]: .\media\web-sites-moniker-custom-domain-name\Moniker_ZoneManager.png
  [Criar modelo de zona de Moniker]: .\media\web-sites-moniker-custom-domain-name\Moniker_CreateZoneTemplate_TM.png
  [2]: .\media\web-sites-moniker-custom-domain-name\Moniker_ZoneAssignment.png
