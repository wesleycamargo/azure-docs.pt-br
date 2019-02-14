---
title: 'Tutorial: Integração do Azure Active Directory ao Infinite Campus | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Infinite Campus.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3995b544-e751-4e0f-ab8b-c9a3862da6ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53eb0c4ad5c0a21f46985062ef8202a87dc0d5e6
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56189354"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>Tutorial: Integração do Azure Active Directory ao Infinite Campus

Neste tutorial, você aprenderá como integrar o Infinite Campus ao Azure AD (Azure Active Directory).

A integração do Campus Infinito ao Microsoft Azure Active Directory oferece os seguintes benefícios:

- Você pode controlar no Microsoft Azure Active Directory quem tem acesso ao Infinite Campus.
- Você pode permitir que seus usuários façam logon automaticamente no Campus Infinito (Logon Único) com suas contas do Microsoft Azure Active Directory.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure Active Directory ao Infinite Campus, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura habilitada para logon único no Infinite Campus

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).
- No mínimo, você precisa ser um administrador do Azure Active Directory para concluir a configuração.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o infinito Campus da Galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-infinite-campus-from-the-gallery"></a>Adicionando o infinito Campus da Galeria

Para configurar a integração do Infinite Campus no Microsoft Azure Active Directory, você precisa adicionar o Infinite Campus da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o infinito Campus da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Infinite Campus**, selecione **Infinite Campus** no painel de resultados e clique no botão **Add** para adicionar o aplicativo.

    ![Campus infinito na lista de resultados](./media/infinitecampus-tutorial/tutorial_infinitecampus_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Microsoft Azure Active Directory com o Infinite Campus com base em um usuário de teste chamado "Britta Simon".

Para que o logon único funcione, o Microsoft Azure Active Directory precisa saber o que o usuário de contraparte do Infinite Campus é para um usuário no Azure AD. Em outras palavras, um relacionamento de link entre um usuário do Microsoft Azure Active Directory e o usuário relacionado no Infinite Campus precisa ser estabelecido.

Para configurar e testar o logon único do Microsoft Azure Active Directory com o Infinite Campus, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do Campus Infinito](#creating-a-infinite-campus-test-user)** - para ter um equivalente de Britta Simon no Campus Infinito vinculado à representação de usuário do Microsoft Azure Active Directory.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Microsoft Azure Active Directory no portal do Azure e configurará o logon único em seu aplicativo Infinite Campus.

**Para configurar o logon único do Microsoft Azure Active Directory com o Infinite Campus, execute as seguintes etapas:**

1. No portal do Azure, sobre o **Campus infinito** página de integração do aplicativo, clique em **logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![Configurar o logon único](common/tutorial_general_301.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Configurar o logon único](common/editconfigure.png)

4. Na seção **Configuração básica do SAML**, se você tiver um **arquivo de metadados do provedor de serviços**, complete as etapas 4.a até 4.d e depois pule para a etapa 11.c. Se você não tiver um arquivo de metadados do provedor de serviços, vá para a etapa 5.

     a. Clique em **Carregar arquivo de metadados**.

        ![image](common/b9_saml.png)

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.

    ![image](common/b9(1)_saml.png)

    c. Depois que o arquivo de metadados for carregado com êxito, os valores **Identificador** e **URL de resposta** serão populados automaticamente na caixa de texto da seção **Configuração Básica do SAML**, conforme mostrado abaixo:

    ![image](./media/infinitecampus-tutorial/tutorial_infinitecampus_url.png)

    d. Na caixa de texto **URL de login**, digite um URL usando o seguinte padrão (o domínio variará com o modelo de hospedagem): `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

5. Se você não tiver o **arquivo de metadados do provedor de serviços**, execute as etapas a seguir (observe que o domínio variará conforme o modelo de hospedagem):

     a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

    ![Domínio Campus Infinito e informações de logon único de URLs](./media/infinitecampus-tutorial/tutorial_infinitecampus_url1.png)

6. Na página **Certificado de assinatura SAML**, na seção **Certificado de assinatura SAML**, clique no **ícone** de cópia para copiar  **URL de metadados da federação de aplicativo**  e cole-o no bloco de notas.

    ![O link de download do Certificado](./media/infinitecampus-tutorial/tutorial_infinitecampus_certificate.png) 

7. Na seção **Configurar Infinito Campus**, use os seguintes valores para validar o upload ou a utilização do arquivo / URL de metadados do Azure.

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

    ![Infinite Campus Configuration](common/configuresection.png)

8. Em uma janela do navegador da Web diferente, faça login no Infinite Campus como um administrador de segurança.

9. No lado esquerdo do menu, clique em **Administração do sistema**.

    ![O administrador](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

10. Navegue para **Segurança do usuário** >  **Gerenciamento do SAML** > **Configuração do provedor de serviços de SSO**.

    ![O saml](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

11. Na página **Configuração do Provedor de Serviços de SSO**, execute as seguintes etapas:

    ![O sso](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. Selecione **habilitar logon único do SAML**.
    
    b. Na seção **Selecionar uma opção para recuperar os dados do servidor de Provedor de Identidade (IDP)**, selecione o **URL de metadados**, cole o **URL de Metadados da Federação do Aplicativo** na caixa de texto e clique em **Sincronizar**.

    c. Clique no link **Metadados do Provedor de Serviços** para salvar o **arquivo de metadados do Provedor de Serviços** no seu computador e carregue-o na seção **Configuração básica do SAML** para preencher automaticamente os valores de **Identificador** e **URL de resposta** no portal do Azure (consulte a etapa 4 para carregamento e preenchimento automático dos valores ou a etapa 5 para entrada manual).

    d. Depois de clicar em **Sincronizar**, os valores são preenchidos automaticamente na página **Configuração do Provedor de Serviços de SSO**.

    e. Clique em **Salvar**.

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure

O objetivo desta seção é criar uma _único_ usuário de teste no portal do Azure chamado Britta Simon.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Criar um usuário do AD do Azure][100]

2. Selecione **Novo usuário** na parte superior da tela.

    ![Criação de um usuário de teste do AD do Azure](common/create_aaduser_01.png) 

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![Criação de um usuário de teste do AD do Azure](common/create_aaduser_02.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No **nome de usuário** , digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.

### <a name="creating-a-infinite-campus-test-user"></a>Criando um usuário de teste do Infinite Campus

Infinite Campus tem uma arquitetura demográfica centrada. Entre em contato com a equipe de suporte do [Infinite Campus](mailto:sales@infinitecampus.com) para adicionar os usuários à plataforma Infinite Campus.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Britta Simon use o logon único do Azure, concedendo acesso ao Infinite Campus.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **Infinite Campus**.

    ![Configurar o logon único](./media/infinitecampus-tutorial/tutorial_infinitecampus_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar na peça Infinite Campus no Painel de Acesso, deverá fazer logon automaticamente no aplicativo Infinite Campus. Se você estiver efetuando login no aplicativo Infinite Campus no mesmo navegador em que estiver administrando o Azure AD, verifique se está conectado ao Azure AD como o usuário de teste. Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
