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
ms.date: 3/13/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: e04fbd97dd4d5ecaf12edf47d80572b32d29ed00
ms.lasthandoff: 03/23/2017



---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C: registrar seu aplicativo

> [!IMPORTANT]
> Os aplicativos criados da folha Azure AD B2C no portal do Azure devem ser gerenciados do mesmo local. Se você editar os aplicativos B2C usando o PowerShell ou outro portal, eles deixarão de ter suporte e não funcionarão com o Azure AD B2C.
> 
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
2. Clique em **+Adicionar** , na parte superior da folha.
3. Insira um **Nome** para o aplicativo que descreva seu aplicativo para os consumidores. Por exemplo, você poderia digitar "Aplicativo B2C da Contoso".
4. Ativar/desativar a opção **Incluir API da Web/aplicativo Web** como **Sim**. As **URLs de Resposta** são pontos de extremidade para onde o Azure AD B2C retornará quaisquer tokens que seus aplicativo solicitarem. Por exemplo, insira: `https://localhost:44316/`.
5. Clique em **Criar** para registrar seu aplicativo.
6. Clique no aplicativo que você acabou de criar e copie a **ID de Aplicativo Cliente** globalmente exclusiva que você usará posteriormente no código. 
7. Se o aplicativo Web também chamar uma API Web protegida pelo Azure AD B2C, crie também um **Segredo de Aplicativo** acessando a folha **Chaves** e clicando no botão **Gerar Chave**.

> [!NOTE]
> Um **Segredo de Aplicativo** é uma credencial de segurança importante e deve ser protegido adequadamente.
> 
   

## <a name="register-a-web-api"></a>Registrar uma api Web
1. Na folha de recursos do B2C no Portal do Azure, clique em **Aplicativos**.
2. Clique em **+Adicionar** , na parte superior da folha.
3. Insira um **Nome** para o aplicativo que descreva seu aplicativo para os consumidores. Por exemplo, você poderia digitar "Api B2C da Contoso".
4. Ativar/desativar a opção **Incluir API da Web/aplicativo Web** como **Sim**. As **URLs de Resposta** são pontos de extremidade para onde o Azure AD B2C retornará quaisquer tokens que seus aplicativo solicitarem. Por exemplo, insira: `https://localhost:44316/`.
5. Insira um **URI da ID do aplicativo**. Esse é o identificador usado para a API Web. Por exemplo, insira 'notas'. Ele gerará o URI do identificador completo abaixo. 
6. Clique em **Criar** para registrar seu aplicativo.
7. Clique no aplicativo que você acabou de criar e copie a **ID de Aplicativo Cliente** globalmente exclusiva que você usará posteriormente no código.
8. Clique em **Escopos publicados**. Isso é onde você define as permissões (escopos) que podem ser concedidas a outros aplicativos.
9. Adicione mais escopos conforme a necessidade. Por padrão, o escopo "user_impersonation" será definido. Isso dá a outros aplicativos a capacidade de acessar essa API no lugar do usuário conectado. Essa opção pode ser removida, se desejar. 
10. Clique em **Salvar**.

## <a name="register-a-mobilenative-application"></a>Registrar um aplicativo móvel/nativo
1. Na folha de recursos do B2C no Portal do Azure, clique em **Aplicativos**.
2. Clique em **+Adicionar** , na parte superior da folha.
3. Insira um **Nome** para o aplicativo que descreva seu aplicativo para os consumidores. Por exemplo, você poderia digitar "Aplicativo B2C da Contoso".
4. Ativar/desativar a opção **Incluir cliente nativo** para **Sim**.
5. Insira um **URI de redirecionamento** com um esquema personalizado. Por exemplo, com.onmicrosoft.contoso.appname://redirect/path. Escolha um [bom URI de redirecionamento](#choosing-a-redirect-uri).
6. Clique em **Salvar** para registrar seu aplicativo.
7. Clique no aplicativo que você acabou de criar e copie a **ID de Aplicativo Cliente** globalmente exclusiva que você usará posteriormente no código.
8. Se o aplicativo nativo também chamar uma API Web protegida pelo Azure AD B2C, crie também um **Segredo de Aplicativo** acessando a folha **Chaves** e clicando no botão **Gerar Chave**.

> [!NOTE]
> Um **Segredo de Aplicativo** é uma credencial de segurança importante e deve ser protegido adequadamente.
> 

### <a name="choosing-a-redirect-uri"></a>Escolha um URI de redirecionamento
Há duas considerações importantes ao escolher um URI de redirecionamento para aplicativos móveis/nativo: 
* **Exclusivo**: o esquema do URI de redirecionamento deve ser exclusivo para cada aplicativo. Em nosso exemplo (com.onmicrosoft.contoso.appname://redirect/path), podemos usar com.onmicrosoft.contoso.appname como o esquema. É recomendável seguir esse padrão. Se dois aplicativos compartilharem o mesmo esquema, o usuário verá uma caixa de diálogo "escolher aplicativo". Se o usuário fizer uma escolha incorreta, o logon falhará. 
* **Completo**: o URI de redirecionamento deve ter um esquema e um caminho. O caminho deve conter pelo menos uma barra “/” depois do domínio (por exemplo, //contoso/ funcionará e //contoso falhará). 

## <a name="build-a-quick-start-application"></a>Criar um Aplicativo de Início Rápido
Agora que já tem um aplicativo registrado com o Azure AD B2C, você pode concluir um de nossos tutoriais de início rápido para começar a trabalhar. Aqui estão algumas recomendações:

[!INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]


