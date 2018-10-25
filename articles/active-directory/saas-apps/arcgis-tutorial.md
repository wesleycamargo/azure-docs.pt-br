---
title: 'Tutorial: Integração do Azure Active Directory ao ArcGIS Online | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ArcGIS Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: jeedes
ms.openlocfilehash: 12ab224481c519db36ae21dd11916649ff0bfbe3
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/04/2018
ms.locfileid: "48269027"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Tutorial: Integração do Azure Active Directory ao ArcGIS Online

Neste tutorial, você aprende a integrar o ArcGIS Online ao Azure AD (Azure Active Directory).

A integração do ArcGIS Online ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao ArcGIS Online.
- É possível permitir que os usuários se conectem automaticamente ao ArcGIS Online (Logon Único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao ArcGIS Online, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do ArcGIS Online

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.
O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o ArcGIS Online por meio da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-arcgis-online-from-the-gallery"></a>Adicionando o ArcGIS Online por meio da galeria

Para configurar a integração do ArcGIS Online ao Azure AD, você precisa adicionar o ArcGIS Online à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o ArcGIS Online por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![image](./media/arcgis-tutorial/selectazuread.png)

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![image](./media/arcgis-tutorial/a_select_app.png)
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![image](./media/arcgis-tutorial/a_new_app.png)

4. Na caixa de pesquisa, digite **ArcGIS Online**, selecione **ArcGIS Online** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![image](./media/arcgis-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o ArcGIS Online, com base em um usuário de teste chamado “Britta Simon”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do ArcGIS Online é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do ArcGIS Online.

No ArcGIS Online, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o ArcGIS Online, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do ArcGIS Online](#create-an-arcgis-online-test-user)** – para ter um equivalente de Brenda Fernandes no ArcGIS Online que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo ArcGIS Online.

**Para configurar o logon único do Azure AD com o ArcGIS Online, realize as seguintes etapas:**

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo do **ArcGIS Online**, selecione **Logon único**.

    ![image](./media/arcgis-tutorial/b1_b2_select_sso.png)

2. Clique em **Alterar modo de logon único** na parte superior da tela para selecionar o modo **SAML**.

      ![image](./media/arcgis-tutorial/b1_b2_saml_ssso.png)

3. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![image](./media/arcgis-tutorial/b1_b2_saml_sso.png)

4. Na página **Definir logon único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Configuração básica de SAML**.

    ![image](./media/arcgis-tutorial/b1-domains_and_urlsedit.png)

5. Na seção **Configuração básica de SAML**, execute as etapas a seguir:

    a. Na caixa de texto **URL de Entrada**, digite uma URL usando o seguinte padrão: `https://<companyname>.maps.arcgis.com`.

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `<companyname>.maps.arcgis.com`.

    ![image](./media/arcgis-tutorial/b1-domains_and_urls.png)

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao Cliente do ArcGIS Online](http://support.esri.com/en/) para obter esses valores.

6. Na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de metadados de federação** e, em seguida, salve o arquivo xml em seu computador.

    ![image](./media/arcgis-tutorial/federationxml.png)

7. Para automatizar a configuração no **ArcGIS Online**, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![image](./media/arcgis-tutorial/install_extension.png)

8. Depois de adicionar a extensão ao navegador, clique em **configurar ArcGIS Online**. Você será direcionado para o aplicativo ArcGIS Online. Lá, forneça as credenciais de administrador para entrar no ArcGIS Online. A extensão do navegador irá configurar automaticamente o aplicativo e automatizar as etapas de 9 a 13.

9. Se quiser configurar o ArcGIS Online manualmente, abra uma nova janela do navegador da Web, entre no site corporativo do ArcGIS Online como administrador e execute as seguintes etapas:

10. Clique em **EDITAR CONFIGURAÇÕES**.

    ![Editar Configurações](./media/arcgis-tutorial/ic784742.png "Editar Configurações")

11. Clique em **Segurança**.

    ![Segurança](./media/arcgis-tutorial/ic784743.png "Segurança")

12. Em **Logons Corporativos**, clique em **DEFINIR PROVEDOR DE IDENTIDADE**.

    ![Logons Corporativos](./media/arcgis-tutorial/ic784744.png "Logons Corporativos")

13. Na página de configuração **Definir Provedor de Identidade** , realize as seguintes etapas:

    ![Definir Provedor de Identidade](./media/arcgis-tutorial/ic784745.png "Definir Provedor de Identidade")

    a. Na caixa de texto **Nome**, digite o nome de sua organização.

    b. Para **Os metadados do Provedor de Identidade Corporativo serão fornecidos usando**, selecione **Um Arquivo**.

    c. Para carregar seu arquivo de metadados baixado, clique em **Escolher arquivo**.

    d. Clique em **DEFINIR PROVEDOR DE IDENTIDADE**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![image](./media/arcgis-tutorial/d_users_and_groups.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![image](./media/arcgis-tutorial/d_adduser.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![image](./media/arcgis-tutorial/d_userproperties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.

### <a name="create-an-arcgis-online-test-user"></a>Criar um usuário de teste do ArcGIS Online

Para permitir que os usuários do Azure AD façam logon no ArcGIS Online, eles devem ser provisionados no ArcGIS Online.  
No caso do ArcGIS Online, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu locatário do **ArcGIS** .

2. Clique em **CONVIDAR MEMBROS**.
   
    ![Convidar Membros](./media/arcgis-tutorial/ic784747.png "Convidar Membros")

3. Selecione **Adicionar membros automaticamente sem enviar um email** e, depois, clique em **AVANÇAR**.
   
    ![Adicionar Membros Automaticamente](./media/arcgis-tutorial/ic784748.png "Adicionar Membros Automaticamente")

4. Na página do diálogo **Membros** , realize as seguintes etapas:
   
     ![Adicionar e examinar](./media/arcgis-tutorial/ic784749.png "Adicionar e examinar")
    
     a. Insira o **Email**, o **Nome** e o **Sobrenome** de uma conta válida do AAD que deseja provisionar.
  
     b. Clique em **ADICIONAR E EXAMINAR**.
5. Examine os dados inseridos e, depois, clique em **ADICIONAR MEMBROS**.
   
    ![Adicionar membro](./media/arcgis-tutorial/ic784750.png "Adicionar membro")
        
    > [!NOTE]
    > O titular da conta do Active Directory do Azure receberá um email e seguirá um link para confirmar a conta antes que ela se torne ativa.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao ArcGIS Online.

1. No portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![image](./media/arcgis-tutorial/d_all_applications.png)

2. Na lista de aplicativos, selecione **ArcGIS Online**.

    ![image](./media/arcgis-tutorial/d_all_application.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![image](./media/arcgis-tutorial/d_leftpaneusers.png)

4. Selecione o botão **Adicionar** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![image](./media/arcgis-tutorial/d_assign_user.png)

4. Na caixa de diálogo **Usuários e grupos**, selecione **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

5. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco ArcGIS Online no Painel de Acesso, deverá ser automaticamente conectado ao aplicativo ArcGIS Online.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



