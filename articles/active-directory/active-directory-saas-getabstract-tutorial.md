---
title: "Tutorial: Integração do Azure Active Directory ao Getabstract | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Getabstract."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2b63d048-b529-4fad-9e90-f244323409dd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: jeedes
ms.openlocfilehash: 01ff91ab86a9140c066050d35da5b59e2a040c28
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2017
---
# <a name="tutorial-azure-active-directory-integration-with-getabstract"></a>Tutorial: Integração do Azure Active Directory ao Getabstract

Neste tutorial, você aprenderá a integrar o Getabstract ao Azure Active Directory (Azure AD).

A integração do Getabstract ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Getabstract.
- Você pode permitir que seus usuários sejam conectados automaticamente no Getabstract (Logon Único) com as respectivas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Getabstract, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Getabstract

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Getabstract da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-getabstract-from-the-gallery"></a>Adição do Getabstract da galeria
Para configurar a integração do Getabstract ao Azure AD, é necessário adicionar o Getabstract da galaria à lista de aplicativos SaaS gerenciados.

**Para adicionar o Getabstract da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Getabstract**, selecione **Getabstract** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Getabstract na lista de resultados](./media/active-directory-saas-getabstract-tutorial/tutorial_getabstract_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você vai configurar e testar o logon único do Azure AD com o Getabstract, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Getabstract é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Getabstract.

No Getabstract, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Getabstract, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar de um usuário de teste do Getabstract](#create-a-getabstract-test-user)**: para ter um equivalente de Brenda Fernandes no Getabstract que esteja vinculado à representação de usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único no aplicativo Getabstract.

**Para configurar o logon único do Azure AD com o Getabstract, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **Getabstract**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-getabstract-tutorial/tutorial_getabstract_samlbase.png)

3. Na seção **Domínio e URLs do Getabstract**, execute as seguintes etapas se desejar configurar o aplicativo no modo iniciado pelo **IDP**:

    ![Informações de logon único de Domínio e URLs do Getabstract](./media/active-directory-saas-getabstract-tutorial/tutorial_getabstract_url.png)

    a. Na caixa de texto **Identificador**, digite a URL:

    Para preparação/pre_production: `https://int.getabstract.com`

    Para produção: `https://www.getabstract.com`

    b. Na caixa de texto **URL de resposta**, digite a URL:
    
    Para preparação/pre_production: `https://int.getabstract.com/ACS.do`
    
    Para produção: `https://www.getabstract.com/ACS.do`

4. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único de Domínio e URLs do Getabstract](./media/active-directory-saas-getabstract-tutorial/tutorial_getabstract_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão:
    
    Para preparação/pre_production: `https://int.getabstract.com/portal/<org_username>`
    
    Para produção: `https://www.getabstract.com/portal/<org_username>`

    > [!NOTE] 
    > Esse valor não é real. Atualize esse valor com a URL de Logon real. Contate a [equipe de suporte ao Cliente do Getabstract](https://www.getabstract.com/en/contact) para obter esse valor.

5. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-getabstract-tutorial/tutorial_getabstract_certificate.png) 

6. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-getabstract-tutorial/tutorial_general_400.png)
    
7. Para configurar o logon único no lado do **Getabstract**, é necessário enviar o download do **XML de metadados** para a [equipe de suporte do Getabstract](https://www.getabstract.com/en/contact). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-getabstract-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-getabstract-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-getabstract-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-getabstract-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-getabstract-test-user"></a>Criar um usuário de teste do Getabstract

O objetivo desta seção é criar uma usuária chamada Brenda Fernandes no Getabstract. O Getabstract dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o Getabstract, caso ele ainda não exista.
>[!Note]
>Se você precisar criar um usuário manualmente, contate a [equipe de suporte do Getabstract](https://www.getabstract.com/en/contact)


### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Getabstract.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Getabstract, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Getabstract**.

    ![O link do Getabstract na lista de Aplicativos](./media/active-directory-saas-getabstract-tutorial/tutorial_getabstract_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Getabstract no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo Getabstract.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-getabstract-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-getabstract-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-getabstract-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-getabstract-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-getabstract-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-getabstract-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-getabstract-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-getabstract-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-getabstract-tutorial/tutorial_general_203.png

