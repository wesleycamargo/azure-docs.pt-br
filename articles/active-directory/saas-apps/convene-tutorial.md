---
title: 'Tutorial: Integração do Active Directory do Azure com o Convene | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Convene.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2540ac46-1aea-496b-a8c5-575a2690f7db
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: jeedes
ms.openlocfilehash: 6ff502f46cfd96b767191d11e3fcc53eb1be81d2
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/14/2018
ms.locfileid: "45607316"
---
# <a name="tutorial-azure-active-directory-integration-with-convene"></a>Tutorial: Integração do Active Directory do Azure com o Convene

Neste tutorial, você aprenderá como integrar o Convene ao Azure AD (Azure Active Directory).

A integração do Convene ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso a convocação.
- Você pode permitir que seus usuários façam logon automaticamente para convocação (logon único) com suas contas do AD do Azure.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Convene, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Assinatura habilitada para um Convene o logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Convene da Galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-convene-from-the-gallery"></a>Adicionando Convene da Galeria
Para configurar a integração do Convene ao Azure AD, você precisará adicionar Convene da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar Convene da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Conv.**, selecione **Convocar** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Convocar na lista de resultados](./media/convene-tutorial/tutorial_convene_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Convene, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber o que o usuário de contrapartida em Convene é para um usuário no Azure AD. Para que o logon único funcione, o Azure AD precisa saber o que o usuário de contrapartida em Convene é para um usuário no Azure AD....

Para configurar e testar o AD do Azure logon único com Convene, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste Convene](#create-a-convene-test-user)** – para ter um equivalente de Britta Simon no Convene que esteja vinculado à representação de usuário do AD do Azure.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único em seu aplicativo Convene.

**Para configurar o logon único do Azure AD com Convene, execute as seguintes etapas:**

1. No portal do Azure, sobre o **Convene** página de integração do aplicativo, clique em **logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/convene-tutorial/tutorial_convene_samlbase.png)

3. Na seção **Reunir domínio e URLs**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    ![Convocar URLs e domínio únicas informações de logon](./media/convene-tutorial/tutorial_convene_url.png)

    Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://portal.convene.me.uk/saml/acs/<UID>`

4. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Convocar URLs e domínio únicas informações de logon](./media/convene-tutorial/tutorial_convene_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL: `https://portal.convene.me.uk/login`
     
    > [!NOTE] 
    > O valor de URL de Resposta não é real. Atualize o valor com a URL de Resposta real. Entre em contato com [equipe de suporte ao cliente](mailto:support@convene.me.uk) para obter o valor.

5. Convocar aplicativo espera as asserções SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. A captura de tela a seguir mostra um exemplo disso.
    
    ![Configurar logon único attb](./media/convene-tutorial/tutorial_convene_attribute.png)

6. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/convene-tutorial/tutorial_convene_certificate.png) 

7. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/convene-tutorial/tutorial_general_400.png)
    
8. Na seção de **Configuração Conveniente**, clique em **Configurar Convidar** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Convocar configuração](./media/convene-tutorial/tutorial_convene_configure.png) 

9. Para configurar o single sign-on no **Convidar**, você precisa enviar o **Certificate (Base64)**, **URL de saída, SAML ID da entidade e SAML Single Sign- Em serviço URL** para [convocar equipe de suporte](mailto:support@convene.me.uk). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/convene-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/convene-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/convene-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/convene-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-convene-test-user"></a>Criar um usuário de teste de convocação

O objetivo desta seção é criar um usuário chamado Britta Simon em Convene. Convocar dá suporte a provisionamento de just-in-time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar Convene se ele ainda não exista.

>[!Note]
>Se você precisar criar um usuário manualmente, entre em contato com [equipe de suporte do Convene](mailto:support@convene.me.uk).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você deve habilitar Britta Simon usar logon único do Azure, concedendo acesso ao Convene.

![Atribuir a função de usuário][200] 

**Para atribuir Britta Simon a Convene, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Convene**.

    ![O link Convene na lista de aplicativos](./media/convene-tutorial/tutorial_convene_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Convocação no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Convene.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/convene-tutorial/tutorial_general_01.png
[2]: ./media/convene-tutorial/tutorial_general_02.png
[3]: ./media/convene-tutorial/tutorial_general_03.png
[4]: ./media/convene-tutorial/tutorial_general_04.png

[100]: ./media/convene-tutorial/tutorial_general_100.png

[200]: ./media/convene-tutorial/tutorial_general_200.png
[201]: ./media/convene-tutorial/tutorial_general_201.png
[202]: ./media/convene-tutorial/tutorial_general_202.png
[203]: ./media/convene-tutorial/tutorial_general_203.png

