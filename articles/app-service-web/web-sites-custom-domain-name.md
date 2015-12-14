<properties
	pageTitle="Configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure"
	description="Saiba como usar um nome de domínio personalizado com um aplicativo Web no Serviço de Aplicativo do Azure."
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor="jimbe"
	tags="top-support-issue"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/23/2015"
	ms.author="cephalin"/>

# Configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure

> [AZURE.SELECTOR]
- [Buy Domain for Web Apps](custom-dns-web-site-buydomains-web-app.md)
- [Web Apps with External Domains](web-sites-custom-domain-name.md)
- [Web Apps with Traffic Manager](web-sites-traffic-manager-custom-domain-name.md)
- [GoDaddy](web-sites-godaddy-custom-domain-name.md)

Quando você cria um aplicativo Web, o Azure o atribui a um subdomínio do azurewebsites.net. Por exemplo, se seu aplicativo Web é chamado **contoso**, a URL é **contoso.azurewebsites.net**. O Azure também fornece um endereço IP virtual.

Para um aplicativo Web de produção, talvez você deseje que os usuários vejam um nome de domínio personalizado. Este artigo explica como configurar um domínio personalizado com [Aplicativos Web do Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714).

Se precisar de mais ajuda em qualquer momento neste artigo, você pode contatar os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](http://azure.microsoft.com/support/forums/). Como alternativa, você também pode registrar um incidente de suporte do Azure. Para enviar um incidente, vá para o [site de Suporte do Azure](http://azure.microsoft.com/support/options/) e clique em **Obter Suporte**.

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

## Visão geral

Se você ainda não registrou um nome de domínio externo (ou seja, não *.azurewebsites.net), a maneira mais fácil de configurar um domínio personalizado é comprá-lo diretamente no [Portal do Azure](https://portal.azure.com). O processo permite que você gerencie o nome de domínio do aplicativo Web diretamente no Portal em vez de acessar um site de terceiros como o GoDaddy para gerenciá-lo. Da mesma forma, a configuração do nome de domínio no aplicativo Web é bastante simplificada, independentemente do aplicativo Web usar ou não o [Gerenciador de Tráfego do Azure](web-sites-traffic-manager-custom-domain-name.md). Para obter mais informações, veja [Comprar e configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure](custom-dns-web-site-buydomains-web-app.md).

Se você já tiver um nome de domínio ou se desejar reservar um domínio de outros registradores de domínio, veja abaixo as etapas gerais para obter um nome de domínio personalizado para o aplicativo Web: (veja as [instruções específicas para o GoDaddy.com](web-sites-godaddy-custom-domain-name.md)):

1. Reserve o seu nome de domínio. Este artigo não aborda este processo. Existem muitas opções de registradores de domínio. Quando você se inscrever, o site do registrador vai fornecer instruções sobre o processo.
1. Crie registros DNS que mapeiem o domínio para seu aplicativo Web do Azure.
1. Adicione o nome de domínio dentro do [Portal do Azure](https://portal.azure.com).

Dentro dessa estrutura de tópicos básica, existem casos específicos a se considerar:

- Mapeando o seu domínio raiz. O domínio raiz é aquele que você reservou com o registrador. Por exemplo, **contoso.com**.
- Mapeando um subdomínio. Por exemplo, **blogs.contoso.com**. Você pode mapear outros subdomínios para aplicativos Web diferentes.
- Mapeando um curinga. Por exemplo, ***.contoso.com**. Uma entrada curinga se aplica a todos os subdomínios do seu domínio.

[AZURE.INCLUDE [modos](../../includes/custom-dns-web-site-modes.md)]


## Tipos de registro DNS

O DNS (Sistema de Nomes de Domínio) usa registros de dados para mapear nomes de domínio em endereços IP. Existem vários tipos de registros DNS. Para aplicativos Web, você vai criar um registro *A* ou um registro *CNAME*.

- Um registro A de **(Endereço)** mapeia um nome de domínio para um endereço IP.
- Um registro CNAME de **(Endereço)** mapeia um nome de domínio para outro nome de domínio. O DNS usa o segundo nome para consultar o endereço. Os usuários verão apenas o primeiro nome de domínio no navegador. Por exemplo, você poderia mapear contoso.com para *&lt;seuaplicativoweb&gt;*.azurewebsites.net.

Se o Endereço IP for alterado, um padrão CNAME ainda será válido, ao passo que o registro A precisará ser atualizado. No entanto, alguns registradores de domínio não permitem registros CNAME para o domínio raiz ou domínios curinga. Nesse caso, será preciso usar um registro A.

> [AZURE.NOTE]O endereço IP pode ser alterado se você excluir e recriar seu aplicativo Web ou alterar o modo do aplicativo Web de volta para Gratuito.


## Localizar o endereço IP virtual

Ignore este passo se você estiver criando um registro CNAME. Para criar um registro A, é preciso ter o endereço IP virtual do seu aplicativo Web. Para obter o endereço IP:

1.	No seu navegador, abra o [Portal do Azure](https://portal.azure.com).
2.	Clique na opção **Procurar** no lado esquerdo da página.
3.	Clique na lâmina **Aplicativos Web**.
4.	Clique no nome do seu aplicativo Web.
5.	Na página **Informações Gerais**, clique em **Todas as Configurações**.
6.	Clique em **Domínios personalizados e SSL**.
7.	Na folha **Domínios personalizados e SSL**, clique em **Trazer domínios externos"**. O endereço IP está localizado na parte inferior dessa parte.

## Criar os registros DNS

Faça logon em seu registrador de domínio e use a ferramenta de controle para inserir um registro A ou CNAME. Todos os aplicativos Web de registradores são ligeiramente diferentes, mas existem algumas diretrizes gerais.

1.	Localize a página para gerenciamento de registros DNS. Procure links ou áreas do site rotuladas como **Nome de domínio**, **DNS** ou **Gerenciamento de servidor de nome**. Normalmente, o link para essa página pode ser encontrado exibindo-se as informações de conta e procurando-se um link como **Meus domínios**.
2.	Ao encontrar a página de gerenciamento, procure por um link que permita que você insira ou edite registros DNS. Ele pode estar listado como um **Arquivo de zona**, **Registros DNS** ou como um link de configuração **Avançado**.

A página pode listar registros A e CNAME em separado, ou fornecer uma lista suspensa para selecionar o tipo de registro. Em alguns casos, ela pode usar outros nomes para os tipos de registro, como **registro de Endereço IP** em vez de registro A, ou **Registros de alias**, em vez de registros CNAME. Geralmente, o registrador cria alguns registros para você, então é possível que já existam registros para o domínio raiz ou para subdomínios comuns, como **www**.

Ao criar ou editar um registro, os campos permitirão que você mapeie o seu nome de domínio para um endereço IP (para registros A) ou outro domínio (para registros CNAME). Para um registro CNAME, você mapeará *do* seu domínio personalizado *para* o seu subdomínio azurewebsites.net.

Em muitas ferramentas de registradores, basta digitar apenas a parte de subdomínio do seu domínio, não o nome de domínio inteiro. Além disso, muitas ferramentas usam "@" para significar o domínio raiz. Por exemplo:

<table cellspacing="0" border="1">
  <tr>
    <th>Host</th>
    <th>Tipo de registro</th>
    <th>Endereço IP ou URL</th>
  </tr>
  <tr>
    <td>@</td>
    <td>(endereço) A</td>
    <td>168.62.48.183</td>
  </tr>
  <tr>
    <td>www</td>
    <td>CNAME (alias)</td>
    <td>contoso.azurewebsites.net</td>
  </tr>
</table>

Considerando que o nome de domínio personalizado é "contoso.com", isso criaria os seguintes registros:

- **contoso.com** mapeado para 168.62.48.183.
- **www.contoso.com** mapeado para **contoso.azurewebsites.net**.

>[AZURE.NOTE]Você pode usar o DNS do Azure para hospedar os registros de domínio necessários para seu aplicativo Web. Para configurar seu domínio personalizado e criar seus registros no DNS do Azure, consulte [Criar registros de DNS personalizados para um aplicativo Web](../dns-web-sites-custom-domain).

<a name="awverify" />
## Criar um registro awverify (apenas registros A)

Ao criar um registro A, o aplicativo Web também exige um registro CNAME especial, utilizado para verificar a existência do domínio que você está tentando utilizar. Esse registro CNAME deve ter o seguinte formato.

- *Se o registro A mapeia o domínio raiz ou um domínio curinga:* crie um registro CNAME que mapeia de **awverify.&lt;seudominio&gt;** para **awverify.&lt;onomedeseuaplicativoweb&gt;.azurewebsites.net**. Por exemplo, se o registro A for para **contoso.com**, crie um registro CNAME para **awverify.contoso.com**.
- *Se o registro A mapeia o domínio raiz ou um subdomínio específico:* crie um registro CNAME que mapeia de **awverify.&lt;seudominio&gt;** para **awverify.&lt;onomedeseuaplicativoweb&gt;.azurewebsites.net**. Por exemplo, se o registro A for para **blogs.contoso.com**, crie um registro CNAME para **awverify.blogs.contoso.com**.

Os visitantes do seu aplicativo Web não enxergarão o subdomínio awverify; ele serve apenas para o Azure verificar o seu domínio.

## Habilitar o nome do domínio no seu aplicativo Web

[AZURE.INCLUDE [modos](../../includes/custom-dns-web-site-enable-on-web-site.md)]

>[AZURE.NOTE]Se desejar começar a usar o Serviço de Aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## Verificar propagação de DNS

Depois de concluir as etapas de configuração, pode levar algum tempo para que as alterações sejam propagadas, dependendo do seu provedor DNS. É possível verificar se a propagação de DNS está funcionando como esperado usando [http://digwebinterface.com/](http://digwebinterface.com/). Depois de navegar para o site, especifique os nomes de host na caixa de texto e clique em **Aprofundar-se**. Verifique os resultados para confirmar se as alterações recentes tiveram efeito.

![](./media/web-sites-custom-domain-name/1-digwebinterface.png)

> [AZURE.NOTE]A propagação das entradas de DNS leva até 48 horas (às vezes, mais). Se você configurou tudo corretamente, você ainda precisa aguardar até que a propagação seja bem-sucedida.

## Próximas etapas

Para obter mais informações, confira: [Introdução ao DNS do Azure](../dns/dns-getstarted-create-dnszone.md) e [Domínio delegado para DNS do Azure](../dns/dns-domain-delegation.md)

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, confira: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- Anchors. -->
[Overview]: #overview
[DNS record types]: #dns-record-types
[Find the virtual IP address]: #find-the-virtual-ip-address
[Create the DNS records]: #create-the-dns-records
[Enable the domain name on your web app]: #enable-the-domain-name-on-your-web-app

<!-- Images -->
[subdomain]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png

<!---HONumber=AcomDC_1203_2015-->