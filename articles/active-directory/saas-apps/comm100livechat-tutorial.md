---
title: 'Tutorial: Integração do Active Directory do Azure com o Comm100 Live Chat | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Comm100 Live Chat.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0340d7f3-ab54-49ef-b77c-62a0efd5d49c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2018
ms.author: jeedes
ms.openlocfilehash: b85162c8392e8ecdb08a3ed04ff5b9de835808a1
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2018
ms.locfileid: "42139766"
---
# <a name="tutorial-azure-active-directory-integration-with-comm100-live-chat"></a>Tutorial: integração do Active Directory do Azure ao Comm100 Live Chat

Neste tutorial, você aprenderá a integrar o Comm100 Live Chat ao Azure AD (Azure Active Directory).

A integração do Chat ao Vivo da Comm100 com o Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Comm100 Live Chat.
- Você pode permitir que seus usuários façam logon automaticamente no Chat ao vivo da Comm100 (Logon único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Comm100 Live Chat, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Comm100 Live Chat

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Chat ao vivo do Comm100 da Galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-comm100-live-chat-from-the-gallery"></a>Adicionando o Chat ao vivo do Comm100 da Galeria
Para configurar a integração do Comm100 Live Chat ao Azure AD, você precisa adicionar o Comm100 Live Chat da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Comm100 Live Chat da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Comm100 Live Chat**, selecione **Comm100 Live Chat** no painel de resultados e clique no botão **Add** para adicionar o aplicativo.

    ![Comm100 Live Chat na lista de resultados](./media/comm100livechat-tutorial/tutorial_comm100livechat_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Comm100 Live Chat com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber o que o usuário de contraparte do Comm100 Live Chat é para um usuário no Azure AD. Em outras palavras, um relacionamento de link entre um usuário do AD do Azure e o usuário relacionado no Comm100 Live Chat precisa ser estabelecido.

Para configurar e testar o logon único do Azure AD com o Comm100 Live Chat, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[ Crie um usuário de teste do Chat ao vivo da Comm100](#create-a-comm100-live-chat-test-user)** - para ter um equivalente de Brenda Fernandes no bate-papo ao vivo da Comm100 que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único no seu aplicativo Comm100 Live Chat.

**Para configurar o logon único do Azure AD com o Comm100 Live Chat, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Comm100 Live Chat** , clique em  **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/comm100livechat-tutorial/tutorial_comm100livechat_samlbase.png)

3. Na seção **Domínio de bate-papo ao vivo da Comm100 e URLs**, execute as seguintes etapas:

    ![Domínio de bate-papo ao vivo da Comm100 e informações de logon único de URLs](./media/comm100livechat-tutorial/tutorial_comm100livechat_url.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`
    
    > [!NOTE] 
    > O valor da URL de logon não é real. Você atualizará o valor da URL de Logon com a URL de Logon real, que será explicada posteriormente no tutorial.

4. Aplicativo de Chat ao vivo do Comm100 espera as asserções SAML contenham atributos específicos. Configure as atribuições a seguir para o aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. A captura de tela a seguir mostra um exemplo disso.

    ![Configurar o logon único](./media/comm100livechat-tutorial/tutorial_attribute_03.png)
    
5. Na seção **Atributos do usuário**, na caixa de diálogo **Logon único**, configure o atributo do token SAML na imagem acima e siga as etapas abaixo:
    
    |  Nome do atributo  | Valor do atributo |
    | --------------- | -------------------- |    
    |   email    | user.mail |

    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar o logon único](./media/comm100livechat-tutorial/tutorial_attribute_04.png)

    ![Configurar o logon único](./media/comm100livechat-tutorial/tutorial_attribute_05.png)
    
    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.
    
    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.

    d. Deixe o **Namespace** em branco.
    
    e. Clique em **OK**.

6. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/comm100livechat-tutorial/tutorial_comm100livechat_certificate.png) 

7. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/comm100livechat-tutorial/tutorial_general_400.png)

8. Na seção **Configuração do bate-papo ao vivo da Comm100**, clique em **Configurar o Chat ao vivo do Comm100** para abrir a janela **Configurar logon**. Copie a **URL do serviço de logon único do SAML e a URL de logoff** da **seção de Referência Rápida.**

    ![Configuração de Chat ao vivo Comm100](./media/comm100livechat-tutorial/tutorial_comm100livechat_configure.png)

9. Em uma janela do navegador da Web diferente, faça o login no Comm100 Live Chat como um administrador de segurança.

10. No canto superior direito da página, clique em **Minha conta**.

    ![Comm100 Live Chat myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

11. Do lado esquerdo do menu, clique em **Segurança** e, em seguida, clique em **Logon Único do Agente**.

    ![Segurança do Chat ao Vivo da Comm100](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

12. Sobre o **agente Single Sign-On** , execute as seguintes etapas:

    ![Segurança do Chat ao Vivo da Comm100](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

    a. Copie o primeiro link destacado e cole-o na caixa de texto **URL de login** na seção **Domínio de bate-papo ao vivo da Comm100 e URLs** no portal do Azure.

    b. Na caixa de texto **URL de SSO do SAML**, cole o valor da **URL de Serviço de Logon Único do SAML** que você copiou do Portal do Azure.

    c. No **URL de logoff remoto** caixa de texto, cole o valor da **URL de saída**, que você copiou do portal do Azure.

    d. Clique em **escolher um arquivo** para carregar base 64 codificados certificado que você baixou do portal do Azure, para o **certificado**.

    e. Clique em **salvar alterações**

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/comm100livechat-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/comm100livechat-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/comm100livechat-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/comm100livechat-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-comm100-live-chat-test-user"></a>Criar um usuário de teste do Comm100 Live Chat

Para permitir que os usuários do Azure AD efetuem login no Comm100 Live Chat, eles devem ser provisionados no Comm100 Live Chat. No Comm100 Chat ao vivo, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça o login no Comm100 Live Chat como um administrador de segurança.

2. No canto superior direito da página, clique em **Minha conta**.

    ![Comm100 Live Chat myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

3. Do lado esquerdo do menu, clique em **Agents** e, em seguida, clique em **New Agent**.

    ![Agente do Chat ao vivo do Comm100](./media/comm100livechat-tutorial/tutorial_comm100livechat_agent.png)

4. Sobre o **novo agente** , execute as seguintes etapas:

    ![Chat ao vivo do Comm100 novo agente](./media/comm100livechat-tutorial/tutorial_comm100livechat_newagent.png)

    a. a. Na caixa de texto **E-mail**, insira o e-mail do usuário como**Brittasimon@contoso.com**.

    b. Na caixa de texto **Nome**, insira o nome do usuário como **Brenda**.

    c. Na **Sobrenome** texto, digite o sobrenome do usuário, como **simon**.

    d. Na caixa de texto **Display Name**, insira o nome de exibição do usuário como **Britta simon**

    e. Na caixa de texto **Senha**, digite sua senha.

    f. Clique em **Salvar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você deve habilitar Britta Simon usar logon único do Azure, concedendo acesso ao Comm100 a Chat ao vivo.

![Atribuir a função de usuário][200] 

**Para atribuir Britta Simon ao Chat em Tempo Real da Comm100, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Comm100 Live Chat**.

    ![O link de Chat ao vivo do Comm100 na lista de aplicativos](./media/comm100livechat-tutorial/tutorial_comm100livechat_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Comm 100 Live Chat no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Comm 100 Live Chat.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/comm100livechat-tutorial/tutorial_general_01.png
[2]: ./media/comm100livechat-tutorial/tutorial_general_02.png
[3]: ./media/comm100livechat-tutorial/tutorial_general_03.png
[4]: ./media/comm100livechat-tutorial/tutorial_general_04.png

[100]: ./media/comm100livechat-tutorial/tutorial_general_100.png

[200]: ./media/comm100livechat-tutorial/tutorial_general_200.png
[201]: ./media/comm100livechat-tutorial/tutorial_general_201.png
[202]: ./media/comm100livechat-tutorial/tutorial_general_202.png
[203]: ./media/comm100livechat-tutorial/tutorial_general_203.png

