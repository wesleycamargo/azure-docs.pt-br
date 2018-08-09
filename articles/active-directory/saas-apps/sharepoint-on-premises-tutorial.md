---
title: 'Tutorial: Integração do Azure Active Directory ao SharePoint local | Microsoft Docs'
description: Aprenda como configurar o logon único entre o Active Directory do Azure e o SharePoint local.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: jeedes
ms.openlocfilehash: f30b2356b9d3d8ecf7afcdd8ad039a1f02c47550
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438232"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>Tutorial: Integração do Active Directory do Azure com o SharePoint no local

Neste tutorial, você aprenderá como integrar o SharePoint local ao Azure AD (Azure Active Directory).

A integração do SharePoint local com o Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao SharePoint no local.
- Você pode permitir que seus usuários façam logon automaticamente no SharePoint local (Single Sign-On) com suas contas do AD do Azure.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o SharePoint local, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único no local do SharePoint

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o SharePoint no local da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>Adicionando o SharePoint no local da galeria
Para configurar a integração do SharePoint local ao Azure AD, você precisa adicionar o SharePoint local da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SharePoint local da galeria, execute as etapas a seguir:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **SharePoint local**, selecione **SharePoint local** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![SharePoint local na lista de resultados](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o SharePoint local, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber o que o usuário de contraparte no SharePoint local é para um usuário no Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SharePoint local.

Para configurar e testar o logon único do Azure AD com o SharePoint local, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Conceder acesso ao usuário de teste do SharePoint local](#grant-access-to-sharePoint-on-premises-test-user)** – para ter um equivalente de Brenda Fernandes no SharePoint local vinculado à representação do usuário no Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo SharePoint local.

**Para configurar o logon único do Azure AD com o SharePoint local, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo do **SharePoint local**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Caixa de diálogo Logon único](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_samlbase.png)

1. Na seção **URLs e domínio do SharePoint local**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do SharePoint local](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_url1.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<YourSharePointServerURL>/_trust/default.aspx`

    b. Na caixa de texto **Identificador**, digite a URL: `urn:sharepoint:federation`

1. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_certificate.png)

1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media\sharepoint-on-premises-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do SharePoint local**, clique em **Configurar SharePoint local** para abrir a janela **Configurar logon**. Copie a **URL do Serviço de Logon Único** da **seção de Referência Rápida.**

    ![Configuração do SharePoint local](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_configure.png)

    > [!NOTE]
    > O aplicativo SharePoint local usa SAML 1.1 token, portanto o Azure AD espera a solicitação WS Fed de servidor do SharePoint e após a autenticação, ele emite SAML 1.1. token.

1. Em outra janela do navegador da Web, faça logon em seu site de empresa do SharePoint local como um administrador.

1. **Configurar um novo provedor de identidade confiável no SharePoint Server 2016**

    Faça logon no servidor do SharePoint Server 2016 e abra o Shell de Gerenciamento do SharePoint 2016. Preencha os valores de $realm, $wsfedurl e $filepath do portal do Azure e execute os comandos a seguir para configurar um novo provedor de identidade confiável.

    > [!TIP]
    > Se você for novo no uso do PowerShell ou se quiser saber mais sobre como funciona o PowerShell, veja [PowerShell do SharePoint](https://docs.microsoft.com/en-us/powershell/sharepoint/overview?view=sharepoint-ps). 

    ```
    $realm = "<Identifier value from the SharePoint on-premises Domain and URLs section in the Azure portal>"
    $wsfedurl="<SAML single sign-on service URL value which you have copied from the Azure portal>"
    $filepath="<Full path to SAML signing certificate file which you have copied from the Azure portal>"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname" -IncomingClaimTypeDisplayName "GivenName" -SameAsIncoming
    $map3 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" -IncomingClaimTypeDisplayName "SurName" -SameAsIncoming
    $map4 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress" -IncomingClaimTypeDisplayName "Email" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    Em seguida, siga estas etapas para habilitar o provedor de identidade confiável para seu aplicativo:

    a. Na Administração Central, navegue até **Gerenciar Aplicativo de Web** e selecione o aplicativo Web que você deseja proteger com o Azure AD.

    b. Na faixa de opções, clique em **Provedores de Autenticação** e escolha a região que você deseja usar.

    c. Selecione **Provedor de Identidade Confiável** e selecione o provedor de identidade *AzureAD* que você acabou de registrar.

    d. Na configuração da URL de página de entrada, selecione **Página de entrada personalizada** e forneça o valor "/_trust/".

    e. Clique em **OK**.

    ![Como configurar seu provedor de autenticação](./media\sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media\sharepoint-on-premises-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media\sharepoint-on-premises-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media\sharepoint-on-premises-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media\sharepoint-on-premises-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.

### <a name="grant-access-to-sharepoint-on-premises-test-user"></a>Conceder acesso a um usuário de teste do SharePoint local

Os usuários que irão fazer logon no Azure AD e acessar o SharePoint precisam ter acesso ao aplicativo. Use as etapas a seguir para definir as permissões para acessar o aplicativo Web.

1. Na Administração Central, clique em **Gerenciamento de Aplicativos**.

1. Na página **Gerenciamento de Aplicativos**, na seção **Aplicativos Web**, clique em **Gerenciar aplicativos Web**.

1. Clique no aplicativo Web apropriado e, em seguida, clique em **Política de Usuário**.

1. Na Política de Aplicativo Web, clique em **Adicionar Usuários**.

    ![Procurando por um usuário pelo nome dele](./media\sharepoint-on-premises-tutorial/fig11-searchbynameclaim.png)

1. Na caixa de diálogo **Adicionar usuários**, clique na zona apropriada em **Zonas** e, em seguida, clique em **Próximo**.

1. Na caixa de diálogo **Policy for Web Application**, na seção **Escolher Usuários**, clique no ícone **Procurar**.

1. Na caixa de texto **Encontrar**, digite o valor do **Nome UPN** para o qual você configurou o aplicativo do SharePoint local no Azure AD e clique em **Pesquisar**. </br>Exemplo: *brittasimon@contoso.com*.

1. Sob o cabeçalho AzureAD na exibição de lista, selecione a propriedade name e clique em **Add** e, em seguida, clique em **OK** para fechar a caixa de diálogo.

1. Em Permissões, clique em **Controle Total**.

    ![Concedendo Controle Total a um Usuário de Reclamações](./media\sharepoint-on-premises-tutorial/fig12-grantfullcontrol.png)

1. Clique em **, clique em Concluir**  e, em seguida, clique em **OK** .

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>Configurando um provedor de identidade confiável para vários aplicativos Web

A configuração funciona para um único aplicativo Web, mas precisa de configuração adicional se você pretende usar o mesmo provedor de identidade confiável para vários aplicativos Web. Por exemplo, imagine que nós estendemos um aplicativo Web para usar a URL `https://portal.contoso.local` e agora queremos autenticar os usuários em `https://sales.contoso.local` também. Para fazer isso, precisamos atualizar o provedor de identidade para respeitar o parâmetro WReply e atualizar o registro do aplicativo no Azure AD para adicionar uma URL de resposta.

1. No portal do Azure, abra o diretório do Azure AD. Clique em **Registros de aplicativo** e, em seguida, clique em **Exibir todos os aplicativos**. Clique no aplicativo que você criou anteriormente (integração SAML do SharePoint).

1. Clique em **Configurações**.

1. Na folha de configurações, clique em **URLs de Resposta**. 

1. Adicione a URL do aplicativo Web adicional com `/_trust/default.aspx` anexado à URL (como `https://sales.contoso.local/_trust/default.aspx`) e clique em **Salvar**.

1. No servidor do SharePoint, abra o **Shell de Gerenciamento do SharePoint 2016** e execute os comandos a seguir, usando o nome do emissor do token de identidade confiável que você usou anteriormente.

    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
1. Em Administração Central, vá para o aplicativo Web e habilite o provedor de identidade confiável existente. Lembre-se de também configurar a URL da página de entrada como uma página de entrada personalizada `/_trust/`.

1. Em Administração Central, clique no aplicativo Web e escolha **Política de Usuário**. Adicione um usuário com as permissões apropriadas, conforme demonstrado anteriormente neste artigo.

### <a name="fixing-people-picker"></a>Como corrigir o Seletor de Pessoas

Agora, os usuários podem fazer logon no SharePoint 2016 usando identidades do Azure AD, mas ainda há oportunidades de melhoria para a experiência do usuário. Por exemplo, a pesquisa por um usuário apresenta vários resultados de pesquisa no seletor de pessoas. Há um resultado de pesquisa para cada um dos 3 tipos de declarações que foram criados no mapeamento de declaração.
 Para escolher um usuário usando o seletor de pessoas, você deve digitar o nome de usuário dele exatamente e escolher o resultado da reivindicação **nome**.

![Reivindica resultados da pesquisa](./media\sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Não há validação nos valores que você pesquisa, o que pode levar a erros de ortografia ou a usuários que escolherem acidentalmente o tipo de declaração errado para atribuir, como a reivindicação de **SurName**. Isso pode impedir que os usuários acessem recursos com êxito.

Para ajudar nesse cenário, há uma solução de código aberto chamada [ AzureCP ](https://yvand.github.io/AzureCP/) que fornece um provedor de declarações personalizado para o SharePoint 2016. Ele usará o Gráfico do Azure AD para resolver o que os usuários inserem e realizam a validação. Saiba mais em [ AzureCP ](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure, concedendo acesso ao SharePoint no local.

![Atribuir a função de usuário][200]

**Para atribuir Britta Simon ao SharePoint no local, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201]

1. Na lista de aplicativos, selecione **SharePoint local**.

    ![O link do SharePoint na lista de aplicativos](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_app.png)

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco no local do SharePoint no Painel de Acesso, você deverá entrar automaticamente no seu aplicativo local do SharePoint.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Usando o Azure AD para Autenticação de Servidor do SharePoint](https://docs.microsoft.com/en-us/office365/enterprise/using-azure-ad-for-sharepoint-server-authentication)

<!--Image references-->

[1]: ./media\sharepoint-on-premises-tutorial/tutorial_general_01.png
[2]: ./media\sharepoint-on-premises-tutorial/tutorial_general_02.png
[3]: ./media\sharepoint-on-premises-tutorial/tutorial_general_03.png
[4]: ./media\sharepoint-on-premises-tutorial/tutorial_general_04.png

[100]: ./media\sharepoint-on-premises-tutorial/tutorial_general_100.png

[200]: ./media\sharepoint-on-premises-tutorial/tutorial_general_200.png
[201]: ./media\sharepoint-on-premises-tutorial/tutorial_general_201.png
[202]: ./media\sharepoint-on-premises-tutorial/tutorial_general_202.png
[203]: ./media\sharepoint-on-premises-tutorial/tutorial_general_203.png

