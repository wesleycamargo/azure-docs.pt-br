---
title: 'Tutorial: integração do Azure Active Directory ao FreshDesk | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o FreshDesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2018
ms.author: jeedes
ms.openlocfilehash: b5968b83fc9beb481e2ad2c0cd44d2c284747fa1
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51010806"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Tutorial: integração do Azure Active Directory ao FreshDesk

Neste tutorial, você aprenderá a integrar o FreshDesk ao Azure AD (Azure Active Directory).

A integração do FreshDesk ao Azure AD oferece os seguintes benefícios:

- No Microsoft Azure AD, é possível controlar quem tem acesso ao FreshDesk.
- Você pode habilitar seus usuários, com as respectivas contas do Microsoft Azure AD, a fazer logon automaticamente no FreshDesk (logon único).
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao FreshDesk, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do FreshDesk com logon único habilitado

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o FreshDesk da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-freshdesk-from-the-gallery"></a>Adicionar o FreshDesk da galeria

Para configurar a integração do FreshDesk ao Azure AD, você precisa adicionar o FreshDesk da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o FreshDesk por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **FreshDesk**, selecione **FreshDesk** no painel de resultados e clique no botão **Add** para adicionar o aplicativo.

    ![FreshDesk na lista de resultados](./media/freshdesk-tutorial/tutorial_freshdesk_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o FreshDesk, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do FreshDesk é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do FreshDesk.

Para configurar e testar o logon único do Azure AD com o FreshDesk, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do FreshDesk](#creating-a-freshdesk-test-user)**  – para ter um equivalente de Britta Simon no FreshDesk que esteja vinculado à representação de usuário do Microsoft Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único em seu aplicativo FreshDesk.

**Para configurar o logon único do Azure AD com o FreshDesk, realize as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **FreshDesk**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![Configurar o logon único](common/tutorial_general_301.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Configurar o logon único](common/editconfigure.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Domínio FreshDesk e informações de logon único de URLs](./media/freshdesk-tutorial/tutorial_freshdesk_url.png)

    a. Na caixa de texto **URL de login**, digite um URL usando o seguinte padrão: `https://<tenant-name>.freshdesk.com` ou qualquer outro valor que o Freshdesk sugeriu.

    b. Na caixa de texto **Identifier (Entity ID)**, digite uma URL usando o seguinte padrão: `https://<tenant-name>.freshdesk.com` ou qualquer outro valor que o Freshdesk sugeriu.

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Entre em contato com [equipe de suporte do cliente FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) para obter esses valores.

5. O aplicativo FreshDesk espera as asserções de SAML em um formato específico, que exige a inclusão de mapeamentos de atributos personalizados na configuração de atributos do token SAML. A captura de tela a seguir mostra um exemplo disso. O valor padrão do **Identificador de Usuário** é **user.userprincipalname**, mas o **FreshDesk** espera que isso seja mapeado com o endereço de email do usuário. Para que você possa usar o atributo **user. mail** na lista ou usar o valor do atributo apropriado com base na configuração da sua organização.

    ![image](./media/freshdesk-tutorial/i4-attribute.png)

6. Na seção **Reivindicações do usuário** da caixa de diálogo **Atributos e reivindicações do usuário**, configure o atributo do token SAML conforme mostrado na imagem acima e execute as seguintes etapas:
    
    a. Clique no ícone **Editar** para abrir a caixa de diálogo **Gerenciar declarações de usuário**.

    ![image](./media/freshdesk-tutorial/i2-attribute.png)

    ![image](./media/freshdesk-tutorial/i3-attribute.png)

    b. Na lista **Atributo de origem**, selecione **user.mail**.

    c. Clique em **Salvar**.

7. Na página **Certificado de assinatura SAML**, na seção **Certificado de assinatura SAML**, clique em **Download** para fazer o download do **Certificado (Base64)** e salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/freshdesk-tutorial/tutorial_freshdesk_certificate.png)

8. Abra o **Prompt de Comando** e execute os seguintes comandos:

    a. Insira o valor `certutil.exe -dump FreshDesk.cer` no prompt de comando.

    > [!NOTE]
    > Aqui, **FreshDesk.cer** é o certificado que você baixou do portal do Azure.

    b. Cópia de **Cert Hash(sha256)** valor e cole-o bloco de notas. 

9. Sobre o **configurar o FreshDesk** seção, copie a URL apropriada, de acordo com suas necessidades.

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

    ![Configuração do FreshDesk](common/configuresection.png)

10. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do Freshdesk como administrador.

11. Selecione o **Ícone de Configurações** e, na seção **Segurança**, realize as seguintes etapas:

    ![Logon Único](./media/freshdesk-tutorial/IC776770.png "Logon Único")
  
    a. Para **SSO (Logon Único)**, selecione **Ativado**.

    b. Selecione **SSO do SAML**.

    c. Na caixa de texto **URL de Logon SAML**, cole o valor do **URL de Login**, que você copiou do portal do Azure.

    d. Na caixa de texto **URL de logoff**, cole o **valor do URL de logout**, copiado do portal do Azure.

    e. Na caixa de texto **Impressão digital de certificado de segurança**, cole o valor **Cert Hash (sha256)** que você obteve anteriormente.
  
    f. Clique em **Salvar**.

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

### <a name="creating-a-freshdesk-test-user"></a>Criar um usuário de teste FreshDesk

Para permitir que os usuários do Azure AD façam logon no FreshDesk, eles devem ser provisionados no FreshDesk.  
No caso do FreshDesk, o provisionamento é uma tarefa manual.

**Para provisionar contas de usuário, execute as seguintes etapas:**

1. Faça logon em seu locatário do **Freshdesk** .

2. No menu na parte superior, clique em **Administrador**.

    ![Admin](./media/freshdesk-tutorial/IC776772.png "Admin")

3. Na guia **Configurações Gerais**, clique em **Agentes**.
  
    ![Agentes](./media/freshdesk-tutorial/IC776773.png "Agentes")

4. Clique em **Novo Agente**.

    ![Novo Agente](./media/freshdesk-tutorial/IC776774.png "Novo Agente")

5. Na caixa de diálogo Informações sobre o Agente, execute as seguintes etapas:

    ![Informações de Agente](./media/freshdesk-tutorial/IC776775.png "Informações de Agente")

    a. Na caixa de texto **Email** , digite o endereço de email do Azure AD da conta do Azure AD que você deseja provisionar.

    b. Na caixa de texto **Nome Completo** , digite o nome da conta do Azure AD que você deseja provisionar.

    c. Na caixa de texto **Título** , digite a conta do Azure AD que você deseja provisionar.

    d. Clique em **Salvar**.

    >[!NOTE]
    >O titular da conta do Azure AD receberá um email que inclui um link para confirmar a conta antes que ela se torne ativa.
    >
    >[!NOTE]
    >É possível usar qualquer outra ferramenta de criação da conta de usuário do Freshdesk ou APIs fornecidas pelo Freshdesk para provisionar as contas de usuário do AAD para o FreshDesk.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você deve habilitar Britta Simon usar logon único do Azure, concedendo acesso ao FreshDesk.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, escolha **FreshDesk**.

    ![Configurar o logon único](./media/freshdesk-tutorial/tutorial_freshdesk_app.png)

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica na peça FreshDesk no painel de acesso, você deve ser conectado automaticamente ao seu aplicativo FreshDesk.
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
