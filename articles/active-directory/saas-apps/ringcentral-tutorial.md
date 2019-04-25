---
title: 'Tutorial: Integração do Azure Active Directory com o RingCentral | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o RingCentral.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.openlocfilehash: 7cd20397c122834a23586bf51af6467f58531094
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997233"
---
# <a name="tutorial-azure-active-directory-integration-with-ringcentral"></a>Tutorial: Integração do Azure Active Directory com o RingCentral

Neste tutorial, você aprenderá a integrar o RingCentral ao Azure AD (Azure Active Directory).
A integração do RingCentral com o Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao RingCentral.
* Você pode permitir que os usuários sejam conectados automaticamente ao RingCentral (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o RingCentral, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura do RingCentral habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O RingCentral é compatível com SSO iniciado por **SP**

## <a name="adding-ringcentral-from-the-gallery"></a>Adicionando o RingCentral da galeria

Para configurar a integração do RingCentral ao Azure AD, você precisará adicionar o RingCentral da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o RingCentral da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **RingCentral**, selecione **RingCentral** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![RingCentral na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o RingCentral usando um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do RingCentral.

Para configurar e testar o logon único do Azure AD com o RingCentral, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do RingCentral](#configure-ringcentral-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do RingCentral](#create-ringcentral-test-user)** – para que haja um equivalente de Brenda Fernandes no RingCentral vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o RingCentral, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **RingCentral**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, se você tiver um **arquivo de metadados do provedor de serviços**, execute as seguintes etapas:

     a. Clique em **Carregar arquivo de metadados**.

    ![Carregar arquivo de metadados](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.

    ![Escolha o arquivo de metadados](common/browse-upload-metadata.png)

    c. Depois que o arquivo de metadados for carregado com êxito, os valores de **Identificador** e **URL de Resposta** serão preenchidos automaticamente na seção **Configuração Básica do SAML**.

    ![Informações de logon único em Domínio e URLs do RingCentral](common/sp-identifier-reply.png)

    Na caixa de texto **URL de Logon**, digite uma URL:
    
    | |
    |--|
    | `https://service.ringcentral.com`|
    | `https://service.ringcentral.com.au`|
    | `https://service.ringcentral.co.uk`|
    | `https://service.ringcentral.eu`|

    > [!Note]
    > O **arquivo de metadados do provedor de serviços** será inserido na página de configuração de SSO do RingCentral, que é explicada posteriormente no tutorial.

5. Se você não tem o **arquivo de metadados do Provedor de Serviços**, execute as seguintes etapas:

     a. Na caixa de texto **URL de Logon**, digite uma URL:

    | |
    |--|
    | `https://service.ringcentral.com` |
    | `https://service.ringcentral.com.au` |
    | `https://service.ringcentral.co.uk` |
    | `https://service.ringcentral.eu` |

    b. Na caixa de texto **Identificador**, digite uma URL:

    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    c. Na caixa de texto **URL de Resposta**, digite uma URL:

    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

    ![Informações de logon único em Domínio e URLs do RingCentral](common/sp-identifier-reply.png)

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar **URL de metadados de federação de aplicativos** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

### <a name="configure-ringcentral-single-sign-on"></a>Configurar logon único do RingCentral

1. Em uma janela de navegador da Web diferente, entre no RingCentral como administrador de segurança.

1. Na parte superior, clique em **Ferramentas**.

    ![image](./media/ringcentral-tutorial/ringcentral1.png)

1. Navegue até **Logon único**.

    ![image](./media/ringcentral-tutorial/ringcentral2.png)

1. Na página **Configuração de Logon Único** em **Configurações de SSO**, da **Etapa 1**, clique em **Editar** e execute as etapas a seguir:

    ![image](./media/ringcentral-tutorial/ringcentral3.png)

1. Na página **Configurar Logon Único**, execute as seguintes etapas:

    ![image](./media/ringcentral-tutorial/ringcentral4.png)

     a. Clique em **Procurar** para fazer upload do arquivo de metadados que você baixou do portal do Azure.

    b. Depois que você carregar os metadados, os valores serão populados automaticamente na seção **Informações Gerais de SSO**.

    c. Na seção **Mapeamento de Atributos**, selecione **Mapear atributo de email para** como `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Clique em **Save** (Salvar).

    e. Na **Etapa 2** clique em **Baixar** para baixar o **arquivo de metadados do provedor de serviço** e carregue-o na seção **Configuração Básica do SAML** para preencher automaticamente os valores de **Identificador** e **URL de resposta** no portal do Azure.

    ![image](./media/ringcentral-tutorial/ringcentral6.png) 

    f. Na mesma página, navegue até a seção **Habilitar SSO** e execute as seguintes etapas:

    ![image](./media/ringcentral-tutorial/ringcentral5.png)

    * Selecione **Habilitar Serviço de SSO**.

    * Selecione **Permitir que os usuários entrem com credenciais de SSO ou do RingCentral**.

    * Clique em **Save** (Salvar).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite `brittasimon@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao RingCentral.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **RingCentral**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **RingCentral**.

    ![O link do RingCentral na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-ringcentral-test-user"></a>Criar usuário de teste do RingCentral

Nesta seção, você criará um usuário chamado Brenda Fernandes no RingCentral. Trabalhe com a  [equipe de suporte ao Cliente do RingCentral](https://success.ringcentral.com/RCContactSupp)  para adicionar os usuários à plataforma do RingCentral. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do RingCentral no Painel de Acesso, você deverá ser conectado automaticamente ao RingCentral, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
