<properties title="Learn how to configure an Azure website to use a domain name registered with Network Solutions" pageTitle="Configure a Network Solutions domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

# Configurando um nome de domínio personalizado para um site do Azure (Network Solutions)

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-BR/documentation/articles/web-sites-custom-domain-name" title="Dom&iacute;nio personalizado">Dom&iacute;nio personalizado</a><a href="/pt-BR/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/pt-BR/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Solu&ccedil;&otilde;es de rede" class="current">Solu&ccedil;&otilde;es de rede</a><a href="/pt-BR/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/pt-BR/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/pt-BR/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/pt-BR/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/pt-BR/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/pt-BR/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/pt-BR/documentation/articles/web-sites-network-solutions-custom-domain-name/" title="Sites" class="current">Sites</a> | <a href="/pt-BR/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/" title="Site usando o Gerenciador de Tr&aacute;fego">Site usando o Gerenciador de Tr&aacute;fego</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Este artigo fornece instruções sobre como usar o nome de domínio personalizado comprado junto a [Network Solutions][Network Solutions] com sites do Azure.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Neste artigo:

-   [Compreendendo os registros DNS][Compreendendo os registros DNS]
-   [Configurar seus sites para modo básico, compartilhado ou padrão][Configurar seus sites para modo básico, compartilhado ou padrão]
-   [Adicionar um registro DNS a seu domínio personalizado][Adicionar um registro DNS a seu domínio personalizado]
-   [Habilitar o domínio no site][Habilitar o domínio no site]

## <a name="understanding-records"></a>Compreendendo os registros DNS

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

## <a name="bkmk_configsharedmode"></a>Configurar seus sites para modo básico, compartilhado ou padrão

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a>

## Adicionar um registro DNS a seu domínio personalizado

Para associar seu domínio personalizado a um site do Azure, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pela Network Solutions. Usar as seguintes etapas para localizar e usar as ferramentas DNS para networksolutions.com

1.  Faça logon na sua conta em networksolutions.com e selecione **Minha Conta** no canto superior direito.

2.  Na guia **Meus Produtos e Serviços**, selecione **Editar DNS**.

    ![editar página dns][editar página dns]

3.  Na seção **Gerenciar <yourdomainname>** da página **Nomes de Domínio**, selecione **Editar Registros DNS Avançados**.

    ![página de nomes de domínio com editar registros dns avançados realçado][página de nomes de domínio com editar registros dns avançados realçado]

4.  A página **Atualizar DNS Avançado** contém uma seção para cada tipo de registro, com um botão **Editar** abaixo de cada seção.

    -   Para registros A, use a seção **Endereço IP (Registros)**.
    -   Para registros CNAME, use a seção **Alias de Host (Registros CNAME)**.

    ![atualizar página dns avançado][atualizar página dns avançado]

5.  Ao clicar no botão **Editar**, você verá um formulário que pode usar para modificar registros existentes ou adicionar novos.

    > [WACOM.NOTE] Antes de adicionar novas entradas, observe que a Network Solutions já criou alguns registros DNS padrão para coisas como o domínio raiz (' @') e um registro curinga ('\*') para subdomínios. Se o registro que você deseja usar já existir, modifique-o em vez de criar um novo.

    -   Ao adicionar um registro CNAME, você deve definir o campo **Alias** como o subdomínio que deseja usar. Por exemplo, **www**. Você deve selecionar o campo circular ao lado do campo **Outro host** e definir **Outro host** como o nome de domínio **.azurewebsites.net** do site do Azure. Por exemplo, **contoso.azurwebsites.net**. Deixe o **Refere-se ao Nome do Host** como **Selecionar**, pois esse campo não é obrigatório durante a criação de um registro CNAME a ser usado com sites do Azure.

        ![formulário cname][formulário cname]

        > [WACOM.NOTE] Se estiver usando um registro A, você também deverá adicionar um registro CNAME com uma das seguintes configurações:
        >
        > -   Um valor **Alias** de **www** com um valor **Outro host** de **\<yourwebsitename\>.azurewebsites.net**.
        >
        > OU
        >
        > -   Um valor **Alias** de **awverify.www** com um valor **Outro host** de **awverify.\<yourwebsitename\>.azurewebsites.net**.
        >
        > Esse registro CNAME é usado pelo Azure para validar que você possui o domínio descrito pelo registro A.

    -   Ao adicionar um registro A, você deve definir o campo **Nome do Host** como **@** (isso representa o nome do domínio raiz, como **contoso.com**,) \* (um caractere curinga para corresponder a vários subdomínios) ou o subdomínio que você deseja usar (por exemplo, **www**). Você deve definir o campo **IP numérico** como o endereço IP do seu Site do Azure.

        ![formulário de registro a][formulário de registro a]

6.  Ao concluir a adição ou a modificação dos registros, clique em **Continuar** para revisar as alterações. Selecione **Salvar apenas as alterações** para salvar as alterações.

## <a name="enabledomain"></a>Habilitar o nome do domínio no seu site

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

  [Network Solutions]: https://www.networksolutions.com
  [Compreendendo os registros DNS]: #understanding-records
  [Configurar seus sites para modo básico, compartilhado ou padrão]: #bkmk_configsharedmode
  [Adicionar um registro DNS a seu domínio personalizado]: #bkmk_configurecname
  [Habilitar o domínio no site]: #enabledomain
  [editar página dns]: ./media/web-sites-custom-domain-name/ns-editdns.png
  [página de nomes de domínio com editar registros dns avançados realçado]: ./media/web-sites-custom-domain-name/ns-editadvanced.png
  [atualizar página dns avançado]: ./media/web-sites-custom-domain-name/ns-updateadvanced.png
  [formulário cname]: ./media/web-sites-custom-domain-name/ns-cname.png
  [formulário de registro a]: ./media/web-sites-custom-domain-name/ns-arecord.png
