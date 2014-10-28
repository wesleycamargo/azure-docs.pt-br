<properties title="Learn how to configure an Azure website to use a domain name registered with Dotster" pageTitle="Configure a Dotster domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, Dotster" description="Learn how to configure an Azure website to use a domain name registered with Dotster" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth"></tags>

# Configurando um nome de domínio personalizado para um Site do Azure (Dotster)

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-BR/documentation/articles/web-sites-custom-domain-name" title="Dom&iacute;nio personalizado">Dom&iacute;nio personalizado</a><a href="/pt-BR/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/pt-BR/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Solu&ccedil;&otilde;es de rede">Solu&ccedil;&otilde;es de rede</a><a href="/pt-BR/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/pt-BR/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/pt-BR/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/pt-BR/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/pt-BR/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/pt-BR/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/pt-BR/documentation/articles/web-sites-dotster-custom-domain-name/" title="Sites" class="current">Sites</a> | <a href="/pt-BR/documentation/articles/web-sites-dotster-traffic-manager-custom-domain-name/" title="Site usando o Gerenciador de Tr&aacute;fego">Site usando o Gerenciador de Tr&aacute;fego</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][websites-cloud-services-css-guided-walkthrough]]

[WACOM.INCLUDE [intro][intro]]

Este artigo fornece instruções sobre como usar um nome de domínio personalizado adquirido do [Dotster.com][Dotster.com] com os Sites do Azure.

[WACOM.INCLUDE [introfooter][introfooter]]

Neste artigo:

-   [Compreendendo os registros DNS][Compreendendo os registros DNS]
-   [Configurar seus sites para modo básico, compartilhado ou padrão][Configurar seus sites para modo básico, compartilhado ou padrão]
-   [Adicionar um registro DNS a seu domínio personalizado][Adicionar um registro DNS a seu domínio personalizado]
-   [Habilitar o domínio no site][Habilitar o domínio no site]

## <a name="understanding-records"></a>Compreendendo os registros DNS

[WACOM.INCLUDE [understandingdns][understandingdns]]

## <a name="bkmk_configsharedmode"></a>Configurar seus sites para modo básico, compartilhado ou padrão

[WACOM.INCLUDE [modes][modes]]

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

    -   Ao adicionar um registro CNAME, você deve definir o campo **Host** como o subdomínio que deseja usar. Por exemplo, **www**. Você deve definir o campo **Aponta para** como o nome do domínio **.azurewebsites.net** do Site do Azure. Por exemplo, **contoso.azurwebsites.net**.

    -   Ao adicionar um registro A, você deve definir o campo **Host** como **@** (isso representa o nome do domínio raiz, como **contoso.com**) ou o subdomínio que você deseja usar (por exemplo, **www**). Você deve definir o campo **Aponta para** como o endereço IP do seu Site do Azure.

        > [WACOM.NOTE] Se estiver usando um registro A, você também deverá adicionar um registro CNAME com uma das seguintes configurações:
        >
        > -   Um valor **Host** de **www** que **Aponta para** um valor de **\<yourwebsitename\>.azurewebsites.net**.
        >
        > OU
        >
        > -   Um valor **Host** de **awverify.www** que **Aponta para** um valor de **awverify.\<yourwebsitename\>.azurewebsites.net**.
        >
        > Esse registro CNAME é usado pelo Azure para validar que você possui o domínio descrito pelo registro A

## <a name="enabledomain"></a>Habilitar o nome do domínio no seu site

[WACOM.INCLUDE [modes][4]]

  [Domínio personalizado]: /pt-BR/documentation/articles/web-sites-custom-domain-name "Domínio personalizado"
  [GoDaddy]: /pt-BR/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy"
  [Soluções de rede]: /pt-BR/documentation/articles/web-sites-network-solutions-custom-domain-name "Soluções de rede"
  [Register.com]: /pt-BR/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [Enom]: /pt-BR/documentation/articles/web-sites-enom-custom-domain-name "Enom"
  [Moniker]: /pt-BR/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [Dotster]: /pt-BR/documentation/articles/web-sites-dotster-custom-domain-name "Dotster"
  [DomainDiscover]: /pt-BR/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover"
  [Directnic]: /pt-BR/documentation/articles/web-sites-directnic-custom-domain-name "Directnic"
  [Sites]: /pt-BR/documentation/articles/web-sites-dotster-custom-domain-name/ "Sites"
  [Site usando o Gerenciador de Tráfego]: /pt-BR/documentation/articles/web-sites-dotster-traffic-manager-custom-domain-name/ "Site usando o Gerenciador de Tráfego"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro.md
  [Dotster.com]: https://dotster.com
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Compreendendo os registros DNS]: #understanding-records
  [Configurar seus sites para modo básico, compartilhado ou padrão]: #bkmk_configsharedmode
  [Adicionar um registro DNS a seu domínio personalizado]: #bkmk_configurecname
  [Habilitar o domínio no site]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-raw.md
  [modes]: ../includes/custom-dns-web-site-modes.md
  [Menu Central de Domínios do Dotster]: .\media\web-sites-dotster-custom-domain-name\Dotster_DomainCentralMenu.png
  [Opções de configuração de domínio do Dotster]: .\media\web-sites-dotster-custom-domain-name\Dotster_DomainMenu.png
  [1]: .\media\web-sites-dotster-custom-domain-name\Dotster_Nameservers.png
  [2]: .\media\web-sites-dotster-custom-domain-name\Dotster_DNS.png
  [3]: .\media\web-sites-dotster-custom-domain-name\Dotster_DNS_CNAME.png
  [4]: ../includes/custom-dns-web-site-enable-on-web-site.md
