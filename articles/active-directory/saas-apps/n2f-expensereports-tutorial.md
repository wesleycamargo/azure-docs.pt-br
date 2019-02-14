---
title: 'Tutorial: integração do Azure Active Directory ao N2F – Relatórios de despesas | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o N2F - Expense reports.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f56d53d7-5a08-490a-bfb9-78fefc2751ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3166a4e19bc137d57b97dbb516e7037228e1c020
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56188810"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>Tutorial: integração do Azure Active Directory ao N2F – Relatórios de despesas

Neste tutorial, você aprende a integrar o N2F - Expense reports ao Microsoft Azure Active Directory.

A integração do N2F - Expense reports ao Microsoft Azure Active Directory oferece os seguintes benefícios:

- No Microsoft Azure Active Directory, é possível controlar quem tem acesso ao N2F - Expense reports.
- Você pode permitir que seus usuários façam logon automaticamente no N2F - Expense reports (logon único) com suas contas do Microsoft Azure Active Directory.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure Active Directory ao N2F - Expense reports, você precisará dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura habilitada para logon único do N2F - Expense reports

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando N2F - Expense reports a partir da Galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>Adicionando N2F - Expense reports a partir da Galeria

Para configurar a integração do N2F - Expense reports ao Microsoft Azure Active Directory, você precisará adicionar o N2F - Expense reports da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o N2F - Expense reports da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **N2F - Expense reports**, selecione **N2F - Expense reports** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![N2F - Expense reports na lista de resultados](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Microsoft Azure Active Directory com o N2F - Expense reports, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário doN2F - Expense reports é equivalente a um usuário do Microsoft Azure Active Directory. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Microsoft Azure Active Directory e o usuário relacionado em no N2F - Expense reports.

Para configurar e testar o logon único do Microsoft Azure Active Directory com o N2F - Expense reports, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **Criar um usuário de teste do N2F – Relatórios de despesas** para ter um equivalente de Brenda Fernandes no N2F – Relatórios de despesas vinculado à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Microsoft Azure Active Directory no portal do Azure e configura o logon único em seu aplicativo Insignia N2F - Expense reports.

**Para configurar o logon único do Microsoft Azure Active Directory com o N2F - Expense reports, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo do **N2F - Expense reports**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Caixa de diálogo Logon único](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_samlbase.png)

3. Na seção **Domínio e URLs do N2F - Expense reports**, se você desejar configurar o aplicativo no modo iniciado pelo **IDP**, o usuário não precisará executar nenhuma etapa, pois o aplicativo já é pré-integrado ao Azure.

    ![Informações de logon único de Domínio e URLs do N2F - Expense reports](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_url1.png)

4. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único de Domínio e URLs do N2F - Expense reports](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_url2.png)

    Na caixa de texto **URL de Logon**, digite a URL: `https://www.n2f.com/app/`

5. Na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar a **URL de metadados de federação do aplicativo** e cole-a no Bloco de Notas.

    ![O link de download do Certificado](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_certificate.png)

6. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/n2f-expensereports-tutorial/tutorial_general_400.png)

7. Na seção **Configuração do N2F - Expense reports**, clique em **Configurar FN2F - Expense reports** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML** da **seção Referência Rápida.**

    ![Configuração do N2F - Expense reports](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_configure.png)

8. Em outra janela do navegador da Web, entre em seu site empresarial do N2F - Expense como um administrador.

9. Clique em **Configurações** e, em seguida, selecione **Configurações de Adiantamento** na lista suspensa.

    ![Configuração do N2F - Expense reports](./media/n2f-expensereports-tutorial/configure1.png)

10. Selecione a guia **Configurações de conta**.

    ![Configuração do N2F - Expense reports](./media/n2f-expensereports-tutorial/configure2.png)

11. Selecione **Autenticação** e, em seguida, selecione a guia **+ Adicionar um método de autenticação**.

    ![Configuração do N2F - Expense reports](./media/n2f-expensereports-tutorial/configure3.png)

12. Selecione **SAML Microsoft Office 365** como método de autenticação.

    ![Configuração do N2F - Expense reports](./media/n2f-expensereports-tutorial/configure4.png)

13. Na seção **Método de autenticação**, realize as seguintes etapas:

    ![Configuração do N2F - Expense reports](./media/n2f-expensereports-tutorial/configure5.png)

     a. Na caixa de texto **Entity ID**, cole o valor da **ID da Entidade SAML** copiado do portal do Azure.

    b. Na caixa de texto **URL de Metadados**, cole o valor da **URL de metadados de federação do aplicativo** que você copiou do portal do Azure.

    c. Clique em **Salvar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/n2f-expensereports-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/n2f-expensereports-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/n2f-expensereports-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/n2f-expensereports-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.

### <a name="create-a-n2f---expense-reports-test-user"></a>Criar um usuário de teste do N2F - Expense reports

Para permitir que os usuários do Microsoft Azure Active Directory façam logon no N2F - Expense reports, eles devem ser provisionados no N2F - Expense reports. No caso do N2F - Expense reports, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do N2F - Expense reports como administrador.

2. Clique em **Configurações** e, em seguida, selecione **Configurações de Adiantamento** na lista suspensa.

   ![Adicionar usuário ao N2F - Expense](./media/n2f-expensereports-tutorial/configure1.png)

3. Selecione a guia **Usuários** do painel de navegação à esquerda.

    ![Configuração do N2F - Expense reports](./media/n2f-expensereports-tutorial/user1.png)

4. Selecione a guia **+ Novo Usuário**.

   ![Configuração do N2F - Expense reports](./media/n2f-expensereports-tutorial/user2.png)

5. Na seção **Usuário**, execute as seguintes etapas:

    ![Configuração do N2F - Expense reports](./media/n2f-expensereports-tutorial/user3.png)

     a. Na caixa de texto **Endereço de email**, digite o endereço de email do usuário, como **brittasimon@contoso.com**.

    b. Na caixa de texto **Nome**, digite o nome do usuário, como **Brenda**.

    c. Na caixa de texto **Nome**, insira o nome de usuário, como **BrendaFernandes**.

    d. Escolher **Função de gerente direto (N + 1)**, e **Divisão**, de acordo com o requisito da organização.

    e. Clique em **Validar e Enviar convite**.

    > [!NOTE]
    > Se você estiver enfrentando problemas ao adicionar o usuário, entre em contato com a equipe de suporte do [N2F - Expense reports](mailto:support@n2f.com)

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao N2F - Expense.

![Atribuir a função de usuário][200]

**Para atribuir Brenda Fernandes ao N2F - Expense reports, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **N2F - Expense reports**.

    ![Link do N2F - Expense reports na lista de Aplicativos](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco N2F - Expense reports na peça do Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo N2F - Expense reports.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/n2f-expensereports-tutorial/tutorial_general_01.png
[2]: ./media/n2f-expensereports-tutorial/tutorial_general_02.png
[3]: ./media/n2f-expensereports-tutorial/tutorial_general_03.png
[4]: ./media/n2f-expensereports-tutorial/tutorial_general_04.png

[100]: ./media/n2f-expensereports-tutorial/tutorial_general_100.png

[200]: ./media/n2f-expensereports-tutorial/tutorial_general_200.png
[201]: ./media/n2f-expensereports-tutorial/tutorial_general_201.png
[202]: ./media/n2f-expensereports-tutorial/tutorial_general_202.png
[203]: ./media/n2f-expensereports-tutorial/tutorial_general_203.png

