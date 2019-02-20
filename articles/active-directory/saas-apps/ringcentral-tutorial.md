---
title: 'Tutorial: Integração do Azure Active Directory com o RingCentral | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o RingCentral.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e01b06b2031da20ee9349588bd4a7ee757fbc22c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56173293"
---
# <a name="tutorial-azure-active-directory-integration-with-ringcentral"></a>Tutorial: Integração do Azure Active Directory com o RingCentral

Neste tutorial, você aprenderá a integrar o RingCentral ao Azure AD (Azure Active Directory).

A integração do RingCentral com o Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao RingCentral.
- Você pode permitir que os usuários façam logon automaticamente no RingCentral (logon único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o RingCentral, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura do RingCentral habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o RingCentral da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-ringcentral-from-the-gallery"></a>Adicionando o RingCentral da galeria
Para configurar a integração do RingCentral ao Azure AD, você precisará adicionar o RingCentral da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o RingCentral da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![image](./media/ringcentral-tutorial/selectazuread.png)

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![image](./media/ringcentral-tutorial/a_select_app.png)
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![image](./media/ringcentral-tutorial/a_new_app.png)

4. Na caixa de pesquisa, digite **RingCentral**, selecione **RingCentral** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![image](./media/ringcentral-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o RingCentral usando um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do RingCentral é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no RingCentral.

Para configurar e testar o logon único do Azure AD com o RingCentral, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do RingCentral](#create-a-ringcentral-test-user)** – para que haja um equivalente de Brenda Fernandes no RingCentral vinculado à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único no aplicativo RingCentral.

**Para configurar o logon único do Azure AD com o RingCentral, execute as seguintes etapas:**

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **RingCentral**, selecione **Logon único**.

    ![image](./media/ringcentral-tutorial/b1_b2_select_sso.png)

2. Clique em **Alterar modo de logon único** na parte superior da tela para selecionar o modo **SAML**.

      ![image](./media/ringcentral-tutorial/b1_b2_saml_ssso.png)

3. Na caixa de diálogo **Selecionar um método de conexão única**, selecione o modo **SAML** para ativar o logon único.

    ![image](./media/ringcentral-tutorial/b1_b2_saml_sso.png)

4. Na página **Definir logon único com SAML**, clique no botão **Editar** para abrir o diálogo **Configuração básica de SAML**.

    ![image](./media/ringcentral-tutorial/b1-domains_and_urlsedit.png)

5. Na seção **Configuração básica do SAML**, se você tiver um **arquivo de metadados do provedor de serviços**, execute as seguintes etapas:

     a. Clique em **Carregar arquivo de metadados**.

    ![image](./media/ringcentral-tutorial/b9_saml.png)

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.

    ![image](./media/ringcentral-tutorial/b9(1)_saml.png)

    c. Depois que o arquivo de metadados for carregado com êxito, os valores **Identificador** e **URL de resposta** serão populados automaticamente na caixa de texto da seção **Configuração Básica do SAML**, conforme mostrado abaixo:

    ![image](./media/ringcentral-tutorial/b21-domains_and_urls.png)

    d. Na caixa de texto **URL de Logon**, digite uma URL:
    | |
    |--|
    | `https://service.ringcentral.com` |
    | `https://service.ringcentral.com.au` |
    | `https://service.ringcentral.co.uk` |
    | `https://service.ringcentral.eu` |

    > [!NOTE]
    > O **arquivo de metadados do provedor de serviços** será inserido na página de configuração de SSO do RingCentral, que é explicada posteriormente no tutorial.

6. Se você não tiver o **arquivo de metadados do Provedor de Serviços**, execute as seguintes etapas:

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

    ![image](./media/ringcentral-tutorial/b2-domains_and_urls.png)

7. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o certificado usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![image](./media/ringcentral-tutorial/certificatebase64.png)
    
8. Em uma janela de navegador da Web diferente, faça logon no RingCentral como administrador de segurança.

9. Na parte superior, clique em **Ferramentas**.

    ![image](./media/ringcentral-tutorial/ringcentral1.png)

10. Navegue até **Logon único**.

    ![image](./media/ringcentral-tutorial/ringcentral2.png)

11. Na página **Configuração de Logon Único** em **Configurações de SSO**, da **Etapa 1**, clique em **Editar** e execute as etapas a seguir:

    ![image](./media/ringcentral-tutorial/ringcentral3.png)

12. Na página **Configurar Logon Único**, execute as seguintes etapas:

    ![image](./media/ringcentral-tutorial/ringcentral4.png)

     a. Clique em **Procurar** para fazer upload do arquivo de metadados que você baixou do portal do Azure.

    b. Depois que você carregar os metadados, os valores serão populados automaticamente na seção **Informações Gerais de SSO**.

    c. Na seção **Mapeamento de Atributos**, selecione **Mapear atributo de email para** como `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Clique em **Salvar**.

    e. Na **Etapa 2** clique em **Baixar** para baixar o **arquivo de metadados do provedor de serviço** e faça upload dele na seção **Configuração Básica do SAML** para popular automaticamente os valores de **Identificador** e **URL de resposta** no portal do Azure.

    ![image](./media/ringcentral-tutorial/ringcentral6.png) 

    f. Na mesma página, navegue até a seção **Habilitar SSO** e execute as seguintes etapas:

    ![image](./media/ringcentral-tutorial/ringcentral5.png)

     a. Selecione **Habilitar Serviço de SSO**.
    
    b. Selecione **Permitir que os usuários entrem com credenciais de SSO ou do RingCentral**.

    c. Clique em **Salvar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![image](./media/ringcentral-tutorial/d_users_and_groups.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![image](./media/ringcentral-tutorial/d_adduser.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![image](./media/ringcentral-tutorial/d_userproperties.png)

     a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.
 
### <a name="create-a-ringcentral-test-user"></a>Criar um usuário de teste do RingCentral

Nesta seção, você criará um usuário chamado Brenda Fernandes no RingCentral. Trabalhe com a  [equipe de suporte ao Cliente do RingCentral](https://success.ringcentral.com/RCContactSupp) para adicionar os usuários à plataforma do RingCentral. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao RingCentral.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![image](./media/ringcentral-tutorial/d_all_applications.png)

2. Na lista de aplicativos, selecione **RingCentral**.

    ![image](./media/ringcentral-tutorial/d_all_proapplications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![image](./media/ringcentral-tutorial/d_leftpaneusers.png)

4. Selecione o botão **Adicionar** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![image](./media/ringcentral-tutorial/d_assign_user.png)

4. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

5. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do RingCentral no painel de Acesso, você será conectado automaticamente ao aplicativo RingCentral.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ringcentral-tutorial/tutorial_general_01.png
[2]: ./media/ringcentral-tutorial/tutorial_general_02.png
[3]: ./media/ringcentral-tutorial/tutorial_general_03.png
[4]: ./media/ringcentral-tutorial/tutorial_general_04.png

[100]: ./media/ringcentral-tutorial/tutorial_general_100.png

[200]: ./media/ringcentral-tutorial/tutorial_general_200.png
[201]: ./media/ringcentral-tutorial/tutorial_general_201.png
[202]: ./media/ringcentral-tutorial/tutorial_general_202.png
[203]: ./media/ringcentral-tutorial/tutorial_general_203.png

