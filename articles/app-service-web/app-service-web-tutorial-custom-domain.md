---
title: Mapear um nome DNS personalizado para um aplicativo web do Azure| Microsoft Docs
description: "Aprenda a adicionar um nome de domínio DNS personalizado (ou seja, domínio intuitivo) ao aplicativo Web, back-end de aplicativo móvel ou aplicativo de API no Serviço de Aplicativo do Azure."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 03/29/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: 25ddbd0c89ec7eee8eb240cf111245bb8b5add48
ms.lasthandoff: 04/04/2017


---
# <a name="map-a-custom-dns-name-to-an-azure-web-app"></a>Mapear um nome DNS personalizado para um aplicativo Web do Azure

Este tutorial mostra como mapear um nome DNS personalizado (ou seja, domínio intuitivo) para seu aplicativo Web, back-end de aplicativo móvel ou aplicativo de API no [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md). 

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

Este tutorial segue o cenário de exemplo de mapeamento de dois nomes DNS para um aplicativo no Serviço de Aplicativo:

- `contoso.com` - um domínio raiz. Você usará um [registro A](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A) para mapeá-lo no Azure. 
- `www.contoso.com` - um subdomínio de `contoso.com`. Use um [registro A](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A) ou um [registro CNAME](https://en.wikipedia.org/wiki/CNAME_record).

O tutorial também mostra como mapear um [DNS curinga](https://en.wikipedia.org/wiki/Wildcard_DNS_record) para o Serviço de Aplicativo (por exemplo, `*.contoso.com`).

## <a name="before-you-begin"></a>Antes de começar

Como este tutorial mostra como mapear um nome DNS para o Serviço de Aplicativo do Azure, você deve ter acesso administrativo para a página de configuração de DNS de seu respectivo provedor de domínio (por exemplo, GoDaddy). Por exemplo, para adicionar um mapeamento para `contoso.com` e `www.contoso.com`, você precisa ser capaz de configurar entradas DNS para `contoso.com`.

Se você ainda não tiver registrado um domínio com um provedor de domínio, poderá simplesmente [comprar um domínio diretamente do Azure e mapeá-lo para seu aplicativo](custom-dns-web-site-buydomains-web-app.md).

## <a name="step-1---prepare-your-app"></a>Etapa 1 - Preparação do aplicativo
Para mapear um nome DNS personalizado, seu [Plano do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/) deve estar no tipo **Compartilhado** ou acima. Nesta etapa, você precisa ter certeza de que seu aplicativo do Azure está no tipo de preço com suporte.

### <a name="log-in-to-azure"></a>Fazer logon no Azure

Abra o portal do Azure. 

Para fazer isso, entre em [https://portal.azure.com](https://portal.azure.com) usando sua conta do Azure.

### <a name="navigate-to-your-app"></a>Navegue até seu aplicativo
No menu à esquerda, clique em **Serviço de Aplicativo**, em seguida, clique no nome do seu aplicativo do Azure.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

Você foi para a _folha_ de seu aplicativo (uma página do portal que abre horizontalmente).  

### <a name="check-the-pricing-tier"></a>Verifique o tipo de preço
Na página **Visão geral**, que abre por padrão, verifique se o seu aplicativo não está no tipo **Gratuito**.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Não há suporte para DNS personalizado no tipo **Gratuito**. Se você precisar escalar verticalmente, siga a próxima seção. Caso contrário, pule para a [Etapa 2]().

### <a name="scale-up-your-app-service-plan"></a>Escalar verticalmente seu plano do Serviço de Aplicativo

Para escalar verticalmente seu plano, clique em **Escalar verticalmente (Plano do Serviço de Aplicativo)** no painel esquerdo.

Selecione o tipo para o qual você deseja escalar. Por exemplo, selecione **Compartilhado**. Quando estiver pronto, clique em **Selecionar**.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Quando você vir a notificação abaixo, a operação de escala terá sido concluída.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="info"></a>

## <a name="step-2---get-mapping-information"></a>Etapa 2 - Obter informações de mapeamento

Nesta etapa, você obtém as informações necessárias para criar os registros DNS posteriormente. 

### <a name="open-the-custom-domain-ui"></a>Abrir a interface do usuário do domínio personalizado

Na folha de seu aplicativo, clique em **Domínios personalizados** no menu. 

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

### <a name="copy-the-mapping-information"></a>Copie as informações de mapeamento

Na página **Domínios personalizados**, copie o **Endereço IP** do aplicativo e seu nome de host padrão em **Nomes de host atribuídos ao site**.

Você precisará do endereço IP mais tarde para o mapeamento do registro A, e do nome de host padrão para o mapeamento do registro CNAME.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

## <a name="step-3---add-dns-records"></a>Etapa 3 - Adicionar registros DNS 

Nesta etapa, adicione os registros DNS necessários para mapear seu DNS personalizado para seu aplicativo. Execute essa etapa com seu próprio provedor de domínio.

### <a name="access-dns-records-with-domain-provider"></a>Acessar registros DNS com o provedor de domínio

Primeiro, faça logon no site do seu provedor de domínio.

Depois, será necessário localizar a página para gerenciamento de registros DNS. Cada provedor de domínio tem sua própria interface de registros DNS, portanto, consulte a documentação do provedor. Em geral, você deve procurar links ou áreas do site rotuladas como **Nome de domínio**, **DNS** ou **Gerenciamento de Servidor de Nomes**. Normalmente, você pode encontrar o link exibindo as informações de conta e procurando um link como **Meus domínios**. 

Depois, procure um link que permita gerenciar os registros DNS. Esse link pode ser chamado de **Arquivo de zona**, **Registros DNS** ou **Configuração avançada**.

A captura de tela a seguir é um exemplo de como deve ser aparência de sua página de registros DNS:

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/example-record-ui.png)

No exemplo de captura de tela, clique em **Adicionar** para criar um registro. Alguns provedores têm links diferentes para adicionar tipos de registro diferentes. Novamente, consulte a documentação de seu provedor.

> [!NOTE]
> Para certos provedores, como GoDaddy, as alterações nos registros DNS não entram em vigor até que você clique em um link **Salvar Alterações** separado após a criação. 
>
>

<a name="a"></a>

### <a name="create-an-a-record"></a>Criar um registro A

No exemplo do tutorial, você quer adicionar um registro A para o domínio raiz, `contoso.com`. 

Para mapear um registro A para seu aplicativo, o Serviço de Aplicativo exige _dois_ registros DNS:

- Um registro **A** para mapear para o endereço IP de seu aplicativo.
- Um registro **TXT** para mapear para o nome de host padrão do aplicativo. Esse registro permite que o Serviço de Aplicativo verifique se você possui o domínio personalizado que você deseja mapear.

A tabela a seguir mostra como você poderia configurar um mapeamento do registro A para os tipos de domínio com suporte (`@` normalmente representa o domínio raiz). 

<table cellspacing="0" border="1">
  <tr>
    <th>Tipo de domínio</th>
    <th>Tipo de registro</th>
    <th>Host</th>
    <th>Valor</th>
  </tr>
  <tr>
    <td rowspan="2" align="left">Domínio raiz<br>(por exemplo, <code>contoso.com</code>)</td>
    <td>O </td>
    <td>@</td>
    <td>Endereço IP da <a href="#info">Etapa 2</a></td>
  </tr>
  <tr>
    <td>TXT</td>
    <td>@</td>
    <td>Nome de host padrão da <a href="#info">Etapa 2</a></td>
  </tr>
  <tr>
    <td rowspan="2" align="left">Subdomínio<br>(por exemplo, <code>www.contoso.com</code>)</td>
    <td>O </td>
    <td>www</td>
    <td>Endereço IP da <a href="#info">Etapa 2</a></td>
  </tr>
  <tr>
    <td>TXT</td>
    <td>www</td>
    <td>Nome de host padrão da <a href="#info">Etapa 2</a></td>
  </tr>
  <tr>
    <td rowspan="2" align="left">Domínio de curinga<br>(por exemplo, <code>*.contoso.com</code>)</td>
    <td>O </td>
    <td>\*</td>
    <td>Endereço IP da <a href="#info">Etapa 2</a></td>
  </tr>
  <tr>
    <td>TXT</td>
    <td>\*</td>
    <td>Nome de host padrão da <a href="#info">Etapa 2</a></td>
  </tr>
</table>

Para o exemplo de domínio `contoso.com`, sua página de registros DNS parece com a captura de tela a seguir:

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="cname"></a>

### <a name="create-a-cname-record"></a>Criar um registro CNAME
No exemplo do tutorial, você quer adicionar um registro CNAME para o domínio raiz, `contoso.com`. 

Ao contrário do [mapeamento de um registro A](#a), o mapeamento de um registro CNAME para seu aplicativo não exige qualquer registro adicional.

> [!IMPORTANT]
> Recomendamos o uso de um mapeamento CNAME. Se você excluir e recriar o aplicativo ou mudar de um tipo de hospedagem dedicada de volta para o tipo **Compartilhado** , o endereço IP virtual do aplicativo poderá mudar. Um mapeamento CNAME é válido com uma alteração desse tipo, enquanto um mapeamento A é possivelmente invalidado por um novo endereço IP. 
>
> No entanto, _não_ crie um registro CNAME para seu domínio raiz (ou seja, o "registro raiz"). Para saber mais, confira [Por que um registro CNAME não pode ser usado no domínio raiz](http://serverfault.com/questions/613829/why-cant-a-cname-record-be-used-at-the-apex-aka-root-of-a-domain).
> Para mapear um domínio raiz para o aplicativo do Azure, use um [registro A](#a) em vez disso.
> 
> 

A tabela a seguir mostra como você poderia configurar um mapeamento de registro CNAME para os tipos de domínio com suporte.

Configure seu registro CNAME da seguinte maneira.

<table cellspacing="0" border="1">
  <tr>
    <th>Tipo de domínio</th>
    <th>Host CNAME</th>
    <th>Valor CNAME</th>
  </tr>
  <tr>
    <td>Subdomínio<br>(por exemplo, <code>www.contoso.com</code>)</td>
    <td>www</td>
    <td>Nome de host padrão da <a href="#info">Etapa 2</a></td>
  </tr>
  <tr>
    <td>Domínio de curinga<br>(por exemplo, <code>*.contoso.com</code>)</td>
    <td>\*</td>
    <td>Nome de host padrão da <a href="#info">Etapa 2</a></td>
  </tr>
</table>

Para o exemplo de domínio `www.contoso.com`, sua página de registros DNS parece com a captura de tela a seguir:

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/cname-record.png)

<a name="enable"></a>

## <a name="step-4---enable-the-custom-dns-in-your-app"></a>Etapa 4 - Habilitar o DNS personalizado em seu aplicativo

Agora você está pronto para adicionar seus nomes DNS configurados (por exemplo, `contoso.com` e `www.contoso.com`) ao seu aplicativo.

De volta à página **Domínios personalizados** de seu aplicativo no Portal do Azure (consulte a [Etapa 1](#info)), você precisa adicionar o FQDN (nome de domínio totalmente qualificado) do seu domínio personalizado à lista.

### <a name="add-the-a-hostname-to-your-app"></a>Adicionar o nome do host A ao seu aplicativo

Clique no ícone **+** próximo a **Adicionar nome de host**.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/add-host-name.png)

Digite o FQDN para o qual você configurou o registro A anteriormente (por exemplo, `contoso.com`), depois clique em **Validar**.

Se você perdeu uma etapa ou cometeu um erro de digitação em algum lugar anteriormente, você verá um erro de verificação na parte inferior da página.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/verification-error.png)

Caso contrário, o botão **Adicionar nome de host** será ativado. 

Verifique se **Tipo de registro de nome de host** está definido como **Registro A (example.com)**.

Clique em **Adicionar nome de host** para adicionar o nome DNS ao seu aplicativo.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

Pode demorar algum tempo para o novo nome do host ser refletido na página **Domínios personalizados** de seu aplicativo. Tente atualizar o navegador para atualizar os dados.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

<a name="add-cname"></a>
### <a name="add-the-cname-hostname-to-your-app"></a>Adicionar o nome do host CNAME ao seu aplicativo

Clique no ícone **+** próximo a **Adicionar nome de host**.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Digite o FQDN para o qual você configurou o registro CNAME anteriormente (por exemplo, `www.contoso.com`), depois clique em **Validar**.

Se você perdeu uma etapa ou cometeu um erro de digitação em algum lugar anteriormente, você verá um erro de verificação na parte inferior da página.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

Caso contrário, o botão **Adicionar nome de host** será ativado. 

Verifique se **Tipo de registro de nome de host** está definido como **Registro A (example.com)**.

Clique em **Adicionar nome de host** para adicionar o nome DNS ao seu aplicativo.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Pode demorar algum tempo para o novo nome do host ser refletido na página **Domínios personalizados** de seu aplicativo. Tente atualizar o navegador para atualizar os dados.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

### <a name="step-5---test-in-browser"></a>Etapa 5 - Testar no navegador

Em seu navegador, navegue até o nome DNS que você configurou anteriormente (`contoso.com` e `www.contoso.com`).

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="more-resources"></a>Mais recursos

[Comprar e configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure.](custom-dns-web-site-buydomains-web-app.md)

