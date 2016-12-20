---
title: "Tutorial: Integração do Azure Active Directory ao HR2day by Merces | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o HR2day by Merces."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 853d08c9-27b1-48d4-b8e7-3705140eb67f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: ca1b2a7435d4ae72cab85c5f0aea5466a95f2bba


---
# <a name="tutorial-azure-active-directory-integration-with-hr2day-by-merces"></a>Tutorial: Integração do Active Directory do Azure ao HR2day by Merces
O objetivo desse tutorial é mostrar como integrar o HR2day by Merces ao Azure Active Directory (Azure AD).  
A integração do HR2day by Merces ao Azure AD oferece os seguintes benefícios:

* Você pode controlar, no AD do Azure, quem tem acesso ao HR2day by Merces
* Você pode permitir que seus usuários façam logon automaticamente no HR2day by Merces (logon único) com suas contas do AD do Azure
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do AD do Azure ao HR2day by Merces, você precisará dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura do HR2day by Merces com logon único habilitado

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

1. Adição do HR2day by Merces da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-hr2day-by-merces-from-the-gallery"></a>Adição do HR2day by Merces da galeria
Para configurar a integração do HR2day by Merces ao AD do Azure, você precisará adicionar o HR2day by Merces da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o HR2day by Merces da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **HR2day by Merces**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_01.png)

7. No painel de resultados, selecione **HR2day by Merces** e clique em **Concluir** para adicionar o aplicativo.

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar logon único do AD do Azure com o HR2day by Merces com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o AD do Azure precisa saber qual usuário do HR2day by Merces é equivalente a um usuário do AD do Azure. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do HR2day by Merces.  
Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** no HR2day by Merces.

Para configurar e testar o logon único do AD do Azure com o HR2day by Merces, você precisará concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar logon único do Azure AD com Britta Simon.
3. **[Criação de um usuário de teste do HR2day by Merces](#creating-a-hr2day-by-merces-test-user)** – para ter um equivalente de Brenda Fernandes no HR2day by Merces que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do AD do Azure
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no HR2day by Merces com sua conta do AD do Azure usando a federação baseada no protocolo SAML.

Seu aplicativo HR2day by Merces espera as declarações de SAML em um formato específico, o que exige adicionar mapeamentos de atributo personalizados à configuração de atributos do token SAML. A captura de tela a seguir mostra um exemplo disso. 

![Configurar Logon Único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_00.png) 

Antes de configurar a declaração SAML, você precisará entrar em contato com a equipe de suporte do HR2day por meio de [servicedesk@merces.nl](mailto:servicedesk@merces.nl) e solicitar o valor do atributo de identificador exclusivo para seu locatário. Você precisa desse valor para concluir as etapas na próxima seção.

**Para configurar o logon único do AD do Azure com o HR2day by Merces, execute as seguintes etapas:**

1. No Portal clássico do Azure, na página de integração do aplicativo **HR2day by Merces**, no menu superior, clique em **Atributos** para abrir a caixa de diálogo **Atributos de Token SAML**. 
   
    ![Configurar o logon único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_06.png) 

2. Para adicionar os mapeamentos de atributo necessários, execute as seguintes etapas: 
   
    ![Configurar Logon Único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_07.png) 

    a. Clique em **adicionar atributo de usuário**.

    b. Na caixa de texto **Nome do Atributo**, digite **“ATTR_LOGINCLAIM”**.

    c. Na lista **Valor do Atributo**, selecione **Join()**. 

    d. Na lista **String1**, selecione **User.mail**. 

    e. Na caixa de texto **String2**, digite o **identificador exclusivo** fornecido pela equipe do HR2day. 

    f. Na caixa de texto **Separador**, digite **@**.

    g. Clique em **Concluído**.


1. Clique em **Aplicar alterações**.

2. No menu na parte superior, clique em **Início Rápido** to open the **Início Rápido** .
   
    ![Configurar Logon Único](./media/active-directory-saas-hr2day-tutorial/tutorial_general_08.png) 

3. Clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 

4. Na página **Como você deseja que os usuários entram no HR2day by Merces**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar o logon único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_03.png) 

5. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas: 
   
    ![Configurar Logon Único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_04.png) 

    a. Na caixa de texto URL de Logon, digite a URL usada pelos usuários para entrar no aplicativo HR2day by Merces usando o seguinte padrão: **“https://\<nome do locatário\>.force.com/\<nome da instância\>”**.

    b. Clique em **Avançar**.

1. Na página **Configurar logon único no HR2day by Merces** , realize as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_05.png) 
   
    a. Clique em **Baixar certificado**e salve o arquivo em seu computador.
   
    b. Clique em **Avançar**.

2. Para configurar o SSO para seu aplicativo, entre em contato com sua equipe de suporte do HR2day by Merces por meio do email [servicedesk@merces.nl](emailTo:servicedesk@merces.nl) e anexe o arquivo de certificado baixado ao email. Também forneça a URL de SSO de SAML, a URL de Saída e a URL do Emissor para que possam ser configuradas para integração com o SSO.

    > [!NOTE]
    > Mencione à equipe do Merces que essa integração precisa que a ID da Entidade seja definida com este padrão **https://hr2day.force.com/INSTANCENAME**
    > 
    > 

1. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
2. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.  

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hr2day-tutorial/create_aaduser_09.png) 

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hr2day-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hr2day-tutorial/create_aaduser_04.png) 

5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hr2day-tutorial/create_aaduser_05.png) 
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em **Próximo**.

6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hr2day-tutorial/create_aaduser_06.png) 
   
    a. Na caixa de texto **Nome**, digite **Brenda**.  
   
    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
    c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   
    d. Na lista **Função**, selecione **Usuário**.
   
    e. Clique em **Próximo**.

7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hr2day-tutorial/create_aaduser_07.png) 

8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hr2day-tutorial/create_aaduser_08.png) 
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.   

### <a name="creating-a-hr2day-by-merces-test-user"></a>Criação de um usuário de teste do HR2day by Merces
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no HR2day by Merces. Trabalhe com a equipe de suporte do HR2day by Merces para adicionar usuários à conta do HR2day by Merces. 

> [!NOTE]
> Se você precisar criar um usuário manualmente, entre em contato com a equipe de suporte do HR2day by Merces.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
O objetivo desta seção é habilitar Brenda Fernandes a usar o logon único do Azure concedendo a ela acesso ao HR2day by Merces.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao HR2day by Merces, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **HR2day by Merces**.
   
    ![Configurar Logon Único](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_50.png) 

3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 

4. Na lista de usuários, selecione **Brenda Fernandes**.

5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste do logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.  
Quando você clica no bloco HR2day by Merces no Painel de Acesso, deve fazer logon automaticamente em seu aplicativo HR2day by Merces.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


