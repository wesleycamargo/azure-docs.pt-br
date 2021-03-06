---
title: 'Tutorial: Integração do Azure Active Directory com o Way We Do | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Way We Do.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 84fc4f36-ecd1-42c6-8a70-cb0f3dc15655
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5dc6d8e2cf7ac4786f30484325406a1fe696dff3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56165120"
---
# <a name="tutorial-azure-active-directory-integration-with-way-we-do"></a>Tutorial: Integração do Azure Active Directory com o Way We Do

Neste tutorial, você aprenderá a integrar o Way We Do ao Azure AD (Azure Active Directory).

A integração do Way We Do ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem terá acesso ao Way We Do.
- Você pode habilitar os usuários a fazerem logon automaticamente no Way We Do (Logon Único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Way We Do, você precisará dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura do Way We Do habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Como adicionar o Way We Do da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-way-we-do-from-the-gallery"></a>Como adicionar o Way We Do da galeria
Para configurar a integração do Way We Do ao Azure AD, você precisará adicionar o Way We Do da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Way We Do da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Way We Do**, selecione **Way We Do**  no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Way We Do na lista de resultados](./media/waywedo-tutorial/tutorial_waywedo_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Way We Do com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Way We Do é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Way We Do.

Para configurar e testar o logon único do Azure AD com o Way We Do, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Way We Do](#create-a-way-we-do-test-user)** – para ter um equivalente de Brenda Fernandes no Way We Do vinculado à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo Way We Do.

**Para configurar o logon único do Azure AD com o Way We Do, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Way We Do**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/waywedo-tutorial/tutorial_waywedo_samlbase.png)

3. Na seção **Domínio e URLs do Way We Do**, execute as seguintes etapas:

    ![Informações de logon único de URL e Domínio do Way We Do](./media/waywedo-tutorial/tutorial_waywedo_url.png)

     a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [Equipe de suporte ao cliente do Way We Do](mailto:support@waywedo.com) para obter esses valores. 
 
4. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Bruto)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/waywedo-tutorial/tutorial_waywedo_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/waywedo-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do Way We Do**, clique em **Configurar o Way We Do** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configuração do Way We Do](./media/waywedo-tutorial/tutorial_waywedo_configure.png) 

7. Em uma janela diferente do navegador da Web, faça logon no Way We Do como Administrador de Segurança.

8. Clique no **ícone de pessoa** no canto superior direito de qualquer página do Way We Do e, em seguida, clique em **Conta** no menu suspenso.

    ![Conta do Way We Do](./media/waywedo-tutorial/tutorial_waywedo_account.png) 

9. Clique no **ícone do menu** para abrir o menu de navegação por push e clique em **Logon Único**.

    ![Único do Way We Do](./media/waywedo-tutorial/tutorial_waywedo_single.png)

10. Na página **Configuração de logon único**, execute as seguintes etapas:

    ![Salvar no Way We Do](./media/waywedo-tutorial/tutorial_waywedo_save.png)

     a. Clique na opção **Ativar logon único** para defini-la como **Sim** para habilitar o Logon Único.

    b. Na caixa de texto **Nome de logon único**, insira seu nome.

    c. Na caixa de texto **ID da Entidade**, cole o valor da **ID da Entidade do SAML** copiado no Portal do Azure.

    d. Na caixa de texto **URL de SSO do SAML**, cole o valor da **URL de Serviço de Logon Único do SAML** que você copiou do Portal do Azure.

    e. Carregue o certificado clicando no botão **selecionar** ao lado de **Certificado**.

    f. **Configurações Opcionais** -
    
    * Habilitar senhas – quando essa opção estiver desabilitada, a senha regular funcionará para o Way We Do, de modo que os usuários só poderão usar o logon único.

    * Habilitar o provisionamento automático – quando essa opção está habilitada, o endereço de email usado no logon será comparado automaticamente à lista de usuários no Way We Do. Se o endereço de email não corresponder a um usuário ativo no Way We Do, ele adicionará automaticamente uma nova conta de usuário para a pessoa que está entrando, solicitando quaisquer informações ausentes.

      > [!NOTE]
      > Os usuários adicionados por meio de logon único são adicionados como usuários gerais e não são atribuídos a uma função no sistema. Um Administrador pode entrar e modificar sua função como um editor ou administrador e também pode atribuir uma ou várias funções do Organograma. 

    g. Clique em **Salvar** para manter suas configurações.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/waywedo-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/waywedo-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/waywedo-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/waywedo-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-way-we-do-test-user"></a>Criar um usuário de teste do Way We Do

O objetivo desta seção é criar uma usuária chamada Brenda Fernandes no Way We Do. O Way We Do dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o Way We Do, caso ele ainda não exista.

> [!Note]
> Se você precisar criar um usuário manualmente, contate a  [equipe de suporte ao Cliente do Way We Do](mailto:support@waywedo.com).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Way We Do.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Way We Do, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Way We Do**.

    ![O link do Way We Do na lista de Aplicativos](./media/waywedo-tutorial/tutorial_waywedo_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco Way We Do no Painel de Acesso, deve fazer logon automaticamente no seu aplicativo Way We Do.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/waywedo-tutorial/tutorial_general_01.png
[2]: ./media/waywedo-tutorial/tutorial_general_02.png
[3]: ./media/waywedo-tutorial/tutorial_general_03.png
[4]: ./media/waywedo-tutorial/tutorial_general_04.png

[100]: ./media/waywedo-tutorial/tutorial_general_100.png

[200]: ./media/waywedo-tutorial/tutorial_general_200.png
[201]: ./media/waywedo-tutorial/tutorial_general_201.png
[202]: ./media/waywedo-tutorial/tutorial_general_202.png
[203]: ./media/waywedo-tutorial/tutorial_general_203.png

