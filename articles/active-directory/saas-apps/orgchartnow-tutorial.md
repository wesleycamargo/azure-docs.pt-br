---
title: 'Tutorial: Integração do Azure Active Directory com OrgChart Now | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o OrgChart Now.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 50a1522f-81de-4d14-9b6b-dd27bb1338a4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f044b6fcefc0c0345ea6c83212da31c7c03c11f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56202880"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Tutorial: Integração do Azure Active Directory com OrgChart Now

Neste tutorial, você aprenderá a integrar o OrgChart Now ao Azure AD (Azure Active Directory).

A integração do OrgChart Now ao Azure AD oferece os seguintes benefícios:

- No Azure AD, você poderá controlar quem tem acesso ao OrgChart Now.
- Você pode permitir que seus usuários façam logon automaticamente no OrgChart No (logon único) com as contas do Azure AD deles.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o OrgChart Now, você precisará dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura habilitada para logon único do OrgChart Now

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o OrgChart Now a partir da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-orgchart-now-from-the-gallery"></a>Adicionar o OrgChart Now a partir da galeria
Para configurar a integração do OrgChart Now ao AD do Azure, você precisará adicionar o OrgChart Now da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o OrgChart Now a partir da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **OrgChart Now**, selecione **OrgChart Now** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![OrgChart Now na lista de resultados](./media/orgchartnow-tutorial/tutorial_orgchartnow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o OrgChart Now, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do OrgChart Now é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do AD do Azure e o usuário relacionado no OrgChart Now.

Para configurar e testar o logon único do AD do Azure com o OrgChart Now, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de teste do OrgChart Now](#create-an-orgchart-now-test-user)** – para ter um equivalente de Brenda Fernandes no OrgChart Now vinculado à representação do usuário no Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo portal OrgChart Now.

**Para configurar o logon único do AD do Azure com o OrgChart Now, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **portal OrgChart Now**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/orgchartnow-tutorial/tutorial_orgchartnow_samlbase.png)

1. Na seção **Domínio e URLs do OrgChart Now**, se você desejar configurar o aplicativo em modo iniciado pelo **IDP**:

    ![Informações de logon único em Domínio e URLs do OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_url.png)

    Na caixa de texto **Identificador**, digite uma URL: `https://sso2.orgchartnow.com`

1. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único em Domínio e URLs do OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`
     
    > [!NOTE]
    > `<YourEntityID>` é a ID de Entidade SAML copiada da seção de Referência Rápida, descrita posteriormente no tutorial.

1. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/orgchartnow-tutorial/tutorial_orgchartnow_certificate.png) 

1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/orgchartnow-tutorial/tutorial_general_400.png)
    
1. Na seção **OrgChart Now Configuration**, clique em **Configurar OrgChart Now** para abrir a janela **Configure sign-on**. Copie **SAML Entity ID** da **seção de Referência Rápida** e use para completar a **URL de Logon** no domínio **OrgChart Now Domain e seção de URLs**.

    ![Configuração do OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_configure.png) 

1. Para configurar o logon único no lado do**OrgChart Now**você precisará enviar o **Metadata XML**baixado para a equipe de suporte do [OrgChart Now](mailto:ocnsupport@officeworksoftware.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/orgchartnow-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/orgchartnow-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/orgchartnow-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/orgchartnow-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-an-orgchart-now-test-user"></a>Criar um usuário de teste no OrgChart Now

Para permitir que os usuários do Azure AD façam logon no OrgChart Now, eles devem ser provisionados no OrgChart Now. 

1. O OrgChart Now dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Um novo usuário é criado durante uma tentativa de acessar o OrgChart Now, caso ele ainda não exista. O recurso de provisionamento de usuário JIT criará apenas um **usuário somente leitura** quando uma solicitação SSO vier de um IDP reconhecido e o email na asserção SAML não for encontrado na lista de usuários. Para esse recurso de provisionamento automático, é necessário criar um grupo de acesso chamado **Geral** no OrgChart Now. Execute as etapas abaixo para criar um grupo de acesso:

     a. Vá para a opção **Gerenciar Grupos** depois de clicar na **engrenagem** no canto superior direito da interface do usuário.

    ![Grupos OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. Selecione o ícone **Adicionar** e nomeia o grupo **Geral**, em seguida clique em **OK**. 

    ![Adicionar OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Selecione a (s) pasta (s) que você deseja que os usuários gerais ou somente de leitura possam acessar:

    ![Pastas OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **Bloqueie** as pastas para que apenas usuários administradores possam modificá-las. Em seguida, pressione **OK**.

    ![Bloquear o OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

1. Para criar usuários **Admin** e **usuários de leitura / gravação**, crie manualmente um usuário para obter acesso ao nível de privilégio por SSO. Para provisionar uma conta de usuário, execute as seguintes etapas:

     a. Faça logon no OrgChart Now como Administrador de Segurança.

    b.  Clique em **Configurações** no canto superior direito e navegue até **Gerenciar Usuários**.

    ![Configurações do OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Clique em **Adicionar** para executar as seguintes etapas:

    ![Gerenciar o OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * Na caixa de texto **ID do usuário**, insira o ID do usuário como **brittasimon@contoso.com**.

    * Na caixa de texto **Endereço de Email**, insira o email do usuário como **brittasimon@contoso.com**.

    * Clique em **Adicionar**.
    
### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao OrgChart Now.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao OrgChart Now, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, escolha **OrgChart Now**.

    ![O link do OrgChart Now na lista de Aplicativos](./media/orgchartnow-tutorial/tutorial_orgchartnow_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco OrgChart Now no Painel de Acesso, deve fazer logon automaticamente no seu aplicativo do OrgChart Now.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/orgchartnow-tutorial/tutorial_general_01.png
[2]: ./media/orgchartnow-tutorial/tutorial_general_02.png
[3]: ./media/orgchartnow-tutorial/tutorial_general_03.png
[4]: ./media/orgchartnow-tutorial/tutorial_general_04.png

[100]: ./media/orgchartnow-tutorial/tutorial_general_100.png

[200]: ./media/orgchartnow-tutorial/tutorial_general_200.png
[201]: ./media/orgchartnow-tutorial/tutorial_general_201.png
[202]: ./media/orgchartnow-tutorial/tutorial_general_202.png
[203]: ./media/orgchartnow-tutorial/tutorial_general_203.png

