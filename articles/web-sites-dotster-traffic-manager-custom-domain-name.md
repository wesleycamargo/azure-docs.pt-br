<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Dotster" pageTitle="Configure a Dotster domain name for an Azure website using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, Dotster, Traffic Manager" description="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Dotster" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth" />

# Configurando um nome de domínio personalizado para um site do Windows Azure usando o Gerenciador de Tráfego (Dotster)

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-BR/documentation/articles/web-sites-custom-domain-name" title="Dom&iacute;nio personalizado">Dom&iacute;nio personalizado</a><a href="/pt-BR/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/pt-BR/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Solu&ccedil;&otilde;es de rede">Solu&ccedil;&otilde;es de rede</a><a href="/pt-BR/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/pt-BR/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/pt-BR/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/pt-BR/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/pt-BR/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/pt-BR/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/pt-BR/documentation/articles/web-sites-dotster-custom-domain-name/" title="Sites">Sites</a> | <a href="/pt-BR/documentation/articles/web-sites-dotster-traffic-manager-custom-domain-name/" title="Site usando o Gerenciador de Tr&aacute;fego" class="current">Site usando o Gerenciador de Tr&aacute;fego</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Este artigo fornece instruções sobre como usar um nome de domínio personalizado adquirido do [Dotster.com][Dotster.com] com os Sites do Azure.

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

Para associar seu domínio personalizado a um Site do Azure, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo Dotster. Use as seguintes etapas para localizar as ferramentas DNS para o Dotster.com

1.  Faça logon em sua conta do Dotster.com. No menu **Domínio**, selecione **DomainCentral**.

    ![Menu Central de Domínios do Dotster][Menu Central de Domínios do Dotster]

2.  Selecione seu domínio para abrir uma lista de configurações. Em seguida, selecione o link **Nameservers**.

    ![Opções de configuração de domínio do Dotster][Opções de configuração de domínio do Dotster]

3.  Selecione **Usar diferentes servidores de nome**. Para tirar proveito dos serviços DNS no Dotster, você deve especificar os seguintes servidores de nome: ns1.nameresolve.com, ns2.nameresolve.com, ns3.nameresolve.com, e ns4.nameresolve.com.

    ![Opções de configuração de domínio do Dotster][1]

    > [WACOM.NOTE] Pode levar de 24 a 48 horas para que a alteração de servidores de nome tenha efeito. As demais etapas deste artigo não funcionarão até o fim desse período esse período de tempo.

4.  Na DomainCentral, selecione o domínio e, em seguida, selecione **DNS**. Na lista **Modificar**, selecione o tipo de registro DNS a ser adicionado (**Alias do CNAME** ou **Registro A**).

    ![Opções de configuração de domínio do Dotster][2]

5.  Em seguida, especifique os campos **Host** e **Aponta para** do registro. Ao concluir, clique no botão **Adicionar**.

    ![Opções de configuração de domínio do Dotster][3]

    -   Ao adicionar um registro CNAME, você deve definir o campo **Host** como o subdomínio que deseja usar. Por exemplo, **www**. Você deve definir o campo **Aponta para** como o nome do domínio **.trafficmanager.net** do perfil do Gerenciador de Tráfego que você está usando com o seu Site do Azure. Por exemplo, **contoso.trafficmanager.net**.

        > [WACOM.NOTE] Você só deve usar registros CNAME ao associar o seu nome de domínio personalizado a um site que cuja carga é balanceada com o Gerenciador de Tráfego.

## <a name="enabledomain"></a>Habilitar o site do Gerenciador de Tráfego

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

  [Dotster.com]: https://dotster.com
  [Compreendendo os registros DNS]: #understanding-records
  [Configurar seus sites para modo padrão]: #bkmk_configsharedmode
  [Adicionar um registro DNS a seu domínio personalizado]: #bkmk_configurecname
  [Habilitar o Gerenciador de Tráfego para o seu site]: #enabledomain
  [Menu Central de Domínios do Dotster]: .\media\web-sites-dotster-custom-domain-name\Dotster_DomainCentralMenu.png
  [Opções de configuração de domínio do Dotster]: .\media\web-sites-dotster-custom-domain-name\Dotster_DomainMenu.png
  [1]: .\media\web-sites-dotster-custom-domain-name\Dotster_Nameservers.png
  [2]: .\media\web-sites-dotster-custom-domain-name\Dotster_DNS.png
  [3]: .\media\web-sites-dotster-custom-domain-name\Dotster_DNS_CNAME_TM.png
