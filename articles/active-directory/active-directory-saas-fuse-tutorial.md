---
title: "Tutorial: integração do Azure Active Directory ao Fuse | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o Fuse."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 5ef34f58-863a-4b37-875c-e8efa3e18bb3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: afc9719a41e9ecb9a690edc7f16d392450fbb932
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-fuse"></a>Tutorial: Integração do Active Directory do Azure ao Fuse

Neste tutorial, você aprenderá a integrar o Fuse ao Azure AD (Azure Active Directory).

A integração do Fuse ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Fuse
- Você pode permitir que seus usuários faça logon automaticamente no Fuse (logon único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um único local - o portal de Gerenciamento do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Fuse, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Fuse com SSO (logon único) habilitado


>[!NOTE]
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
>
>

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode obter uma [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testa o SSO do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Fuse da galeria
2. Configurar e testar o SSO do Azure AD

## <a name="add-fuse-from-the-gallery"></a>Adicionar o Fuse da galeria
Para configurar a integração do Fuse com o Azure AD, você precisará adicionar o Fuse à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Fuse por meio da galeria, execute as seguintes etapas:**

1. No **[Portal de Gerenciamento do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Fuse**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-fuse-tutorial/tutorial_fuse_001.png)

5. No painel de resultados, selecione **Fuse** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-fuse-tutorial/tutorial_fuse_0001.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD
Nesta seção, você configurará e testará o logon único (SSO) do Azure AD com o Fuse, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o SSO funcione, o Azure AD precisa saber qual usuário do Fuse é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Fuse.

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Nome de Usuário** no Fuse.

Para configurar e testar o SSO do Azure AD com o Fuse, você precisa concluir os seguintes blocos de construção:

1. **[Configurar logon único do Azure AD](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do Fuse](#creating-a-fuse-test-user)** - para ter um equivalente de Brenda Fernandes no Fuse que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o SSO do Azure AD no Portal de Gerenciamento do Azure e configura o SSO no aplicativo Fuse.

**Para configurar o SSO do Azure AD com o Fuse, execute as seguintes etapas:**

1. No Portal de Gerenciamento do Azure, na página de integração de aplicativos do **Fuse**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, como **Modo**, selecione **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-fuse-tutorial/tutorial_fuse_01.png)

3. Na seção **URLs e Domínio do Fuse**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-fuse-tutorial/tutorial_fuse_02.png)
  1. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<tenant name>.fusion-universal.com/`
  2. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<tenant name>.fusion-universal.com`

      >[!NOTE] 
      >Esses não são os valores reais. É necessário atualizar esses valores com a URL de Entrada e o Identificador reais. Entre em contato com a [equipe de suporte do Fuse](mailto:support@fusion-universal.com) para obter valores. 
      >
      >

4. Sobre o **certificado de autenticação SAML** seção, clique em **criar novo certificado**.

    ![Configurar Logon Único](./media/active-directory-saas-fuse-tutorial/tutorial_fuse_03.png)     

5. Sobre o **criar um novo certificado** caixa de diálogo, clique no ícone de calendário e selecione um **data de expiração**. Em seguida, clique no botão **Salvar**.

    ![Configurar o logon único](./media/active-directory-saas-fuse-tutorial/tutorial_general_300.png)

6. Sobre o **certificado de autenticação SAML** seção, selecione **ativar o novo certificado** e clique em **salvar** botão.

    ![Configurar Logon Único](./media/active-directory-saas-fuse-tutorial/tutorial_fuse_04.png)

7. No pop-up **certificado de substituição** janela, clique em **OK**.

    ![Configurar Logon Único](./media/active-directory-saas-fuse-tutorial/tutorial_general_400.png)

8. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Bruto)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar o logon único](./media/active-directory-saas-fuse-tutorial/tutorial_fuse_05.png) 

9. No **configuração fusível** seção, clique em **configurar fusível** para abrir **logon** janela.

    ![Configurar o logon único](./media/active-directory-saas-fuse-tutorial/tutorial_fuse_06.png) 

    ![Configurar Logon Único](./media/active-directory-saas-fuse-tutorial/tutorial_fuse_07.png)

10. Para que o SSO seja configurado para seu aplicativo, entre em contato com a [equipe de suporte do Fuse](mailto:support@fusion-universal.com) e forneça o seguinte: 

    • O **arquivo de certificado** baixado • A **URL de logon único do serviço SAML** • A **ID da entidade SAML** • A **URL de saída**
  
### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no portal de Gerenciamento do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-fuse-tutorial/create_aaduser_01.png) 

2. Vá para **usuários e grupos** e clique em **todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-fuse-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo clique **adicionar** para abrir o **usuário** caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-fuse-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-fuse-tutorial/create_aaduser_04.png) 
  1. Na caixa de texto **Nome**, digite **Brenda Fernandes**.
  2. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.
  3. Selecione **Mostrar senha** e anote o valor de **senha**.
  4. Clique em **Criar**. 

### <a name="create-a-fuse-test-user"></a>Criar um usuário de teste do Fuse

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Fuse. Trabalhe com a [equipe de suporte do Fuse](mailto:support@fusion-universal.com) para adicionar os usuários na plataforma do Fuse.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Fuse.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Fuse, execute as seguintes etapas:**

1. No portal de gerenciamento do Azure, abra a exibição de aplicativos e, em seguida, navegue até o modo de exibição de diretório e vá para **aplicativos empresariais** e clique em **todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **Fuse**.

    ![Configurar Logon Único](./media/active-directory-saas-fuse-tutorial/tutorial_fuse_50.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de SSO do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Fuse no Painel de Acesso, você deverá ser conectado automaticamente a seu aplicativo do Fuse.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-fuse-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-fuse-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-fuse-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-fuse-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-fuse-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-fuse-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-fuse-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-fuse-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-fuse-tutorial/tutorial_general_203.png

