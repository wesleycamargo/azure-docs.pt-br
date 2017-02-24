---
title: "Tutorial: Integração do Azure Active Directory com o SAP NetWeaver | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o SAP NetWeaver."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1a2ff324cfa65977685f6a0eee9cd398b0717519
ms.openlocfilehash: c7205f2a743bf2d835e9a1a5e6aad2542a7782ad


---
# <a name="tutorial-azure-active-directory-integration-with-sap-netweaver"></a>Tutorial: Integração do Azure Active Directory com o SAP NetWeaver
Neste tutorial, você aprenderá a integrar o SAP NetWeaver ao Azure Active Directory.

A integração do SAP NetWeaver ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD que tem acesso ao SAP NetWeaver
* Você pode permitir que seus usuários façam logon automaticamente no SAP NetWeaver (logon único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD ao SAP NetWeaver, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura habilitada para logon único do SAP NetWeaver

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar SAP NetWeaver da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-sap-netweaver-from-the-gallery"></a>Adicionar SAP NetWeaver da galeria
Para configurar a integração do SAP NetWeaver ao Azure AD, você precisará adicionar o SAP NetWeaver da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SAP NetWeaver da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **SAP NetWeaver**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sap-netweaver-tutorial/tutorial_sapnetweaver_01.png)
7. No painel de resultados, selecione **SAP NetWeaver** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sap-netweaver-tutorial/tutorial_sapnetweaver_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o SAP NetWeaver, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do SAP NetWeaver é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado no SAP NetWeaver.

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** no SAP NetWeaver.

Para configurar e testar o logon único do Azure AD com o SAP NetWeaver, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do SAP NetWeaver](#creating-a-sap-netweaver-test-user)** – para ter um equivalente de Brenda Fernandes no SAP NetWeaver que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD
Nesta seção, você habilitará o logon único do Azure AD no portal clássico e configurará o logon único em seu aplicativo do SAP NetWeaver.

**Para configurar o logon único do Azure AD com o SAP NetWeaver, execute as seguintes etapas:**

1. No portal clássico, na página de integração de aplicativo do **SAP NetWeaver**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
2. Na página **Como você deseja que os usuários façam logon no SAP NetWeaver**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar o logon único](./media/active-directory-saas-sap-netweaver-tutorial/tutorial_sapnetweaver_03.png) 
3. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-sap-netweaver-tutorial/tutorial_sapnetweaver_04.png) 
   
    a. Na caixa de texto **URL de Entrada**, digite a URL usada pelos usuários para entrar no aplicativo SAP NetWeaver usando o seguinte padrão: **https://\<instância de sua empresa do SAP NetWeaver\>**.
   
    b. Na caixa de texto **Identificador**, digite a URL usando o seguinte padrão: **https://\<instância de sua empresa do SAP NetWeaver\>**.
   
    c. Na caixa de texto **URL de Resposta**, digite a URL usando o seguinte padrão: **https://\<instância de sua empresa do SAP NetWeaver\>/sap/saml2/sp/acs/100**.
   
    > [!NOTE]
    > Você poderá localizar todos esses valores no documento de Metadados de Federação fornecido por seu parceiro do SAP NetWeaver.
    > 
    > 
   
    d. Clique em **Avançar**

4. Na página **Configurar logon único no SAP NetWeaver** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-sap-netweaver-tutorial/tutorial_sapnetweaver_05.png)
   
    a. Clique em **Baixar metadados**e salve o arquivo no computador.
   
    b. Clique em **Avançar**.
5. Para que o SSO seja configurado para seu aplicativo, entre em contato com o parceiro do SAP NetWeaver e forneça o seguinte:
   
    • Os **metadados**
   
    • A **ID da Entidade**
   
    • A **URL de SSO do SAML**
   
    • A **URL do Serviço de Logoff Único**
6. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
7. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
Nesta seção, você criará uma usuária de teste no portal clássico chamada Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sap-netweaver-tutorial/create_aaduser_09.png) 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sap-netweaver-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sap-netweaver-tutorial/create_aaduser_04.png) 
5. Na página de diálogo **Conte-nos sobre este usuário**, realize as seguintes etapas:  ![criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sap-netweaver-tutorial/create_aaduser_05.png) 
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em **Próximo**.
6. Na caixa de diálogo **perfil de usuário**, realize as etapas a seguir:  ![criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sap-netweaver-tutorial/create_aaduser_06.png) 
   
    a. Na caixa de texto **Nome**, digite **Brenda**.  
   
    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
    c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   
    d. Na lista **Função**, selecione **Usuário**.
   
    e. Clique em **Próximo**.

7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sap-netweaver-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sap-netweaver-tutorial/create_aaduser_08.png) 
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.   

### <a name="creating-an-sap-netweaver-test-user"></a>Criação de um usuário de teste do SAP NetWeaver
Nesta seção, você criará uma usuária chamada Brenda Fernandes no SAP NetWeaver. Trabalhe com seu parceiro do SAP NetWeaver para adicionar os usuários à plataforma SAP NetWeaver.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao SAP NetWeaver.

![Atribuir usuário][200] 

**Para atribuir Britta Simon ao SAP NetWeaver, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, selecione **SAP NetWeaver**.
   
    ![Configurar Logon Único](./media/active-directory-saas-sap-netweaver-tutorial/tutorial_sapnetweaver_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203]
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste do logon único
Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do SAP NetWeaver no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo do SAP NetWeaver.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO1-->


