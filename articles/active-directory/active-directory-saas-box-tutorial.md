---
title: 'Tutorial: Integrar o Azure Active Directory ao Box | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Box.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3b565c8d-35e2-482a-b2f4-bf8fd7d8731f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/8/2017
ms.author: jeedes
ms.openlocfilehash: daad9104798dc02b479b4e022287c3630e4a67a0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
---
# <a name="integrate-azure-active-directory-with-box"></a>Integrar o Azure Active Directory ao Box

Neste tutorial, você aprenderá a integrar o Azure AD (Azure Active Directory) ao Box.

Ao integrar o Azure AD ao Box, você obtém os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Box.
- Você pode permitir que seus usuários façam logon automaticamente no Box (logon único ou SSO) com suas respectivas contas do Azure AD.
- Você pode gerenciar suas contas em um único local, o portal clássico do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, consulte [O que é o acesso de aplicativos e o logon único com o Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Azure AD ao Box, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada pelo SSO do Box

> [!NOTE]
> Ao testar as etapas deste tutorial, recomendamos que você *não* use um ambiente de produção.

Para testar as etapas neste tutorial, siga estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. 

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Box da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="add-box-from-the-gallery"></a>Adicionar o Box da galeria
Para configurar a integração do Azure AD ao Box, adicione o Box da galeria à sua lista de aplicativos SaaS gerenciados, da seguinte maneira:

1. No [Portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Selecione **Aplicativos empresariais** > **Todos os aplicativos**.

    ![A janela “Aplicativos empresariais”][2]
    
3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior da janela.

    ![O botão “Novo aplicativo”][3]

4. Na caixa de pesquisa, digite **Box**, selecione **Box** na lista de resultados e, em seguida, selecione **Adicionar**.

    ![Box na lista de resultados](./media/active-directory-saas-box-tutorial/tutorial_box_search.png)
### <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Box com base em uma usuária de teste chamada “Brenda Fernandes.”

Para que o logon único funcione, o Azure AD precisa identificar o usuário do Box e sua contraparte no Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o mesmo usuário no Box.

Para estabelecer a relação de vínculo, atribua como *Nome de usuário* no Box o mesmo valor do *nome de usuário* no Azure AD.

Para configurar e testar o logon único do Azure AD com o Box, complete os blocos de construção nas próximas cinco seções.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Habilite o logon único do Azure AD no portal do Azure e configure o logon único no aplicativo Box da seguinte maneira:

1. No portal do Azure, na janela de integração de aplicativo do **Box**, selecione **Logon único**.

    ![O link “Logon único”][4]

2. Na janela **Logon único**, na caixa **Modo Logon único**, selecione **Logon baseado em SAML**.
 
    ![A janela “Logon único”](./media/active-directory-saas-box-tutorial/tutorial_box_samlbase.png)

3. Em **Domínio e URLs do Box**, faça o seguinte:

    ![Informações de logon único de "Domínio e URLs do Box"](./media/active-directory-saas-box-tutorial/url3.png)

    a. Na caixa **URL de Entrada**, digite uma URL usando o seguinte formato:  *https://\<subdomain>.box.com*.

    b. Na caixa de texto **Identificador**, digite **box.net**.
     
    > [!NOTE] 
    > Os valores anteriores não são reais. Atualize-os com a URL de logon e o identificador reais. Para obter os valores, entre em contato com a [equipe de suporte do Box](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire). 

4. Em **Certificado de Autenticação SAML**, selecione **XML de Metadados** e salve o arquivo de metadados no computador.

    ![O link de download do Certificado](./media/active-directory-saas-box-tutorial/tutorial_box_certificate.png) 

5. Clique em **Salvar**.

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-box-tutorial/tutorial_general_400.png)
    
6. Para configurar o SSO para seu aplicativo, siga o procedimento em [Configurar SSO por conta própria](https://community.box.com/t5/How-to-Guides-for-Admins/Setting-Up-Single-Sign-On-SSO-for-your-Enterprise/ta-p/1263#ssoonyourown).

> [!NOTE] 
> Se não for possível habilitar as configurações de SSO para a conta do Box, talvez seja necessário entrar em contato com a [equipe de suporte ao cliente do Box](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire) e fornecer o arquivo XML baixado.

> [!TIP]
> Ao configurar o aplicativo, você pode ler as instruções anteriores em uma versão concisa no [portal do Azure](https://portal.azure.com). Depois de adicionar esse aplicativo na seção **Active Directory** > **Aplicativos Empresariais**, selecione a guia **Logon Único** e, em seguida, acesse a documentação inserida na seção **Configuração**, na parte inferior. Para saber mais informações sobre o recurso de documentação inserida, consulte [Documentação inserida do Microsoft Azure Active Directory]( https://go.microsoft.com/fwlink/?linkid=845985).
>

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você cria o usuário de teste Brenda Fernandes no portal do Azure fazendo o seguinte:

![Criar um usuário de teste do Azure AD][100]

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**.

    ![O link do Azure Active Directory](./media/active-directory-saas-box-tutorial/create_aaduser_01.png)

2. Para exibir uma lista de usuários atuais, selecione **Usuários e grupos** > **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-box-tutorial/create_aaduser_02.png)

3. Na parte superior da janela **Todos os usuários**, selecione **Adicionar**.

    ![O botão Adicionar](./media/active-directory-saas-box-tutorial/create_aaduser_03.png)

    A janela **Usuário** abre.

4. Na janela **Usuário**, faça o seguinte:

    ![A janela de usuário](./media/active-directory-saas-box-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-box-test-user"></a>Criar um usuário de teste do Box

Nesta seção, você cria a usuária de teste Brenda Fernandes no Box. O Box dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Se um usuário ainda não existir no Box, um novo será criado quando você tentar acessar o Box. Nenhuma ação é requerida para criar o usuário.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Box. Para fazer isso, faça o seguinte:

![Atribuir a função de usuário][200]

1. No portal do Azure, abra a exibição de **Aplicativos**, acesse a exibição de **Diretório** e, em seguida, selecione **Aplicativos Empresariais** > **Todos os aplicativos**.

    ![Os links “Aplicativos empresariais” e “Todos os aplicativos”][201] 

2. Na lista de **Aplicativos**, selecione **Box**.

    ![O link de Box](./media/active-directory-saas-box-tutorial/tutorial_box_app.png)  

3. No painel esquerdo, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Selecione **Adicionar** e, em seguida, no painel **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![O painel Adicionar Atribuição][203]

5. Na janela **Usuários e grupos**, na lista **Usuários**, selecione **Britta Simon**.

6. Escolha o botão **Selecionar**.

7. Na janela **Adicionar Atribuição**, selecione **Atribuir**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você selecionar o bloco **Box** no Painel de Acesso, você abrirá a página de entrada para entrar no seu aplicativo do Box.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Configurar provisionamento de usuário](active-directory-saas-box-userprovisioning-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-box-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-box-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-box-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-box-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-box-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-box-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-box-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-box-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-box-tutorial/tutorial_general_203.png

