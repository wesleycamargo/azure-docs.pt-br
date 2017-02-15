---
title: "Tutorial: integração do Azure Active Directory com o Cezanne HR Software | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Cezanne HR Software."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 4743435b-ee73-46b2-86e6-a612a51dfce4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a62536deafe04ac885a308e1a42ad64cd867a9ca


---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Tutorial: Integração do Azure Active Directory ao Cezanne HR Software
O objetivo desse tutorial é mostrar como integrar o Cezanne HR Software ao Azure AD (Azure Active Directory).

A integração do Cezanne HR Software ao Azure AD oferece os seguintes benefícios:

* Você pode controlar, no Azure AD, quem tem acesso ao Cezanne HR Software
* Você pode habilitar seus usuários a fazerem logon automaticamente no Cezanne HR Software (logon único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD com o Cezanne HR Software, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura do Cezanne HR Software habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Cezanne HR Software da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Adição do Cezanne HR Software da galeria
Para configurar a integração do Cezanne HR Software com o Azure AD, você precisa adicionar o Cezanne HR Software, por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Cezanne HR Software por meio da galeria, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Cezanne HR Software**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_01.png)
7. No painel de resultados, escolha **Cezanne HR Software** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Seleção do aplicativo na galeria](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_0001.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar logon único do Azure AD com o Cezanne HR Software, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Cezanne HR Software é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Cezanne HR Software.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** ao Azure AD como sendo o valor de **Nome de usuário** no Cezanne HR Software.

Para configurar e testar o logon único do Azure AD com o Cezanne HR Software, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do Cezanne HR Software](#creating-a-cezanne-hr-software-test-user)** - para ter um equivalente de Brenda Fernandes no Cezanne HR Software que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD
Nesta seção, você habilitará o logon único do Azure AD no portal clássico e configurará o logon único em seu aplicativo Cezanne HR Software.

**Para configurar o logon único do Azure AD com o Cezanne HR Software, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração do aplicativo **Cezanne HR Software**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
    ![Configurar o logon único][6] 
2. Na página **Como você deseja que os usuários façam logon no Cezanne HR Software**, selecione **Logon único do Azure AD** e clique em **Avançar**.
   
    ![Configurar o logon único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_03.png)
3. Na página de diálogo **Definir Configurações do Aplicativo**, execute as seguintes etapas e clique em **Avançar**:
   
    ![Configurar o logon único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_04.png)
   
    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://w3.cezanneondemand.com/cezannehr/-/<tenant id>`.
   
    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://w3.cezanneondemand.com:443/CezanneOnDemand/-/<tenant id>/Saml/samlp`.
   
    c. Clique em **Próximo**
   
   > [!NOTE]
   > Observe que você precisa atualizar esses valores com a URL de Logon e a URL de Resposta reais. Para obter esses valores, entre em contato com a equipe de suporte de Software de RH Cezanne via <mailto:info@cezannehr.com>.
   > 
   > 
4. Na página **Configurar logon único no Cezanne HR Software**, execute as seguintes etapas e clique em **Avançar**:
   
    ![Configurar Logon Único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_05.png)
   
    a. Clique em **Baixar certificado**e salve o arquivo em seu computador.
   
    b. Clique em **Avançar**.
5. Em uma janela diferente do navegador da Web, faça logon em seu locatário do Cezanne HR Software como um administrador.
6. No painel de navegação esquerdo, clique em **Configuração do Sistema**. Vá para **Configurações de Segurança**. Em seguida, navegue até **Configuração de Logon Único**.
   
    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)
7. No painel **Permitir que os usuários façam logon usando o SSO (Serviço de Logon Único)** a seguir, marque a caixa **SAML 2.0** e selecione a opção **Configuração Avançada** ao lado dela.
   
    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)
8. Clique no botão **Adicionar Novo** .
   
    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)
9. Execute as etapas a seguir na seção **PROVEDORES DE IDENTIDADE DO SAML 2.0** .
   
    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)
   
    a. Insira o nome do seu Provedor de Identidade como o **Nome de Exibição**.
   
    b. Na caixa de texto **Identificador de Entidade**, coloque o valor de **ID da Entidade** do assistente de configuração de aplicativo do Azure AD.
   
    c. Altere a **Associação SAML** para 'POST'.
   
    d. Na caixa de texto **Ponto de Extremidade de Serviço de Token de Segurança**, insira o valor da **URL de Serviço de Logon Único** do assistente de configuração de aplicativo do Azure AD.
   
    e. Insira 'http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name' no **Nome do Atributo da ID do Usuário**.
   
    f. Clique no ícone **Carregar** para carregar o certificado baixado do Azure AD.
   
    g. Clique no botão **Ok** . 
10. Clique no botão **Salvar** .
    
    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)
11. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
    
    ![Logon Único do AD do Azure][10]
12. Na página **Confirmação de logon único**, clique em **Concluir**.  
    
    ![Logon Único do AD do Azure][11]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_09.png)
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png)
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png)
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_05.png)
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_06.png)
   
   a. Na caixa de texto **Nome**, digite **Brenda**.  
   
   b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
   c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   
   d. Na lista **Função**, selecione **Usuário**.
   
   e. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_07.png)
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_08.png)
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.   

### <a name="creating-a-cezanne-hr-software-test-user"></a>Criação de um usuário de teste do Cezanne HR Software
Para habilitar usuários do AD do Azure a fazer logon no Cezanne HR Software, eles devem ser provisionados no Cezanne HR Software. No caso do Cezanne HR Software, o provisionamento é uma tarefa manual.

#### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para provisionar uma conta de usuário, execute as seguintes etapas:
1. Faça logon no site da empresa Cezanne HR Software como um administrador.
2. No painel de navegação esquerdo, clique em **Configuração do Sistema**. Vá para **Gerenciar Usuários**. Em seguida, navegue até **Add New User**.
   
   ![Novo Usuário](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "New User")
3. Na seção **Person Details** , execute etapas abaixo:
   
   ![Novo Usuário](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "New User")
   
   a. Defina **Internal User** como OFF.
   
   b. Na caixa de texto **Nome**, digite **Brenda**.  
   
   c. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
   d. Na caixa de texto **Email** , digite o endereço de email da conta de Brenda Fernandes.
4. Na seção **Informações da Conta** , execute as etapas abaixo:
   
   ![Novo Usuário](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "New User")
   
   a. Na caixa de texto **Nome de usuário** , digite o endereço de email da conta de Brenda Fernandes.
   
   b. Na caixa de texto **Senha** , digite a senha da conta de Brenda Fernandes.
   
   c. Selecione **Profissional de RH** como **Função de Segurança**.
   
   d. Clique em **OK**.
5. Navegue até a guia **Logon Único** e selecione **Adicionar Novo** na área **Identificadores SAML 2.0**.
   
    ![Usuário](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "User")
6. Escolha o **Provedor de Identidade** para o Provedor de Identidade e, na caixa de texto de **Identificador do Usuário**, insira o endereço de email da conta de Brenda Fernandes.
   
    ![Usuário](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "User")
7. Clique no botão **Salvar** .
   
    ![Usuário](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "User")

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao Cezanne HR Software.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao Cezanne HR Software, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201]
2. Na lista de aplicativos, selecione **Cezanne HR Software**.
   
    ![Configurar Logon Único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_50.png)
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203]
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste do logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando clicar no bloco Cezanne HR Software no Painel de Acesso, você deverá fazer logon automaticamente no seu aplicativo Cezanne HR Software.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


