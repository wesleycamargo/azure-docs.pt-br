---
title: 'Tutorial: Integração do Azure Active Directory com Drift | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory Domain Services e o Drift.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 39dcbb95-c192-448c-86a1-cedede1c0972
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2018
ms.author: jeedes
ms.openlocfilehash: f7973ccb384a8e882a9ced5020a53824bf0c4e7d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55169868"
---
# <a name="tutorial-azure-active-directory-integration-with-drift"></a>Tutorial: Integração do Azure Active Directory com Drift

Neste tutorial, você aprenderá como integrar o Drift ao Microsoft Azure Active Directory Domain Services (Azure AD).

A integração do Drift ao Microsoft Azure Active Directory oferece os seguintes benefícios:

- Você pode controlar no Microsoft Azure AD quem tem acesso ao Drift.
- Você pode permitir que seus usuários entrem automaticamente no Drift (Single Sign-On) com suas contas do Microsoft Azure Active Directory.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure Active Directory ao Drift, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para conexão única do Drift

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Deriva da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-drift-from-the-gallery"></a>Adicionando Deriva da galeria

Para configurar a integração do Drift ao Microsoft Azure Active Directory, você precisa adicionar o Drift da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar Drift da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Drift**, selecione **Drift** no painel de resultados e clique no botão **Add** para adicionar o aplicativo.

    ![Descompasso na lista de resultados](./media/drift-tutorial/tutorial_drift_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Microsoft Azure Active Directory com o Drift com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Microsoft Azure Active Directory precisa saber o que o usuário de contrapartida no Drift é para um usuário no Microsoft Azure Active Directory. Em outras palavras, um relacionamento de link entre um usuário do Microsoft Azure Active Directory e o usuário relacionado no Drift precisa ser estabelecido.

Para configurar e testar o logon único do Microsoft Azure Active Directory com o Drift, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do Drift](#creating-a-drift-test-user)** - para ter um equivalente de Brenda Fernandes no Drift vinculado à representação do usuário do Microsoft Azure Active Directory.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Microsoft Azure Active Directory no portal do Azure e configurará o logon único no seu aplicativo Drift.

**Para configurar o logon único do Microsoft Azure Active Directory com o Drift, execute as seguintes etapas:**

1. No portal do Microsoft Azure, na página de integração do aplicativo **Drift**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![Configurar o logon único](common/tutorial_general_301.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Configurar o logon único](common/editconfigure.png)

4. Na seção **Configuração Básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    ![Domínio de desvio e informações de logon único de URLs](./media/drift-tutorial/tutorial_drift_url.png)

     a. Clique em **Definir URLs adicionais**.

    b. Na caixa de texto **Estado de Retransmissão**, digite uma URL: `https://app.drift.com`

    c. Se você deseja configurar o aplicativo no modo **SP** iniciado, na caixa de texto **URL de login**, digite um URL: `https://start.drift.com`

5. O aplicativo Drift espera as asserções SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos na seção **Atributos e reivindicações do usuário** na página de integração de aplicativos. Clique no botão **Editar** para abrir a caixa de diálogo **Atributos do usuário e reivindicações**.

    ![image](./media/drift-tutorial/tutorial_drift_attribute.png)

6. Na seção **Reivindicações do usuário** da caixa de diálogo **Atributos e reivindicações do usuário**, configure o atributo do token SAML conforme mostrado na imagem acima e execute as seguintes etapas:
    
    | Nome do atributo | Valor do atributo |
    | ---------------| --------------- |    
    | NOME | user.displayname |

     a. Clique na reivindicação `name` (reivindicação destacada) para abrir a caixa de diálogo **Gerenciar declarações do usuário**.

    ![image](./media/drift-tutorial/tutorial_drift_attribute1.png)

    ![image](./media/drift-tutorial/tutorial_drift_attribute3.png)

    b. Escolha Origem como **Atributo**.

    c. Verifique os **Namespace** em branco.

    d. Na lista **Atributo de origem**, selecione **user.displayname**.

    e. Clique em **Salvar**.

7. Na página **Certificado de Assinatura SAML**, na seção **Certificado de Assinatura SAML**, clique em **Download** para fazer o download do **XML de Metadados da Federação** e salve o arquivo de metadados no computador.

    ![O link de download do Certificado](./media/drift-tutorial/tutorial_drift_certificate.png) 

8. Em uma janela diferente do navegador da Web, faça login no Drift como administrador.

9. Do lado esquerdo da barra de menu, clique no **ícone Configurações** > **Configurações do aplicativo** > **Autenticação** e execute as seguintes etapas:

    ![O link do administrador](./media/drift-tutorial/tutorial_drift_admin.png)

     a. Faça o upload do **XML de metadados de federação** baixado do portal do Azure na caixa de texto **Carregar arquivo de metadados do Provedor de identidade**.

    b. Depois de carregar o arquivo de metadados, os valores restantes obtém automaticamente populada automaticamente na página.

    c. Clique em **Habilitar SAML**.

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

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

### <a name="creating-a-drift-test-user"></a>Criando um usuário de teste de descompasso

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Drift. O Drift suporta o provisionamento just-in-time, que é ativado por padrão. Não há itens de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar o Drift, se ele ainda não existir.
>[!Note]
>Se você precisar criar um usuário manualmente, entre em contato com a  [Equipe de suporte do Drift](mailto:integrations@drift.com).

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure, concedendo acesso ao Drift.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **descompasso**.

    ![Configurar o logon único](./media/drift-tutorial/tutorial_drift_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Na caixa de diálogo **Add Assignment**, selecione o botão **Atribuir**.

### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar na peça Drift no Painel de Acesso, você deve fazer logon automaticamente no seu aplicativo Drift.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

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
