<properties title="Learn how to configure an Azure website to use a custom domain name" pageTitle="Configure a custom domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth, mwasson" />

# Configurando um nome de domínio personalizado para um Site do Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/web-sites-custom-domain-name" title="Dom&iacute;nio personalizado" class="current">Dom&iacute;nio personalizado</a><a href="/pt-br/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/pt-br/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Solu&ccedil;&otilde;es de rede">Solu&ccedil;&otilde;es de rede</a><a href="/pt-br/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/pt-br/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/pt-br/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/pt-br/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/pt-br/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/pt-br/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/web-sites-custom-domain-name/" title="Sites" class="current">Site</a> | <a href="/pt-br/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Site usando o Gerenciador de Tr&aacute;fego">Site usando o Gerenciador de Tr&aacute;fego</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

Quando você cria um site, o Azure fornece um subdomínio em azurewebsites.net. Por exemplo, se o seu site tem o nome**contoso**, a URL será **contoso.azurewebsites.net**. O Azure também fornece um endereço IP virtual.

![subdomínio contoso.azurewebsites.net][subdomínio contoso.azurewebsites.net]

Para um site de produção, é bem provável que você queira que os usuários vejam um nome de domínio personalizado. Este artigo explica como configurar um nome de domínio personalizado com o Sites do Azure. (Este artigo fornece instruções genéricas para qualquer registrador de domínio. As guias na parte superior do artigo fornecem links para artigos sobre determinados registradores.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Neste artigo:

-   [Visão geral][Visão geral]
-   [Tipos de registro DNS][Tipos de registro DNS]
-   [Localizar o endereço IP virtual][Localizar o endereço IP virtual]
-   [Criar os registros DNS][Criar os registros DNS]
-   [Criar um registro “awverify” (somente registros A)][Criar um registro “awverify” (somente registros A)]
-   [Habilitar o nome do domínio no seu site][Habilitar o nome do domínio no seu site]

## Visão geral

Estas são as etapas gerais para configurar um nome de domínio personalizado:

1.  Reserve o seu nome de domínio. Este artigo não aborda este processo. Existem muitas opções de registradores de domínio. Quando você se inscrever, o site do registrador vai fornecer instruções sobre o processo.
2.  Criar registros DNS que mapeiam o domínio do seu site do Azure.
3.  Adicionar o nome de domínio dentro do Portal de Gerenciamento do Azure.

Dentro dessa estrutura de tópicos básica, existem casos específicos a se considerar:

-   Mapeando o seu domínio raiz. O domínio raiz é aquele que você reservou com o registrador. Por exemplo, **contoso.com**.
-   Mapeando um subdomínio. Por exemplo, **blogs.contoso.com**. É possível mapear diferentes subdomínios em diferentes sites.
-   Mapeando um curinga. Por exemplo, \***.contoso.com**. Uma entrada curinga se aplica a todos os subdomínios do seu domínio.

[WACOM.INCLUDE [modos](../includes/custom-dns-web-site-modes.md)]

## Tipos de registro DNS

O DNS (Sistema de Nomes de Domínio) usa registros de dados para mapear nomes de domínio em endereços IP. Existem vários tipos de registros DNS. Para sites, você vai criar um registro *A* ou um registro *CNAME*.

-   Um registro A de **(Endereço)** mapeia um nome de domínio para um endereço IP.
-   Um registro CNAME de **(Endereço)** mapeia um nome de domínio para outro nome de domínio. O DNS usa o segundo nome para consultar o endereço. Os usuários verão apenas o primeiro nome de domínio no navegador. Por exemplo, você poderia mapear contoso.com para *\<seusite\>*.azurewebsites.net.

Se Endereço IP for alterado, um padrão CNAME ainda será válido, ao passo que o registro A precisará ser atualizado. No entanto, alguns registradores de domínio não permitem registros CNAME para o domínio raiz ou domínios curinga. Nesse caso, será preciso usar um registro A.

> [WACOM.NOTE] O endereço IP pode ser alterado se você excluir e recriar seu site ou alterar o modo de site de volta para grátis.

## Localizar o endereço IP virtual

Ignore este passo se você estiver criando um registro CNAME. Para criar um registro A, é preciso ter o endereço IP do seu site. Para obter o endereço IP:

1.  No seu navegador, abra o [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  Na guia **Sites**, clique no nome do seu site e selecione **Painel**.
3.  Selecione **Gerenciar Domínios** na inferior da página. (Se essa opção estiver desabilitada, verifique se você não está usando o modo Compartilhado, Básico ou Padrão. Para obter mais informações, consulte [Como dimensionar sites][Como dimensionar sites].)

    ![][0]

4.  O endereço IP está listado na direção da parte inferior da caixa de diálogo.

    ![][1]

## Criar os registros DNS

Faça logon em seu registrador de domínio e use a ferramenta de controle para inserir um registro A ou CNAME. Os sites de registradores são ligeiramente diferentes, mas existem algumas diretrizes gerais.

1.  Localize a página para gerenciamento de registros DNS. Procure links ou áreas do site rotuladas como **Nome de domínio**, **DNS** ou **Gerenciamento de servidor de nome**. Normalmente, o link para essa página pode ser encontrado exibindo-se as informações de conta e procurando-se um link como **Meus domínios**.
2.  Ao encontrar a página de gerenciamento, procure por um link que permita que você insira ou edite registros DNS. Ele pode estar listado como um **Arquivo de zona**, **Registros DNS** ou como um link de configuração **Avançado**.

A página pode listar registros A e CNAME em separado, ou fornecer uma lista suspensa para selecionar o tipo de registro. Em alguns casos, ela pode usar outros nomes para os tipos de registro, como **registro de Endereço IP** em vez de registro A, ou **Registros de alias**, em vez de registros CNAME. Geralmente, o registrador cria alguns registros para você, então é possível que já existam registros para o domínio raiz ou para subdomínios comuns, como **www**.

Ao criar ou editar um registro, os campos permitirão que você mapeie o seu nome de domínio para um endereço IP (para registros A) ou outro domínio (para registros CNAME). Para um registro CNAME, você mapeará *do* seu domínio personalizado *para* o seu subdomínio azurewebsites.net.

Em muitas ferramentas de registradores, basta digitar apenas a parte de subdomínio do seu domínio, não o nome de domínio inteiro. Além disso, muitas ferramentas usam “@” para significar o domínio raiz. Por exemplo:

<table cellspacing="0" border="1">
<tr>
    <th>Host</th>
    <th>Tipo de registro</th>
    <th>Endere&ccedil;o IP ou URL</th>
</tr>
<tr>
    <td>@</td>
    <td>(endere&ccedil;o) A</td>
    <td>127.0.0.1</td>
</tr>
<tr>
    <td>www</td>
    <td>CNAME (alias)</td>
    <td>contoso.azurewebsites.net</td>
</tr>
</table>

Considerando que o nome de domínio é “contoso.com”, isso criaria os seguintes registros:

-   **contoso.com** mapeado para 127.0.0.1.
-   **www.contoso.com** mapeado para **contoso.azurewebsites.net**.

## Criar um registro “awverify” (somente registros A)

Se você criar um registro A, os sites do Azure também exigem um registro CNAME especial, usado para verificar se você tem o domínio que está tentando usar. Esse registro CNAME deve ter o seguinte formato.

-   *Se o registro A mapeia o domínio raiz ou um domínio curinga:* Criar um registro CNAME que mapeia de **awverify.\<seudomínio\>** para **awverify.\<seunomedesite\>.azurewebsites.net**. Por exemplo, se o registro A for para **contoso.com**, crie um registro CNAME para **awverify.contoso.com**.
-   *Se o registro A mapeia um subdomínio específico:* Criar um registro CNAME que mapeia de **awverify.\<subdomínio\>** para **awverify.\<seunomedesite\>.azurewebsites.net**. Por exemplo, se o registro A for para **blogs.contoso.com**, crie um registro CNAME para **awverify.blogs.contoso.com**.

Os visitantes do seu site não enxergarão o subdomínio awverify; ele serve apenas para o Azure verificar o seu domínio.

## Habilitar o nome do domínio no seu site

[WACOM.INCLUDE [modos][2]]

<!-- Anchors. --> 
<!-- Images -->

  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [subdomínio contoso.azurewebsites.net]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Visão geral]: #overview
  [Tipos de registro DNS]: #dns-record-types
  [Localizar o endereço IP virtual]: #find-the-virtual-ip-address
  [Criar os registros DNS]: #create-the-dns-records
  [Habilitar o nome do domínio no seu site]: #enable-the-domain-name-on-your-website
  [modos]: ../includes/custom-dns-web-site-modes.md
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com
  [Como dimensionar sites]: http://www.windowsazure.com/pt-br/documentation/articles/web-sites-scale/
  [0]: media/web-sites-custom-domain-name/dncmntask-cname-6.png
  [1]: media/web-sites-custom-domain-name/ipaddress.png
  [2]: ../includes/custom-dns-web-site-enable-on-web-site.md
