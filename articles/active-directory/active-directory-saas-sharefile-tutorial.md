---
title: "Tutorial: integração do Azure Active Directory com o Citrix ShareFile | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Citrix ShareFile."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ddf6c69b-4a76-4b42-8619-6f2a22800a23
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 9dfbf4bcdcd580773a368b90c869bf9c4fefbd77
ms.lasthandoff: 03/25/2017


---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Tutorial: integração do Azure Active Directory ao Citrix ShareFile

Nesse tutorial, você aprenderá a integrar o Citrix ShareFile ao Azure AD (Azure Active Directory).

A integração do Citrix ShareFile ao Azure AD oferece os seguintes benefícios:

- Você pode controlar, no Azure AD, quem tem acesso ao Citrix ShareFile
- Você pode habilitar seus usuários a fazerem logon automaticamente no Citrix ShareFile (logon único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um único local - o portal de Gerenciamento do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Citrix ShareFile, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Citrix ShareFile


> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Citrix ShareFile da galeria
2. Configurar e testar o logon único do AD do Azure


## <a name="adding-citrix-sharefile-from-the-gallery"></a>Adicionar o Citrix ShareFile da galeria
Para configurar a integração do Citrix ShareFile com o Azure AD, você precisa adicionar o Citrix ShareFile, por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Citrix ShareFile da galeria, execute as seguintes etapas:**

1. No **[Portal de Gerenciamento do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Citrix ShareFile**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_001.png)

5. No painel de resultados, selecione **Citrix ShareFile** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você vai configurar e testar o logon único do Azure AD com o Citrix ShareFile, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Citrix ShareFile é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Citrix ShareFile.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD ao valor de **nome de usuário** no Citrix ShareFile.

Para configurar e testar o logon único do Azure AD com o Citrix ShareFile, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste do Citrix ShareFile](#creating-a-citrix-sharefile-test-user)** – para ter um equivalente de Brenda Fernandes no Citrix ShareFile que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal de Gerenciamento do Azure e configura o logon único em seu aplicativo Citrix ShareFile.

**Para configurar o logon único do Azure AD com o Citrix ShareFile, execute as seguintes etapas:**

1. No Portal de Gerenciamento do Azure, na página de integração de aplicativos do **Citrix ShareFile**, clique em **Logon único**.

    ![Configurar o logon único][4]

2. Na caixa de diálogo **Logon único**, como **Modo**, selecione **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_01.png)

3. Na seção **URLs e Domínio do Citrix ShareFile**, execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_02.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<tenant-name>.sharefile.com/saml/login`    

    > [!NOTE] 
    > Observe que esses não são os valores reais. É necessário atualizar esses valores com a URL de Entrada e o Identificador reais. Se você não souber sobre essas URLs, digite URLs de exemplo com padrão de exemplo e, em seguida, você obterá as URLs reais após concluir a etapa 13.

4. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar Logon Único](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_05.png) 

5. Na seção **Configuração do Citrix ShareFile**, clique em **Configurar Citrix ShareFile** para abrir a janela **Configurar logon**.

    ![Configurar Logon Único](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_06.png) 

    ![Configurar Logon Único](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_07.png)

6. Em outra janela do navegador da Web, faça logon em seu site de empresa do Citrix ShareFile como administrador.

7. Na barra de ferramentas na parte superior, clique em **Administrador**.

8. No painel de navegação à esquerda, selecione **Configurar Logon Único**.

    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_002.png)

9. Na página **Configuração de Logon Único/SAML 2.0** em **Configurações Básicas**, realize as seguintes etapas:

    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-sharefile-tutorial/sso_configuration_2.png)

    a. Clique em **Habilitar SAML**.

    b. Na caixa de texto **Emissor IdP/ID da Entidade**, coloque o valor de **ID da Entidade SAML** do assistente de configuração de aplicativo do Azure AD.

    c. Clique em **Alterar** ao lado do campo **Certificado X.509** e carregue o certificado baixado. 

    d. Na caixa de texto **URL de Logon**, insira o valor da **URL de Serviço de Logon Único SAML** do assistente de configuração de aplicativo do Azure AD.

    e. Na caixa de texto **URL de Logoff**, insira o valor da **URL de Saída** do assistente de configuração de aplicativo do Azure AD.

10. Clique em **Salvar** no portal de gerenciamento do Citrix ShareFile.
    
 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no portal de Gerenciamento do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sharefile-tutorial/create_aaduser_01.png) 

2. Vá para **usuários e grupos** e clique em **todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sharefile-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo clique **adicionar** para abrir o **usuário** caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sharefile-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sharefile-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**. 



### <a name="creating-a-citrix-sharefile-test-user"></a>Criar um usuário de teste do Citrix ShareFile

Para permitir que os usuários do Azure AD façam logon no Citrix ShareFile, eles devem ser provisionados no Citrix ShareFile. No caso do Citrix ShareFile, o provisionamento é uma tarefa manual.

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>Para provisionar uma conta de usuário, execute as seguintes etapas:

1. Faça logon em seu site de empresa do Citrix ShareFile como um administrador.

2. Na barra de ferramentas na parte superior, clique em **Gerenciar Usuários**. Em seguida, vá para o **Início de Gerenciar Usuários** e clique em **Criar Funcionário**.

    ![Novo Usuário](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_010.png "Novo Usuário")

3. Na seção **Informações Básicas**, execute as etapas abaixo:

    ![Novo Usuário](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_011.png "Novo Usuário")

    a. Na caixa de texto **Endereço de Email**, digite o endereço de email da conta de Brenda Fernandes.  

    b. Na caixa de texto **Nome**, digite **Brenda**.  

    c. Na caixa de texto **Sobrenome**, digite **Fernandes**.

4. Clique em **Adicionar Usuário**.

    > [!NOTE]
    > O titular da conta do AAD receberá um email e clicará em um link para confirmar sua conta antes que ela se torne ativa. É possível usar qualquer outra ferramenta de criação da conta de usuário do Citrix ShareFile ou APIs fornecidas pelo Citrix ShareFile para provisionar as contas de usuário do AAD.



### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao Citrix ShareFile.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Citrix ShareFile, execute as seguintes etapas:**

1. No portal de gerenciamento do Azure, abra a exibição de aplicativos e, em seguida, navegue até o modo de exibição de diretório e vá para **aplicativos empresariais** e clique em **todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Citrix ShareFile**.

    ![Configurar Logon Único](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_50.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    


### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco Citrix ShareFile. no Painel de Acesso, você deve fazer logon automaticamente no seu aplicativo Citrix ShareFile.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_203.png
