---
title: Mapear um nome DNS personalizado existente para aplicativos Web do Azure | Documentos do Microsoft
description: "Aprenda a adicionar um nome de domínio DNS de personalizada existente (domínio banido) para o aplicativo web, back-end do aplicativo móvel ou aplicativo de API no serviço de aplicativo do Azure.Aprenda a adicionar um nome de domínio DNS personalizado (ou seja, domínio intuitivo) ao aplicativo Web, back-end de aplicativo móvel ou aplicativo de API no Serviço de Aplicativo do Azure."
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
ms.date: 05/04/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 000440fb2c38eadc0ffdcab84a3c23bb034e834f
ms.contentlocale: pt-br
ms.lasthandoff: 05/09/2017

---
# <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a>Mapear um nome DNS personalizado existente para aplicativos Web do Azure

Este tutorial mostra como mapear um nome DNS personalizado existente para [aplicativos Web do Azure](app-service-web-overview.md). 

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Mapear um subdomínio (por exemplo, `www.contoso.com`) usando um registro CNAME
> * Mapear um domínio raiz (por exemplo, `contoso.com`) usando um registro A
> * Mapear um domínio curinga (por exemplo, `*.contoso.com`) usando um registro CNAME
> * Automatizar o mapeamento de domínio com scripts

Você pode usar um **registro CNAME** ou um **registro A** para mapear um nome DNS personalizado para o serviço de aplicativo.

> [!NOTE]
> É recomendável que você use um CNAME para todos os nomes DNS personalizados, exceto um domínio raiz (por exemplo, `contoso.com`). 
> 
> 

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa de acesso ao registro DNS para seu provedor de domínio (como GoDaddy) e as permissões para editar a configuração de seu domínio. 

Por exemplo, para adicionar entradas DNS para `contoso.com` e `www.contoso.com`, você deve ter acesso para definir as configurações de DNS para o `contoso.com` domínio raiz. 

> [!NOTE]
> Se você não tiver um domínio existente de nome, considere o seguinte a [tutorial de domínio do serviço de aplicativo](custom-dns-web-site-buydomains-web-app.md) para adquirir um domínio usando o portal do Azure. 
>
>

## <a name="prepare-your-app"></a>Preparação do aplicativo
Para mapear um nome DNS personalizado, o [plano de serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/) deve ser uma camada paga (**compartilhado**, **básica**, **padrão**, ou **Premium**). Nesta etapa, você certifique-se de que seu aplicativo de serviço de aplicativo está em com suporte camada de preços.

### <a name="sign-in-to-azure"></a>Entrar no Azure

Abra o portal do Azure. Para fazer isso, entre em [https://portal.azure.com](https://portal.azure.com) usando sua conta do Azure.

### <a name="navigate-to-your-app"></a>Navegue até seu aplicativo
No menu à esquerda, clique em **Serviços de Aplicativos** e, em seguida, clique no nome do seu aplicativo da Web.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

Você foi para a folha de gerenciamento de seu aplicativo Web (_folha_: uma página do portal que abre horizontalmente).  

### <a name="check-the-pricing-tier"></a>Verifique o tipo de preço

No painel de navegação à esquerda da folha do aplicativo Web, role até a seção **Configurações** e selecione **Escalar verticalmente (Plano do Serviço de Aplicativo)**.

![Menu Escalar verticalmente](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Certifique-se de que o aplicativo Web não esteja na camada **Gratuita**. A camada atual do seu aplicativo é realçada por uma caixa azul escuro. 

![Verificar tipo de preço](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Não há suporte para DNS personalizado no tipo **Gratuito**. Se você precisar escalar verticalmente, siga a próxima seção. Caso contrário, feche o **escolha sua camada de preços** folha e pule para [mapear um registro CNAME](#cname) ou [mapear um registro](#a).

### <a name="scale-up-your-app-service-plan"></a>Escalar verticalmente seu plano do Serviço de Aplicativo

Selecione qualquer uma das camadas não estão livres (**compartilhado**, **básica**, **padrão**, ou **Premium**). 

Clique em **Selecionar**.

![Verificar tipo de preço](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Quando você vir a notificação abaixo, a operação de escala terá sido concluída.

![Confirmação da operação de escala](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname"></a>

## <a name="map-a-cname-record"></a>Criar um registro CNAME

No exemplo do tutorial, você quer adicionar um registro CNAME para o `www` subdomínio (`www.contoso.com`). 

### <a name="access-dns-records-with-domain-provider"></a>Acessar registros DNS com o provedor de domínio

Primeiro, entre no site do seu provedor de domínio.

Localize a página para gerenciamento de registros DNS. Cada provedor de domínio tem sua própria interface de registros DNS, portanto, consulte a documentação do provedor. Procure links ou áreas do site rotuladas como **Nome de domínio**, **DNS** ou **Gerenciamento de Servidor de Nomes**. 

Normalmente, você pode encontrar o link exibindo as informações de conta e procurando um link como **Meus domínios**. Depois, procure um link que permita gerenciar os registros DNS. Esse link pode ser chamado de **Arquivo de zona**, **Registros DNS** ou **Configuração avançada**.

A captura de tela a seguir é um exemplo de uma página de registros DNS:

![Exemplo de página de registros DNS](./media/app-service-web-tutorial-custom-domain/example-record-ui.png)

No exemplo de captura de tela, clique em **Adicionar** para criar um registro. Alguns provedores têm links diferentes para adicionar tipos de registro diferentes. Novamente, consulte a documentação de seu provedor.

> [!NOTE]
> Para certos provedores, como GoDaddy, as alterações nos registros DNS não entram em vigor até que você clique em um link **Salvar Alterações** separado. 
>
>

### <a name="create-the-cname-record"></a>Criar um registro CNAME

Adicionar um registro CNAME para mapear um subdomínio para o nome de host do aplicativo padrão (`<app_name>.azurewebsites.net`).

Para o `www.contoso.com` exemplo de domínio, o registro CNAME deve apontar o nome `www` para `<app_name>.azurewebsites.net`.

Sua página de registros DNS parece com a captura de tela a seguir:

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/cname-record.png)

### <a name="enable-the-cname-record-mapping-in-your-app"></a>Habilitar o mapeamento de registro CNAME em seu aplicativo

Agora você está pronto para adicionar seus nomes DNS configurados ao seu aplicativo.

No painel de navegação à esquerda da folha do aplicativo, clique em **domínios personalizados**. 

![Menu de domínio personalizado](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Na folha **Domínios personalizados** de seu aplicativo, você precisa adicionar o nome DNS totalmente qualificado personalizado (`www.contoso.com`) à lista.

Clique no ícone **+** próximo a **Adicionar nome de host**.

![Adicionar nome do host](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Digite o nome de domínio totalmente qualificado para o qual você configurou o registro CNAME anteriormente (por exemplo, `www.contoso.com`), depois clique em **Validar**.

Caso contrário, o botão **Adicionar nome de host** será ativado. 

Verifique se **Tipo de registro de nome de host** está definido como **Registro A (example.com)**.

Clique em **Adicionar nome de host** para adicionar o nome DNS ao seu aplicativo.

![Adicionar nome DNS para o aplicativo](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Pode demorar algum tempo para o novo nome do host ser refletido na página **Domínios personalizados** de seu aplicativo. Tente atualizar o navegador para atualizar os dados.

![Registro CNAME adicionado](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Se você perdeu uma etapa ou cometeu um erro de digitação em algum lugar anteriormente, você verá um erro de verificação na parte inferior da página.

![Erro de verificação](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a"></a>

## <a name="map-an-a-record"></a>Mapear um registro A

No exemplo do tutorial, você quer adicionar um registro A para o domínio raiz, `contoso.com`. 

<a name="info"></a>

### <a name="copy-your-apps-ip-address"></a>Copie o endereço IP do seu aplicativo

Para mapear um registro, você precisa de endereço IP externo do aplicativo. Você pode encontrar esse endereço IP na folha **domínios personalizados**.

No painel de navegação à esquerda da folha do aplicativo, clique em **domínios personalizados**. 

![Menu de domínio personalizado](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Na página **domínios personalizados**, copie o endereço IP do aplicativo.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="access-dns-records-with-domain-provider"></a>Acessar registros DNS com o provedor de domínio

Primeiro, entre no site do seu provedor de domínio.

Localize a página para gerenciamento de registros DNS. Cada provedor de domínio tem sua própria interface de registros DNS, portanto, consulte a documentação do provedor. Procure links ou áreas do site rotuladas como **Nome de domínio**, **DNS** ou **Gerenciamento de Servidor de Nomes**. 

Normalmente, você pode encontrar o link exibindo as informações de conta e procurando um link como **Meus domínios**. Depois, procure um link que permita gerenciar os registros DNS. Esse link pode ser chamado de **Arquivo de zona**, **Registros DNS** ou **Configuração avançada**.

A captura de tela a seguir é um exemplo de uma página de registros DNS:

![Exemplo de página de registros DNS](./media/app-service-web-tutorial-custom-domain/example-record-ui.png)

No exemplo de captura de tela, clique em **Adicionar** para criar um registro. Alguns provedores têm links diferentes para adicionar tipos de registro diferentes. Novamente, consulte a documentação de seu provedor.

> [!NOTE]
> Para certos provedores, como GoDaddy, as alterações nos registros DNS não entram em vigor até que você clique em um link **Salvar Alterações** separado. 
>
>

<a name="create-a"></a>

### <a name="create-the-a-record"></a>Criar um conjunto A de registros

Para mapear um registro A para seu aplicativo, o Serviço de Aplicativo exige **dois** registros DNS:

- Um registro **A** para mapear para o endereço IP de seu aplicativo.
- Um registro **TXT** para mapear o nome de host padrão do aplicativo `<app_name>.azurewebsites.net`. Esse registro permite que o Serviço de Aplicativo verifique se você possui o domínio personalizado que você deseja mapear.

Para o `www.contoso.com` exemplo de domínio, crie os registros TXT e acordo com a tabela a seguir (`@` normalmente representa o domínio raiz). 

| Tipo de registro | Host | Valor |
| - | - | - |
| O  | `@` | Endereço IP do [copiar o endereço IP do seu aplicativo](#info) |
| TXT | `@` | `<app_name>.azurewebsites.net` |

A página de registros DNS deve parecer com a seguinte captura de tela:

![Página de Registros DNS](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a"></a>

### <a name="enable-the-a-record-mapping-in-your-app"></a>Habilitar o mapeamento de registro A em seu aplicativo

Agora você está pronto para adicionar seus nomes DNS configurados ao seu aplicativo.

De volta à página **Domínios personalizados** do seu aplicativo no portal do Azure, você precisa adicionar o nome DNS totalmente qualificado personalizado (`contoso.com`) à lista.

Clique no ícone **+** próximo a **Adicionar nome de host**.

![Adicionar nome do host](./media/app-service-web-tutorial-custom-domain/add-host-name.png)

Digite o nome de domínio totalmente qualificado para o qual você configurou o registro CNAME anteriormente (por exemplo, `contoso.com`), depois clique em **Validar**.

Caso contrário, o botão **Adicionar nome de host** será ativado. 

Verifique se **Tipo de registro de nome de host** está definido como **Registro A (example.com)**.

Clique em **Adicionar nome de host** para adicionar o nome DNS ao seu aplicativo.

![Adicionar nome DNS para o aplicativo](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

Pode demorar algum tempo para o novo nome do host ser refletido na página **Domínios personalizados** de seu aplicativo. Tente atualizar o navegador para atualizar os dados.

![Registro A adicionado](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

Se você perdeu uma etapa ou cometeu um erro de digitação em algum lugar anteriormente, você verá um erro de verificação na parte inferior da página.

![Erro de verificação](./media/app-service-web-tutorial-custom-domain/verification-error.png)

<a name="wildcard"></a>

## <a name="map-a-wildcard-domain"></a>Mapear um domínio curinga

Você também pode mapear um [DNS curinga](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (por exemplo, `*.contoso.com`) ao seu aplicativo de serviço de aplicativo. 

As etapas mostram como mapear o domínio curinga `*.contoso.com` usando um registro CNAME. 

### <a name="access-dns-records-with-domain-provider"></a>Acessar registros DNS com o provedor de domínio

Primeiro, entre no site do seu provedor de domínio.

Localize a página para gerenciamento de registros DNS. Cada provedor de domínio tem sua própria interface de registros DNS, portanto, consulte a documentação do provedor. Procure links ou áreas do site rotuladas como **Nome de domínio**, **DNS** ou **Gerenciamento de Servidor de Nomes**. 

Normalmente, você pode encontrar o link exibindo as informações de conta e procurando um link como **Meus domínios**. Depois, procure um link que permita gerenciar os registros DNS. Esse link pode ser chamado de **Arquivo de zona**, **Registros DNS** ou **Configuração avançada**.

A captura de tela a seguir é um exemplo de uma página de registros DNS:

![Exemplo de página de registros DNS](./media/app-service-web-tutorial-custom-domain/example-record-ui.png)

No exemplo de captura de tela, clique em **Adicionar** para criar um registro. Alguns provedores têm links diferentes para adicionar tipos de registro diferentes. Novamente, consulte a documentação de seu provedor.

> [!NOTE]
> Para certos provedores, como GoDaddy, as alterações nos registros DNS não entram em vigor até que você clique em um link **Salvar Alterações** separado. 
>
>

### <a name="create-the-cname-record"></a>Criar um registro CNAME

Adicionar um registro CNAME para mapear um nome curinga como nome de host do aplicativo padrão (`<app_name>.azurewebsites.net`).

Para o `*.contoso.com` exemplo de domínio, o registro CNAME deve apontar o nome `*` para `<app_name>.azurewebsites.net`.

Sua página de registros DNS parece com a captura de tela a seguir:

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/cname-record.png)

### <a name="enable-the-cname-record-mapping-in-your-app"></a>Habilitar o mapeamento de registro CNAME em seu aplicativo

Agora você pode adicionar qualquer subdomínio que coincide com o nome de curinga.

Para o `*.contoso.com` exemplo curinga, agora você pode adicionar `sub1.contoso.com` e `sub2.contoso.com`. 

No painel de navegação à esquerda da folha do aplicativo, clique em **domínios personalizados**. 

![Menu de domínio personalizado](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Clique no ícone **+** próximo a **Adicionar nome de host**.

![Adicionar nome do host](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Digite o nome de domínio totalmente qualificado para um subdomínio que corresponde a seu domínio curinga (por exemplo, `sub1.contoso.com`), em seguida, clique em **validar**.

Caso contrário, o botão **Adicionar nome de host** será ativado. 

Verifique se **Tipo de registro de nome de host** está definido como **Registro A (example.com)**.

Clique em **Adicionar nome de host** para adicionar o nome DNS ao seu aplicativo.

![Adicionar nome DNS para o aplicativo](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

Pode demorar algum tempo para o novo nome do host ser refletido na página **Domínios personalizados** de seu aplicativo. Tente atualizar o navegador para atualizar os dados.

Você pode clicar na  **+**  ícone novamente para adicionar outro nome de host que corresponde a seu domínio curinga.

Por exemplo, adicionar `sub2.contoso.com` usando as mesmas etapas acima.

![Registro CNAME adicionado](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard2.png)

## <a name="test-in-browser"></a>Testar no navegador

Em seu navegador, navegue até o nome DNS que você configurou anteriormente (`contoso.com` e `www.contoso.com`).

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="automate-with-scripts"></a>Automatizar com scripts

Você pode automatizar o gerenciamento de domínios personalizados com scripts, usando o [CLI do Azure](/cli/azure/install-azure-cli) ou [PowerShell do Azure](/powershell/azure/overview). 

### <a name="azure-cli"></a>CLI do Azure 

O comando a seguir adiciona um nome DNS personalizado configurado para um aplicativo de serviço de aplicativo. 

```bash 
az appservice web config hostname add \
    --webapp <app_name> \
    --resource-group <resourece_group_name> \ 
    --name <fully_qualified_domain_name> 
``` 

Para obter mais informações, consulte [mapear um domínio personalizado para um aplicativo web](scripts/app-service-cli-configure-custom-domain.md) 

### <a name="azure-powershell"></a>Azure PowerShell 

O comando a seguir adiciona um nome DNS personalizado configurado para um aplicativo de serviço de aplicativo. 

```PowerShell  
Set-AzureRmWebApp `
    -Name <app_name> `
    -ResourceGroupName <resourece_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app_name>.azurewebsites.net") 
```

Para obter mais informações, consulte [Atribuir um domínio personalizado para um aplicativo web](scripts/app-service-powershell-configure-custom-domain.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Mapear um subdomínio usando um registro CNAME
> * Mapear um domínio raiz usando um registro A
> * Mapear um domínio curinga usando um registro CNAME
> * Automatizar o mapeamento de domínio com scripts

Avance para o próximo tutorial para saber como associar um certificado SSL personalizado a ele.

> [!div class="nextstepaction"]
> [Associar um certificado SSL personalizado existente a Aplicativos Web do Azure](app-service-web-tutorial-custom-ssl.md)

