---
title: "Tutorial: Integração do Azure Active Directory ao Dealpath | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Dealpath."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 51ace608-5a4f-48c0-9446-d9f86ad2e890
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: jeedes
ms.openlocfilehash: 268df1b91f458279b7d79d963fe1ab318d759f65
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2017
---
# <a name="tutorial-azure-active-directory-integration-with-dealpath"></a>Tutorial: Integração do Azure Active Directory ao Dealpath

Neste tutorial, você aprenderá a integrar o Dealpath ao Azure Active Directory (Azure Active Directory).

A integração do Dealpath ao Azure Active Directory oferece os seguintes benefícios:

- Você pode controlar no Azure Active Directory quem terá acesso ao Dealpath.
- Você pode permitir que seus usuários façam logon automaticamente no Dealpath (logon único) com as contas do Azure Active Directory deles.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Azure Active Directory ao Dealpath, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Dealpath

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Dealpath da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-dealpath-from-the-gallery"></a>Adição do Dealpath da galeria
Para configurar a integração do Dealpath ao Azure Active Directory, você precisará adicionar o Dealpath da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Dealpath da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Dealpath**, selecione **Dealpath** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Dealpath na lista de resultados](./media/active-directory-saas-dealpath-tutorial/tutorial_dealpath_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure Active Directory com o Dealpath, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure Active Directory precisa saber qual usuário do Dealpath é equivalente a um usuário do Azure Active Directory. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure Active Directory e o usuário relacionado no Dealpath.

No Dealpath, atribua o valor do **nome de usuário** no Azure Active Directory como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure Active Directory com o Dealpath, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Dealpath](#create-a-dealpath-test-user)** – para ter um equivalente de Brenda Fernandes no Dealpath vinculado à representação do usuário no Azure Active Directory.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure Active Directory no Portal do Azure e configurará o logon único no aplicativo Dealpath.

**Para configurar o logon único do Azure Active Directory com o Dealpath, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Dealpath**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-dealpath-tutorial/tutorial_dealpath_samlbase.png)

3. Na seção **Domínio e URLs do Dealpath**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Dealpath](./media/active-directory-saas-dealpath-tutorial/tutorial_dealpath_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://app.dealpath.com/account/login`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://api.dealpath.com/saml/metadata/<ID>`

    > [!NOTE] 
    > O Identificador não é real. Atualize o valor com o identificador real. Entre em contato com a [equipe de suporte ao cliente do Dealpath](mailto:kenter@dealpath.com) para obter o valor. 
 
4. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do Certificado no computador.

    ![O link de download do Certificado](./media/active-directory-saas-dealpath-tutorial/tutorial_dealpath_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-dealpath-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do Dealpath**, clique em **Configurar o Dealpath** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configuração do Dealpath](./media/active-directory-saas-dealpath-tutorial/tutorial_dealpath_configure.png) 

7. Em outra janela do navegador da Web, faça logon no Dealpath como administrador.

8. No canto superior direito, clique em **Ferramentas de administração** e navegue até **Integrações**. Em seguida, na seção **Autenticação SAML 2.0**, clique em **Atualizar Configurações**:

    ![Configuração do Dealpath](./media/active-directory-saas-dealpath-tutorial/tutorial_dealpath_admin.png)

9. Na página **Configurar a autenticação SAML 2.0**, realize as seguintes etapas:

    ![Configuração do Dealpath](./media/active-directory-saas-dealpath-tutorial/tutorial_dealpath_saml.png) 

    a. Na caixa de texto **URL do SSO do SAML**, cole o valor da **URL de Serviço de Logon Único do SAML** que você copiou do Portal do Azure.

    b. Na caixa de texto **Emissor do Provedor de Identidade**, cole o valor da **ID da Entidade SAML** copiado do portal do Azure.

    c. Copie o conteúdo do arquivo de **certificado baixado(Base64)** no bloco de notas e cole-o na caixa de texto **Certificado público**.

    d. Clique em **Atualizar Configurações**.


> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-dealpath-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-dealpath-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-dealpath-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-dealpath-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-dealpath-test-user"></a>Criar um usuário de teste Dealpath

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Dealpath. Trabalhe com a [equipe de suporte ao Cliente do Dealpath](mailto:kenter@dealpath.com) para adicionar os usuários à plataforma Dealpath. Os usuários devem ser criados e ativados antes de você usar o logon único

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Dealpath.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Dealpath, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **Dealpath**.

    ![O link do Dealpath na lista de Aplicativos](./media/active-directory-saas-dealpath-tutorial/tutorial_dealpath_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco Dealpath no Painel de Acesso, deve ser conectado automaticamente ao seu aplicativo Dealpath.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_203.png

