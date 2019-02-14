---
title: 'Tutorial: Integração do Azure Active Directory com o ProMaster (por Inlogik) | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e ProMaster (por Inlogik).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 348dbd37-dc4f-49df-bb90-53d249d456b3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 218f0c8a49d99c611219a43cc4b51214e4087012
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56205232"
---
# <a name="tutorial-azure-active-directory-integration-with-promaster-by-inlogik"></a>Tutorial: Integração do Azure Active Directory com o ProMaster (por Inlogik)

Neste tutorial, você aprenderá a integrar o ProMaster (por Inlogik) com o Microsoft Azure Active Directory.

A integração do ProMaster (por Inlogik) ao Microsoft Azure Active Directory oferece os seguintes benefícios:

- No Microsoft Azure Active Directory, é possível controlar quem tem acesso ao ProMaster (por Inlogik).
- Você pode permitir que os usuários façam logon automaticamente no ProMaster (por Inlogik) com suas contas do Microsoft Azure Active Directory.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure Active Directory com o ProMaster (por Inlogik), você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura do ProMaster (por Inlogik) habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.
O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando ProMaster (por Inlogik) da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-promaster-by-inlogik-from-the-gallery"></a>Adicionando ProMaster (por Inlogik) da galeria

Para configurar a integração do ProMaster (por Inlogik) no Microsoft Azure Active Directory, você precisa adicionar o ProMaster (por Inlogik) da galeria para a sua lista dos aplicativos SaaS geenciados.

**Para adicionar ProMaster (por Inlogik) da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **ProMaster (por Inlogik)**, selecione **ProMaster (por Inlogik)** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![ProMaster (por Inlogik) na lista de resultados](./media/promaster-tutorial/tutorial_promaster_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Microsoft Azure Active Directory com o ProMaster (por Inlogik) com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Microsoft Azure Active Directory precisa saber qual usuário equivalente no ProMaster (por Inlogik) é para um usuário no Microsoft Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Microsoft Azure Active Directory e o usuário relacionado no ProMaster (por Inlogik) precisa ser estabelecido.

Para configurar e testar o logon único do Microsoft Azure Active Directory com ProMaster (por Inlogik), é necessário concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste ProMaster (por Inlogik)](#create-a-promaster-by-inlogik-test-user)** – para ter um equivalente de Brenda Fernandes no ProMaster (por Inlogik) que é vinculado para a representação do Microsoft Azure Active Directory do usuário.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Microsoft Azure Active Directory no portal do Azure e configurará o logon único no aplicativo ProMaster (por Inlogik).

**Para configurar o logon único do Microsoft Azure Active Directory com o ProMaster (por Inlogik), execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **ProMaster (por Inlogik)**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Caixa de diálogo Logon único](./media/promaster-tutorial/tutorial_promaster_samlbase.png)

3. Na seção **Domínio ProMaster (por Inlogik) e URLs**, execute s etapas a seguir se você desejar configurar o aplicativo no modo iniciado **IDP**:

    ![Domínio ProMaster (por Inlogik) e informações de URLs de logon único](./media/promaster-tutorial/tutorial_promaster_url1.png)

    a. Na caixa de texto **Identificador**, use um dos seguintes padrões de URL:

    | |
    | - |-|
    |  `https://secure.inlogik.com/<COMPANYNAME>`|
    | `https://<CUSTOMDOMAIN>/SAMLBASE`|
    | |

    b. Na caixa de texto **URL de Resposta**, use uma das seguintes padrões de URL:

    | |
    | - |-|
    | `https://secure.inlogik.com/<COMPANYNAME>/saml/acs`|
    | `https://<CUSTOMDOMAIN>/SAMLBASE/saml/acs`|
    | |

4. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Domínio ProMaster (por Inlogik) e informações de URLs de logon único](./media/promaster-tutorial/tutorial_promaster_url2.png)

    Na caixa de texto **URL de logon**, use um dos seguintes padrões de URL:

    | |
    | - |-|
    | `https://secure.inlogik.com/<COMPANYNAME>/saml/acs `|
    | `https://<CUSTOMDOMAIN>/SAMLBASE/saml/acs`|
    | |

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. Entre em contato com a equipe de suporte ao cliente [ProMaster (por Inlogik)](mailto:michael.boldiston@inlogik.com) para obter esses valores.

5. Na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar a **URL de metadados de federação do aplicativo** e cole-a no Bloco de Notas.

    ![O link de download do Certificado](./media/promaster-tutorial/tutorial_promaster_certificate.png)

6. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/promaster-tutorial/tutorial_general_400.png)

7. Para configurar o logon único no lado **ProMaster (por Inlogik)**, é necessário enviar a **URL de metadados de federação do aplicativo** para a [equipe de suporte do ProMaster (por Inlogik)](mailto:michael.boldiston@inlogik.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/promaster-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/promaster-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/promaster-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/promaster-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.

### <a name="create-a-promaster-by-inlogik-test-user"></a>Criar um usuário de teste ProMaster (por Inlogik)

Nesta seção, você criará uma usuária chamada Brenda Fernandes no ProMaster (por Inlogik). Trabalhe com a  [equipe de suporte do ProMaster (por Inlogik)](mailto:michael.boldiston@inlogik.com) para adicionar os usuários na plataforma ProMaster (por Inlogik). Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilitará Brenda Fernandes para usar o logon único do Azure, concedendo acesso ao ProMaster (por Inlogik).

![Atribuir a função de usuário][200]

**Para atribuir Brenda Fernandes ao ProMaster, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **ProMaster (por Inlogik)**.

    ![Link ProMaster (por Inlogik) na lista de aplicativos](./media/promaster-tutorial/tutorial_promaster_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar na peça ProMaster (por Inlogik) no Painel de Acesso, você deve ser conectado automaticamente ao aplicativo ProMaster (por Inlogik).
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/promaster-tutorial/tutorial_general_01.png
[2]: ./media/promaster-tutorial/tutorial_general_02.png
[3]: ./media/promaster-tutorial/tutorial_general_03.png
[4]: ./media/promaster-tutorial/tutorial_general_04.png

[100]: ./media/promaster-tutorial/tutorial_general_100.png

[200]: ./media/promaster-tutorial/tutorial_general_200.png
[201]: ./media/promaster-tutorial/tutorial_general_201.png
[202]: ./media/promaster-tutorial/tutorial_general_202.png
[203]: ./media/promaster-tutorial/tutorial_general_203.png

