<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a custom domain name" pageTitle="Configure a custom domain name for an Azure website that uses Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

# Configurando um nome de domínio personalizado para um site do Azure usando o Gerenciador de Tráfego

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-BR/documentation/articles/web-sites-custom-domain-name" title="Dom&iacute;nio personalizado" class="current">Dom&iacute;nio personalizado</a><a href="/pt-BR/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/pt-BR/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Solu&ccedil;&otilde;es de rede">Solu&ccedil;&otilde;es de rede</a><a href="/pt-BR/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/pt-BR/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/pt-BR/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/pt-BR/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/pt-BR/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/pt-BR/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/pt-BR/documentation/articles/web-sites-custom-domain-name/" title="Sites">Sites</a> | <a href="/pt-BR/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Site usando o Gerenciador de Tr&aacute;fego" class="current">Site usando o Gerenciador de Tr&aacute;fego</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Este artigo fornece instruções genéricas sobre como usar um nome de domínio personalizado com sites do Azure que usem o Gerenciador de Tráfego no balanceamento de carga. Marque as guias na parte superior deste artigo para ver se o registrador de domínio está listado. Em caso afirmativo, selecione essa guia para etapas específicas do registrador.

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

Para associar seu domínio personalizado a um site do Azure, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo registrador de domínio do qual comprou seu nome de domínio. Use as seguintes etapas para localizar e usar as ferramentas DNS.

1.  Faça logon na sua conta em seu registrador de domínio e procure uma página de gerenciamento de registros DNS. Procure links ou áreas do site rotuladas como **Nome de domínio**, **DNS**, ou **Gerenciamento de servidor de nome**. Normalmente, um link para essa página pode ser encontrado exibindo-se as informações de conta e procurando-se um link como **Meus domínios**.

2.  Depois de localizar a página de gerenciamento para seu nome de domínio, procure um link que permita editar os registros DNS. Ele pode estar listado como um **Arquivo de zona**, **Registros DNS** ou como um link de configuração **Avançado**.

    -   A página deverá ter alguns poucos registros já criados, como uma associação de entrada '**@**' ou '\*' com uma página de "estacionamento de domínio". Ela também pode conter registros para subdomínios comuns como **www**.
    -   A página mencionará **registros CNAME** ou fornecerá uma lista suspensa para selecionar um tipo de registro. Ela também pode mencionar outros registros, como **registros A** e **registros MX**. Em alguns casos, registros CNAME serão chamados por outros nomes como um **Registro de Alias**.
    -   A página também terá campos que permitem **mapear** de um **Nome do host** ou **Nome de domínio** para outro nome de domínio.

3.  Embora as especificidades de cada registrador variem, em geral, você mapeia *do* nome de domínio personalizado (como **contoso.com**) *para* o nome de domínio do Gerenciador de Tráfego (**contoso.trafficmanager.net**) usado no site do Azure.

4.  Depois de terminar a adição ou a modificação de registros DNS no registrador, salve as alterações.

## <a name="enabledomain"></a>Habilitar o site do Gerenciador de Tráfego

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

  [Compreendendo os registros DNS]: #understanding-records
  [Configurar seus sites para modo padrão]: #bkmk_configsharedmode
  [Adicionar um registro DNS a seu domínio personalizado]: #bkmk_configurecname
  [Habilitar o Gerenciador de Tráfego para o seu site]: #enabledomain
