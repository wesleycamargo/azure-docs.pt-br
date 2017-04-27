---
title: "Tutorial: integração do Azure Active Directory ao FreshDesk | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o FreshDesk."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 73ee330c276263a21931a7b9a16cc33f86c58a26
ms.openlocfilehash: f35da063fc637f315b52ea38b63d2c8222cecd21
ms.lasthandoff: 04/05/2017


---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Tutorial: integração do Azure Active Directory ao FreshDesk

Neste tutorial, você aprenderá a integrar o FreshDesk ao Azure AD (Azure Active Directory).

A integração do FreshDesk ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao FreshDesk
- Você pode habilitar seus usuários, com as respectivas contas do Azure AD, a fazer logon automaticamente no FreshDesk (logon único)
- Você pode gerenciar suas contas em um único local - o portal de Gerenciamento do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao FreshDesk, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do FreshDesk com logon único habilitado

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o FreshDesk da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-freshdesk-from-the-gallery"></a>Adicionar o FreshDesk da galeria
Para configurar a integração do FreshDesk ao Azure AD, você precisa adicionar o FreshDesk da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o FreshDesk por meio da galeria, execute as seguintes etapas:**

1. No **[Portal de Gerenciamento do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **FreshDesk**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_search.png)

5. No painel de resultados, selecione **FreshDesk** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o FreshDesk, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do FreshDesk é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do FreshDesk.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** ao Azure AD como sendo o valor de **Nome de usuário** no FreshDesk.

Para configurar e testar o logon único do Azure AD com o FreshDesk, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do FreshDesk](#creating-a-freshdesk-test-user)** – para ter um equivalente de Brenda Fernandes no FreshDesk que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal de Gerenciamento do Azure e configura o logon único em seu aplicativo FreshDesk.

**Para configurar o logon único do Azure AD com o FreshDesk, realize as seguintes etapas:**

1. No Portal de Gerenciamento do Azure, na página de integração de aplicativos do **FreshDesk**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, como **Modo**, selecione **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_samlbase.png)

3. Na seção **Domínio e URLs do FreshDesk**, insira a **URL de logon** como: `https://<tenant-name>.freshdesk.com` ou qualquer outro valor sugerido pelo FreshDesk.

    ![Configurar Logon Único](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_url.png)

    > [!NOTE] 
    > Observe que esse não é o valor real. Você precisa atualizar o valor com a URL de Entrada real. Para obter esse valor, entre em contato com a [equipe de suporte do cliente FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg).  

4. Na seção **Certificado de Autenticação SAML**, clique em **Certificado** e, em seguida, salve o certificado em seu computador.

    ![Configurar Logon Único](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_certificate.png) 

5. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-freshdesk-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do FreshDesk**, clique em **Configurar o FreshDesk** para abrir a janela Configurar logon. Copie a URL do serviço de logon único do SAML e a URL de logoff da seção de **Referência Rápida**.

    ![Configurar o logon único](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_configure.png)

7. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do Freshdesk como administrador.

8. No menu na parte superior, clique em **Administrador**.
   
       ![Admin](./media/active-directory-saas-freshdesk-tutorial/IC776768.png "Admin")

9. Na guia **Configurações Gerais**, clique em **Segurança**.
   
       ![Security](./media/active-directory-saas-freshdesk-tutorial/IC776769.png "Security")

10. Na seção **Segurança** , realize as seguintes etapas:
   
    ![Logon Único](./media/active-directory-saas-freshdesk-tutorial/IC776770.png "Logon Único")
   
    a. Para **SSO (Logon Único)**, selecione **Ativado**.

    b. Selecione **SSO do SAML**.

    c. Digite a **URL do serviço de logon único do SAML** que você copiou do Portal do Azure para a caixa de texto **URL de Logon do SAML**.

    d. Digite a **URL de Saída** que você copiou do Portal do Azure para a caixa de texto **URL de Logoff**.

    e. Copie o valor de **Impressão Digital** do certificado baixado do Portal do Azure e cole-o na caixa de texto **Impressão Digital do Certificado de Segurança**.  
 
    >[!TIP]
    >Para obter mais detalhes, consulte [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI). 
    
    f. Clique em **Salvar**.


### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no portal de Gerenciamento do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_01.png) 

2. Vá para **usuários e grupos** e clique em **todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo clique **adicionar** para abrir o **usuário** caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-freshdesk-test-user"></a>Criar um usuário de teste FreshDesk

Para permitir que os usuários do Azure AD façam logon no FreshDesk, eles devem ser provisionados no FreshDesk.  
No caso do FreshDesk, o provisionamento é uma tarefa manual.

**Para provisionar contas de usuário, execute as seguintes etapas:**

1. Faça logon em seu locatário do **Freshdesk** .
2. No menu na parte superior, clique em **Administrador**.
   
       ![Admin](./media/active-directory-saas-freshdesk-tutorial/IC776772.png "Admin")

3. Na guia **Configurações Gerais**, clique em **Agentes**.
   
       ![Agents](./media/active-directory-saas-freshdesk-tutorial/IC776773.png "Agents")

4. Clique em **Novo Agente**.
   
       ![New Agent](./media/active-directory-saas-freshdesk-tutorial/IC776774.png "New Agent")

5. Na caixa de diálogo Informações sobre o Agente, execute as seguintes etapas:
   
       ![Agent Information](./media/active-directory-saas-freshdesk-tutorial/IC776775.png "Agent Information")
   
       a. In the **Full Name** textbox, type the name of the Azure AD account you want to provision.

       b. In the **Email** textbox, type the Azure AD email address of the Azure AD account you want to provision.

       c. In the **Title** textbox, type the title of the Azure AD account you want to provision.

       d. Select **Agents role**, and then click **Assign**.
       
       e. Click **Save**.     
   
    >[!NOTE]
    >O titular da conta do AD do Azure receberá um email que inclui um link para confirmar a conta antes que ela se torne ativa. 
    > 
    
    >[!NOTE]
    >É possível usar qualquer outra ferramenta de criação da conta de usuário do Freshdesk ou APIs fornecidas pelo Freshdesk para provisionar as contas de usuário do AAD. para o FreshDesk.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Box.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao FreshDesk, realize as seguintes etapas:**

1. No portal de gerenciamento do Azure, abra a exibição de aplicativos e, em seguida, navegue até o modo de exibição de diretório e vá para **aplicativos empresariais** e clique em **todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **FreshDesk**.

    ![Configurar Logon Único](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando clicar no bloco FreshDesk no Painel de Acesso, você deverá ser conduzido à página de logon para ser conectado automaticamente ao seu aplicativo FreshDesk.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_203.png


