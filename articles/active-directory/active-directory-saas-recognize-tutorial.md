---
title: "Tutorial: Integração do Azure Active Directory com o Recognize | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Recognize."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 43e9febd4bc230db69b55ae3f5bb13ff17b2e3f6


---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Tutorial: integração do Azure Active Directory com o Recognize
O objetivo desse tutorial é mostrar como integrar o Recognize ao Azure AD (Azure Active Directory).

A integração do Recognize ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Recognize
* Você pode permitir que os usuários façam logon automaticamente no Recognize (Logon Único) com suas contas do Azure AD
* Você pode gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD ao Recognize, você precisará dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura do Recognize com logon único habilitado

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

1. Adicionando o Recognize da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-recognize-from-the-gallery"></a>Adicionando o Recognize da galeria
Para configurar a integração do Recognize ao Azure AD, você precisará adicionar o Recognize da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Recognize da galeria, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Recognize**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_01.png)
7. No painel de resultados, selecione **Recognize** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Seleção do aplicativo na galeria](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_0001.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do Azure AD com o Recognize, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Recognize é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Recognize.

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD ao valor do **Nome de Usuário** no Recognize.

Para configurar e testar o logon único do Azure AD com o Recognize, você precisa concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do Recognize](#creating-a-recognize-test-user)** – para ter um equivalente de Brenda Fernandes no Recognize que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD
Nesta seção, você habilitará o logon único do Azure AD no portal clássico e configurará o logon único no aplicativo Recognize.

**Para configurar o logon único do Azure AD com o Recognize, execute as seguintes etapas:**

1. No portal clássico, na página de integração de aplicativos do **Recognize**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
2. Na página **Como você deseja que os usuários façam logon no Recognize**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_03.png)
3. Na página de diálogo **Definir Configurações do Aplicativo**, execute as seguintes etapas e clique em **Avançar**:
   
    ![Configurar o logon único](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_04.png)
   
    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://recognizeapp.com/<your-domain>/saml/sso`.
   
    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://recognizeapp.com/<your-domain>/saml/metadata`.
   
    c. Clique em **Próximo**
   
   > [!NOTE]
   > Se você não conhecer essas URLs, digite URLs de amostra, com o padrão de exemplo. Para obter esses valores, confira a etapa 9 para ver mais detalhes ou entre em contato com a equipe de suporte do Recognize por <mailto:support@recognizeapp.com>.
   > 
   > 
4. Na página **Configurar logon único no Recognize**, clique em **Baixar certificado** e, em seguida, salve o arquivo no computador:
   
    ![Configurar Logon Único](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_05.png)
5. Em uma janela diferente do navegador da Web, faça logon em seu locatário do Recognize como um administrador.
6. No canto superior direito, clique em **Menu**. Vá para **Administrador da Empresa**.
   
    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_000.png)
7. No painel de navegação esquerdo, clique em **Configurações**.
   
    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_001.png)
8. Realize as seguintes etapas na seção **Configurações do SSO**.
   
    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_002.png)
   
    a. Em **Habilitar SSO**, selecione **ATIVADO**.
   
    b. Na caixa de texto **ID da entidade do IDP**, coloque o valor da **URL do Emissor** do assistente de configuração de aplicativo do Azure AD.
   
    c. Na caixa de texto **URL de destino do SSO**, insira o valor da **URL de serviço de logon único** do assistente de configuração de aplicativo do Azure AD.
   
    d. Na caixa de texto **URL de destino do SLO**, insira o valor da **URL de serviço de logoff único** do assistente de configuração de aplicativo do Azure AD.
   
    e. Abra seu arquivo de certificado baixado no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado**. 
   
    f. Clique no botão **Salvar configurações**. 
9. Ao lado da seção **Configurações de SSO**, copie a URL em **URL de metadados do provedor de serviço**.
   
    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_003.png)
10. Abra o **link da URL de metadados** em um navegador em branco para baixar o documento de metadados. Em seguida, use o valor de EntityDescriptor que o Recognize forneceu como **Identificador** na caixa de diálogo **Definir configurações de aplicativo**.
    
    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_004.png)
11. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
    
    ![Logon Único do AD do Azure][10]
12. Na página **Confirmação de logon único**, clique em **Concluir**.  
    
    ![Logon Único do AD do Azure][11]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-recognize-tutorial/create_aaduser_09.png)
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-recognize-tutorial/create_aaduser_03.png)
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-recognize-tutorial/create_aaduser_04.png)
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-recognize-tutorial/create_aaduser_05.png)
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-recognize-tutorial/create_aaduser_06.png)
   
   a. Na caixa de texto **Nome**, digite **Brenda**.  
   
   b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
   c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   
   d. Na lista **Função**, selecione **Usuário**.
   
   e. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-recognize-tutorial/create_aaduser_07.png)
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-recognize-tutorial/create_aaduser_08.png)
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.   

### <a name="creating-a-recognize-test-user"></a>Criando um usuário de teste do Recognize
Para permitir que os usuários do Azure AD façam logon no Recognize, eles deverão ser provisionados no Recognize. No caso do Recognize, o provisionamento é uma tarefa manual.

Este aplicativo não dá suporte ao provisionamento de SCIM, mas tem uma sincronização de usuário alternativa que provisiona usuários. 

#### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para provisionar uma conta de usuário, execute as seguintes etapas:
1. Faça logon em seu site de empresa Recognize como um administrador.
2. No canto superior direito, clique em **Menu**. Vá para **Administrador da Empresa**.
3. No painel de navegação esquerdo, clique em **Configurações**.
4. Execute as seguintes etapas na seção **Sincronização de usuário**.
   
   ![Novo Usuário](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_005.png "New User")
   
   a. Em **Sincronização habilitada**, selecione **ATIVADO**.
   
   b. Em **Escolher o provedor de sincronização**, selecione **Microsoft / Office 365**.
   
   c. Clique em **Executar sincronização de usuário**

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
O objetivo dessa seção é habilitar Brenda Fernandes a usar o logon único do Azure, concedendo a ela acesso ao Recognize.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao Recognize, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201]
2. Na lista de aplicativos, escolha **Recognize**.
   
    ![Configurar o logon único](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_50.png)
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203]
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste do logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Recognize no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo do Recognize.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


