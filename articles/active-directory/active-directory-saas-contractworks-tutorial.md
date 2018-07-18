---
title: 'Tutorial: Integração do Azure Active Directory com o ContractWorks | Microsoft Docs'
description: Saiba como configurar logon único entre o Azure Active Directory e o ContractWorks.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: e7b269d6-3c4e-4bc4-a55f-5071d1f52591
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 459b8f424a05f6332f6285f35645ec93c67403cf
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-contractworks"></a>Tutorial: Integração do Azure Active Directory com o ContractWorks

Neste tutorial, você aprende a integrar o ContractWorks ao Microsoft Azure AD (Azure Active Directory).

A integração do ContractWorks ao Microsoft Azure AD oferece os seguintes benefícios:

- Você pode controlar no Microsoft Azure AD quem tem acesso ao ContractWorks.
- Você pode permitir que seus usuários façam logon automaticamente no ContractWorks (logon único) com suas contas do Microsoft Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Microsoft Azure AD com o ContractWorks, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do ContractWorks

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o ContractWorks da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-contractworks-from-the-gallery"></a>Adicionar o ContractWorks da galeria
Para configurar a integração do ContractWorks ao Microsoft Azure AD, você precisa adicionar o ContractWorks da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o ContractWorks da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **ContractWorks**, selecione **ContractWorks** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![ContractWorks na lista de resultados](./media/active-directory-saas-contractworks-tutorial/tutorial_contractworks_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Microsoft Azure AD com o ContractWorks, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Microsoft Azure AD precisa saber qual usuário do ContractWorks é equivalente a um usuário do Microsoft Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Microsoft Azure AD e o usuário relacionado do ContractWorks.

Para configurar e testar o logon único do Microsoft Azure AD com o ContractWorks, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do ContractWorks](#create-a-contractworks-test-user)** – para ter um equivalente de Brenda Fernandes no ContractWorks vinculado à representação do usuário no Microsoft Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Microsoft Azure AD no Portal do Azure e configurará o logon único no aplicativo portal do ContractWorks.

**Para configurar o logon único do Microsoft Azure AD com o ContractWorks, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração do aplicativo **ContractWorks**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Caixa de diálogo Logon único](./media/active-directory-saas-contractworks-tutorial/tutorial_contractworks_samlbase.png)

3. Na seção **Domínio e URLs do ContractWorks**, execute as seguintes etapas se desejar configurar o aplicativo no modo iniciado pelo **IDP**:

    ![Informações de logon único de Domínio e URLs do ContractWorks](./media/active-directory-saas-contractworks-tutorial/tutorial_contractworks_url.png)

    Na caixa de texto **Identificador**, digite uma URL: `https://login.securedocs.com/saml/metadata`

4. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único de Domínio e URLs do ContractWorks](./media/active-directory-saas-contractworks-tutorial/tutorial_contractworks_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL: `https://login.securedocs.com/saml/hint`
     
5. O aplicativo ContractWorks espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. A captura de tela a seguir mostra um exemplo disso.
    
    ![Configurar o logon único](./media/active-directory-saas-contractworks-tutorial/tutorial_ContractWorks_attribute.png)

6. Na seção **Atributos do usuário**, na caixa de diálogo **Logon único**, configure o atributo do token SAML na imagem acima e siga as etapas abaixo:
    
    | Nome do atributo | Valor do atributo |
    | ---------------| --------------- |
    | mail | user.mail |
    | displayName | user.displayname |

    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar o logon único](./media/active-directory-saas-contractworks-tutorial/tutorial_attribute_04.png)

    ![Configurar o logon único](./media/active-directory-saas-contractworks-tutorial/tutorial_attribute_05.png)
    
    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.
    
    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.

    d. Deixe o **Namespace** em branco.
    
    d. Clique em **Ok**

7. Na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar a **URL de metadados de federação do aplicativo** e cole-a no bloco de notas.
    
    ![Configurar o logon único](./media/active-directory-saas-contractworks-tutorial/tutorial_metadataurl.png)
     
8. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-contractworks-tutorial/tutorial_general_400.png)

9. Para configurar logon único no **ContractWorks**, é necessário enviar a **URL de metadados de federação do aplicativo** gerada para a [Equipe de suporte do ContractWorks](mailto:support@contractworks.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-contractworks-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-contractworks-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-contractworks-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-contractworks-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-contractworks-test-user"></a>Criar um usuário de teste do ContractWorks

Nesta seção, você criará um usuário chamado Brenda Fernandes no ContractWorks. Trabalhe com a [equipe de suporte do ContractWorks](mailto:support@contractworks.com) para adicionar os usuários na plataforma do ContractWorks. Os usuários devem ser criados e ativados antes de usar o logon único. 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure, concedendo acesso ao ContractWorks.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao ContractWorks, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **ContractWorks**.

    ![O link do ContractWorks na lista de Aplicativos](./media/active-directory-saas-contractworks-tutorial/tutorial_contractworks_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco ContractWorks no Painel de Acesso, deve fazer logon automaticamente no seu aplicativo do ContractWorks.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-contractworks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-contractworks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-contractworks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-contractworks-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-contractworks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-contractworks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-contractworks-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-contractworks-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-contractworks-tutorial/tutorial_general_203.png

