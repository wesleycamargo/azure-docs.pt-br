<properties title="Learn how to configure an Azure website to use a domain name registered with Register.com" pageTitle="Configure a Register.com domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

# Configurando um nome de domínio personalizado para um Site do Azure (Register.com)

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-BR/documentation/articles/web-sites-custom-domain-name" title="Dom&iacute;nio personalizado">Dom&iacute;nio personalizado</a><a href="/pt-BR/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/pt-BR/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Solu&ccedil;&otilde;es de rede">Solu&ccedil;&otilde;es de rede</a><a href="/pt-BR/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/pt-BR/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/pt-BR/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/pt-BR/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/pt-BR/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/pt-BR/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/pt-BR/documentation/articles/web-sites-registerdotcom-custom-domain-name/" title="Sites" class="current">Sites</a> | <a href="/pt-BR/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/" title="Site usando o Gerenciador de Tr&aacute;fego">Site usando o Gerenciador de Tr&aacute;fego</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Este artigo fornece instruções sobre como usar um nome de domínio personalizado adquirido do [Register.com][1] com os Sites do Azure.

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

Para associar seu domínio personalizado a um Site do Azure, você deve adicionar uma nova entrada na tabela DNS para seu domínio personalizado usando as ferramentas fornecidas pelo Register.com. Use as seguintes etapas para localizar e usar as ferramentas DNS.

1.  Faça logon na conta em register.com e selecione **Sua Conta** no canto superior direito para exibir seus domínios e selecione seu nome de domínio personalizado.

    ![a página minha conta][a página minha conta]

2.  Role a página até ver **Configurações Técnicas Avançadas**. Os links nesta seção permitem gerenciar os registros do domínio.

    -   Para registros A, use o link **Editar Registros de Endereço IP**.
    -   Para registros CNAME, use o link **Editar Registros de Aliases de Domínio**.

    ![Configurações técnicas avançadas][Configurações técnicas avançadas]

3.  Ao clicar no botão **Editar**, você verá um formulário que pode usar para modificar registros existentes ou adicionar novos. O formulário é semelhante para registros CNAME e A.

    -   Ao adicionar um registro CNAME, você deve definir o campo **.mydomainname.com** como o subdomínio que deseja usar. Por exemplo, **www**. Você deve selecionar o valor **aponta para** como o nome do domínio **.azurewebsites.net** do site do Azure. Por exemplo, **contoso.azurwebsites.net**. Deixe o **Refere-se ao Nome do Host** como **Selecionar**, pois esse campo não é obrigatório durante a criação de um registro CNAME a ser usado com sites do Azure.

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

    -   Ao adicionar um registro A, você deve definir o campo **.mydomainname.com** como o subdomínio que deseja usar (por exemplo, **www**). Deixe o campo em branco para definir o domínio raiz ou use **\*\*para criar um mapeamento de caractere curinga. Você deve definir o campo** Aponta para\*\* como o endereço IP do seu Site do Azure.

        ![formulário de registro a][formulário de registro a]

4.  Ao concluir a adição ou a modificação dos registros, clique em **Continuar** para revisar as alterações. Selecione **Continuar** novamente para salvar as alterações.

## <a name="enabledomain"></a>Habilitar o nome do domínio no seu site

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

  [Register.com]: /pt-BR/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [1]: https://www.register.com
  [Compreendendo os registros DNS]: #understanding-records
  [Configurar seus sites para modo básico, compartilhado ou padrão]: #bkmk_configsharedmode
  [Adicionar um registro DNS a seu domínio personalizado]: #bkmk_configurecname
  [Habilitar o domínio no site]: #enabledomain
  [a página minha conta]: ./media/web-sites-custom-domain-name/rdotcom-myaccount.png
  [Configurações técnicas avançadas]: ./media/web-sites-custom-domain-name/rdotcom-advancedsettings.png
  [formulário cname]: ./media/web-sites-custom-domain-name/rdotcom-editcnamerecord.png
  [formulário de registro a]: ./media/web-sites-custom-domain-name/rdotcom-editarecord.png
