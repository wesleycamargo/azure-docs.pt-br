<properties
	pageTitle="Configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure"
	description="Saiba como usar um nome de domínio personalizado com um aplicativo Web no Serviço de Aplicativo do Azure."
	services="app-service\web"
	documentationCenter=""
	authors="MikeWasson"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="mwasson"/>

# Configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure

<div class="dev-center-tutorial-selector sublanding">
  <a href="/documentation/articles/web-sites-custom-domain-name" title="Custom Domain" class="current">Domínio personalizado</a>
  <a href="/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a>
  <a href="/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Soluções de rede</a>
  <a href="/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a>
  <a href="/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a>
  <a href="/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a>
  <a href="/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a>
  <a href="/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a>
  <a href="/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a>
</div>
<div class="dev-center-tutorial-subselector">
  <a href="/documentation/articles/web-sites-custom-domain-name/" title="Websites" class="current">Aplicativos Web</a> | <a href="/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Web Apps using Traffic Manager">Aplicativos Web usando o Gerenciador de Tráfego</a>
</div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

Quando você cria um aplicativo Web, o Azure o atribui a um subdomínio do azurewebsites.net. Por exemplo, se seu aplicativo Web é chamado **contoso**, a URL é **contoso.azurewebsites.net**. O Azure também fornece um endereço IP virtual.

<!--todo:![contoso.azurewebsites.net subdomain][subdomain]-->

Para um aplicativo Web de produção, é bem provável que você queira que os usuários vejam um nome de domínio personalizado. Este artigo explica como configurar um domínio personalizado com [Aplicativos Web do Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714). (Este artigo fornece instruções genéricas para qualquer registrador de domínio. As guias na parte superior do artigo fornecem links para artigos sobre determinados registradores.

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Neste artigo:


-   [Visão geral]
-   [Tipos de registro DNS]
-   [Localizar o endereço IP virtual]
-   [Criar os registros DNS]
-   [Criar um registro "awverify" (somente registros A)](#awverify)
-   [Habilitar o nome do domínio no seu aplicativo Web]


## Visão geral

Estas são as etapas gerais para configurar um nome de domínio personalizado:

1. Reserve o seu nome de domínio. Este artigo não aborda este processo. Existem muitas opções de registradores de domínio. Quando você se inscrever, o site do registrador vai fornecer instruções sobre o processo.
1. Crie registros DNS que mapeiem o domínio para seu aplicativo Web do Azure.
1. Adicionar o nome de domínio dentro do [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

Dentro dessa estrutura de tópicos básica, existem casos específicos a se considerar:

- Mapeando o seu domínio raiz. O domínio raiz é aquele que você reservou com o registrador. Por exemplo, **contoso.com**.
- Mapeando um subdomínio. Por exemplo, **blogs.contoso.com**.  Você pode mapear outros subdomínios para aplicativos Web diferentes.
- Mapeando um curinga. Por exemplo, ***.contoso.com**. Uma entrada curinga se aplica a todos os subdomínios do seu domínio.

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]


## Tipos de registro DNS

O DNS (Sistema de Nomes de Domínio) utiliza registros de dados para mapear nomes de domínio em endereços IP. Existem vários tipos de registros DNS. Para aplicativos Web, você vai criar um registro *A* ou um registro *CNAME*.

- Um registro A **(Address - Endereço)** mapeia um nome de domínio para um endereço IP.
- Um registro **CNAME (Nome Canônico)** mapeia um nome de domínio para outro nome de domínio. O DNS usa o segundo nome para consultar o endereço. Os usuários verão apenas o primeiro nome de domínio no navegador. Por exemplo, você poderia mapear contoso.com para *&lt;seusite&gt;*.azurewebsites.net.

Se o endereço IP for alterado, uma entrada CNAME ainda é válida, visto que um registro A precisará ser atualizado. No entanto, alguns registradores de domínio não permitem registros CNAME para o domínio raiz ou domínios curinga. Nesse caso, será preciso usar um registro A.

> [AZURE.NOTE] O endereço IP pode ser alterado se você excluir e recriar seu aplicativo Web ou alterar o modo do aplicativo Web de volta para Gratuito.


## Localizar o endereço IP virtual

Ignore este passo se você estiver criando um registro CNAME. Para criar um registro A, é preciso ter o endereço IP virtual do seu aplicativo Web. Para obter o endereço IP:

1.	No seu navegador, abra o [Portal de Gerenciamento do Azure](https://portal.azure.com).
2.	Clique na opção **Procurar** no lado esquerdo da página.
3.	Clique na lâmina **Aplicativos Web**.
4.	Clique no nome do seu aplicativo Web.
5.	Na página **Informações Gerais**, clique em **Todas as Configurações**.
6.	Clique em **Domínios personalizados e SSL**. O endereço IP está localizado na parte inferior da página (logo acima da seção **Associações SSL**).

## Criar os registros DNS

Faça logon em seu registrador de domínio e use a ferramenta para adicionar um registro A ou CNAME. Todos os aplicativos Web de registradores são ligeiramente diferentes, mas existem algumas diretrizes gerais.

1.	Localize a página para gerenciamento de registros DNS. Procure links ou áreas do site rotuladas como **Nome de domínio**, **DNS** ou **Gerenciamento de servidor de nome**. Normalmente, o link para essa página pode ser encontrado exibindo-se as informações de conta e procurando-se um link como **Meus domínios**.
2.	Ao encontrar a página de gerenciamento, procure por um link que permita que você insira ou edite registros DNS. Ele pode estar listado como um **Arquivo de zona**, **Registros DNS** ou como um link de configuração **Avançado**.

A página pode listar registros A e CNAME separadamente ou fornecer uma lista suspensa para selecionar o tipo de registro. Em alguns casos, ela pode usar outros nomes para os tipos de registro, como **Registro de Endereço IP** em vez de registro A, ou **Registro de alias,** em vez de registro CNAME.  Geralmente, o registrador cria alguns registros para você, então é possível que já existam registros para o domínio raiz ou para subdomínios comuns, como **www**.

Ao criar ou editar um registro, os campos permitirão que você mapeie o seu nome de domínio para um endereço IP (para registros A) ou outro domínio (para registros CNAME). Para um registro CNAME, você mapeará *from* seu domínio personalizado *to* o seu subdomínio azurewebsites.net.

Em muitas ferramentas de registradores, basta digitar a parte de subdomínio do seu domínio, não o nome inteiro do domínio. Além disso, muitas ferramentas usam "@" para significar o domínio raiz. Por exemplo:

<table cellspacing="0" border="1">
  <tr>
    <th>Host</th>
    <th>Tipo de registro</th>
    <th>Endereço IP ou URL</th>
  </tr>
  <tr>
    <td>@</td>
    <td>(endereço) A</td>
    <td>127.0.0.1</td>
  </tr>
  <tr>
    <td>www</td>
    <td>CNAME (alias)</td>
    <td>contoso.azurewebsites.net</td>
  </tr>
</table>

Considerando que o nome de domínio é "contoso.com", isso criaria os seguintes registros:

- **contoso.com** mapeado para 127.0.0.1.
- **www.contoso.com** mapeado para **contoso.azurewebsites.net**.


<h2 id="awverify">Criar um registro de "awverify" (apenas registros A)</h2>

Ao criar um registro A, os o aplicativo Web também exige um registro CNAME especial, utilizado para verificar a existência do domínio que você está tentando utilizar. Esse registro CNAME deve ter o seguinte formato.

- *Se o registro A mapeia o domínio raiz ou um domínio curinga:* Criar um registro CNAME que mapeia desde **awverify.&lt;SeuDomínio&gt;** até **awverify.&lt;NomeDoSeuAplicativo&gt;.azurewebsites.net**.  Por exemplo, se o registro A for para **contoso.com**, crie um registro CNAME para **awverify.contoso.com**.
- *Se o registro A mapeia um subdomínio específico:* Criar um registro CNAME que mapeia desde **awverify.&lt;subdomínio&gt;** até **awverify.&lt;NomeDoSeuAplicativo&gt;.azurewebsites.net**. Por exemplo, se o registro A for para **blogs.contoso.com**, crie um registro CNAME para **awverify.blogs.contoso.com**.

Os visitantes do seu aplicativo Web não enxergarão o subdomínio awverify; ele serve apenas para o Azure verificar o seu domínio.

## Habilitar o nome do domínio no seu aplicativo Web

[AZURE.INCLUDE [modos](../includes/custom-dns-web-site-enable-on-web-site.md)]

>[AZURE.NOTE] Se você deseja começar a usar o Serviço de Aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## O que mudou
* Para obter um guia para a mudança de sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia para a mudança do portal antigo para o novo portal, consulte: [Referência para navegação no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- Anchors. -->
[Visão geral]: #overview
[Tipos de registro DNS]: #dns-record-types
[Localizar o endereço IP virtual]: #find-the-virtual-ip-address
[Criar os registros DNS]: #create-the-dns-records
[Habilitar o nome do domínio no seu aplicativo Web]: #enable-the-domain-name-on-your-web-app

<!-- Images -->
[subdomain]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png

<!--HONumber=49-->