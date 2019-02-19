---
title: 'Tutorial: Integração do Azure Active Directory ao Dome9 Arc | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o Dome9 Arc.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/31/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91ef20398bc3e15b1ee47c6e7c28795f4d9bc08c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56190935"
---
# <a name="tutorial-azure-active-directory-integration-with-dome9-arc"></a>Tutorial: Integração do Azure Active Directory ao Dome9 Arc

Neste tutorial, você aprenderá a integrar o Dome9 Arc ao Azure AD (Azure Active Directory).
A integração do Dome9 Arc ao Azure Active Directory oferece os seguintes benefícios:

* Você pode controlar no Azure Active Directory quem terá acesso ao Dome9 Arc.
* Você pode permitir que os usuários sejam conectados automaticamente ao Dome9 Arc (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure Active Directory com o Dome9 Arc, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do Dome9 Arc habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Dome9 Arc dá suporte ao SSO iniciado por **SP** e **IDP**

## <a name="adding-dome9-arc-from-the-gallery"></a>adicionar o Dome9 Arc da galeria

Para configurar a integração do Dome9 Arc ao Azure Active Directory, será necessário adicionar o Dome9 Arc da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Dome9 Arc da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Dome9 Arc**, selecione **Dome9 Arc** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Dome9 Arc na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Dome9 Arc, com base em uma usuária de teste chamada **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Dome9 Arc.

Para configurar e testar o logon único do Azure Active Directory com o Dome9 Arc, será necessário concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do Dome9 Arc](#configure-dome9-arc-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Dome9 Arc](#create-dome9-arc-test-user)** – para ter um equivalente de Brenda Fernandes no Dome9 Arc vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Dome9 Arc, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Dome9 Arc**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    ![Informações de logon único de Domínio e URLs do Dome9 Arc](common/idp-intiated.png)

     a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://secure.dome9.com/`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://secure.dome9.com/sso/saml/yourcompanyname`

    > [!NOTE]
    > Você selecionará o valor do nome da empresa no portal de administração do dome9, que será explicado mais adiante no tutorial.

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único de Domínio e URLs do Dome9 Arc](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de login**, digite um URL usando o seguinte padrão: `https://secure.dome9.com/sso/saml/<yourcompanyname>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e a URL de Logon reais. Contate a [equipe de suporte ao Cliente do Dome9 Arc](mailto:support@dome9.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. O aplicativo Dome9 Arc espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![image](common/edit-attribute.png)

7. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, edite as declarações usando o **ícone Editar** ou adicione as declarações usando **Adicionar nova declaração** para configurar o atributo de token SAML conforme mostrado na imagem acima e executar as seguintes etapas: 

    | NOME |  Atributo de Origem|
    | ---------------| --------------- |
    | memberof | user.assignedroles |

     a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Salvar**.

8. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

9. Na seção **Configurar o Dome9 Arc**, copie a URL apropriada de acordo com seus requisitos.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-dome9-arc-single-sign-on"></a>Configurar logon único do Dome9 Arc

1. Em uma janela do navegador da Web diferente, faça logon no seu site da empresa Dome9 Arc como administrador.

2. Clique nas **Configurações de Perfil** no canto superior direito e depois em **Configurações de Conta**. 

    ![Configuração do Dome9 Arc](./media/dome9arc-tutorial/configure1.png)

3. Navegue até **SSO** e, em seguida, clique em **HABILITAR**.

    ![Configuração do Dome9 Arc](./media/dome9arc-tutorial/configure2.png)

4. Na seção de Configuração de SSO, execute as seguintes etapas:

    ![Configuração do Dome9 Arc](./media/dome9arc-tutorial/configure3.png)

     a. Digite o nome da empresa na caixa de texto **ID da Conta**. Esse valor deve ser usado na URL de resposta mencionada na seção URL do Portal do Azure.

    b. Na caixa de texto **Emissor**, cole o valor de **Identificador do Azure AD** que você copiou do portal do Azure.

    c. Na caixa de texto **URL do ponto de extremidade IDP**, cole o valor de **URL de Logon** que você copiou do portal do Azure.

    d. Abra seu certificado codificado com Base64 baixado no bloco de notas, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **Certificado X.509**.

    e. Clique em **Salvar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Dome9 Arc.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Dome9 Arc**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Dome9 Arc**.

    ![O link do Dome9 Arc na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-dome9-arc-test-user"></a>Criar um usuário de teste do Dome9 Arc

Para habilitar que os usuários do Azure Active Directory façam logon no Dome9 Arc, eles devem ser provisionados no aplicativo. O Dome9 Arc suporta provisionamento just-in-time, mas para que ele funcione corretamente, o usuário deve selecionar a **Função** específica e atribuir o mesmo ao usuário.

   >[!Note]
   >Para a criação da **Função** e outros detalhes contate a [Equipe de suporte ao cliente Dome9 Arc](https://dome9.com/about/contact-us/).

**Para provisionar uma conta de usuário manualmente, execute as seguintes etapas:**

1. Faça logon em seu site da empresa Dome9 Arc como administrador.

2. Clique em **Usuários e Funções** e, em seguida, clique em **Usuários**.

    ![Adicionar Funcionário](./media/dome9arc-tutorial/user1.png)

3. Clique em **ADICIONAR USUÁRIO**.

    ![Adicionar Funcionário](./media/dome9arc-tutorial/user2.png)

4. Na seção **Criar Usuário** , realize as seguintes etapas:

    ![Adicionar Funcionário](./media/dome9arc-tutorial/user3.png)

     a. Na caixa de texto **Email**, digite o email do usuário como Brittasimon@contoso.com.

    b. Na caixa de texto **Nome**, digite o nome do usuário, como Brenda.

    c. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário, como Fernandes.

    d. Faça o **Usuário do SSO** como **On**.

    e. Clique em **CRIAR**.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Dome9 Arc no Painel de Acesso, você deve ser conectado automaticamente ao Dome9 Arc para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

