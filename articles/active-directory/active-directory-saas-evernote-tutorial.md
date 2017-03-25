---
title: "Tutorial: Integração do Azure Active Directory ao Evernote | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Evernote."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: e4e2ca40ae270e7d3bfe4e828fcd571b5594ffc8
ms.lasthandoff: 03/09/2017


---
# <a name="tutorial-azure-active-directory-integration-with-evernote"></a>Tutorial: Integração do Azure Active Directory ao Evernote

Neste tutorial, você aprenderá a integrar o Evernote ao Azure AD (Azure Active Directory).

A integração do Evernote ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Evernote
- Você pode permitir que seus usuários façam logon automaticamente no Evernote usando logon único com suas contas do Azure AD
- Você pode gerenciar suas contas em um único local - o portal de Gerenciamento do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Evernote, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Evernote com logon único habilitado


> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Evernote da galeria
2. Configurar e testar o logon único do AD do Azure


## <a name="adding-evernote-from-the-gallery"></a>Adicionar o Evernote da galeria
Para configurar a integração do Evernote ao Azure AD, você precisará adicionar o Evernote da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Evernote da galeria, execute as seguintes etapas:**

1. No **[Portal de Gerenciamento do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Evernote**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_search01.png)

5. No painel de resultados, selecione **Evernote** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_search_result01.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Evernote, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Evernote é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Evernote.

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD ao valor do **Nome de Usuário** no Evernote.

Para configurar e testar o logon único do Azure AD com o Evernote, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
4. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal de Gerenciamento do Azure e configura o logon único em seu aplicativo Evernote.

**Para configurar o logon único do Azure AD com o Evernote, execute as seguintes etapas:**

1. No portal de Gerenciamento do Azure, na página de integração de aplicativos do **Evernote**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, como **Modo**, selecione **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_samlbase.png)

3. Na seção **Domínio e URLs do Evernote**, se você quiser configurar o aplicativo no **Modo iniciado por IDP**, não é necessário executar qualquer etapa.

    ![Configurar Logon Único](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_idp.png)
    
4. Na seção **Domínio e URLs do Evernote**, se você quiser configurar o aplicativo no **Modo iniciado por SP**, não é necessário executar qualquer etapa:

    ![Configurar o logon único](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_sp_01.png)
    
    a. Clique na opção **Mostrar URL configurações avançadas**

    b. Na caixa de texto **URL de Logon**, digite a seguinte URL: `https://www.evernote.com/Login.action`

5. Sobre o **certificado de autenticação SAML** seção, clique em **criar novo certificado**.

    ![Configurar Logon Único](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_cert.png)     

6. Sobre o **criar um novo certificado** caixa de diálogo, clique no ícone de calendário e selecione um **data de expiração**. Em seguida, clique no botão **Salvar**.

    ![Configurar o logon único](./media/active-directory-saas-evernote-tutorial/tutorial_general_300.png)

7. Sobre o **certificado de autenticação SAML** seção, selecione **ativar o novo certificado** e clique em **salvar** botão.

    ![Configurar Logon Único](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_makecertactive.png)

8. No pop-up **certificado de substituição** janela, clique em **OK**.

    ![Configurar o logon único](./media/active-directory-saas-evernote-tutorial/tutorial_general_400.png)

9. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar Logon Único](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_certificate.png) 

10. Na seção **Configuração do Evernote**, clique em **Configurar o Evernote** para abrir a janela **Configurar logon**.

    ![Configurar Logon Único](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_configure.png) 

    ![Configurar o logon único](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_configuresignon.png)

11. Em outra janela do navegador da Web, faça logon em seu site de empresa do Evernote como administrador.

12. Acesse **'Console de Administração'**

    ![Admin-Console](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_adminconsole.png)

13. No **'Console de Administração'**, acesse **'Segurança'** e selecione **'Logon Único'**

    ![SSO-Setting](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_sso.png)

14. Configure os seguintes valores:

    a.  **Habilitar SSO:** o SSO está habilitado por padrão (clique em **Desabilitar Logon Único** para remover o requisito de SSO)

    b. **URL de Solicitação de HTTP de SAML** - insira **URL de Serviço de logon único de SAML** na seção **Configurar o Evernote** no Azure AD

    c. **Certificado X.509** - abra o certificado baixado no Azure AD em um bloco de notas e copie o conteúdo, incluindo "BEGIN CERTIFICATE" e "END CERTIFICATE"

    ![Certificate-Setting](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_certx.png)

    d.Clique em **Salvar Alterações** 


### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no portal de Gerenciamento do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-evernote-tutorial/create_aaduser_01.png) 

2. Vá para **usuários e grupos** e clique em **todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-evernote-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo clique **adicionar** para abrir o **usuário** caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-evernote-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-evernote-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**. 



### <a name="creating-an-evernote-test-user"></a>Criar um usuário de teste do Evernote

Para permitir que os usuários do Azure AD façam logon no Evernote, eles deverão ser provisionados no Evernote.  
No caso do Evernote, o provisionamento é uma tarefa manual.

**Para provisionar contas de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do Evernote como administrador.

2. Clique no **'Console de Administração'**.

    ![Admin-Console](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_adminconsole.png)

3. No **'Console de Administração'**, acesse **'Adicionar usuários'**.

    ![Add-testUser](./media/active-directory-saas-evernote-tutorial/create_aaduser_0001.png)

4. **Adicionar membros da equipe** na caixa de texto **Email**, digite o endereço de email da conta de usuário e clique em **Convidar.**

    ![Add-testUser](./media/active-directory-saas-evernote-tutorial/create_aaduser_0002.png)
    
5. Após o envio do convite, o titular da conta do Azure Active Directory receberá um email para aceitar o convite.   


### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Evernote.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Evernote, execute as seguintes etapas:**

1. No portal de gerenciamento do Azure, abra a exibição de aplicativos e, em seguida, navegue até o modo de exibição de diretório e vá para **aplicativos empresariais** e clique em **todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **Evernote**.

    ![Configurar Logon Único](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    


### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Evernote no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo de Evernote. Você entrará como uma conta de Organização, mas precisará fazer logon com sua conta pessoal.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_203.png

