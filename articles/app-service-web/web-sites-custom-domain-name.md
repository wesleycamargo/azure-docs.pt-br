---
title: "Mapear um nome de domínio personalizado para um aplicativo do Azure"
description: "Saiba como mapear um nome de domínio (domínio vazio) para seu aplicativo no Serviço de Aplicativo do Azure."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: jimbe
tags: top-support-issue
ms.assetid: 48644a39-107c-45fb-9cd3-c741974ff590
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 59565c22ecd42985e8a6b81c4983fc2e87637e36
ms.openlocfilehash: 589701270770494e4ec4d127a252712249da9f3a
ms.lasthandoff: 02/16/2017


---
# <a name="map-a-custom-domain-name-to-an-azure-app"></a>Mapear um nome de domínio personalizado para um aplicativo do Azure
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

Este artigo mostra como mapear manualmente um nome de domínio personalizado para seu aplicativo Web, back-end do aplicativo móvel ou aplicativo de API no [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md). 

> [!NOTE] 
> Você sempre tem a opção de simplesmente [comprar um nome de domínio personalizado diretamente do Azure](custom-dns-web-site-buydomains-web-app.md).
>
>

Há três etapas principais para mapear o domínio personalizado para seu aplicativo:

1. [*(Somente registro A)* Obter o endereço IP do aplicativo](#vip).
2. [Crie registros DNS que mapeiam seu domínio para o aplicativo](#createdns). 
   * **Onde**: a própria ferramenta de gerenciamento do seu registrador de domínio (por exemplo, DNS do Azure, GoDaddy etc.).
   * **Por que**: para que seu registrador de domínio saiba determinar o domínio personalizado desejado para seu aplicativo do Azure.
3. [Habilitar o nome de domínio personalizado para seu aplicativo do Azure](#enable).
   * **Onde**: [portal do Azure](https://portal.azure.com).
   * **Por que**: para que seu aplicativo saiba responder às solicitações feitas para o nome de domínio personalizado.
4. [Verificar propagação do DNS](#verify).

## <a name="types-of-domains-you-can-map"></a>Tipos de domínios que você pode mapear
O Serviço de Aplicativo do Azure permite que você mapeie as seguintes categorias de domínios personalizados para seu aplicativo.

* **Domínio raiz** – o nome de domínio que você reservou com o registrador de domínios (representado pelo registro do host `@`, normalmente). 
  Por exemplo, **contoso.com**.
* **Subdomínio** -qualquer domínio em seu domínio-raiz. Por exemplo, **www.contoso.com** (representado pelo registro de host `www`).  Você pode mapear outros subdomínios do mesmo domínio-raiz para diferentes aplicativos no Azure.
* **Domínio curinga** - [qualquer subdomínio cujo rótulo mais à esquerda do DNS é `*`](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (por exemplo, os registros de host `*` e `*.blogs`). Por exemplo, **\*.contoso.com**.

## <a name="types-of-dns-records-you-can-use"></a>Tipos de registros DNS que você pode usar
Dependendo da necessidade, você pode usar dois tipos diferentes de registros DNS padrão para mapear seu domínio personalizado: 

* [A](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A) - mapeia o nome de domínio personalizado para o endereço IP virtual do aplicativo do Azure diretamente. 
* [CNAME](https://en.wikipedia.org/wiki/CNAME_record) – mapeia o nome de domínio personalizado para o nome de domínio do Azure do seu aplicativo, **&lt;*appname*>.azurewebsites.net**. 

A vantagem do CNAME é que ele persiste nas alterações do endereço IP. Se você excluir e recriar o aplicativo ou mudar de um tipo de preço mais alto de volta para o tipo **Compartilhado** , o endereço IP virtual do aplicativo poderá mudar. Com essa alteração, um registro CNAME continua sendo válido, enquanto o registro A precisa ser atualizado. 

O tutorial mostra as etapas para usar o registro A e também para usar o registro CNAME.

> [!IMPORTANT]
> Não crie um registro CNAME para seu domínio raiz (ou seja, o "registro raiz"). Para saber mais, confira [Por que um registro CNAME não pode ser usado no domínio raiz](http://serverfault.com/questions/613829/why-cant-a-cname-record-be-used-at-the-apex-aka-root-of-a-domain).
> Para mapear um domínio raiz para o aplicativo do Azure, use um registro A em vez disso.
> 
> 

<a name="vip"></a>

## <a name="step-1-a-record-only-get-apps-ip-address"></a>Etapa 1. *(Somente registro A)* Obter o endereço IP do aplicativo
Para mapear um nome de domínio personalizado usando um registro A, você precisa do endereço IP do seu aplicativo do Azure. Se você mapear usando um registro CNAME, ignore esta etapa e vá para a próxima seção.

1. Faça logon no [Portal do Azure](https://portal.azure.com).
2. Clique em **Serviços de Aplicativos** no menu à esquerda.
3. Clique em seu aplicativo e em **Domínios personalizados**.
4. Anote o endereço IP na seção Nomes de host acima...
   
   ![Mapear nome de domínio personalizado com um registro A: obter endereço IP para o aplicativo do Serviço de Aplicativo do Azure](./media/web-sites-custom-domain-name/virtual-ip-address.png)
5. Mantenha esta folha do portal folha. Você voltará a ela depois de criar os registros DNS.

<a name="createdns"></a>

## <a name="step-2-create-the-dns-records"></a>Etapa 2. Criar o(s) registro(s) DNS
Faça logon em seu registrador de domínio e use a ferramenta de controle para inserir um registro A ou CNAME. Cada interface do usuário do registrador é um pouco diferente, portanto, você deve consultar a documentação do provedor. Estas são algumas diretrizes gerais.

1. Localize a página para gerenciamento de registros DNS. Procure links ou áreas do site rotuladas como **Nome de domínio**, **DNS** ou **Gerenciamento de Servidor de Nomes**. Normalmente, você pode encontrar o link exibindo as informações de conta e procurando um link como **Meus domínios**.
2. Procure um link que permita adicionar ou editar os registros DNS. Ele pode ser um **Arquivo de zona**, link de **Registros DNS** ou um link de configuração **Avançado**.
3. Crie o registro e salve suas alterações.
   * [As instruções de um registro A estão aqui](#a).
   * [As instruções de um registro CNAME estão aqui](#cname).

<a name="a"></a>

### <a name="create-an-a-record"></a>Criar um registro A
Para usar um registro A para mapear para o endereço IP do seu aplicativo do Azure, você precisa realmente criar um registro A e um registro TXT. O registro A é para a própria resolução de DNS e o registro TXT é para o Azure verificar se você tem o nome de domínio personalizado. 

Configure seu registro A da seguinte maneira (@ normalmente representa o domínio raiz):

<table cellspacing="0" border="1">
  <tr>
    <th>Exemplo de FQDN</th>
    <th>Host A</th>
    <th>Valor A</th>
  </tr>
  <tr>
    <td>contoso.com (raiz)</td>
    <td>@</td>
    <td>Endereço IP da <a href="#vip">Etapa 1</a></td>
  </tr>
  <tr>
    <td>www.contoso.com (sub)</td>
    <td>www</td>
    <td>Endereço IP da <a href="#vip">Etapa 1</a></td>
  </tr>
  <tr>
    <td>\*.contoso.com (curinga)</td>
    <td>\*</td>
    <td>Endereço IP da <a href="#vip">Etapa 1</a></td>
  </tr>
</table>

O registro TXT adicional assume a convenção que mapeia de &lt;*subdomain*>.&lt;*rootdomain*> para &lt;*appname*>.azurewebsites.net. Configure seu registro TXT da seguinte maneira:

<table cellspacing="0" border="1">
  <tr>
    <th>Exemplo de FQDN</th>
    <th>Host TXT</th>
    <th>Valor TXT</th>
  </tr>
  <tr>
    <td>contoso.com (raiz)</td>
    <td>@</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
  </tr>
  <tr>
    <td>www.contoso.com (sub)</td>
    <td>www</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
  </tr>
  <tr>
    <td>\*.contoso.com (curinga)</td>
    <td>\*</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
  </tr>
</table>

<a name="cname"></a>

### <a name="create-a-cname-record"></a>Criar um registro CNAME
Se você usa um registro CNAME para mapear para o nome do domínio padrão do seu aplicativo do Azure, não é necessário um registro TXT adicional como é feito com um registro A. 

> [!IMPORTANT]
> Não crie um registro CNAME para seu domínio raiz (ou seja, o "registro raiz"). Para saber mais, confira [Por que um registro CNAME não pode ser usado no domínio raiz](http://serverfault.com/questions/613829/why-cant-a-cname-record-be-used-at-the-apex-aka-root-of-a-domain).
> Para mapear um domínio raiz para o aplicativo do Azure, use um [registro A](#a) em vez disso.
> 
> 

Configure seu registro CNAME da seguinte maneira (@ normalmente representa o domínio raiz):

<table cellspacing="0" border="1">
  <tr>
    <th>Exemplo de FQDN</th>
    <th>Host CNAME</th>
    <th>Valor CNAME</th>
  </tr>
  <tr>
    <td>www.contoso.com (sub)</td>
    <td>www</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
  </tr>
  <tr>
    <td>\*.contoso.com (curinga)</td>
    <td>\*</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
  </tr>
</table>

<a name="enable"></a>

## <a name="step-3-enable-the-custom-domain-name-for-your-app"></a>Etapa 3. Habilitar o nome de domínio personalizado para seu aplicativo
De volta à folha **Domínios personalizados** no portal do Azure (consulte a [Etapa 1](#vip)), você precisa adicionar o FQDN (nome de domínio totalmente qualificado) do seu domínio personalizado à lista.

1. Se não tiver feito isso, entre no [Portal do Azure](https://portal.azure.com).
2. No Portal do Azure, clique em **Serviços de Aplicativos** no menu à esquerda.
3. Clique em seu aplicativo e em **Domínios personalizados** > **Adicionar nome do host**.
4. Adicionar o FQDN de seu domínio personalizado à lista (por exemplo, **www.contoso.com**).
   
   ![Mapear um nome de domínio personalizado para um aplicativo do Azure: adicionar à lista de nomes de domínio](./media/web-sites-custom-domain-name/add-custom-domain.png)
   
   > [!NOTE]
   > O Azure tentará verificar o nome de domínio que você usar aqui. Certifique-se de que ele seja o mesmo nome de domínio para o qual você criou um registro DNS na [Etapa 2](#createdns). 
   > 
   > 
5. Clique em **Validar**.
6. Ao clicar em **Validar** , o Azure iniciará o fluxo de trabalho de verificação de domínio. Isso verificará a propriedade do domínio, bem como a disponibilidade do nome do host e o sucesso ou o erro detalhado do relatório com diretrizes prescritivas sobre como corrigir o erro.    
7. Após a validação bem-sucedida, o botão **Adicionar nome do host** ficará ativo e você poderá atribuir o nome do host. 
8. Quando o Azure terminar de configurar seu novo nome de domínio personalizado, navegue até ele em um navegador. O navegador deve abrir seu aplicativo do Azure, o que significa que o nome de domínio personalizado está configurado corretamente.

## <a name="migrate-an-active-domain-name"></a>Migrar um domínio personalizado ativo

Se o nome de domínio que você deseja mapear já está em uso por um site existente e você quiser evitar tempo de inatividade, consulte [Migrar um domínio personalizado ativo para o Serviço de Aplicativo](app-service-custom-domain-name-migrate.md).

<a name="verify"></a>

## <a name="verify-dns-propagation"></a>Verificar propagação de DNS
Depois de concluir as etapas de configuração, pode levar algum tempo para que as alterações sejam propagadas, dependendo do seu provedor DNS. É possível verificar se a propagação de DNS está funcionando como esperado usando [http://digwebinterface.com/](http://digwebinterface.com/). Depois de navegar para o site, especifique os nomes de host na caixa de texto e clique em **Aprofundar-se**. Verifique os resultados para confirmar se as alterações recentes tiveram efeito.  

![Mapear um nome de domínio personalizado para um aplicativo do Azure: verificar propagação DNS](./media/web-sites-custom-domain-name/1-digwebinterface.png)

> [!NOTE]
> A propagação das entradas de DNS pode levar até 48 horas (às vezes, mais). Se você configurou tudo corretamente, você ainda precisa aguardar até que a propagação seja bem-sucedida.
> 
> 

## <a name="next-steps"></a>Próximas etapas
Saiba como proteger seu nome de domínio personalizado com HTTPS [comprando um certificado SSL no Azure](web-sites-purchase-ssl-web-site.md) ou [usando um certificado SSL de outro lugar](web-sites-configure-ssl-certificate.md).

> [!NOTE]
> Se você deseja começar a usar o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](https://azure.microsoft.com/try/app-service/), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.
> 
> 

[Introdução ao DNS do Azure](../dns/dns-getstarted-create-dnszone.md)  
[Criar registros DNS para um aplicativo Web em um domínio personalizado](../dns/dns-web-sites-custom-domain.md)  
[Delegar domínio para o DNS do Azure](../dns/dns-domain-delegation.md)

<!-- Images -->
[subdomain]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png

