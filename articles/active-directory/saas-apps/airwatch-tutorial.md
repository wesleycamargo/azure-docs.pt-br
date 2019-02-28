---
title: 'Tutorial: Integração do Azure Active Directory com AirWatch | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o AirWatch.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9bbf8e4ebf59e8084d0a831c55685238c053e13
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56882698"
---
# <a name="tutorial-azure-active-directory-integration-with-airwatch"></a>Tutorial: Integração do Azure Active Directory com AirWatch

Neste tutorial, você aprenderá a integrar o AirWatch ao Azure AD (Azure Active Directory).
A integração do AirWatch ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao AirWatch.
* Você pode permitir que os usuários sejam conectados automaticamente ao AirWatch (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao AirWatch, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do AirWatch

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O AirWatch dá suporte ao SSO iniciado por **SP**

## <a name="adding-airwatch-from-the-gallery"></a>Adição do AirWatch da Galeria

Para configurar a integração do AirWatch com o Azure AD, você precisa adicionar o AirWatch à sua lista de aplicativos SaaS gerenciados a partir da galeria.

**Para adicionar o AirWatch da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **AirWatch**, selecione **AirWatch** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![AirWatch na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o AirWatch, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do AirWatch.

Para configurar e testar o logon único do Azure AD com o AirWatch, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do AirWatch](#configure-airwatch-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Criar um usuário de teste do AirWatch](#create-airwatch-test-user)** – para ter um equivalente de Brenda Fernandes no AirWatch que esteja vinculado à representação de usuário no Azure AD.
5. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o AirWatch, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **AirWatch**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do AirWatch](common/sp-identifier.png)

     a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    b. Na caixa de texto **Identificador (ID da Entidade)**, digite um valor como: `AirWatch`

    > [!NOTE]
    > Esse valor não é o real. Atualize esse valor com a URL de Entrada real. Para obter esse valor, entre em contato com a [equipe de suporte do cliente AirWatch](https://www.air-watch.com/company/contact-us/). Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. O aplicativo AirWatch espera as declarações SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![image](common/edit-attribute.png)

6. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, edite as declarações usando o **ícone Editar** ou adicione as declarações usando **Adicionar nova declaração** para configurar o atributo de token SAML conforme mostrado na imagem acima e executar as seguintes etapas:

    | NOME |  Atributo de Origem|
    |---------------|----------------|
    | UID | user.userprincipalname |
    | | |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Salvar**.

7. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

8. Na seção **Configurar o AirWatch**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-airwatch-single-sign-on"></a>Configurar o Logon Único do AirWatch

1. Em outra janela do navegador da Web, faça logon em seu site de empresa AirWatch como um administrador.

2. No painel de navegação à esquerda, clique em **Contas** e em **Administradores**.

   ![Administradores](./media/airwatch-tutorial/ic791920.png "Administradores")

3. Expanda o menu **Configurações** e clique em **Serviços de Diretório**.

   ![Configurações](./media/airwatch-tutorial/ic791921.png "Configurações")

4. Clique na guia **Usuário**, na caixa de texto **DN Base**, digite seu nome de domínio e clique em **Salvar**.

   ![Usuário](./media/airwatch-tutorial/ic791922.png "Usuário")

5. Clique na guia **Servidor** .

   ![Servidor](./media/airwatch-tutorial/ic791923.png "Servidor")

6. Execute as seguintes etapas:

    ![Upload](./media/airwatch-tutorial/ic791924.png "Upload")   

     a. Para **Tipo de Diretório**, selecione **Nenhum**.

    b. Selecione **Usar SAML para Autenticação**.

    c. Para carregar o certificado baixado, clique em **Carregar**.

7. Na seção **Solicitar** , realize as seguintes etapas:

    ![Solicitação](./media/airwatch-tutorial/ic791925.png "Solicitação")  

     a. Para **Tipo de Associação de Solicitação**, selecione **POST**.

    b. No portal do Azure, na página da caixa de diálogo **Configurar logon único no AirWatch**, copie o valor da **URL de Logon** e, em seguida, cole-o na caixa de texto **URL de Logon Único do Provedor de Identidade**.

    c. Para **Formato de NameID**, selecione **Endereço de Email**.

    d. Clique em **Salvar**.

8. Clique na guia **Usuário** novamente.

    ![Usuário](./media/airwatch-tutorial/ic791926.png "Usuário")

9. Na seção **Atributo** , realize as seguintes etapas:

    ![Atributo](./media/airwatch-tutorial/ic791927.png "Atributo")

     a. Na caixa de texto **Identificador de Objeto**, digite `http://schemas.microsoft.com/identity/claims/objectidentifier`.

    b. Na caixa de texto **Nome de usuário**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    c. Na caixa de texto **Nome de Exibição**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    d. Na caixa de texto **Nome**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    e. Na caixa de texto **Sobrenome**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    f. Na caixa de texto **Email**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    g. Clique em **Salvar**.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao AirWatch.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **AirWatch**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **AirWatch**.

    ![O link do AirWatch na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-airwatch-test-user"></a>Criar um usuário de teste do AirWatch

Para permitir que os usuários do Azure AD façam logon no AirWatch, eles devem ser provisionados no AirWatch. No caso do AirWatch, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento de usuários, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do **AirWatch** como administrador.

2. No painel de navegação à esquerda, clique em **Contas** e em **Usuários**.
  
   ![Usuários](./media/airwatch-tutorial/ic791929.png "Usuários")

3. No menu **Usuários**, clique em **Exibição de Lista** e em **Adicionar \> Adicionar Usuário**.
  
   ![Adicionar Usuário](./media/airwatch-tutorial/ic791930.png "Adicionar Usuário")

4. No diálogo **Adicionar/Editar Usuário** , realize as seguintes etapas:

   ![Adicionar Usuário](./media/airwatch-tutorial/ic791931.png "Adicionar Usuário")

    a. Digite **Nome**, **Senha**, **Confirmar Senha**, **Nome**, **Sobrenome** e **Endereço de Email** de uma conta válida do Azure Active Directory que você deseja provisionar nas caixas de texto relacionadas.

   b. Clique em **Salvar**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do AirWatch ou as APIs fornecidas pelo AirWatch para provisionar as contas de usuário do AAD.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do AirWatch no Painel de Acesso, você deverá ser conectado automaticamente ao AirWatch, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
