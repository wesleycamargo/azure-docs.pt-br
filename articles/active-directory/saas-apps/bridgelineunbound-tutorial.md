---
title: 'Tutorial: Integração do Azure Active Directory ao Bridgeline Unbound | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Bridgeline Unbound.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b018472f-c8b3-403d-ae66-9ed26a35f413
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2018
ms.author: jeedes
ms.openlocfilehash: c429afa12bc11db68d041fef96f66b3f4c7f0b1b
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39206539"
---
# <a name="tutorial-azure-active-directory-integration-with-bridgeline-unbound"></a>Tutorial: Integração do Azure Active Directory ao Bridgeline Unbound

Neste tutorial, você aprenderá a integrar o Bridgeline Unbound ao Azure AD (Azure Active Directory).

A integração do Bridgeline Unbound ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Bridgeline Unbound.
- Você pode permitir que os usuários façam logon automaticamente no Bridgeline Unbound (Logon Único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Bridgeline Unbound, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Assinatura habilitada para logon único do Bridgeline Unbound

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Como adicionar Bridgeline Unbound da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-bridgeline-unbound-from-the-gallery"></a>Como adicionar Bridgeline Unbound da galeria
Para configurar a integração do Bridgeline Unbound ao Azure AD, você precisa adicionar o Bridgeline Unbound da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Bridgeline Unbound da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Bridgeline Unbound**, selecione **Bridgeline Unbound** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Bridgeline Unbound na lista de resultados](./media/bridgelineunbound-tutorial/tutorial_bridgelineunbound_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Bridgeline Unbound com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário no Bridgeline Unbound é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Bridgeline Unbound.

Para configurar e testar o logon único do Azure AD com o Bridgeline Unbound, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Bridgeline Unbound](#create-a-bridgeline-unbound-test-user)** – para ter um equivalente de Brenda Fernandes no Bridgeline Unbound que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Bridgeline Unbound.

**Para configurar o logon único do Azure AD com o Bridgeline Unbound, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo do **Bridgeline Unbound**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/bridgelineunbound-tutorial/tutorial_bridgelineunbound_samlbase.png)
 
3. Na seção **Domínio e URLs do Bridgeline Unbound**, realize as seguintes etapas se desejar configurar o aplicativo no modo iniciado pelo **IDP**:

    ![Informações de logon único de URLs e Domínio do Bridgeline](./media/bridgelineunbound-tutorial/tutorial_bridgelineunbound_url.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `iApps_UPSTT_<ENVIRONMENTNAME>`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<SUBDOMAIN>.iapps.com/SAMLAssertionService.aspx`

4. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único de URLs e Domínio do Bridgeline](./media/bridgelineunbound-tutorial/tutorial_bridgelineunbound_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.iapps.com/CommonLogin/login?<INSTANCENAME>`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com [equipe de suporte do cliente do Bridgeline Unbound](mailto:support@iapps.com) para obter esses valores. 

4. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/bridgelineunbound-tutorial/tutorial_bridgelineunbound_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/bridgelineunbound-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do Bridgeline Unbound**, clique em **Configurar Bridgeline Unbound** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configuração do Bridgeline Unbound](./media/bridgelineunbound-tutorial/tutorial_bridgelineunbound_configure.png) 

7. Para configurar o logon único no lado do **Bridgeline Unbound**, é necessário enviar o **Certificado (Base64)** baixado, a **URL de Saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** para a [equipe de suporte do Bridgeline Unbound](mailto:support@iapps.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/bridgelineunbound-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grobridgelineinbound** e clique em **Todos os usuários**.

    ![Os links "Usuários e grobridgelineinbound" e "Todos os usuários"](./media/bridgelineunbound-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/bridgelineunbound-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/bridgelineunbound-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-bridgeline-unbound-test-user"></a>Criar um usuário de teste do Bridgeline Unbound

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Bridgeline Unbound. O Bridgeline Unbound dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar o Bridgeline Unbound, caso ele ainda não exista.

>[!Note]
>Se você precisar criar um usuário manualmente, entre em contato com [equipe de suporte do Bridgeline Unbound](mailto:support@iapps.com).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Bridgeline Unbound.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Bridgeline Unbound, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Bridgeline Unbound**.

    ![O link Bridgeline Unbound na lista de aplicativos](./media/bridgelineunbound-tutorial/tutorial_bridgelineunbound_app.png)  

3. No menu à esquerda, clique em **Usuários e grobridgelineinbound**.

    ![O link "Usuários e grobridgelineinbound"][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **Usuários e grobridgelineinbound** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição][203]

5. Na caixa de diálogo **Usuários e grobridgelineinbound**, selecione **Brenda Fernandes** na lista de usuários.

6. Clique no botão **Selecionar** na caixa de diálogo **Usuários e grobridgelineinbound**.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco Bridgeline Unbound no painel de acesso, deverá ser conectado automaticamente ao seu aplicativo Bridgeline Unbound.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bridgelineunbound-tutorial/tutorial_general_01.png
[2]: ./media/bridgelineunbound-tutorial/tutorial_general_02.png
[3]: ./media/bridgelineunbound-tutorial/tutorial_general_03.png
[4]: ./media/bridgelineunbound-tutorial/tutorial_general_04.png

[100]: ./media/bridgelineunbound-tutorial/tutorial_general_100.png

[200]: ./media/bridgelineunbound-tutorial/tutorial_general_200.png
[201]: ./media/bridgelineunbound-tutorial/tutorial_general_201.png
[202]: ./media/bridgelineunbound-tutorial/tutorial_general_202.png
[203]: ./media/bridgelineunbound-tutorial/tutorial_general_203.png

