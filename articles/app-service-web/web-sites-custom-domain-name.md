<properties
	pageTitle="Mapear um nome de domínio personalizado para um aplicativo do Azure"
	description="Saiba como mapear um nome de domínio (domínio vazio) para seu aplicativo no Serviço de Aplicativo do Azure."
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
	ms.date="07/26/2016"
	ms.author="cephalin"/>

# Mapear um nome de domínio personalizado para um aplicativo do Azure

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

Este artigo mostra como mapear manualmente um nome de domínio personalizado para seu aplicativo Web, back-end do aplicativo móvel ou aplicativo de API no [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md).

Seu aplicativo já vem com um subdomínio exclusivo do azurewebsites.net. Por exemplo, se seu aplicativo é chamado **contoso**, o nome de domínio é **contoso.azurewebsites.net**. No entanto, você pode mapear um nome de domínio personalizado para o aplicativo para que a URL, como `www.contoso.com`, reflita sua marca.

>[AZURE.NOTE] Obtenha ajuda dos especialistas do Azure nos [fóruns do Azure](https://azure.microsoft.com/support/forums/). Para ter um nível ainda maior de suporte, vá para o [site de Suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **Obter Suporte**.

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

## Comprar um novo domínio personalizado no portal do Azure

Se você ainda não adquiriu um nome de domínio personalizado, poderá comprá-lo e gerenciá-lo diretamente nas configurações do aplicativo no [portal do Azure](https://portal.azure.com). Esta opção facilita mapear um domínio personalizado para seu aplicativo, se seu aplicativo usa o [Gerenciador de Tráfego do Azure](web-sites-traffic-manager-custom-domain-name.md) ou não.

Para obter instruções, consulte [Comprar um nome de domínio personalizado para o Serviço de Aplicativo](custom-dns-web-site-buydomains-web-app.md).

## Mapear um domínio personalizado adquirido externamente

Se você já adquiriu um domínio personalizado no [DNS do Azure](https://azure.microsoft.com/services/dns/) ou com um provedor de terceiros, há três etapas principais para mapear o domínio personalizado para seu aplicativo:

1. [*(Somente registro A)* Obter o endereço IP do aplicativo](#vip).
1. [Habilitar o nome de domínio personalizado para seu aplicativo do Azure](#enable).
    - **Onde**: [portal do Azure](https://portal.azure.com).
    - **Por que**: para que seu aplicativo saiba responder às solicitações feitas para o nome de domínio personalizado.
2. [Crie registros DNS que mapeiam seu domínio para o aplicativo](#dns).
    - **Onde**: a própria ferramenta de gerenciamento do seu registrador de domínio (por exemplo, DNS do Azure, www.godaddy.com etc.).
    - **Por que**: para que seu registrador de domínio saiba determinar o domínio personalizado desejado para seu aplicativo do Azure.
3. [Verificar propagação do DNS](#verify).

### Tipos de domínios que você pode mapear

O Serviço de Aplicativo do Azure permite que você mapeie as seguintes categorias de domínios personalizados para seu aplicativo.

- **Domínio-raiz** - o nome de domínio que você reservou com o registrador de domínio (representado pelo registro do host `@`, normalmente). Por exemplo, **contoso.com**.
- **Subdomínio** -qualquer domínio em seu domínio-raiz. Por exemplo, **www.contoso.com** (representado pelo registro de host `www`). Você pode mapear outros subdomínios do mesmo domínio-raiz para diferentes aplicativos no Azure.
- **Domínio curinga** - [qualquer subdomínio cujo rótulo mais à esquerda do DNS é `*`](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (por exemplo, os registros de host `*` e `*.blogs`). Por exemplo, ***.contoso.com**.

### Tipos de registros DNS que você pode usar

Dependendo da necessidade, você pode usar dois tipos diferentes de registros DNS padrão para mapear seu domínio personalizado:

- [A](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A) - mapeia o nome de domínio personalizado para o endereço IP virtual do aplicativo do Azure diretamente.
- [CNAME](https://en.wikipedia.org/wiki/CNAME_record) - mapeia o nome de domínio personalizado para o nome de domínio do Azure do seu aplicativo, **&lt;*appname*>.azurewebsites.net**.

A vantagem do CNAME é que ele persiste nas alterações do endereço IP. O endereço IP virtual de seu aplicativo poderá mudar se você excluir e recriar o aplicativo ou mudar de um tipo de preço mais alto de volta para a camada **Compartilhada**. Com essa alteração, um registro CNAME ainda é válido, visto que um registro A deve ser atualizado.

O tutorial mostra as etapas para usar o registro A e também para usar o registro CNAME.

<a name="vip"></a>
## Etapa 1. *(Somente registro A)* Obter o endereço IP do aplicativo
Para mapear um nome de domínio personalizado usando um registro A, você precisa do endereço IP do seu aplicativo do Azure. Se você mapear usando um registro CNAME, ignore esta etapa e vá para a próxima seção.

1.	Faça logon no [Portal do Azure](https://portal.azure.com).
2.	Clique em **Serviços de Aplicativos** no menu à esquerda.
4.	Clique em seu aplicativo, clique em **Configurações** > **Domínios personalizados e SSL** > **Trazer Domínios Externos**.
5.	Em **Nomes de Domínio**, digite seu nome de domínio personalizado.
6.  Anote o endereço IP para um uso posterior.
7.  Mantenha esta folha do portal folha. Você voltará a ela depois de criar os registros DNS.

<a name="dns"></a>
## Etapa 2. Criar o(s) registro(s) DNS

Faça logon em seu registrador de domínio e use a ferramenta de controle para inserir um registro A ou CNAME. Cada interface do usuário do registrador é um pouco diferente, portanto, você deve consultar a documentação do provedor. Estas são algumas diretrizes gerais.

1.	Localize a página para gerenciamento de registros DNS. Procure links ou áreas do site rotuladas como **Nome de domínio**, **DNS** ou **Gerenciamento de servidor de nome**. Normalmente, o link para essa página pode ser encontrado exibindo-se as informações de conta e procurando-se um link como **Meus domínios**.
2.	Procure um link que permita adicionar ou editar os registros DNS. Pode ser listado como um **Arquivo de zona**, **Registros DNS** ou um link de configuração **Avançado**.
3.  Crie o registro e salve suas alterações.
    - [As instruções de um registro A estão aqui](#a).
    - [As instruções de um registro CNAME estão aqui](#cname).

<a name="a"></a>
### Criar um registro A

Para usar um registro A para mapear para o endereço IP do seu aplicativo do Azure, você precisa realmente criar um registro A e um registro CNAME. O registro A é para a própria resolução de DNS e o registro CNAME é para o Azure verificar se você tem o nome de domínio personalizado.

O registro A deve ser configurado da seguinte maneira (@ normalmente representa o domínio-raiz):
 
<table cellspacing="0" border="1">
  <tr>
    <th>Exemplo de FQDN</th>
    <th>Host/Nome/Nomehost</th>
    <th>Valor</th>
  </tr>
  <tr>
    <td>contoso.com (raiz)</td>
    <td>@</td>
    <td>Endereço IP da [Etapa 1]()</td>
  </tr>
  <tr>
    <td>www.contoso.com (sub)</td>
    <td>www</td>
    <td>Endereço IP da [Etapa 1]()</td>
  </tr>
  <tr>
    <td>*. contoso.com (curinga)</td>
    <td>*</td>
    <td>Endereço IP da [Etapa 1]()</td>
  </tr>
</table>

O registro CNAME adicional assume a convenção que mapeia de awverify.&lt;*subdomínio*>.& lt;*domínioraiz*> para awverify.&lt;*subdomínio*>. azurewebsites. Veja os exemplos abaixo:

<table cellspacing="0" border="1">
  <tr>
    <th>Exemplo de FQDN</th>
    <th>Host/Nome/Nomehost</th>
    <th>Valor</th>
  </tr>
  <tr>
    <td>contoso.com (raiz)</td>
    <td>awverify</td>
    <td>awverify.&lt;<i>nomeaplicativo</i>>. azurewebsites.net</td>
  </tr>
  <tr>
    <td>www.contoso.com (sub)</td>
    <td>awverify.www</td>
    <td>awverify.www.&lt;<i>nomeaplicativo</i>>. azurewebsites.net</td>
  </tr>
  <tr>
    <td>*. contoso.com (curinga)</td>
    <td>awverify</td>
    <td>awverify.&lt;<i>nomeaplicativo</i>>. azurewebsites.net</td>
  </tr>
</table>

<a name="cname"></a>
### Criar um registro CNAME

Se você usa um registro CNAME para mapear para o nome do domínio padrão do seu aplicativo do Azure, não é necessário um registro CNAME adicional como é feito com um registro A.

O registro CNAME deve ser configurado da seguinte maneira (@ normalmente representa o domínio-raiz):

<table cellspacing="0" border="1">
  <tr>
    <th>Exemplo de FQDN</th>
    <th>Host/Nome/Nomehost</th>
    <th>Valor</th>
  </tr>
  <tr>
    <td>contoso.com (raiz)</td>
    <td>@</td>
    <td>&lt;<i>nomeaplicativo</i>>. azurewebsites.net</td>
  </tr>
  <tr>
    <td>www.contoso.com (sub)</td>
    <td>www</td>
    <td>&lt;<i>nomeaplicativo</i>>. azurewebsites.net</td>
  </tr>
  <tr>
    <td>*. contoso.com (curinga)</td>
    <td>*</td>
    <td>&lt;<i>nomeaplicativo</i>>. azurewebsites.net</td>
  </tr>
</table>


>[AZURE.NOTE] Você pode usar o DNS do Azure para hospedar os registros de domínio necessários para seu aplicativo Web. Para configurar seu domínio personalizado e criar seus registros no DNS do Azure, consulte [Criar registros de DNS personalizados para um aplicativo Web](../dns/dns-web-sites-custom-domain.md).

<a name="enable"></a>
## Etapa 3. Habilitar o nome de domínio personalizado para seu aplicativo

De volta à folha **Trazer Domínios Externos** no portal do Azure (consulte a [Etapa 1](#vip)), você precisa adicionar o nome de domínio totalmente qualificado (FQDN) do seu domínio personalizado à lista.

1.	Navegue de volta para a folha **Trazer Domínios Externos** no portal do Azure.

2.	Adicionar o FQDN de seu domínio personalizado à lista (por exemplo, **www.contoso.com**).

    >[AZURE.NOTE] O Azure tentará verificar o nome de domínio usado aqui, portanto, verifique se é o mesmo nome de domínio para o qual você criou um registro DNS na [Etapa 2](#dns). Se você tiver certeza

6.  Clique em **Salvar**.

7.  Depois do novo nome de domínio personalizado estar configurado com êxito, navegue até o nome de domínio personalizado em um navegador. Agora, você deve ver seu aplicativo em execução e seu personalizado

<a name="verify"></a>
## Verificar propagação de DNS

Depois de concluir as etapas de configuração, pode levar algum tempo para que as alterações sejam propagadas, dependendo do seu provedor DNS. É possível verificar se a propagação de DNS está funcionando como esperado usando [http://digwebinterface.com/](http://digwebinterface.com/). Depois de navegar para o site, especifique os nomes de host na caixa de texto e clique em **Aprofundar-se**. Verifique os resultados para confirmar se as alterações recentes tiveram efeito.

![](./media/web-sites-custom-domain-name/1-digwebinterface.png)

> [AZURE.NOTE] A propagação das entradas de DNS pode levar até 48 horas (às vezes, mais). Se você configurou tudo corretamente, você ainda precisa aguardar até que a propagação seja bem-sucedida.

## Próximas etapas

Para obter mais informações, consulte: [Introdução ao DNS do Azure](../dns/dns-getstarted-create-dnszone.md) e [Domínio delegado para DNS do Azure](../dns/dns-domain-delegation.md)

>[AZURE.NOTE] Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.


<!-- Anchors. -->
[Overview]: #overview
[DNS record types]: #dns-record-types
[Find the virtual IP address]: #find-the-virtual-ip-address
[Create the DNS records]: #create-the-dns-records
[Enable the domain name on your web app]: #enable-the-domain-name-on-your-web-app

<!-- Images -->
[subdomain]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png

<!---HONumber=AcomDC_0727_2016-->