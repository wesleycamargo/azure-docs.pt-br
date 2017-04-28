---
title: "Tutorial: Integração do Azure Active Directory com informações de varejo – gerenciamento de informações | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Infor Retail – Information Management."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 5ff49168-ef81-4169-8e5e-dc86e24dd5e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: ad9c78483af88a85a1b790269231683155760d76
ms.openlocfilehash: aef560c2b05a80c59743c4cc6d68d6d2c14bdc3f
ms.lasthandoff: 02/15/2017


---
# <a name="tutorial-azure-active-directory-integration-with-infor-retail--information-management"></a>Tutorial: Integração do Azure Active Directory com informações de varejo – gerenciamento de informações

Neste tutorial, você aprenderá a integrar o Infor Retail – Information Management ao Azure AD (Azure Active Directory).

Integrar informações varejo – gerenciamento de informações com o Azure AD oferece os seguintes benefícios:

- Você pode controlar no AD do Azure que tenha acesso a informações comerciais – gerenciamento de informações
- É possível permitir que seus usuários façam logon automaticamente no Infor Retail – Information Management (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um único local - o portal de Gerenciamento do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do AD do Azure com informações de varejo – gerenciamento de informações, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Um varejo informações – gerenciamento de informações de logon único assinatura habilitada


> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Infor Retail – Information Management da galeria
2. Configurar e testar o logon único do AD do Azure


## <a name="adding-infor-retail--information-management-from-the-gallery"></a>Adição do Infor Retail – Information Management da galeria
Para configurar a integração de varejo de informações – gerenciamento de informações no AD do Azure, você precisa adicionar informações de varejo – gerenciamento de informações da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar informações de varejo – gerenciamento de informações da galeria, execute as seguintes etapas:**

1. No **[Portal de Gerenciamento do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **informações varejo – gerenciamento de informação**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_001.png)

5. No painel resultados, selecione **informações varejo – gerenciamento de informação**e, em seguida, clique em **adicionar** botão para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Infor Retail – Information Management, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Infor Retail – Information Management é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Infor Retail – Information Management.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** ao Azure AD como o valor de **Nome de usuário** no Infor Retail – Information Management.

Para configurar e testar o logon único do Azure AD com o Infor Retail – Information Management, você precisa concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar logon único do Azure AD com Britta Simon.
3. **[Criando um varejo informações – o usuário de teste do gerenciamento de informações](#creating-an-infor-retail---information-management-test-user)** - para ter um equivalente de Britta Simon no varejo de informações – gerenciamento de informação que é vinculado à representação no AD do Azure.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitar o AD do Azure logon único no portal de gerenciamento do Azure e configurar o logon único no setor de varejo suas informações – aplicativo de gerenciamento de informações.

**Para configurar o logon único do Azure AD com informações de varejo – gerenciamento de informações, execute as seguintes etapas:**

1. No portal de gerenciamento do Azure, sobre o **informações varejo – gerenciamento de informações** página de integração de aplicativos, clique em **sign-on único**.

    ![Configurar Logon Único][4]

2. Na página da caixa de diálogo **Logon único**, como **Modo**, selecione **Logon com base em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_01.png)

3. No **informações varejo – domínio de gerenciamento de informações e URLs** seção, se você quiser configurar o aplicativo em **modo iniciado pelo IDP**, execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_02.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<company name>.mingle.infor.com`
    
    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<company name>.mingle.infor.com/sp/ACS.saml2`
    
4. Se você quiser configurar o aplicativo em **modo iniciado do SP**, no **informações varejo – domínio de gerenciamento de informações e URLs** seção execute as seguintes etapas:
    
    ![Configurar Logon Único](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_03.png)

    a. Clique na opção **Mostrar URL configurações avançadas**

    b. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<company name>.mingle.infor.com/<company code>`

    > [!NOTE] 
    > Observe que esses não são os valores reais. Você precisa atualizar esses valores com a URL de Entrada, o Identificador e a URL de Resposta reais. Entre em contato com [Infor Retail – equipe de suporte de gerenciamento de informações](mailto:innovate@infor.com) para obter esses valores.

5. Sobre o **certificado de autenticação SAML** seção, clique em **criar novo certificado**.

    ![Configurar Logon Único](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_04.png)     

6. Sobre o **criar um novo certificado** caixa de diálogo, clique no ícone de calendário e selecione um **data de expiração**. Em seguida, clique no botão **Salvar**.

    ![Configurar o logon único](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_300.png)

7. Sobre o **certificado de autenticação SAML** seção, selecione **ativar o novo certificado** e clique em **salvar** botão.

    ![Configurar Logon Único](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_05.png)

8. No pop-up **certificado de substituição** janela, clique em **OK**.

    ![Configurar o logon único](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_400.png)

9. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![Configurar o logon único](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_06.png) 

10. Para que o SSO seja configurado para o aplicativo, entre em contato com a [equipe de suporte do Infor Retail – Information Management](mailto:innovate@infor.com) e forneça o arquivo de **metadados** baixado.
  

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no portal de Gerenciamento do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-inforretailinformationmanagement-tutorial/create_aaduser_01.png) 

2. Vá para **usuários e grupos** e clique em **todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-inforretailinformationmanagement-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo clique **adicionar** para abrir o **usuário** caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-inforretailinformationmanagement-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-inforretailinformationmanagement-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**. 



### <a name="creating-an-infor-retail--information-management-test-user"></a>Criando um varejo informações – o usuário de teste do gerenciamento de informações

Nesta seção, você deve criar um usuário chamado Britta Simon no varejo de informações – gerenciamento de informações. Trabalhe com [informações varejo – a equipe de suporte de gerenciamento de informações](mailto:innovate@infor.com) para adicionar os usuários no varejo de informações – plataforma de gerenciamento de informações.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Infor Retail – Information Management.

![Atribuir usuário][200] 

**Para atribuir Britta Simon comercial de informações – gerenciamento de informações, execute as seguintes etapas:**

1. No portal de gerenciamento do Azure, abra a exibição de aplicativos e, em seguida, navegue até o modo de exibição de diretório e vá para **aplicativos empresariais** e clique em **todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **informações varejo – gerenciamento de informação**.

    ![Configurar Logon Único](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_50.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    


### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica em varejo informações – bloco de gerenciamento de informações no painel de acesso, você deve obter logon automaticamente no seu comercial de informações – aplicativo de gerenciamento de informações.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_203.png
