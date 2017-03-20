---
title: "Tutorial: Integração do Azure Active Directory ao Slack | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Slack."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 9c027decf5d982519bc9f41aa9588fa431ef3975
ms.openlocfilehash: 17ad8938cbee539e74f87210077a12c72e777edc
ms.lasthandoff: 02/24/2017


---
# <a name="tutorial-azure-ad-integration-with-slack"></a>Tutorial: integração do Azure AD com o Slack

Neste tutorial, você aprenderá a integrar o Slack ao Azure AD (Azure Active Directory).

Ao integrar o Slack no Azure AD, você pode:

* controlar no Azure AD quem tem acesso ao Slack.
* permitir que os usuários entrem automaticamente no Slack com logon único (SSO), com suas contas do Azure AD.
* gerenciar suas contas em um local central, o portal do Azure.

Para aprender sobre a integração de aplicativos de SaaS (software como serviço) no Azure AD, consulte [O que é acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Slack, você precisará dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura habilitada pelo SSO do Slack

> [!NOTE]
> Não é recomendável testar as etapas neste tutorial usando um ambiente de produção.

Para testar as etapas neste tutorial, siga estas recomendações:

* Use seu ambiente de produção apenas se for necessário.
* Se você não tiver um ambiente de avaliação do Azure AD, poderá [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testa o SSO do Azure AD em um ambiente de teste. O cenário a ser seguido consiste em dois blocos de construção principais:

* Adicionar o Slack da galeria
* Configurar e testar o SSO do Azure AD

## <a name="add-slack-from-the-gallery"></a>Adicionar o Slack na galeria
Para configurar a integração do Slack no Azure AD, adicione o Slack na galeria à sua lista de aplicativos SaaS gerenciados fazendo o seguinte:

1. Abra o [Portal do Azure](https://portal.azure.com).
2. No painel esquerdo, clique no botão **Azure Active Directory**.

    ![Botão "Azure Active Directory"][1]

3. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![Botão "Todos os aplicativos" na folha "Aplicativos da empresa"][2]

4. Na parte superior da caixa de diálogo **Todos os aplicativos**, clique em **Adicionar**.

    ![Botão "Adicionar" na caixa de diálogo "Todos os aplicativos"][3]

5. Na caixa de pesquisa, digite **Slack**.

    ![Caixa de pesquisa "Adicionar um aplicativo"](./media/active-directory-saas-slack-tutorial/tutorial_slack_000.png)

6. No painel de resultados, selecione **Slack** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Selecionar "Slack" no painel de resultados](./media/active-directory-saas-slack-tutorial/tutorial_slack_0001.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar o SSO do Azure AD
Nesta seção, você irá configurar e testar o SSO do Azure AD com o Slack usando o usuário de teste “Brenda Fernandes”.

Para SSO funcionar, o Azure AD deve estabelecer uma relação de vínculo entre o usuário do Azure AD e o usuário correspondente no Slack. Você estabelece essa relação de vínculo atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** no Slack.

Para configurar e testar o SSO do Azure AD com o Slack, conclua os seguintes blocos de construção:

1. [Configure o SSO do Azure AD](#configuring-azure-ad-single-sign-on) para permitir que o usuário use esse recurso.
2. [Crie um usuário de teste do Azure AD](#creating-an-azure-ad-test-user) para testar o SSO do Azure AD com o usuário Brenda Fernandes.
3. [Crie um usuário de teste do Slack](#creating-a-slack-test-user) para fornecer ao usuário Brenda Fernandes do Azure AD um equivalente do Slack para vincular.
4. [Atribua o usuário de teste do Azure AD](#assigning-the-azure-ad-test-user) para permitir que o usuário Brenda Fernandes use SSO do Azure AD.
5. [Teste o SSO](#testing-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta seção, você habilita o SSO do Azure AD no portal do Azure e configura o SSO em seu aplicativo Slack fazendo seguinte:

1. No portal do Azure, na página de integração de aplicativos do **Slack**, clique em **Logon único**.

    ![Página integração de aplicativos do Slack][4]

2. Na caixa de diálogo **Logon único**, na lista **Modo**, selecione **Logon baseado em SAML** para habilitar o SSO.

    ![Caixa de diálogo "Logon único"](./media/active-directory-saas-slack-tutorial/tutorial_slack_01.png)

3. Em **Domínio e URLS do Slack**, execute as seguintes etapas:

    ![Caixa de diálogo "Logon único"](./media/active-directory-saas-slack-tutorial/tutorial_slack_02.png)

    a. Na caixa **URL de logon**, digite uma URL que usa a convenção de nomenclatura *https://<company name>.slack.com*.

    b. Na caixa **Identificador**, digite **https://slack.com**.

    > [!NOTE]
    > Os valores anteriores não são valores reais. Aqui, é recomendável usar valores exclusivos para a URL e o identificador. Posteriormente, você irá atualizar os valores com a URL e o identificador reais. Para obter os valores, entre em contato com a [equipe de suporte do Slack](https://slack.com/help/contact).

4. O aplicativo Slack espera que as asserções SAML (Security Assertion Markup Language) sejam exibidas em um formato específico. Configure as declarações e gerencie os valores dos atributos na seção **Atributos do Usuário** da página de integração de aplicativos do Slack como mostrado na seguinte captura de tela:

    ![Configurar declarações na seção Atributos do Usuário](./media/active-directory-saas-slack-tutorial/tutorial_slack_03.png)

5. Na caixa de diálogo **Logon único**, na seção **Atributos do Usuário**, selecione **user.mail** como o **Identificador de Usuário**. Para cada linha na tabela, execute as seguintes etapas:

    | Nome do atributo | Valor do atributo |
    | --- | --- |    
    | User.Email | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | User.Username | extractmailprefix([userprincipalname]) |

    a. Clique no botão **Adicionar atributo**.

    ![Configurar Logon Único](./media/active-directory-saas-slack-tutorial/tutorial_slack_04.png)

    b. Na caixa de diálogo **Adicionar Atributo**, na caixa **Nome**, digite o primeiro nome na coluna **Nome do atributo** da tabela.

    ![Configurar Logon Único](./media/active-directory-saas-slack-tutorial/tutorial_slack_05.png)

    c. Na caixa **Valor**, digite o primeiro valor na coluna **Valor do atributo** da tabela.

    d. Clique em **OK**.

    e. Repita as etapas de "a" até "d" para as três linhas da tabela seguintes.

6. Em **Certificado de Assinatura de SAML**, clique em **Criar novo certificado**.

    ![Criar um certificado](./media/active-directory-saas-slack-tutorial/tutorial_slack_06.png)     

7. Na caixa de diálogo **Criar Novo Certificado**, clique no botão **Calendário**, selecione uma data de expiração (validade), em seguida, clique em **Salvar**.

    ![Selecionar uma data de validade do certificado](./media/active-directory-saas-slack-tutorial/tutorial_general_300.png)

8. Em **Certificado de Assinatura de SAML**, marque a caixa de seleção **Tornar ativo o novo certificado** e clique em **Salvar**.

    ![Ativar certificado de assinatura de SAML](./media/active-directory-saas-slack-tutorial/tutorial_slack_07.png)

9. Na janela pop-up **Certificado de substituição**, clique em **OK**.

    ![Janela pop-up do Certificado de substituição](./media/active-directory-saas-slack-tutorial/tutorial_general_400.png)

10. Em **Certificado de Assinatura de SAML**, clique em **Certificado (base64)**, em seguida, salve o arquivo do certificado em sua unidade local.

    ![Salvar o certificado em sua unidade local](./media/active-directory-saas-slack-tutorial/tutorial_slack_08.png)

11. Em **Configuração do Slack**, clique em **Configurar o Slack** para abrir a janela **Configurar logon**.

    ![Clique em "Configurar Slack" para abrir a janela Configurar logon](./media/active-directory-saas-slack-tutorial/tutorial_slack_09.png)

    ![Janela "Configurar logon"](./media/active-directory-saas-slack-tutorial/tutorial_slack_10.png)

12. Abra uma nova janela do navegador e conecte o site de sua empresa Slack como administrador.

13. Vá para **Microsoft Azure AD**, em seguida, vá para **Configurações da Equipe**.

    ![Botão "Configurações da Equipe" do Microsoft Azure AD no site da empresa Slack](./media/active-directory-saas-slack-tutorial/tutorial_slack_001.png)

14. Em **Configurações da Equipe**, clique na guia **Autenticação** e em **Alterar Configurações**.

    ![Botão "Alterar Configurações" na página Configurações da Equipe](./media/active-directory-saas-slack-tutorial/tutorial_slack_002.png)

15. Na caixa de diálogo **Configurações de Autenticação de SAML**, realize as seguintes etapas:

    ![Caixa de diálogo "Configurações de Autenticação de SAML"](./media/active-directory-saas-slack-tutorial/tutorial_slack_003.png)

    a. Na caixa **Ponto de Extremidade do SAML 2.0 (HTTP)**, digite o valor da **URL do Serviço de Logon Único da SAML** na janela de configuração de aplicativos do Azure AD.

    b. Na caixa **Emissor do Provedor de Identidade**, digite o valor da **ID de Entidade da SAML** na janela de configuração de aplicativos do Azure AD.

    c. Abra o arquivo de certificados baixado no Bloco de Notas, copie o conteúdo e cole-o na caixa **Certificado Público**.

    d. Configure as três definições anteriores como for apropriado para sua equipe do Slack. Para obter mais informações sobre as configurações, consulte o [Guia para logon único com Slack](https://get.slack.help/hc/en-us/articles/220403548-Guide-to-single-sign-on-with-Slack).

    e. Clique em **Salvar Configuração**.

    <!-- Desmarque **Permitir que os usuários alterem seu endereço de email**.

    e. Selecione **Permitir que os usuários escolham seu próprio nome de usuário**.

    f. Para **A autenticação de sua equipe deve ser usada por**, selecione **É opcional**. -->


### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
Nesta seção, você criará um usuário de teste no portal do Azure denominado Brenda Fernandes fazendo o seguinte:

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![Botão "Azure Active Directory"](./media/active-directory-saas-slack-tutorial/create_aaduser_01.png)

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.

    ![Botão "Todos os usuários" do Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_02.png)

3. Na parte superior da caixa de diálogo **Todos os usuários**, clique em **Adicionar**.

    ![Botão "Adicionar" na caixa de diálogo Adicionar usuários](./media/active-directory-saas-slack-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, insira as seguintes informações:

    ![Caixa de diálogo "Usuário"](./media/active-directory-saas-slack-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha**, em seguida, anote o valor mostrado na caixa **Senha**.

    d. Clique em **Criar**.

### <a name="create-a-slack-test-user"></a>Criar um usuário de teste do Slack

Nesta seção, é criado um usuário denominado Brenda Fernandes no Slack. O Slack dá suporte ao provisionamento just-in-time, que está habilitado por padrão.

Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Slack, um novo será criado quando você tentar acessar o Slack.

> [!NOTE]
> Se você precisar criar um usuário manualmente, entre em contato com a [equipe de suporte do Slack](https://slack.com/help/contact).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que o usuário Brenda Fernandes use o SSO do Azure concedendo-lhe acesso ao Slack.

![Atribuir um usuário de SSO do Azure][200]

Para atribuir o usuário Brenda Fernandes ao Slack, realize as seguintes etapas:

1. No portal do Azure, abra a exibição de aplicativos, vá para a exibição de diretório, para **Aplicativos da empresa**, então, clique em **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista **Aplicativos**, selecione **Slack**.

    ![Lista de aplicativos do portal do Azure](./media/active-directory-saas-slack-tutorial/tutorial_slack_50.png)

3. No painel esquerdo, clique em **Usuários e grupos**.

    ![Botão "Usuários e grupos" do painel esquerdo][202]

4. Clique no botão **Adicionar**, em seguida, na folha **Adicionar Atribuição** e selecione **Usuários e grupos**.

    ![Botão "Adicionar" e folha "Adicionar Atribuição"][203]

5. Na caixa de diálogo **Usuários e grupos**, na lista **Usuários**, selecione **Brenda Fernandes**.

6. Clique no botão **Selecionar**.

7. Na folha **Adicionar Atribuição**, clique no botão **Atribuir**.

### <a name="test-sso"></a>Testar SSO

Nesta seção, você testará sua configuração de SSO do Azure AD usando o Painel de Acesso.

Para testar a configuração, vá para o Painel de Acesso, em seguida, clique no bloco **Slack**. O usuário deve ser automaticamente conectado ao aplicativo Slack.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS ao Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-slack-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-slack-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-slack-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-slack-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-slack-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-slack-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-slack-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-slack-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-slack-tutorial/tutorial_general_203.png

