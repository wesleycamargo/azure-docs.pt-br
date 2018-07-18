---
title: 'Tutorial: Integração do Azure Active Directory ao Servidor de Segredo (local)| Microsoft Docs'
description: Saiba como configurar logon único entre o Azure Active Directory e o Servidor de Segredo (local).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: be4ba84a-275d-4f71-afce-cb064edc713f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: jeedes
ms.openlocfilehash: 8aaf31022d54ebbd8c7c3f0bc72763f8c5139cbc
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-secret-server-on-premises"></a>Tutorial: Integração do Azure Active Directory ao Servidor de Segredo (local)

Neste tutorial, você aprenderá a integrar o Servidor de Segredo (local) ao Microsoft Azure AD (Azure Active Directory).

A integração do Servidor de Segredo (local) com o Microsoft Azure AD oferece os seguintes benefícios:

- Você pode controlar no Microsoft Azure AD quem tem acesso ao Servidor de Segredo (local).
- Você pode permitir que os usuários entrem automaticamente no Servidor de Segredo (local) (Logon Único) com suas contas do Microsoft Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Microsoft Azure AD ao Servidor de Segredo (local), você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único no Servidor de Segredo (local)

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Servidor de Segredo (no local) da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-secret-server-on-premises-from-the-gallery"></a>Adicionar o Servidor de Segredo (no local) da galeria
Para configurar a integração do Servidor de Segredo (local) ao Microsoft Azure AD, você precisa adicionar o Servidor de Segredo (local) da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Servidor de Segredo (local) da galeria, execute as etapas a seguir:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Servidor de Segredo (local)**, selecione **Servidor de Segredo (local)** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Servidor de Segredo (local) na lista de resultados](./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_secretserver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Microsoft Azure AD com o Servidor de Segredo (local), com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Microsoft Azure AD precisa saber qual usuário do Servidor de Segredo (local) é equivalente a um usuário do Microsoft Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Microsoft Azure AD e o usuário relacionado do Servidor de Segredo (local).

Para configurar e testar o logon único do Microsoft Azure AD com o Servidor de Segredo (local), você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Servidor de Segredo (local)](#create-a-secret-server-on-premises-test-user)** – para ter um equivalente de Brenda Fernandes no Servidor de Segredo (local) vinculado à representação do usuário no Microsoft Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Microsoft Azure AD no Portal do Azure e configura o logon único no aplicativo Servidor de Segredo (local).

**Para configurar o logon único do Microsoft Azure AD com o Servidor de Segredo (local), execute as etapas a seguir:**

1. No Portal do Azure, na página de integração do aplicativo do **Servidor de Segredo (local)**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Caixa de diálogo Logon único](./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_secretserver_samlbase.png)

3. Na seção **Domínio e URLs do Servidor de Segredo (local)**, execute as etapas a seguir se você deseja configurar o aplicativo no modo iniciado pelo **IDP**:

    ![Informações de logon único de Domínio e URLs do Servidor de Segredo (local)](./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_secretserver_url.png)

    a. Na caixa de texto **Identificador**, insira o valor escolhido pelo usuário como um exemplo: `https://secretserveronpremises.azure`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<SecretServerURL>/SAML/AssertionConsumerService.aspx `

    > [!NOTE]
    > A ID da Entidade mostrada acima é apenas um exemplo e você é livre para escolher qualquer valor exclusivo que identifique a instância do Servidor de Segredo no Microsoft Azure AD. É necessário enviar essa ID da Entidade à [Equipe de suporte ao Cliente do Servidor de Segredo (local)](https://thycotic.force.com/support/s/) para que possam configurá-lo. Para obter mais detalhes, leia[este artigo](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server).

4. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único de Domínio e URLs do Servidor de Segredo (local)](./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_secretserver_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<SecretServerURL>/login.aspx`
     
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e a URL de Logon reais. Contate a [Equipe de suporte ao Cliente do Servidor de Segredo (local)](https://thycotic.force.com/support/s/) para obter esses valores.

5. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_secretserver_certificate.png)

6. Marque **Mostrar configurações avançadas de assinatura de certificado** e selecione **Opção de assinatura** como **Assinar resposta e declaração SAML**.

    ![Opções de assinatura](./media/active-directory-saas-secretserver-on-premises-tutorial/signing.png)

7. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_400.png)
    
8. Na seção **Configuração do Servidor de Segredo (local)**, clique em **Configurar Servidor de Segredo (local)** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configuração do Servidor de Segredo (local)](./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_secretserver_configure.png)

9. Para configurar logon único no **Servidor de Segredo (local)**, é necessário enviar o **Certificado (Base64) baixado, a URL de Entrada, URL do Serviço de Logon Único do SAML** e **ID da Entidade SAML** para a [Equipe de suporte do Servidor de Segredo (local) ](https://thycotic.force.com/support/s/). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-secretserver-on-premises-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-secretserver-on-premises-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-secretserver-on-premises-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-secretserver-on-premises-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-secret-server-on-premises-test-user"></a>Criar um usuário de teste do Servidor de Segredo (local)

Nesta seção, você cria um usuário chamado Brenda Fernandes no Servidor de Segredo (local). Trabalhe com a [Equipe de suporte do Servidor de Segredo (local)](https://thycotic.force.com/support/s/) para adicionar os usuários na plataforma do Servidor de Segredo (local). Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Servidor de Segredo (local).

![Atribuir a função de usuário][200]

**Para atribuir Brenda Fernandes ao Servidor de Segredo (local), execute as etapas a seguir:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, escolha **Servidor de Segredo (local)**.

    ![O link do Servidor de Segredo (local) na lista de Aplicativos](./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_secretserver_app.png)

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Servidor de Segredo (local) no Painel de Acesso, você deverá ser conectado automaticamente a seu aplicativo Servidor de Segredo (local).
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-secretserver-on-premises-tutorial/tutorial_general_203.png

