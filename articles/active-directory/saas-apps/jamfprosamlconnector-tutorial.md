---
title: 'Tutorial: integração do Azure Active Directory ao Jamf Pro | Microsoft Docs'
description: Saiba como configurar um logon único entre o Azure Active Directory e o Jamf Pro.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2018
ms.author: jeedes
ms.openlocfilehash: 94b8b935728110cd5dd07b2066e8320274e3b082
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39428410"
---
# <a name="tutorial-azure-active-directory-integration-with-jamf-pro"></a>Tutorial: integração do Azure Active Directory ao Jamf Pro

Neste tutorial, você aprenderá a integrar o Jamf Pro ao Azure AD (Azure Active Directory).

A integração do Jamf Pro ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem terá acesso ao Jamf Pro.
- Você pode permitir que seus usuários façam logon automaticamente no Jamf Pro (Logon Único) com suas contas do Azure Active Directory.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Jamf Pro, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Jamf Pro habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Como adicionar o Jamf Pro da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-jamf-pro-from-the-gallery"></a>Como adicionar o Jamf Pro da galeria
Para configurar a integração do Jamf Pro ao AD do Azure, você precisará adicionar o Jamf Pro da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Jamf Pro por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **Jamf Pro**, selecione **Jamf Pro** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Jamf Pro na lista de resultados](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Jamf Pro, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Jamf Pro é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Jamf Pro.

Para configurar e testar o logon único do Azure AD com o Jamf Pro, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de teste do Jamf Pro](#create-a-jamf-pro-test-user)** – para ter um equivalente de Brenda Fernandes no Jamf Pro que esteja vinculado à representação de usuário do Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo portal Jamf Pro.

**Para configurar o logon único do Azure AD com o Jamf Pro, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Jamf Pro**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_samlbase.png)

1. Na seção **Domínio e URLs do Jamf Pro**, realize as seguintes etapas se desejar configurar o aplicativo no modo iniciado pelo **IDP**:

    ![Informações de logon único em Domínio e URLs do Jamf Pro](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_url.png)

    a. Na caixa de texto **Identificador (ID da Entidade)**, digite uma URL usando o seguinte padrão: `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<subdomain>.jamfcloud.com/saml/SSO`

1. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único em Domínio e URLs do Jamf Pro](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<subdomain>.jamfcloud.com`
     
    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. Você obterá o valor real do Identificador de **Logon único** seção no portal de Jamf Pro, que é explicado no tutorial posteriormente. Você pode extrair o valor real **subdomínio** o valor do identificador de valor e usar as informações do **subdomínio** na URL de entrada e da URL de resposta.

1. Na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar a **URL de metadados de federação do aplicativo** e cole-a no Bloco de Notas.

    ![O link de download do Certificado](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_certificate.png) 

1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/jamfprosamlconnector-tutorial/tutorial_general_400.png)
    
1. Em uma janela de navegador da Web diferente, faça logon no site de sua empresa do Jamf Pro como administrador.

1. Clique no **ícone de Configurações** no canto superior direito da página.

    ![Configuração do Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

1. Clique no **Logon Único**.

    ![Configuração do Jamf Pro](./media/jamfprosamlconnector-tutorial/configure2.png)

1. Na página **Single Sign-On**, execute as seguintes etapas:

    ![O Jamf Pro único](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. Selecione **Jamf Pro Server** para ativar o acesso ao Single Sign-On.

    b. Ao selecionar **Permitir desvio para todos os usuários**, os usuários não serão redirecionados para a página de login do Provedor de identidade para autenticação, mas poderão efetuar login no Jamf Pro diretamente. Quando um usuário tenta acessar o Jamf Pro por meio do Provedor de Identidade, ocorre a autenticação e autorização de SSO iniciada pelo IdP.

    c. Selecione a opção **NameID** para **MAPEAMENTO DE USUÁRIO: SAML**. Por padrão, essa configuração é definida como **NameID**, mas você pode definir um atributo personalizado.

    d. Selecione **E-mail** para **MAPEAMENTO DO USUÁRIO: JAMF PRO**. O Jamf Pro mapeia atributos SAML enviados pelo IdP das seguintes maneiras: por usuários e por grupos. Quando um usuário tenta acessar o Jamf Pro, por padrão, o Jamf Pro obtém informações sobre o usuário do Provedor de Identidade e o associa às contas de usuário do Jamf Pro. Se a conta de usuário recebida não existir no Jamf Pro, ocorrerá a correspondência do nome do grupo.

    e. Cole o valor `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` no **nome do grupo de atributo** caixa de texto.
 
1. Na mesma página rolagem para baixo até **provedor de identidade** sob o **Single Sign-On** seção e execute as seguintes etapas:

    ![Configuração do Jamf Pro](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Selecione **Outro** como opção no menu suspenso **IDENTITY PROVIDER**.

    b. Na caixa de texto **OTHER PROVIDER**, digite **Azure AD**.

    c. Selecione **URL de metadados** como uma opção do menu suspenso **IDENTITY PROVIDER METADATA SOURCE** e, na caixa de texto a seguir, cole o valor da **URL de metadados de federação do aplicativo** que você copiou do portal do Azure.

    d. Copie o valor **ID da entidade** e cole-o na caixa de texto **Identifier (Entity ID)** na seção **Jamf Pro Domain e URLs** no portal do Azure.

    >[!NOTE]
    > Aqui, o valor borrado é a parte do subdomínio. Use esse valor para concluir o URL de conexão e o URL de resposta na seção **Domínio e URLs Jamf Pro** no portal do Azure.

    e. Clique em **Salvar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/jamfprosamlconnector-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/jamfprosamlconnector-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/jamfprosamlconnector-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/jamfprosamlconnector-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-jamf-pro-test-user"></a>Criar um usuário de teste do Jamf Pro

Para permitir que os usuários do Azure AD façam logon no Jamf Pro, eles devem ser provisionados no Jamf Pro. No caso do Jamf Pro, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu site corporativo do Jamf Pro como administrador.

1. Clique no **ícone de Configurações** no canto superior direito da página.

    ![Adicionar Funcionário](./media/jamfprosamlconnector-tutorial/configure1.png)

1. Clique em **Contas de usuário e grupos do Pro Jamf**.

    ![Adicionar Funcionário](./media/jamfprosamlconnector-tutorial/user1.png)

1. Clique em **Novo**.

    ![Adicionar Funcionário](./media/jamfprosamlconnector-tutorial/user2.png)

1. Selecione **Criar Conta Padrão**.

    ![Adicionar Funcionário](./media/jamfprosamlconnector-tutorial/user3.png)

1. Na caixa de diálogo **Novo Usuário**, realize as seguintes etapas:

    ![Adicionar Funcionário](./media/jamfprosamlconnector-tutorial/user4.png)

    a. Na caixa de texto **USERNAME**, digite o nome completo de BrendaFernandes.

    b. Selecione as opções apropriadas de acordo com a sua organização para o **NÍVEL DE ACESSO**, **CONJUNTO DE PRIVILÉGIOS** e para o **STATUS DE ACESSO**.
    
    c. Na caixa de texto **NOME COMPLETO**, digite o nome completo de Brenda Fernandes.

    d. Na caixa de texto **ENDEREÇO DE EMAIL**, digite o endereço de email da conta de Brenda Fernandes.

    e. Na caixa de texto **SENHA**, digite a senha do usuário.

    f. Na caixa de texto **VERIFICAR SENHA**, digite a senha do usuário.

    g. Clique em **Salvar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Jamf Pro.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Jamf Pro, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, escolha **Jamf Pro**.

    ![O link do Jamf Pro na lista de Aplicativos](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco Jamf Pro no Painel de Acesso, deverá ser conectado automaticamente ao seu aplicativo Jamf Pro.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/jamfprosamlconnector-tutorial/tutorial_general_01.png
[2]: ./media/jamfprosamlconnector-tutorial/tutorial_general_02.png
[3]: ./media/jamfprosamlconnector-tutorial/tutorial_general_03.png
[4]: ./media/jamfprosamlconnector-tutorial/tutorial_general_04.png

[100]: ./media/jamfprosamlconnector-tutorial/tutorial_general_100.png

[200]: ./media/jamfprosamlconnector-tutorial/tutorial_general_200.png
[201]: ./media/jamfprosamlconnector-tutorial/tutorial_general_201.png
[202]: ./media/jamfprosamlconnector-tutorial/tutorial_general_202.png
[203]: ./media/jamfprosamlconnector-tutorial/tutorial_general_203.png

