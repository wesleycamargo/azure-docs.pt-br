---
title: "Tutorial: integração do Azure Active Directory com o Cezanne HR Software | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Cezanne HR Software."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: fb8f95bf-c3c1-4e1f-b2b3-3b67526be72d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 9585494ebff68891d374a29e8e3e4b7756914bcc
ms.openlocfilehash: d8a654340df56002e503f2f61e910facb51696c5


---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Tutorial: Integração do Azure Active Directory ao Cezanne HR Software

Neste tutorial, você aprende a integrar o Cezanne HR Software ao Azure AD (Azure Active Directory).

A integração do Cezanne HR Software ao Azure AD oferece os seguintes benefícios:

- Você pode controlar, no Azure AD, quem tem acesso ao Cezanne HR Software
- Você pode habilitar seus usuários a fazerem logon automaticamente no Cezanne HR Software (logon único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um único local - o portal de Gerenciamento do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Cezanne HR Software, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Cezanne HR Software habilitada para logon único


> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Cezanne HR Software da galeria
2. Configurar e testar o logon único do AD do Azure


## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Adição do Cezanne HR Software da galeria
Para configurar a integração do Cezanne HR Software com o Azure AD, você precisa adicionar o Cezanne HR Software, por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Cezanne HR Software por meio da galeria, execute as seguintes etapas:**

1. No **[Portal de Gerenciamento do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Cezanne HR Software**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_00001.png)

5. No painel de resultados, escolha **Cezanne HR Software** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configura e testa o logon único do Azure AD com o Cezanne HR Software, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Cezanne HR Software é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Cezanne HR Software.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** ao Azure AD como sendo o valor de **Nome de usuário** no Cezanne HR Software.

Para configurar e testar o logon único do Azure AD com o Cezanne HR Software, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do Cezanne HR Software](#creating-a-cezanne-hr-software-test-user)** - para ter um equivalente de Brenda Fernandes no Cezanne HR Software que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal de Gerenciamento do Azure e configurará o logon único em seu aplicativo Cezanne HR Software.

**Para configurar o logon único do Azure AD com o Cezanne HR Software, execute as seguintes etapas:**

1. No Portal de Gerenciamento do Azure, na página de integração de aplicativos do **Cezanne HR Software**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na página da caixa de diálogo **Logon único**, como **Modo**, selecione **Logon com base em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_01.png)

3. Na seção **URLs e Domínio do Cezanne HR Software**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_02.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://w3.cezanneondemand.com/cezannehr/-/<tenant id>`
    
    b. Na caixa de texto **Identificador**, digite: `https://w3.cezanneondemand.com/CezanneOnDemand/`

    > [!NOTE] 
    > Observe que esses não são os valores reais. É necessário atualizar esses valores com a URL de Entrada e o Identificador reais. Aqui, sugerimos que você use o valor exclusivo de URL no Identificador. Para obter esses valores, entre em contato com a [equipe de suporte do Cezanne HR Software](mailto:info@cezannehr.com).

4. Sobre o **certificado de autenticação SAML** seção, clique em **criar novo certificado**.

    ![Configurar Logon Único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_03.png)     

5. Sobre o **criar um novo certificado** caixa de diálogo, clique no ícone de calendário e selecione um **data de expiração**. Em seguida, clique no botão **Salvar**.

    ![Configurar o logon único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_300.png)

6. Sobre o **certificado de autenticação SAML** seção, selecione **ativar o novo certificado** e clique em **salvar** botão.

    ![Configurar Logon Único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_04.png)

7. No pop-up **certificado de substituição** janela, clique em **OK**.

    ![Configurar o logon único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_400.png)

8. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar o logon único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_05.png) 

9. Na seção **Configuração do Cezanne HR Software**, clique em **Configurar o Cezanne HR Software** para abrir a janela **Configurar logon**.

    ![Configurar Logon Único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_06.png) 

    ![Configurar Logon Único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_07.png)

10. Em uma janela diferente do navegador da Web, faça logon em seu locatário do Cezanne HR Software como um administrador.

11. No painel de navegação esquerdo, clique em **Configuração do Sistema**. Vá para **Configurações de Segurança**. Em seguida, navegue até **Configuração de Logon Único**.

    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

12. No painel **Permitir que os usuários façam logon usando o SSO (Serviço de Logon Único)** a seguir, marque a caixa **SAML 2.0** e selecione a opção **Configuração Avançada**.

    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

13. Clique no botão **Adicionar Novo** .

    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

14. Execute as etapas a seguir na seção **PROVEDORES DE IDENTIDADE DO SAML 2.0** .

    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

    a. Insira o nome do seu Provedor de Identidade como o **Nome de Exibição**.

    b. Na caixa de texto **Identificador de Entidade**, coloque o valor de **ID da Entidade SAML** do assistente de configuração de aplicativo do Azure AD.

    c. Altere a **Associação SAML** para 'POST'.

    d. Na caixa de texto **Ponto de Extremidade de Serviço de Token de Segurança**, insira o valor da **URL de Serviço de Logon Único SAML** do assistente de configuração de aplicativo do Azure AD.

    e. Na caixa de texto **Nome de Atributo da ID de Usuário** insira 'http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name'.

    f. Clique no ícone **Carregar** para carregar o certificado baixado do Azure AD.

    g. Clique no botão **Ok** . 

15. Clique no botão **Salvar** .

    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)



### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no portal de Gerenciamento do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_01.png) 

2. Vá para **usuários e grupos** e clique em **todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo clique **adicionar** para abrir o **usuário** caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**. 



### <a name="creating-a-cezanne-hr-software-test-user"></a>Criação de um usuário de teste do Cezanne HR Software

Para habilitar usuários do AD do Azure a fazer logon no Cezanne HR Software, eles devem ser provisionados no Cezanne HR Software. No caso do Cezanne HR Software, o provisionamento é uma tarefa manual.

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>Para provisionar uma conta de usuário, execute as seguintes etapas:

1.  Faça logon no site da empresa Cezanne HR Software como um administrador.

2.  No painel de navegação esquerdo, clique em **Configuração do Sistema**. Vá para **Gerenciar Usuários**. Em seguida, navegue até **Add New User**.

    ![Novo Usuário](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Novo Usuário")

3.  Na seção **Person Details** , execute etapas abaixo:

    ![Novo Usuário](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Novo Usuário")

    a. Defina **Internal User** como OFF.

    b. Na caixa de texto **Nome**, digite **Brenda**.  

    c. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    d. Na caixa de texto **Email** , digite o endereço de email da conta de Brenda Fernandes.

4.  Na seção **Informações da Conta** , execute as etapas abaixo:

    ![Novo Usuário](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Novo Usuário")

    a. Na caixa de texto **Nome de usuário** , digite o endereço de email da conta de Brenda Fernandes.

    b. Na caixa de texto **Senha** , digite a senha da conta de Brenda Fernandes.

    c. Selecione **Profissional de RH** como **Função de Segurança**.

    d. Clique em **OK**.

5. Navegue até a guia **Logon Único** e selecione **Adicionar Novo** na área **Identificadores SAML 2.0**.

    ![Usuário](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Usuário")

6. Escolha o **Provedor de Identidade** para o Provedor de Identidade e, na caixa de texto de **Identificador do Usuário**, insira o endereço de email da conta de Brenda Fernandes.

    ![Usuário](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Usuário")
    
7. Clique no botão **Salvar** .

    ![Usuário](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Usuário")



### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao Cezanne HR Software.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Cezanne HR Software, execute as seguintes etapas:**

1. No portal de gerenciamento do Azure, abra a exibição de aplicativos e, em seguida, navegue até o modo de exibição de diretório e vá para **aplicativos empresariais** e clique em **todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Cezanne HR Software**.

    ![Configurar Logon Único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_50.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    


### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando clicar no bloco Cezanne HR Software no Painel de Acesso, você deverá fazer logon automaticamente no seu aplicativo Cezanne HR Software.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_203.png


<!--HONumber=Feb17_HO1-->


