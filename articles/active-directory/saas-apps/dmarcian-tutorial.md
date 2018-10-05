---
title: 'Tutorial: Integração do Azure Active Directory ao dmarcian | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o dmarcian.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a04b9383-3a60-4d54-9412-123daaddff3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2018
ms.author: jeedes
ms.openlocfilehash: 0f8878505280371bf6046c1d1f0d7fc1275dd496
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47039880"
---
# <a name="tutorial-azure-active-directory-integration-with-dmarcian"></a>Tutorial: Integração do Azure Active Directory ao dmarcian

Neste tutorial, você aprenderá a integrar o dmarcian ao Azure AD (Azure Active Directory).

A integração do dmarcian ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem terá acesso ao dmarcian.
- Você pode permitir que seus usuários façam logon automaticamente no dmarcian (logon único) com as contas do Azure AD deles.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao dmarcian, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do dmarcian

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Como adicionar dmarcian da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-dmarcian-from-the-gallery"></a>Como adicionar dmarcian da galeria
Para configurar a integração do dmarcian ao Azure AD, você precisará adicionar o dmarcian da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o dmarcian da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos Empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **dmarcian**, selecione **dmarcian** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![dmarcian na lista de resultados](./media/dmarcian-tutorial/tutorial_dmarcian_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o dmarcian, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do dmarcian é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado do dmarcian.

Para configurar e testar o logon único do Azure AD com o dmarcian, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do dmarcian](#create-a-dmarcian-test-user)** – para ter um equivalente de Brenda Fernandes no dmarcian vinculado à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único no aplicativo dmarcian.

**Para configurar o logon único do Azure AD com o dmarcian, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **dmarcian**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/dmarcian-tutorial/tutorial_dmarcian_samlbase.png)

3. Na seção **Domínio e URLs do dmarcian**, realize as seguintes etapas se desejar configurar o aplicativo no modo iniciado pelo **IDP**:

    ![Informações de logon único de Domínio e URLs do dmarcian](./media/dmarcian-tutorial/tutorial_dmarcian_url.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:
    | |
    | -- |
    | `https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml` |

    b. Na caixa de texto **URL de resposta** , digite uma URL no seguinte padrão:
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/` |

4. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único de Domínio e URLs do dmarcian](./media/dmarcian-tutorial/tutorial_dmarcian_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão:
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>` |
    | `https://dmarciam-ap.com/login/<ACCOUNT_ID>` |
     
    > [!NOTE] 
    > Esses valores não são reais. Você atualizará esses valores com o identificador, URL de resposta e URL de logon reais, explicado mais adiante no tutorial. 

5. Na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar a **URL de metadados de federação do aplicativo** e cole-a no bloco de notas.

    ![O link de download do Certificado](./media/dmarcian-tutorial/tutorial_dmarcian_certificate.png) 

6. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/dmarcian-tutorial/tutorial_general_400.png)
    
7. Em uma janela de navegador da Web diferente, entre no dmarcian como um administrador de segurança.

8. Clique em **Perfil** no canto superior direito canto e navegue até **Preferências**.

    ![As preferências ](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

9. Role para baixo e clique na seção **Logon Único** e, em seguida, clique em **Configurar**.

    ![Único ](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

10. Na página **Logon Único de SAML**, defina o **Status** como **Habilitado** e execute as seguintes etapas:

    ![A de autenticação ](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * Na seção **Adicionar dmarcian ao seu provedor de identidade**, clique em **Copiar** para copiar a **URL de serviço do consumidor de declaração** para sua instância e colá-lo na caixa de texto **URL de Resposta** na **seção de URLs e Domínio do dmarcian** no portal do Azure.

    * Na seção **Adicionar dmarcian ao seu Provedor de Identidade**, clique em **COPY** para copiar a **ID da Entidade** para sua instância e colá-lo na caixa de texto **Identificador** na **seção de URLs e Domínio do dmarcian** no portal do Azure.

    * Na seção **Configurar Autenticação**, a caixa de texto **Metadados do Provedor de Identidade** cola a **Url de Metadados de Federação do Aplicativo**, que você copiou do portal do Azure.

    * Na seção **Configurar a Autenticação**, na caixa de texto **Declarações de Atributo**, cole a url `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    * Na seção **Configurar URL de Logon**, copie a **URL de Logon** para sua instância e cole-a na caixa de texto **URL de Logon** na **seção de URLs e Domínio do dmarcian** no portal do Azure.

        > [!Note]
        > Você pode modificar a **URL de Logon** de acordo com a sua organização.

    * Clique em **Salvar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/dmarcian-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/dmarcian-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/dmarcian-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/dmarcian-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-dmarcian-test-user"></a>Criar um usuário de teste dmarcian

Para permitir que os usuários do Azure AD entrem no dmarcian, eles devem ser provisionados no dmarcian. No dmarcian, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no dmarcian como um Administrador de Segurança.

2. Clique em **Perfil** no canto superior direito e navegue até **Gerenciar Usuários**.

    ![O usuário ](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. No lado direito da seção **Usuários de SSO**, clique em **Adicionar Novo Usuário**.

    ![A ação de adicionar usuário ](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. Na janela pop-up **Adicionar Novo Usuário**, realize as seguintes etapas:

    ![O novo usuário ](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. Na caixa de texto **Email do Novo Usuário**, insira o email do usuário como **brittasimon@contoso.com**.

    b. Se você quiser conceder direitos de administrador ao usuário, selecione **Tornar o Usuário um Administrador**.

    c. Clique em **Adicionar Usuário**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao dmarcian.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao dmarcian, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **dmarcian**.

    ![O link dmarcian na lista de Aplicativos](./media/dmarcian-tutorial/tutorial_dmarcian_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar na peça do dmarcian no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo dmarcian.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/dmarcian-tutorial/tutorial_general_01.png
[2]: ./media/dmarcian-tutorial/tutorial_general_02.png
[3]: ./media/dmarcian-tutorial/tutorial_general_03.png
[4]: ./media/dmarcian-tutorial/tutorial_general_04.png

[100]: ./media/dmarcian-tutorial/tutorial_general_100.png

[200]: ./media/dmarcian-tutorial/tutorial_general_200.png
[201]: ./media/dmarcian-tutorial/tutorial_general_201.png
[202]: ./media/dmarcian-tutorial/tutorial_general_202.png
[203]: ./media/dmarcian-tutorial/tutorial_general_203.png

