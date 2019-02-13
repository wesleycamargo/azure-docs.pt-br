---
title: Tutorial – adicionar provedores de identidade a seus aplicativos – Azure Active Directory B2C | Microsoft Docs
description: Saiba como adicionar provedores de identidade a seus aplicativos no Azure Active Directory B2C usando o portal do Azure.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 02/01/2019
ms.author: davidmu
ms.openlocfilehash: 2a1843f941c6abc46928b38a66025fa87c4bcea5
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55757293"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>Tutorial: Adicionar provedores de identidade a seus aplicativos no Azure Active Directory B2C

Em seus aplicativos, você talvez queira permitir que os usuários entrem com diferentes provedores de identidade. Um *provedor de identidade* cria, mantém e gerencia as informações de identidade, fornecendo serviços de autenticação para aplicativos. Você pode adicionar provedores de identidade compatíveis com o Azure AD (Azure Active Directory) B2C a seus [fluxos dos usuários](active-directory-b2c-reference-policies.md) usando o portal do Azure.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar aplicativos do provedor de identidade
> * Adicionar os provedores de identidade ao seu locatário
> * Adicione os provedores de identidade ao seu fluxo de usuário

Você normalmente usa apena um provedor de identidade em seus aplicativos, mas tem a opção de adicionar mais. Este tutorial mostra como adicionar um provedor de identidade do Azure AD e um provedor de identidade do Facebook ao seu aplicativo. Adicionar ambos esses provedores de identidade para seu aplicativo é opcional. Você também pode adicionar outros provedores de identidade, como [Amazon](active-directory-b2c-setup-amzn-app.md), [Github](active-directory-b2c-setup-github-app.md), [Google](active-directory-b2c-setup-goog-app.md), [LinkedIn](active-directory-b2c-setup-li-app.md), [Microsoft](active-directory-b2c-setup-msa-app.md) ou [Twitter](active-directory-b2c-setup-twitter-app.md). 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[Criar um fluxo de usuário](tutorial-create-user-flows.md) para permitir que os usuários se registrem e entrem em seu aplicativo. 

## <a name="create-applications"></a>Criar aplicativos

Aplicativos do provedor de identidade fornecem o identificador e a chave para habilitar a comunicação com seu locatário do Azure AD B2C. Nesta seção do tutorial, você pode criar um aplicativo do Azure AD e um aplicativo do Facebook do qual você obtém identificadores e chaves para adicionar os provedores de identidade para seu locatário. Se você estiver adicionando apenas um dos provedores de identidade, precisará criar o aplicativo para o provedor.

### <a name="create-an-azure-active-directory-application"></a>Criar um aplicativo do Azure Active Directory

Para habilitar a entrada para usuários do Azure AD, você precisará registrar um aplicativo no locatário do Azure AD. Locatário do Azure AD não é o mesmo que seu locatário do Azure AD B2C.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém seu locatário do Azure AD clicando nos **filtros de assinatura e diretório** no menu superior e escolhendo o diretório que contém o locatário do Azure AD.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
4. Selecione **Novo registro de aplicativo**.
5. Insira um nome para seu aplicativo. Por exemplo, `Azure AD B2C App`.
6. Para o **Tipo de aplicativo**, selecione `Web app / API`.
7. Para a **URL de Logon**, digite a seguinte URL em letras minúsculas, em que `your-B2C-tenant-name` é substituído pelo nome do seu locatário do B2C do Azure AD.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```
    
    Por exemplo, `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
    
    Agora todas as URLs devem estar usando [b2clogin.com](b2clogin.md).

8. Clique em **Criar**. Copie a **ID do aplicativo** a ser usada posteriormente.
9. Selecione o aplicativo e, em seguida, selecione **Configurações**.
10. Selecione **Chaves**, insira a descrição da chave, selecione uma duração e, em seguida, clique em **Salvar**. Copie o valor da chave para que você possa usá-lo posteriormente no tutorial.

### <a name="create-a-facebook-application"></a>Criar um aplicativo do Facebook

Para usar uma conta do Facebook como provedor de identidade no Azure AD B2C, você precisará criar um aplicativo no Facebook. Se ainda não tiver uma conta do Facebook, consiga uma conta em [https://www.facebook.com/](https://www.facebook.com/).

1. Entre no site [Desenvolvedores do Facebook](https://developers.facebook.com/) com suas credenciais de conta do Facebook.
2. Se ainda não tiver feito isso, você precisará registrar-se como desenvolvedor do Facebook. Para registrar-se, selecione **Registrar**, no canto superior direito da página, aceite as políticas do Facebook e conclua as etapas de registro.
3. Escolha **Meus Aplicativos** e clique em **Adicionar um Novo Aplicativo**. 
4. Insira um **Nome de Exibição** e um **Email de Contato** válido.
5. Clique em **Criar ID de Aplicativo**. Você talvez precise aceitar as políticas de plataforma do Facebook e conclua uma verificação de segurança online.
6. Escolha **Configurações** > **Básicas**.
7. Escolha uma **Categoria**, por exemplo, `Business and Pages`. Esse valor é exigido pelo Facebook, mas não é usado para o Azure AD B2C.
8. Na parte inferior da página, escolha **Adicionar Plataforma** e, em seguida, escolha **Site**.
9. Na **URL do Site**, insira `https://your-tenant-name.b2clogin.com/` substituindo `your-tenant-name` pelo nome do seu locatário. Insira uma URL para a **URL da Política de Privacidade**, por exemplo `http://www.contoso.com`. A URL da política é uma página que você mantém para fornecer informações de privacidade para o seu aplicativo.
10. Selecione **Salvar alterações**.
11. Na parte superior da página, copie o valor de **ID do Aplicativo**. 
12. Clique em **Mostrar** e copie o valor de **Segredo do Aplicativo**. Você usará ambos para configurar o Facebook como um provedor de identidade em seu locatário. **Segredo do Aplicativo** é uma credencial de segurança importante.
13. Escolha **Produtos** e, em seguida, escolha **Configurar** em **Logon do Facebook**.
14. Escolha **Configurações** em **Logon do Facebook**.
15. Em **URIs de Redirecionamento do OAuth Válidos**, insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Substitua `your-tenant-name` pelo nome do seu locatário. Clique em **Salvar Alterações** na parte inferior da página.
16. Para disponibilizar seu aplicativo do Facebook ao Azure AD B2C, clique no seletor **Status** na parte superior direita da página e defina-o como **Ligado**. Clique em **Confirmar**. Neste ponto, o Status deverá mudar de **Desenvolvimento** para **Ativo**.

## <a name="add-the-identity-providers"></a>Adicionar os provedores de identidade

Depois de criar o aplicativo para o provedor de identidade que você deseja adicionar, adicione o provedor de identidade ao seu locatário.

### <a name="add-the-azure-active-directory-identity-provider"></a>Adicionar o provedor de identidade do Azure Active Directory

1. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C clicando nos **filtros de pastas e de assinatura** no menu superior e escolhendo o diretório que contém seu locatário do Azure AD B2C.
2. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
3. Escolha **Provedores de identidade** e escolha **Adicionar**.
4. Insira um **Nome**. Por exemplo, insira *Contoso Azure AD*.
5. Selecione **Tipo de provedor de identidade**, **Open ID Connect (versão prévia)** e, em seguida, clique em **OK**.
6. Clique em **Configurar este provedor de identidade**
7. Para a **URL dos metadados**, insira a seguinte URL, substituindo `your-AD-tenant-domain` pelo nome de domínio do locatário do Azure AD.

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Por exemplo, `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

8. Para **ID do cliente**, insira a ID do aplicativo que você registrou anteriormente e, para **Segredo do cliente**, insira o valor da chave que você registrou anteriormente.
9. Opcionalmente, digite um valor para **Domain_hint**. Por exemplo, `ContosoAD`. 
10. Clique em **OK**.
11. Selecione **Mapear declarações do provedor de identidade** e defina as seguintes declarações:
    
    - Para **ID de usuário**, digite `oid`.
    - Para **Nome de exibição**, digite `name`.
    - Para **Nome**, digite `given_name`.
    - Para **Sobrenome**, digite `family_name`.
    - Para **Email**, digite `unique_name`.

12. Clique em **OK** e, em seguida, em **Criar** para salvar sua configuração.

### <a name="add-the-facebook-identity-provider"></a>Adicionar o provedor de identidade do Facebook

1. Escolha **Provedores de identidade** e escolha **Adicionar**.
2. Insira um **Nome**. Por exemplo, insira *Facebook*.
3. Escolha **Tipo de provedor de identidade**, marque **Facebook** e clique em **OK**.
4. Selecione **Configurar este provedor de identidade** e insira a ID do aplicativo que você anotou anteriormente como a **ID do cliente**. Insira o Segredo do Aplicativo que você registrou como o **Segredo do cliente**.
5. Clique em **OK** e em **Criar** para salvar sua configuração do Facebook.

## <a name="update-the-user-flow"></a>Atualizar o fluxo de usuário

No tutorial que você concluiu como parte dos pré-requisitos, você criou um fluxo de usuário para inscrição e entrada chamado *B2C_1_signupsignin1*. Nesta seção, você adiciona provedores de identidade ao fluxo de usuários *B2C_1_signupsignin1*.

1. Selecione **Fluxos dos usuários (políticas)** e, em seguida, selecione o fluxo de usuário *B2C_1_signupsignin1*.
2. Selecione **Provedores de identidade**, selecione os provedores de identidade do **Facebook** e **Azure AD da contoso** que você adicionou.
3. Clique em **Salvar**.

### <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. Na página Visão geral do fluxo do usuário que você criou, selecione **Executar o fluxo do usuário**.
2. Para **Aplicativo**, selecione o aplicativo Web denominado *webapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
3. Clique em **Executar o fluxo de usuário** e, em seguida, entre com um provedor de identidade que você adicionou anteriormente.
4. Repita as etapas 1 a 3 para outros provedores de identidade que você adicionou.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Criar aplicativos do provedor de identidade
> * Adicionar os provedores de identidade ao seu locatário
> * Adicione os provedores de identidade ao seu fluxo de usuário

> [!div class="nextstepaction"]
> [Personalize a interface do usuário dos aplicativos no Azure Active Directory B2C](tutorial-customize-ui.md)