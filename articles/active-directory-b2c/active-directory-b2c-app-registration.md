---
title: 'Azure Active Directory B2C: registro de aplicativos | Microsoft Docs'
description: Como registrar seu aplicativo com o B2C do Active Directory do Azure
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 20e92275-b25d-45dd-9090-181a60c99f69
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 6/13/2017
ms.author: parakhj
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 3499ff57e650c70679dfa018eec5dbe1a6173a33
ms.contentlocale: pt-br
ms.lasthandoff: 07/04/2017



---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C: registrar seu aplicativo

> [!IMPORTANT]
> Os aplicativos criados da folha Azure AD B2C no portal do Azure devem ser gerenciados do mesmo local. Se você editar os aplicativos B2C usando o PowerShell ou outro portal, eles deixarão de ter suporte e não funcionarão com o Azure AD B2C. Leia mais [abaixo](#faulted-apps).
>

## <a name="prerequisite"></a>Pré-requisito

Para compilar um aplicativo que aceite a inscrição e a entrada do consumidor, primeiro será necessário registrar o aplicativo em um locatário do Azure Active Directory B2C. Obtenha seu próprio locatário usando as etapas descritas em [Criar um locatário do Azure AD B2C](active-directory-b2c-get-started.md). Depois de seguir todas as etapas indicadas nesse artigo, você terá a folha de recursos B2C fixada em seu Quadro Inicial.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="navigate-to-the-b2c-features-blade"></a>Navegue até a folha de recursos do B2C

Se a folha de recursos B2C estiver fixada no seu Quadro Inicial, você a verá assim que entrar no [Portal do Azure](https://portal.azure.com/) como o Administrador Global do locatário B2C.

Você também poderá acessar a folha clicando em **Mais serviços** e então pesquisar **Azure AD B2C** no painel de navegação à esquerda no [portal do Azure](https://portal.azure.com/).

> [!IMPORTANT]
> Você precisa ser um Administrador Global do locatário do B2C para ser capaz de acessar a folha de recursos do B2C. Um Administrador Global de qualquer outro locatário ou um Usuário de qualquer outro locatário não pode acessá-la.  Você pode alternar para o locatário B2C usando o seletor de locatário no canto superior direito do portal do Azure.
>
>

## <a name="register-a-web-application"></a>Registrar um aplicativo Web

1. Na folha de recursos do B2C no Portal do Azure, clique em **Aplicativos**.
1. Clique em **+Adicionar** , na parte superior da folha.
1. Insira um **Nome** para o aplicativo que descreva seu aplicativo para os consumidores. Por exemplo, você poderia digitar "Aplicativo B2C da Contoso".
1. Ativar/desativar a opção **Incluir API da Web/aplicativo Web** como **Sim**.
1. Insira um valor [adequado](#limitations) para as **URLs de Resposta**, que são pontos de extremidade onde o Azure AD B2C retornará todos os tokens que seu aplicativo solicitar. Por exemplo, insira: `https://localhost:44316/`.
1. Clique em **Criar** para registrar seu aplicativo.
1. Clique no aplicativo que você acabou de criar e copie a **ID de Aplicativo Cliente** globalmente exclusiva que você usará posteriormente no código.
1. Se seu aplicativo da web também for chamar uma API da web protegida pelo Azure AD B2C, você deve:
    1. Criar um **segredo do aplicativo** acessando a folha **Chaves** e clicando o botão **Gerar Chave**.
    1. Clique em **Acesso à API**, clique em **Adicionar** e selecione sua API da web e escopos (permissões).

> [!NOTE]
> Um **Segredo de Aplicativo** é uma credencial de segurança importante e deve ser protegido adequadamente.
>

## <a name="register-a-web-api"></a>Registrar uma api Web

1. Na folha de recursos do B2C no Portal do Azure, clique em **Aplicativos**.
1. Clique em **+Adicionar** , na parte superior da folha.
1. Insira um **Nome** para o aplicativo que descreva seu aplicativo para os consumidores. Por exemplo, você poderia digitar "Api B2C da Contoso".
1. Ativar/desativar a opção **Incluir API da Web/aplicativo Web** como **Sim**.
1. Insira um valor [adequado](#choosing-a-web-app/api-reply-url) para as **URLs de Resposta**, que são pontos de extremidade onde o Azure AD B2C retornará todos os tokens que seu aplicativo solicitar. Por exemplo, insira: `https://localhost:44316/`.
1. Insira um **URI da ID do aplicativo**. Esse é o identificador usado para a API Web. Por exemplo, insira 'notas'. Ele gerará o URI do identificador completo abaixo.
1. Clique em **Criar** para registrar seu aplicativo.
1. Clique no aplicativo que você acabou de criar e copie a **ID de Aplicativo Cliente** globalmente exclusiva que você usará posteriormente no código.
1. Clique em **Escopos publicados**. Isso é onde você define as permissões (escopos) que podem ser concedidas a outros aplicativos.
1. Adicione mais escopos conforme a necessidade. Por padrão, o escopo "user_impersonation" será definido. Isso dá a outros aplicativos a capacidade de acessar essa API no lugar do usuário conectado. Essa opção pode ser removida, se desejar.
1. Clique em **Salvar**.

## <a name="register-a-mobilenative-application"></a>Registrar um aplicativo móvel/nativo

1. Na folha de recursos do B2C no Portal do Azure, clique em **Aplicativos**.
1. Clique em **+Adicionar** , na parte superior da folha.
1. Insira um **Nome** para o aplicativo que descreva seu aplicativo para os consumidores. Por exemplo, você poderia digitar "Aplicativo B2C da Contoso".
1. Ativar/desativar a opção **Incluir cliente nativo** para **Sim**.
1. Insira um **URI de redirecionamento** com um esquema personalizado. Por exemplo, com.onmicrosoft.contoso.appname://redirect/path. Escolha um [bom URI de redirecionamento](#choosing-a-native-application-redirect-uri) e não inclua caracteres especiais, como sublinhados.
1. Clique em **Salvar** para registrar seu aplicativo.
1. Clique no aplicativo que você acabou de criar e copie a **ID de Aplicativo Cliente** globalmente exclusiva que você usará posteriormente no código.
1. Se seu aplicativo nativo também for chamar uma API da web protegida pelo Azure AD B2C, você deve:
    1. Criar um **segredo do aplicativo** acessando a folha **Chaves** e clicando o botão **Gerar Chave**.
    1. Clique em **Acesso à API**, clique em **Adicionar** e selecione sua API da web e escopos (permissões).

> [!NOTE]
> Um **Segredo de Aplicativo** é uma credencial de segurança importante e deve ser protegido adequadamente.
>

## <a name="limitations"></a>Limitações

### <a name="choosing-a-web-appapi-reply-url"></a>Escolha de uma URL de resposta do aplicativo/api Web

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

### <a name="choosing-a-native-application-redirect-uri"></a>Escolha de um URI de redirecionamento do aplicativo nativo

Há duas considerações importantes ao escolher um URI de redirecionamento para aplicativos móveis/nativo:

* **Exclusivo**: o esquema do URI de redirecionamento deve ser exclusivo para cada aplicativo. Em nosso exemplo (com.onmicrosoft.contoso.appname://redirect/path), podemos usar com.onmicrosoft.contoso.appname como o esquema. É recomendável seguir esse padrão. Se dois aplicativos compartilharem o mesmo esquema, o usuário verá uma caixa de diálogo "escolher aplicativo". Se o usuário fizer uma escolha incorreta, o logon falhará.
* **Completo**: o URI de redirecionamento deve ter um esquema e um caminho. O caminho deve conter pelo menos uma barra “/” depois do domínio (por exemplo, //contoso/ funcionará e //contoso falhará).

Verifique se não há caracteres especiais, como sublinhados, no uri de redirecionamento.

### <a name="faulted-apps"></a>Aplicativos com falha

Os aplicativos B2C NÃO devem ser editados:

* Em outros portais de gerenciamento de aplicativo, como o [portal clássico do Azure](https://manage.windowsazure.com/) e o [Portal de Registro de Aplicativo](https://apps.dev.microsoft.com/).
* Uso da API do Graph ou do PowerShell

Se você editar o aplicativo B2C conforme descrito acima e tentar editá-lo novamente na folha de recursos do Azure AD B2C no portal do Azure, ele se tornará um aplicativo com falha e o aplicativo não poderá ser usado com o Azure AD B2C. Você precisará excluir o aplicativo e criá-lo novamente.

Para excluir o aplicativo, vá para o [Portal de Registro de Aplicativo](https://apps.dev.microsoft.com/) e exclua o aplicativo localmente. Para que o aplicativo fique visível, você precisa ser o proprietário do aplicativo (e não apenas um administrador do locatário).

## <a name="next-steps"></a>Próximas etapas

Agora que já tem um aplicativo registrado com o Azure AD B2C, você pode concluir um de nossos [tutoriais de início rápido](active-directory-b2c-overview.md#get-started) para começar a trabalhar.

