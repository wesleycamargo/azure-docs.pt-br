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
ms.sourcegitcommit: 14972d3e1212fb0cf6653bd4a693470425294d2c
ms.openlocfilehash: c975231ea18c7c1853d9b20fc49542c10ef9abcc


---
# <a name="tutorial-azure-active-directory-integration-with-slack"></a>Tutorial: Integração do Active Directory do Azure com o Slack

Neste tutorial, você aprenderá a integrar o Slack ao Azure AD (Azure Active Directory).

A integração do Slack ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem terá acesso ao Slack
- Você pode permitir que seus usuários façam logon automaticamente no Slack (logon único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um único local - o portal de Gerenciamento do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Slack, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Slack habilitada para logon único


> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Slack da galeria
2. Configurar e testar o logon único do AD do Azure


## <a name="adding-slack-from-the-gallery"></a>Adicionar o Slack da galeria
Para configurar a integração do Slack ao Azure AD, você precisa adicionar o Slack por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Slack por meio da galeria, execute as seguintes etapas:**

1. No **[Portal de Gerenciamento do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Slack**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-slack-tutorial/tutorial_slack_000.png)

5. No painel de resultados, selecione **Slack** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-slack-tutorial/tutorial_slack_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Slack, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Slack é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Slack.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD como o valor de **Nome de usuário** no Slack.

Para configurar e testar o logon único do Azure AD com o Slack, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar logon único do Azure AD com Britta Simon.
3. **[Criar um usuário de teste do Slack](#creating-a-slack-test-user)** - para ter um equivalente de Brenda Fernandes no Slack que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal de Gerenciamento do Azure e configura o logon único em seu aplicativo Slack.

**Para configurar o logon único do Azure AD com o Slack, realize as seguintes etapas:**

1. No Portal de Gerenciamento do Azure, na página de integração de aplicativos do **Slack**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, como **Modo**, selecione **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-slack-tutorial/tutorial_slack_01.png)

3. Na seção **URLs e Domínio do Slack**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-slack-tutorial/tutorial_slack_02.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<company name>.slack.com`

    b. Na caixa de texto **Identificador**, digite: `https://slack.com`

    > [!NOTE] 
    > Observe que esses não são os valores reais. É necessário atualizar esses valores com a URL de Entrada e o Identificador reais. Aqui, sugerimos que você use o valor exclusivo de URL no Identificador. Entre em contato com a [equipe de suporte do Slack](https://slack.com/help/contact) para obter valores. 

4. O aplicativo Slack espera que as declarações SAML estejam em um formato específico. Configure as seguintes declarações para o aplicativo. Você pode gerenciar os valores desses atributos da seção "**Atributos de Usuário**" na página de integração do aplicativo. A captura de tela a seguir mostra um exemplo disso.
    
    ![Configurar o logon único](./media/active-directory-saas-slack-tutorial/tutorial_slack_03.png)

5. Na seção **Atributos do Usuário**, na caixa de diálogo **Logon único**, selecione **user.mail** como **Identificador de Usuário** e, para cada linha mostrada na tabela a seguir, execute as seguintes etapas:
    
    | Nome do atributo | Valor do atributo |
    | --- | --- |    
    | User.Email | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | User.Username | extractmailprefix([userprincipalname]) |

    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar o logon único](./media/active-directory-saas-slack-tutorial/tutorial_slack_04.png)

    ![Configurar Logon Único](./media/active-directory-saas-slack-tutorial/tutorial_slack_05.png)
    
    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.
    
    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.
    
    d. Clique em **OK**

6. Sobre o **certificado de autenticação SAML** seção, clique em **criar novo certificado**.

    ![Configurar Logon Único](./media/active-directory-saas-slack-tutorial/tutorial_slack_06.png)     

7. Sobre o **criar um novo certificado** caixa de diálogo, clique no ícone de calendário e selecione um **data de expiração**. Em seguida, clique no botão **Salvar**.

    ![Configurar o logon único](./media/active-directory-saas-slack-tutorial/tutorial_general_300.png)

8. Sobre o **certificado de autenticação SAML** seção, selecione **ativar o novo certificado** e clique em **salvar** botão.

    ![Configurar Logon Único](./media/active-directory-saas-slack-tutorial/tutorial_slack_07.png)

9. No pop-up **certificado de substituição** janela, clique em **OK**.

    ![Configurar Logon Único](./media/active-directory-saas-slack-tutorial/tutorial_general_400.png)

10. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar o logon único](./media/active-directory-saas-slack-tutorial/tutorial_slack_08.png) 

11. Na seção **Configuração do Slack**, clique em **Configurar o Slack** para abrir a janela **Configurar logon**.

    ![Configurar Logon Único](./media/active-directory-saas-slack-tutorial/tutorial_slack_09.png) 

    ![Configurar Logon Único](./media/active-directory-saas-slack-tutorial/tutorial_slack_10.png)

12.  Em outra janela do navegador da Web, faça logon em seu site de empresa Slack como um administrador.

13.  Navegue até **Microsoft Azure AD** e depois em **Configurações de Equipe**.

    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-slack-tutorial/tutorial_slack_001.png)

14.  Na seção **Configurações de Equipe**, clique na guia **Autenticação** e em **Alterar Configurações**.

    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-slack-tutorial/tutorial_slack_002.png)

15. No diálogo **Configurações de Autenticação SAML** , realize as seguintes etapas:

    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-slack-tutorial/tutorial_slack_003.png)

    a.  Na caixa de texto **Ponto de extremidade do SAML 2.0 (HTTP)**, insira o valor da **URL de Serviço de Logon Único SAML** do assistente de configuração de aplicativo do Azure AD.

    b.  Na caixa de texto **Emissor do Provedor de Identidade**, insira o valor de **ID de Entidade SAML** na janela de configuração de aplicativo do Azure AD.

    c.  Abra seu arquivo de certificado baixado no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado Público**.

    d. Defina as três configurações acima conforme apropriado para sua equipe do Slack. Para saber mais sobre as configurações, encontre o **guia de configuração de SSO do Slack** aqui. `https://get.slack.help/hc/en-us/articles/220403548-Guide-to-single-sign-on-with-Slack`

    e.  Clique em **Salvar Configuração**.
     
    <!-- Desmarque **Permitir que os usuários alterem seu endereço de email**.

    e.  Selecione **Permitir que os usuários escolham seu próprio nome de usuário**.

    f.  Para **A autenticação de sua equipe deve ser usada por**, selecione **É opcional**. -->
  

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no portal de Gerenciamento do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-slack-tutorial/create_aaduser_01.png) 

2. Vá para **usuários e grupos** e clique em **todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-slack-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo clique **adicionar** para abrir o **usuário** caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-slack-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-slack-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**. 



### <a name="creating-a-slack-test-user"></a>Criar um usuário de teste do Slack

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Slack. O Slack dá suporte ao provisionamento just-in-time, que está habilitado por padrão.

Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o Slack, caso ele ainda não exista.

> [!NOTE]
> Se você precisar criar um usuário manualmente, entre em contato com a [equipe de suporte do Slack](https://slack.com/help/contact).


### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Slack.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Slack, realize as seguintes etapas:**

1. No portal de gerenciamento do Azure, abra a exibição de aplicativos e, em seguida, navegue até o modo de exibição de diretório e vá para **aplicativos empresariais** e clique em **todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **Slack**.

    ![Configurar o logon único](./media/active-directory-saas-slack-tutorial/tutorial_slack_50.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    


### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Slack no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo de Slack.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
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


<!--HONumber=Feb17_HO1-->


