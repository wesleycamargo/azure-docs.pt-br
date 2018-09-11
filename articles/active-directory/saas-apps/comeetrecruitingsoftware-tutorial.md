---
title: 'Tutorial: Integração do Active Directory do Azure ao Comeet Recruiting Software | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Comeet Recruiting Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 75f51dc9-9525-4ec6-80bf-28374f0c8adf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2018
ms.author: jeedes
ms.openlocfilehash: 137ba7a7e82ff3e57d862868859e8049838701a3
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43311101"
---
# <a name="tutorial-azure-active-directory-integration-with-comeet-recruiting-software"></a>Tutorial: Integração do Active Directory do Azure com o software de recrutamento Comeet

Neste tutorial, você aprenderá a integrar o software de recrutamento Comeet ao Azure AD (Azure Active Directory).

A integração do software de recrutamento Comeet ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Comeet Recruiting Software.
- Você pode permitir que seus usuários façam logon automaticamente no Software de recrutamento Comeet (Logon Único) com suas contas do AD do Azure.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Comeet Recruiting Software, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Assinatura habilitada para um Software de recrutamento Comeet o logon único

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Comeet Recruiting Software da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-comeet-recruiting-software-from-the-gallery"></a>Adicionando Comeet Recruiting Software da galeria

Para configurar a integração do Comeet Recruiting Software ao Azure AD, você precisa adicionar o Comeet Recruiting Software da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Comeet Recruiting Software da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Comeet Recruiting Software**, selecione **Comeet Recruiting Software** no painel de resultados e clique no botão **Add** para adicionar o aplicativo.

    ![Comeet Recruiting Software na lista de resultados](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Comeet Recruiting Software, com base em um usuário de teste chamado "Britta Simon".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Comeet Recruiting Software está para um usuário no Azure AD. Em outras palavras, um relacionamento de link entre um usuário do Azure AD e o usuário relacionado no Comeet Recruiting Software precisa ser estabelecido.

Para configurar e testar o AD do Azure logon único com o Software de recrutamento Comeet, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste de Software de recrutamento Comeet](#create-a-comeet-recruiting-software-test-user)** – para ter um equivalente de Britta Simon no Software Comeet recrutamento que esteja vinculado à representação de usuário do AD do Azure.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único no seu aplicativo Comeet Recruiting Software.

**Para configurar o logon único do Azure AD com o Comeet Recruiting Software, execute as seguintes etapas:**

1. No portal do Azure, sobre o **Comeet Software de recrutamento** página de integração do aplicativo, clique em **logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Caixa de diálogo Logon único](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_samlbase.png)

3. Na seção **Domínio e URL de Comeet Recruiting Software**, execute as seguintes etapas se você deseja configurar o aplicativo no modo iniciado por **IDP**:

    ![Domínio Comeet e informações de conexão única de URLs](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_url1.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://app.comeet.co/adfs_auth/acs/<UNIQUEID>/`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://app.comeet.co/adfs_auth/acs/<UNIQUEID>/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador real, URL de resposta. Você vai ter esses valores a partir do portal de recrutamento Software Comeet como mostrado na [página de suporte](https://support.comeet.co/knowledgebase/adfs-single-sign-on/).

4. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Comeet Recruiting Software Domínio e URLs de conexão única](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_url2.png)

    Na caixa de texto **URL de Logon**, digite a URL: `https://app.comeet.co`

5. O aplicativo Comeet Recruiting Software espera as asserções SAML em um formato específico, que exige a inclusão de mapeamentos de atributos personalizados na configuração de atributos do token SAML. A captura de tela a seguir mostra um exemplo disso. O valor padrão de **Identificador de Usuário** é **user.userprincipalname**, mas **Comeet Recruiting Software** espera que isso seja mapeado com o endereço de e-mail do usuário. Para que você possa usar o atributo **user. mail** na lista ou usar o valor do atributo apropriado com base na configuração da sua organização.

    ![Configurar o logon único](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_attribute.png)

6. Clique na caixa de seleção **Exibir e editar todos os outros atributos de usuário** na seção **Atributos de Usuário** para expandir os atributos. Realize as seguintes etapas em cada um dos atributos exibidos:

    | Nome do atributo | Valor do atributo |
    | ---------------| --------------- |
    | comeet_id | user.userprincipalname |

    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar o logon único](./media/comeetrecruitingsoftware-tutorial/tutorial_attribute_04.png)

    ![Configurar o logon único](./media/comeetrecruitingsoftware-tutorial/tutorial_attribute_05.png)

    b. Na caixa de texto **Nome**, digite o **nome do atributo** mostrado para essa linha.

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.

    d. Clique em **OK**.

7. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_certificate.png)

8. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/comeetrecruitingsoftware-tutorial/tutorial_general_400.png)

9. Para configurar o single sign-on no lado **Comeet Recruiting Software**, cole o conteúdo do Metadata XML baixado no Comeet Recruiting Software, conforme mostrado na [Página de Suporte](https://support.comeet.co/knowledgebase/adfs-single-sign-on/).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/comeetrecruitingsoftware-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/comeetrecruitingsoftware-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/comeetrecruitingsoftware-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/comeetrecruitingsoftware-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.

### <a name="create-a-comeet-recruiting-software-test-user"></a>Crie um usuário de teste do Comeet Recruiting Software

Nesta seção, você cria um usuário chamado Britta Simon no Comeet Recruiting Software. Trabalhe com a equipe de suporte do [Comeet Recruiting Software](mailto:support@comeet.co) para adicionar os usuários na plataforma Comeet Recruiting Software. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure, concedendo acesso ao Comeet Recruiting Software.

![Atribuir a função de usuário][200]

**Para atribuir Britta Simon ao Software de recrutamento Comeet, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **Comeet recrutamento Software**.

    ![O link do Software de recrutamento Comeet na lista de aplicativos](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Comeet Recruiting Software no Painel de Acesso, você deve ser conectado automaticamente ao seu aplicativo Comeet Recruiting Software.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_01.png
[2]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_02.png
[3]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_03.png
[4]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_04.png

[100]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_100.png

[200]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_200.png
[201]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_201.png
[202]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_202.png
[203]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_203.png