---
title: Tutorial - Registrar um aplicativo para habilitar a inscrição e entrada usando o Azure Active Directory B2C | Microsoft Docs
description: Use o Portal do Azure para criar um locatário Azure AD B2C e registrar um aplicativo nele.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 03/08/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ebfe4a2d8c6a5b1d5334034e406131fac81f0c19
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713891"
---
# <a name="tutorial-register-an-application-to-enable-sign-up-and-sign-in-using-azure-active-directory-b2c"></a>Tutorial - Registrar um aplicativo para habilitar a inscrição e entrada usando o Azure Active Directory B2C

Este tutorial ajuda você a registrar um aplicativo em um locatário do Microsoft Azure Active Directory (Azure AD) B2C em poucos minutos.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar um locatário do Azure AD B2C
> * Vincular seu locatário à sua assinatura
> * Registre seu aplicativo

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no [Portal do Azure](https://portal.azure.com/).

## <a name="create-an-azure-ad-b2c-tenant"></a>Criar um locatário do Azure AD B2C

Os recursos B2C não podem ser habilitados em seus locatários existentes. Você precisa um locatário do Azure AD B2C.

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

Parabéns, você criou um locatário do Azure Active Directory B2C. Você é um Administrador Global do locatário. Você pode adicionar outros Administradores Globais conforme necessário. Para alternar para o novo locatário, clique no *link gerenciar seu novo locatário*.

![Gerenciar seu novo link de locatário](./media/active-directory-b2c-get-started/manage-new-b2c-tenant-link.png)

> [!IMPORTANT]
> Se você está planejando usar um locatário B2C para um aplicativo de produção, leia o artigo sobre [escala de produção versus locatários de visualização do B2C](active-directory-b2c-reference-tenant-type.md). Há problemas conhecidos quando você exclui um locatário B2C existente e o recria com o mesmo nome de domínio. Você precisa criar um locatário B2C com um nome de domínio diferente.
>
>

## <a name="link-your-tenant-to-your-subscription"></a>Vincular seu locatário à sua assinatura

Você precisa vincular o seu locatário do Azure AD B2C à sua assinatura do Azure para habilitar todas as funcionalidades do B2C e pagar pelos encargos de uso. Para saber mais, leia este [artigo](active-directory-b2c-how-to-enable-billing.md). Se você não vincular seu locatário do Azure AD B2C à sua assinatura do Azure, alguma funcionalidade será bloqueada e você verá uma mensagem de aviso ("Nenhuma assinatura vinculada a este locatário B2C ou A assinatura precisa de sua atenção.") nas configurações do B2C. É importante que você execute esta etapa antes de enviar seus aplicativos para produção.


[!INCLUDE [active-directory-b2c-find-service-settings](../../includes/active-directory-b2c-find-service-settings.md)]

Você também pode acessar a folha digitando `Azure AD B2C` em **Pesquisar recursos** na parte superior do portal. Na lista de resultados, selecione **Azure AD B2C** para acessar a folha de configurações do B2C.

## <a name="register-your-application"></a>Registre seu aplicativo

Para compilar um aplicativo que aceite a inscrição e a entrada do consumidor, primeiro será necessário registrar o aplicativo em um locatário do Azure Active Directory B2C. Obtenha seu próprio locatário usando as etapas descritas em [Criar um locatário do Azure AD B2C](active-directory-b2c-get-started.md).

Os aplicativos criados no Portal do Azure devem ser gerenciados do mesmo local. Se você editar os aplicativos B2C do Azure AD usando o PowerShell ou outro portal, eles deixarão de ter suporte e não funcionarão com o Azure AD B2C. Consulte os detalhes da seção [aplicativos com falha](#faulted-apps). 

Este artigo usa exemplos que ajudarão você a começar a usar nossos exemplos. Você pode aprender mais sobre esses exemplos nos artigos subsequentes.

### <a name="navigate-to-b2c-settings"></a>Navegue até as configurações de B2C

Entre no [Portal do Azure](https://portal.azure.com/) como Administrador Global do locatário B2C. 

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](../../includes/active-directory-b2c-portal-navigate-b2c-service.md)]

### <a name="choose-next-steps-based-on-your-application-type"></a>Escolha as próximas etapas com base em seu tipo de aplicativo

* [Registrar um aplicativo Web](#register-a-web-app)
* [Registrar uma API Web](#register-a-web-api)
* [Registrar um aplicativo móvel ou nativo](#register-a-mobile-or-native-app)
 
### <a name="register-a-web-app"></a>Registrar um aplicativo Web

[!INCLUDE [active-directory-b2c-register-web-app](../../includes/active-directory-b2c-register-web-app.md)]

### <a name="create-a-web-app-client-secret"></a>Criar um segredo de cliente do aplicativo Web

Se o aplicativo Web chamar uma API da Web protegida pelo Azure AD B2C, execute estas etapas:
   1. Crie um segredo do aplicativo acessando a folha **Chaves** e clicando no botão **Gerar Chave**. Anote o valor da **Chave do Aplicativo**. Use o valor como o segredo do aplicativo no código do seu aplicativo.
   2. Clique em **Acesso à API**, clique em **Adicionar** e selecione sua API da Web e escopos (permissões).

> [!NOTE]
> Um **Segredo de Aplicativo** é uma credencial de segurança importante e deve ser protegido adequadamente.
> 

[Vá até **próximas etapas**](#next-steps)

### <a name="register-a-web-api"></a>Registrar uma API Web

[!INCLUDE [active-directory-b2c-register-web-api](../../includes/active-directory-b2c-register-web-api.md)]

Clique em **Escopos publicados** para adicionar mais escopos, conforme o necessário. Por padrão, o escopo "user_impersonation" está definido. O escopo user_impersonation dá a outros aplicativos a capacidade de acessar essa API em nome do usuário conectado. Se você quiser, o escopo user_impersonation pode ser removido.

[Vá até **próximas etapas**](#next-steps)

### <a name="register-a-mobile-or-native-app"></a>Registrar um aplicativo móvel ou nativo

[!INCLUDE [active-directory-b2c-register-mobile-native-app](../../includes/active-directory-b2c-register-mobile-native-app.md)]

[Vá até **próximas etapas**](#next-steps)

### <a name="choosing-a-web-app-or-api-reply-url"></a>Escolher aplicativo Web ou URL de resposta da API

No momento, os aplicativos registrados no Azure AD B2C estão restritos a um conjunto limitado de valores para URLs de resposta. O URL de resposta para serviços e aplicativos Web deve começar com o esquema `https`, e todos os valores de URL de resposta devem compartilhar um único domínio DNS. Por exemplo, você não pode registrar um aplicativo Web que tenha uma destas URLs de resposta:

`https://login-east.contoso.com`

`https://login-west.contoso.com`

O sistema de registro compara o nome DNS completo da URL de resposta existente ao nome DNS da URL de resposta que você está adicionando. A solicitação para adicionar o nome DNS falhará se alguma das condições abaixo for verdadeira:

* Se o nome DNS completo da URL de resposta nova não coincidir com o nome DNS da URL de resposta existente.
* Se o nome DNS completo da URL de resposta nova não for um subdomínio da URL de resposta existente.

Por exemplo, se o aplicativo tiver esta URL de resposta:

`https://login.contoso.com`

Você pode adicionar a ele, desta forma:

`https://login.contoso.com/new`

Nesse caso, os nome DNS corresponde exatamente. Ou você pode fazer isto:

`https://new.login.contoso.com`

Nesse caso, você está se referindo a um subdomínio DNS logon.contoso.com. Se você quiser ter um aplicativo com login-east.contoso.com e login-west.contoso.com como URLs de resposta, deverá adicionar as seguintes URLs de resposta nesta ordem:

`https://contoso.com`

`https://login-east.contoso.com`

`https://login-west.contoso.com`

Os dois últimos podem ser adicionados porque eles são subdomínios da primeira URL de resposta, contoso.com.

### <a name="choosing-a-native-app-redirect-uri"></a>Escolher um URI de redirecionamento do aplicativo nativo

Há duas considerações importantes ao escolher um URI de redirecionamento para aplicativos móveis/nativo:

* **Exclusivo**: o esquema do URI de redirecionamento deve ser exclusivo para cada aplicativo. NO exemplo (com.onmicrosoft.contoso.appname://redirect/path), usamos com.onmicrosoft.contoso.appname como o esquema. É recomendável seguir esse padrão. Se dois aplicativos compartilharem o mesmo esquema, o usuário verá uma caixa de diálogo "escolher aplicativo". Se o usuário fizer uma escolha incorreta, o logon falhará.
* **Completo**: o URI de redirecionamento deve ter um esquema e um caminho. O caminho deve conter pelo menos uma barra “/” depois do domínio (por exemplo, //contoso/ funciona e //contoso falha).

Verifique se não há caracteres especiais, como sublinhados, no uri de redirecionamento.

### <a name="faulted-apps"></a>Aplicativos com falha

Os aplicativos B2C NÃO devem ser editados:

* Em outros portais de gerenciamento de aplicativo, como o [Portal de Registro de Aplicativo](https://apps.dev.microsoft.com/).
* Uso da API do Graph ou do PowerShell

Se você editar o aplicativo B2C do Azure AD conforme descrito e tentar editá-lo novamente nos recursos do Azure AD B2C no Portal do Azure, ele se tornará um aplicativo com falha e o aplicativo não poderá ser usado com o Azure AD B2C. Você precisa excluir o aplicativo e criá-lo novamente.

Para excluir o aplicativo, vá para o [Portal de Registro de Aplicativo](https://apps.dev.microsoft.com/) e exclua o aplicativo localmente. Para que o aplicativo fique visível, você precisa ser o proprietário do aplicativo (e não apenas um administrador do locatário).

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Criar um locatário do Azure AD B2C
> * Vincular seu locatário à sua assinatura
> * Registre seu aplicativo

> [!div class="nextstepaction"]
> [Habilite um aplicativo web para autenticar com as contas](active-directory-b2c-tutorials-web-app.md)