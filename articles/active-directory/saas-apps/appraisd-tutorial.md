---
title: 'Tutorial: Integração do Azure Active Directory com o Appraisd | Microsoft Docs'
description: Saiba como configurar o logon único entre o Microsoft Azure Active Directory e o Appraisd.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: db063306-4d0d-43ca-aae0-09f0426e7429
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2018
ms.author: jeedes
ms.openlocfilehash: dae9a59b89f03a50b1adaed55cd8f97c06906526
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49118041"
---
# <a name="tutorial-azure-active-directory-integration-with-appraisd"></a>Tutorial: Integração do Azure Active Directory ao Appraisd

Neste tutorial, você aprende a integrar o Appraisd ao Azure Active Directory (Azure AD).

A integração do Appraisd ao Microsoft Azure Active Directory oferece os seguintes benefícios:

- Você pode controlar no Azure Active Directory quem terá acesso ao Appraisd.
- Você pode permitir que seus usuários façam logon automaticamente no Appraisd (logon único) com as contas do Azure Active Directory deles.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure Active Directory ao Appraisd, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Appraisd

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Appraisd por meio da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-appraisd-from-the-gallery"></a>Adicionando o Appraisd por meio da galeria
Para configurar a integração do Appraisd ao Azure Active Directory você precisará adicionar o Appraisd da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Appraisd da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![image](./media/appraisd-tutorial/selectazuread.png)

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![image](./media/appraisd-tutorial/a_select_app.png)
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![image](./media/appraisd-tutorial/a_new_app.png)

4. Na caixa de pesquisa, digite **Appraisd**, selecione **Appraisd** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

     ![image](./media/appraisd-tutorial/tutorial_appraisd_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure Active Directory com o Appraisd, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure Active Directory precisa saber qual usuário do Appraisd é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure Active Directory e o usuário relacionado do Appraisd.

Para configurar e testar o logon único do Azure Active Directory com o Appraisd, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Appraisd](#create-an-appraisd-test-user)** – para ter um equivalente de Brenda Fernandes no Appraisd que está vinculado à representação de usuário do Microsoft Azure Active Directory.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Microsoft Azure Active Directory no portal do Azure e configurará o logon único no aplicativo portal Appraisd.

**Para configurar o logon único do Azure Active Directory com Appraisd, execute as seguintes etapas:**

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Appraisd**, selecione **Logon único**.

    ![image](./media/appraisd-tutorial/B1_B2_Select_SSO.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML** para habilitar o logon único.

    ![image](./media/appraisd-tutorial/b1_b2_saml_sso.png)

3. Na página **Configurar logon único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Configuração básica de SAML**.

    ![image](./media/appraisd-tutorial/b1-domains_and_urlsedit.png)

4. Na seção **Configuração Básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_url.png)

    a. Clique em **Definir URLs adicionais**. 

    b. Na caixa de texto **Estado de Retransmissão**, digite uma URL: `<TENANTCODE>`

    c. Se você deseja configurar o aplicativo no modo iniciado pelo **SP**, na caixa de texto **URL de Logon** digite uma URL usando o seguinte padrão: `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > Você pode obter o valor real de URL de logon e o estado de retransmissão na página de configuração de SSO Appraisd que é explicada posteriormente no tutorial.
    
5. O aplicativo Appraisd espera as asserções SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Clique no botão **Editar** para abrir a caixa de diálogo **Atributos de usuário**.

    ![image](./media/appraisd-tutorial/i3-attribute.png)

6. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de usuário**, configure o atributo de token SAML conforme mostrado na imagem acima e execute as seguintes etapas:
    
    a. Clique em **Editar** para abrir a caixa de diálogo **Gerenciar declarações de usuário**.

    ![image](./media/appraisd-tutorial/i2-attribute.png)

    ![image](./media/appraisd-tutorial/i4-attribute.png)

    b. Na lista **Atributo de origem**, selecione o valor do atributo.

    c. Clique em **Salvar**. 

7. Na seção **Certificado de Assinatura SAML**, clique em **Baixar** para baixar o **certificado (Base64)** e salve-o em seu computador.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_certficate.png) 

8. Na seção **Configurar Appraisd**, copie a URL apropriada, de acordo com suas necessidades.

    a. URL de logon

    b. Azure Ad Identifier

    c. URL de logoff

    ![image](./media/appraisd-tutorial/d1_samlsonfigure.png)

9. Em uma janela de navegador da web diferente, entre no Appraisd como administrador de segurança.

10. No canto superior direito da página, clique no ícone **Configurações**, em seguida, navegue até **Configuração**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

11. Do lado esquerdo do menu, clique em **Logon único SAML**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_single.png)

12. Na página **Configuração de Logon Único SAML 2.0**, realize as seguintes etapas:
    
    ![image](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Copiar o **Estado de retransmissão padrão** de valor e cole-o na caixa de texto **Estado de retransmissão** **Configuração SAML Básica** no portal do Azure.

    b. Cópia de **URL de logon iniciado pelo serviço** de valor e cole-o na caixa de texto **URL de logon** **Configuração Básica SAML** no portal do Azure.

13. Role para baixo em sintonia **identificando usuários**, execute as seguintes etapas:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. Na caixa de texto **URL do Provedor de identidade de Logon único**, cole o valor da **URL de logon**, que você copiou do portal do Azure e clique em **Salvar**.

    b. Na caixa de texto **URL do Provedor de identidade de Logon único**, cole o valor da **URL de logon**, que você copiou do portal do Azure e clique em **Salvar**.

    c. No bloco de notas, abra o certificado codificado em base 64 que você baixou do portal do Azure, copie seu conteúdo e, em seguida, cole-o na caixa **certificado x. 509**e clique em **Salvar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, selecione **Todos os usuários**.

    ![image](./media/appraisd-tutorial/d_users_and_groups.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![image](./media/appraisd-tutorial/d_adduser.png)

3. Nas propriedades do Usuário, execute as seguintes etapas.

    ![image](./media/appraisd-tutorial/d_userproperties.png)

    a. No campo **Nome** insira **Brendafernandes**.
  
    b. No campo **Nome de usuário** digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.
 
### <a name="create-an-appraisd-test-user"></a>Criar um usuário de teste Appraisd

Para habilitar o Azure Active Directory, os usuários entram no Appraisd, eles devem ser provisionados no Appraisd. No Appraisd, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no Appraisd como um Administrador de Segurança.

2. No canto superior direito da página, clique no ícone **Configurações**, em seguida, navegue até **Centro de administração**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. Na barra de ferramentas na parte superior da página, clique em **Pessoas**, em seguida, navegue até **Adicionar um novo usuário**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Na página **Adicionar um novo usuário**, execute as seguintes etapas:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)
    
    a. Na caixa de texto **Nome**, insira o nome do usuário como **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário, como **simon**.

    c. Na caixa de texto **E-mail**, insira o e-mail do usuário como**Brittasimon@contoso.com**.

    d. Clique em **Adicionar usuário**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Appraisd.

1. No portal do Azure, selecione **Aplicativos Empresariais**, selecione **todos os aplicativos**.

    ![image](./media/appraisd-tutorial/d_all_applications.png)

2. Na lista de aplicativos, escolha **Appraisd**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_app.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![image](./media/appraisd-tutorial/d_leftpaneusers.png)

4. Selecione o botão **Adicionar** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar atribuição**.

    ![image](./media/appraisd-tutorial/d_assign_user.png)

4. Na caixa de diálogo **Usuários e grupos**, selecione **Britta Simon** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

5. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar na peça de Appraisd no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo de Appraisd.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
