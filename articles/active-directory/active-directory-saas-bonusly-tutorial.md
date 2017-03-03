---
title: "Tutorial: Integração do Azure Active Directory ao Bonusly | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Bonusly."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 42875d53-0769-4520-a6af-7308b5240d6b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: af00ed428dda846a06466df1e30ee7e8a7e5758c
ms.openlocfilehash: c160829519a14a55e76a2ecedcb286663dc89d65
ms.lasthandoff: 02/15/2017


---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Tutorial: integração do Azure Active Directory ao Bonusly

Neste tutorial, você aprenderá a integrar o Bonusly ao Azure AD (Azure Active Directory).

A integração do Bonusly ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Bonusly
- Você pode permitir que seus usuários façam logon automaticamente no Bonusly (Logon único) com as contas do Azure AD
- Você pode gerenciar suas contas em um único local - o portal de Gerenciamento do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Bonusly, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura com logon único do Bonusly habilitado


> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Bonusly da galeria
2. Configurar e testar o logon único do AD do Azure


## <a name="adding-bonusly-from-the-gallery"></a>Adicionando o Bonusly da galeria
Para configurar a integração do Bonusly ao Azure AD, você precisa adicionar o Bonusly da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Bonusly da galeria, execute as seguintes etapas:**

1. No **[Portal de Gerenciamento do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Bonusly**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_001.png)

5. No painel de resultados, selecione **Bonusly** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Bonusly, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Bonusly é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Bonusly.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD como o valor de **Nome de usuário** no Bonusly.

Para configurar e testar o logon único do Azure AD com o Bonusly, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do Bonusly](#creating-a-bonusly-test-user)** – para ter um equivalente de Brenda Fernandes no Bonusly que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal de Gerenciamento do Azure e configura o logon único em seu aplicativo Bonusly.

**Para configurar o logon único do Azure AD com o Bonusly, realize as seguintes etapas:**

1. No Portal de Gerenciamento do Azure, na página de integração do aplicativo do **Bonusly**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, como **Modo**, selecione **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_01.png)

3. Na seção **Domínio e URLs do Bonusly**, se você quiser configurar o aplicativo em **Modo iniciado pelo IDP**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_02.png)

    a. Na caixa de texto **Identificador**, digite o valor como `bonusly`
    
    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://bonus.ly/saml/<APP-ID>/consume`
    
4. Se você quiser configurar o aplicativo em **Modo iniciado pelo SP**, na seção **Domínio e URLs do Bonusly**, execute as seguintes etapas:
    
    ![Configurar Logon Único](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_03.png)

    a. Clique na opção **Mostrar URL configurações avançadas**

    b. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://bonus.ly/saml/<your_subdomain>/consume`

    > [!NOTE] 
    > Observe que esses não são os valores reais. Você precisa atualizar esses valores com a URL de Entrada, o Identificador e a URL de Resposta reais. Aqui, sugerimos que você use o valor exclusivo de cadeia de caracteres no Identificador. Entre em contato com a [equipe de suporte do Bonusly](mailto:sales@easyterritory.com) para obter esses valores.

5. Sobre o **certificado de autenticação SAML** seção, clique em **criar novo certificado**.

    ![Configurar Logon Único](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_04.png)     

6. Sobre o **criar um novo certificado** caixa de diálogo, clique no ícone de calendário e selecione um **data de expiração**. Em seguida, clique no botão **Salvar**.

    ![Configurar o logon único](./media/active-directory-saas-bonusly-tutorial/tutorial_general_300.png)

7. Sobre o **certificado de autenticação SAML** seção, selecione **ativar o novo certificado** e clique em **salvar** botão.

    ![Configurar Logon Único](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_05.png)

8. No pop-up **certificado de substituição** janela, clique em **OK**.

    ![Configurar o logon único](./media/active-directory-saas-bonusly-tutorial/tutorial_general_400.png)

9. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar Logon Único](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_06.png) 

10. Na seção **Configuração do Bonusly**, clique em **Configurar o Bonusly** para abrir a janela **Configurar logon**.

    ![Configurar o logon único](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_07.png) 

    ![Configurar Logon Único](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_08.png)

11. Em outra janela do navegador da Web, faça logon em seu site de empresa Bonusly como um administrador.

12. Na barra de ferramentas na parte superior, clique em **Configurações** e selecione **Integrações e aplicativos**.

    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_002.png)

13. Em **Logon Único**, selecione **SAML**.

14. Na página do diálogo **SAML** , realize as seguintes etapas:

    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_003.png)

    a. Na caixa de texto **URL de destino de SSO IdP**, insira o valor da **URL de Serviço de Logon Único SAML** da janela de configuração de aplicativo do Azure AD.

    b. Na caixa de texto **URL de logon IdP**, insira o valor da **URL de Serviço de Logon Único SAML** da janela de configuração de aplicativo do Azure AD.

    c. Na caixa de texto **Emissor IdP**, coloque o valor de **ID da Entidade SAML** do assistente de configuração de aplicativo do Azure AD.

    d. Copie o valor de Impressão Digital do certificado baixado e cole-o na caixa de texto **Impressão Digital do Certificado**.

    e. Clique em **Salvar**.

    > [!NOTE] 
    > Para obter mais detalhes, consulte [Como recuperar o valor de impressão digital de um certificado](https://www.youtube.com/watch?v=YKQF266SAxI&feature=youtu.be).



### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no portal de Gerenciamento do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-bonusly-tutorial/create_aaduser_01.png) 

2. Vá para **usuários e grupos** e clique em **todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-bonusly-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo clique **adicionar** para abrir o **usuário** caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-bonusly-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-bonusly-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**. 



### <a name="creating-a-bonusly-test-user"></a>Criar um usuário de teste do Bonusly

Para permitir que os usuários do Azure AD façam logon no Bonusly, eles devem ser provisionados no Bonusly.
No caso do Bonusly, o provisionamento é uma tarefa manual.

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>Para provisionar uma conta de usuário, execute as seguintes etapas:

1. Faça logon em seu site de empresa Bonusly como um administrador.

2. Clique em **Configurações**.

    ![Novo Usuário](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_010.png "Novo Usuário")

3. Clique na guia **Usuários e bônus** .

    ![Novo Usuário](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_011.png "Novo Usuário")

4. Clique em **Gerenciar Usuários**.

    ![Novo Usuário](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_012.png "Novo Usuário")

5. Clique em **Adicionar Usuário**.

    ![Novo Usuário](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_013.png "Novo Usuário")

6. Na seção **Adicionar Usuário** , realize as seguintes etapas:

    ![Novo Usuário](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_014.png "Novo Usuário")

    a. Na caixa de texto **Nome**, digite **Brenda**.  

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Email**, digite o endereço de email da conta de Brenda Fernandes.

    d. Clique em **Salvar**.

    > [!NOTE] 
    > O titular da conta do AAD receberá um email com um link de confirmação de conta no qual ele deve clicar para torná-la ativa. É possível usar qualquer outra ferramenta de criação da conta de usuário do Bonus.ly ou as APIs fornecidas pelo Bonus.ly para provisionar as contas de usuário do AAD.



### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Bonusly.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Bonusly, execute as seguintes etapas:**

1. No portal de gerenciamento do Azure, abra a exibição de aplicativos e, em seguida, navegue até o modo de exibição de diretório e vá para **aplicativos empresariais** e clique em **todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha o **Bonusly**.

    ![Configurar Logon Único](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_50.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    


### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco Bonusly no Painel de Acesso, deverá ser automaticamente conectado ao seu aplicativo Bonusly.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_203.png
