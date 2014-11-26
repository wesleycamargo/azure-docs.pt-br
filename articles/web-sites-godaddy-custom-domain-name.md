<properties title="Learn how to configure an Azure website to use a domain name registered with GoDaddy" pageTitle="Configure a GoDaddy domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

# Configurando um nome de domínio personalizado para um Site do Azure (GoDaddy)

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-BR/documentation/articles/web-sites-custom-domain-name" title="Dom&iacute;nio personalizado">Dom&iacute;nio personalizado</a><a href="/pt-BR/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy" class="current">GoDaddy</a><a href="/pt-BR/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Solu&ccedil;&otilde;es de rede" class="current">Solu&ccedil;&otilde;es de rede</a><a href="/pt-BR/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/pt-BR/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/pt-BR/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/pt-BR/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/pt-BR/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/pt-BR/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/pt-BR/documentation/articles/web-sites-godaddy-custom-domain-name/" title="Sites" class="current">Sites</a> | <a href="/pt-BR/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/" title="Site usando o Gerenciador de Tr&aacute;fego">Site usando o Gerenciador de Tr&aacute;fego</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Este artigo fornece instruções sobre como usar um nome de domínio personalizado adquirido do [GoDaddy][1] com os Sites do Azure.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Neste artigo:

-   [Compreendendo os registros DNS][Compreendendo os registros DNS]
-   [Adicionar um registro DNS a seu domínio personalizado][Adicionar um registro DNS a seu domínio personalizado]
-   [Habilitar o domínio no site][Habilitar o domínio no site]

## <a name="understanding-records"></a>Compreendendo os registros DNS

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

## <a name="bkmk_configurecname"></a>Adicionar um registro DNS a seu domínio personalizado

Para associar seu domínio personalizado a um Site do Azure, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo GoDaddy. Use as seguintes etapas para localizar as ferramentas DNS para o GoDaddy.com

1.  Faça logon na sua conta do GoDaddy.com e selecione **Minha Conta** e, em seguida, **Gerenciar meus domínios**. Por último, selecione o menu suspenso para o nome de domínio que você deseje usar com seu site do Azure e selecione **Gerenciar DNS**.

    ![página de domínio personalizado para GoDaddy][página de domínio personalizado para GoDaddy]

2.  Na página **Detalhes do domínio**, role até guia **Arquivo de zona DNS**. Esta é a seção usada para adicionar e modificar registros DNS para o nome do domínio.

    ![Guia Arquivo de Zona DNS][Guia Arquivo de Zona DNS]

    Selecione **Adicionar registro** para adicionar um registro existente.

    Para **editar** um registro existente, selecione o ícone de lápis e papel do lado do registro.

    > [WACOM.NOTE] Antes de adicionar os novos registros, observe que o GoDaddy já criou os registros DNS para subdomínios populares (chamados **Host** no editor), como **email**, **arquivos**, **mail** e outros. Se o nome que você deseja usar já existir, modifique o registro existente em vez de criar um novo.

3.  Ao adicionar um registro, você deve primeiro, selecionar o tipo de registro.

    ![selecione o tipo de registro][selecione o tipo de registro]

    Em seguida, você deve fornecer o **Host** (o domínio ou subdomínio personalizado) e o que **Aponta**.

    ![adicionar um registro de zona][adicionar um registro de zona]

    -   Ao adicionar um **registro (host) A** - você deve definir o campo **Host** como **@** (isso representa o nome do domínio raiz, como **contoso.com**) \* (um caractere curinga para corresponder a vários subdomínios) ou o subdomínio que você deseja usar (por exemplo, **www**). Você deve definir o campo **Aponta para** como o endereço IP do seu Site do Azure.

        > [WACOM.NOTE] Ao usar um registro (host) A, você também deve adicionar um registro CNAME com a seguinte configuração:
        >
        > -   Um valor **Host** de **awverify** que **Aponta para** um valor de **awverify.\<yourwebsitename\>.azurewebsites.net**.
        >
        > Esse registro CNAME é usado pelo Azure para validar que você possui o domínio descrito pelo registro A

    -   Ao adicionar um **registro CNAME (alias)** - você deve definir o campo **Host** como o subdomínio que deseja usar. Por exemplo, **www**. Você deve definir o campo **Aponta para** como o nome do domínio **.azurewebsites.net** do Site do Azure. Por exemplo, **contoso.azurwebsites.net**.

4.  Ao concluir a adição ou a modificação dos registros, clique em **Concluir** para salvar as alterações.

## <a name="enabledomain"></a>Habilitar o nome do domínio no seu site

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

  [GoDaddy]: /pt-BR/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy"
  [1]: https://godaddy.com
  [Compreendendo os registros DNS]: #understanding-records
  [Adicionar um registro DNS a seu domínio personalizado]: #bkmk_configurecname
  [Habilitar o domínio no site]: #enabledomain
  [página de domínio personalizado para GoDaddy]: ./media/web-sites-custom-domain-name/godaddy-customdomain.png
  [Guia Arquivo de Zona DNS]: ./media/web-sites-custom-domain-name/godaddy-zonetab.png
  [selecione o tipo de registro]: ./media/web-sites-custom-domain-name/godaddy-selectrecordtype.png
  [adicionar um registro de zona]: ./media/web-sites-custom-domain-name/godaddy-addzonerecord.png
