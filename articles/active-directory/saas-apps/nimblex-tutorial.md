---
title: 'Tutorial: Integração do Azure Active Directory com o Nimblex | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Nimblex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d3e165a5-f062-4b50-ac0b-b400838e99cd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: jeedes
ms.openlocfilehash: 7b5dc6d892741f63596589a48ad5d45891b14c21
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39040398"
---
# <a name="tutorial-azure-active-directory-integration-with-nimblex"></a>Tutorial: Integração do Azure Active Directory com o Nimblex

Neste tutorial, você aprenderá a integrar o Nimblex ao Azure AD (Azure Active Directory).

A integração do Nimblex com o Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Nimblex.
- Você pode permitir que usuários façam logon automaticamente no Nimblex (logon único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Nimblex, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Nimblex habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Nimblex da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-nimblex-from-the-gallery"></a>Adicionando o Nimblex da galeria
Para configurar a integração do Nimblex ao Azure AD, você precisará adicionar o Nimblex da galeria para a lista de aplicativos SaaS gerenciados.

**Para adicionar o Nimblex da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Nimblex**, selecione **Nimblex** no painel de resultados, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Nimblex na lista de resultados](./media/nimblex-tutorial/tutorial_nimblex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Nimblex com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Nimblex é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Nimblex.

Para configurar e testar o logon único do Azure AD com o Nimblex, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Nimblex](#create-a-nimblex-test-user)** – para ter um equivalente de Brenda Fernandes no Nimblex vinculado à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo Nimblex.

**Para configurar o logon único do Azure AD com o Nimblex, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Nimblex**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Caixa de diálogo Logon único](./media/nimblex-tutorial/tutorial_nimblex_samlbase.png)

3. Na seção **URLs e Domínio do Nimblex**, execute as seguintes etapas:

    ![Informações de Domínio e URLs do Nimblex para logon único](./media/nimblex-tutorial/tutorial_nimblex_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<YOUR APPLICATION PATH>/Login.aspx`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<YOUR APPLICATION PATH>/`

    c. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<path-to-application>/SamlReply.aspx`

    > [!NOTE] 
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Contate a [equipe de suporte ao cliente do Nimblex](mailto:support@ebms.com.au) para obter esses valores.

4. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/nimblex-tutorial/tutorial_nimblex_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/nimblex-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do Nimblex**, clique em **Configurar Nimblex** para abrir a janela **Configurar logon**. Copie a **URL de serviço de logon único SAML** da **seção de Referência Rápida.**

    ![Configuração do Nimblex](./media/nimblex-tutorial/tutorial_nimblex_configure.png) 

7. Em outra janela do navegador da Web, faça logon no Nimblex como Administrador de Segurança.

9. No lado superior direito da página, clique no logotipo de **Configurações**.

    ![Configurações do Nimblex](./media/nimblex-tutorial/tutorial_nimblex_settings.png)

10. Na página **Painel de Controle**, na seção **Segurança** clique em **Logon Único**.

    ![Configurações do Nimblex](./media/nimblex-tutorial/tutorial_nimblex_single.png)

11. Na página **Gerenciar Logon Único**, selecione o nome da instância e clique em **Editar**.

    ![SAML do Nimblex](./media/nimblex-tutorial/tutorial_nimblex_saml.png)

12. Na página **Editar Configurações de SSO**, execute as seguintes etapas:

    ![SAML do Nimblex](./media/nimblex-tutorial/tutorial_nimblex_sso.png)

    a. Na caixa de texto **Descrição**, digite o nome da instância.

    b. No Bloco de Notas, abra o certificado codificado em Base 64 baixado do portal do Azure, copie o conteúdo e cole-o na caixa **Certificado**.

    c. Na caixa de texto **URL de Destino de SSO do Provedor de Identidade**, cole o valor da **URL do Serviço de Logon Único SAML** copiado do portal do Azure.

    d. Clique em **Salvar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/nimblex-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/nimblex-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/nimblex-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/nimblex-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-nimblex-test-user"></a>Criar um usuário de teste do Nimblex

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Nimblex. O Nimblex dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Caso ainda não exista um usuário, ele será criado durante uma tentativa de acessar o Nimblex.

>[!Note]
>Se você precisar criar um usuário manualmente, contate a [equipe de suporte ao cliente do Nimblex](mailto:support@ebms.com.au).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Nimblex.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Nimblex, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **Nimblex**.

    ![O link do Nimblex na lista de aplicativos](./media/nimblex-tutorial/tutorial_nimblex_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Nimblex no Painel de Acesso, você será conectado automaticamente ao aplicativo Nimblex.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/nimblex-tutorial/tutorial_general_01.png
[2]: ./media/nimblex-tutorial/tutorial_general_02.png
[3]: ./media/nimblex-tutorial/tutorial_general_03.png
[4]: ./media/nimblex-tutorial/tutorial_general_04.png

[100]: ./media/nimblex-tutorial/tutorial_general_100.png

[200]: ./media/nimblex-tutorial/tutorial_general_200.png
[201]: ./media/nimblex-tutorial/tutorial_general_201.png
[202]: ./media/nimblex-tutorial/tutorial_general_202.png
[203]: ./media/nimblex-tutorial/tutorial_general_203.png

