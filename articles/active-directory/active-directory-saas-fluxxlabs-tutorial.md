---
title: 'Tutorial: integração do Azure Active Directory ao Fluxx Labs | Microsoft Docs'
description: Saiba como configurar o logon único entre o Microsoft Azure Active Directory e o Fluxx Labs.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d8fac770-bb57-4e1f-b50b-9ffeae239d07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2018
ms.author: jeedes
ms.openlocfilehash: 0bba820c14c5eddc6db99923e3fb1de58c110f4c
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2018
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>Tutorial: Integração do Azure Active Directory com o Fluxx Labs

Neste tutorial, você aprenderá a integrar o Fluxx Labs ao Azure AD (Azure Active Directory).

A integração do Fluxx Labs ao Microsoft Azure Active Directory oferece os seguintes benefícios:

- No Microsoft Azure Active Directory, é possível controlar quem tem acesso ao Fluxx Labs.
- Você pode permitir que os usuários façam logon automaticamente no Fluxx Labs (Logon Único) com suas contas do Microsoft Azure Active Directory.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Microsoft Azure Active Directory com o Fluxx Labs, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Fluxx Labs habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Fluxx Labs da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-fluxx-labs-from-the-gallery"></a>Adição do Fluxx Labs da galeria
Para configurar a integração do Fluxx Labs ao Microsoft Azure Active Directory, você precisará adicionar o Fluxx Labs da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Fluxx Labs da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Fluxx Labs**, selecione **Fluxx Labs** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Fluxx Labs na lista de resultados](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Microsoft Azure Active Directory com o Fluxx Labs, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Microsoft Azure Active Directory precisa saber qual usuário do Fluxx Labs é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Microsoft Azure Active Directory e o usuário relacionado no Fluxx Labs.

No Fluxx Labs, atribua o valor do **nome de usuário** no Microsoft Azure Active Directory como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Microsoft Azure Active Directory com o Fluxx Labs, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar de um usuário de teste do Fluxx Labs](#create-a-fluxx-labs-test-user)** : para ter um equivalente de Brenda Fernandes no Fluxx Labs que esteja vinculado à representação de usuário no Microsoft Azure Active Directory.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Microsoft Azure Active Directory no Portal do Azure e configurará o logon único no aplicativo Fluxx Labs.

**Para configurar o logon único do Microsoft Azure Active Directory com o Fluxx Labs, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração do aplicativo **Fluxx Labs**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_samlbase.png)

3. Na seção **Domínio e URLs do Fluxx Labs**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_url.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:

    | Ambiente | Padrão de URL|
    |-------------|------------|
    | Produção | `https://<subdomain>.fluxx.io` |
    | Pré-produção | `https://<subdomain>.preprod.fluxxlabs.com`|
        
    b. Na caixa de texto **URL de resposta** , digite uma URL no seguinte padrão:

    | Ambiente | Padrão de URL|
    |-------------|------------|
    | Produção | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Pré-produção | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|
        
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte do Fluxx Labs](mailto:travis@fluxxlabs.com) para obter esses valores.

4. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do Fluxx Labs**, clique em **Configurar Fluxx Labs** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configuração do Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_configure.png)

7. Em outra janela do navegador da Web, entre em seu site de empresa do Fluxx Labs como administrador.

8. Selecione **Administrador** abaixo da seção **Configurações**.

    ![Configuração do Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/config1.png)

9. No Painel de Administração, selecione **Plug-ins** > **Integrações** e, em seguida, selecione **SSO de SAML (Desabilitado)**

    ![Configuração do Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/config2.png)
    
10. Na seção de atributos, realize as seguintes etapas:
    
    ![Configuração do Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/config3.png)

    a. Marque a caixa de seleção **SSO do SAML**.

    b. Na caixa de texto **Caminho de solicitação**, digite **/auth/saml**.

    c. Na caixa de texto **Caminho de retorno de chamada**, digite **/auth/saml/callback**.

    d. Na caixa de texto **URL do Serviço do Consumidor de Declaração (URL de Logon Único)**, cole o valor de **URL do Serviço de Logon Único SAML** copiado do portal do Azure.

    e. Na caixa de texto **ID da Entidade SP(Público-alvo)**, cole o valor da **ID da Entidade do SAML** que você copiou do portal do Azure.

    f. Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado do Provedor de Identidade**.

    g. Na caixa de texto **Formato do identificador de nome**, insira o valor `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    h. Clique em **Salvar**.

    > [!NOTE]
    > Depois que o conteúdo for salvado, o campo aparecerá em branco por questões de segurança, mas o valor foi salvo na configuração.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-fluxxlabs-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-fluxxlabs-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-fluxxlabs-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-fluxxlabs-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
  
### <a name="create-a-fluxx-labs-test-user"></a>Criar um usuário de teste do Fluxx Labs

Para permitir que os usuários do Microsoft Azure Active Directory façam logon no Fluxx Labs, eles devem ser provisionados no Fluxx Labs. No caso do Fluxx Labs, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do Fluxx Labs como administrador.

2. Clique no **ícone** exibido abaixo.

    ![Configuração do Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/config6.png)

3. No painel, clique no ícone exibido abaixo para abrir o cartão **Novas PESSOAS**.

    ![Configuração do Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/config4.png)

4. Na seção **NOVAS PESSOAS**, execute as etapas a seguir:
    
    ![Configuração do Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/config5.png)

    a. O Fluxx Labs usa email como identificador exclusivo para logons únicos. Preencha o campo **UID do SSO** com o endereço de email do usuário, que corresponde ao endereço de email, que estão usando como logon com o SSO.

    b. Clique em **Salvar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Fluxx Labs.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Fluxx Labs, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **Fluxx Labs**.

    ![O link do Fluxx Labs na lista de Aplicativos](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco Fluxx Labs no Painel de Acesso, deve fazer logon automaticamente no seu aplicativo Fluxx Labs.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_203.png
