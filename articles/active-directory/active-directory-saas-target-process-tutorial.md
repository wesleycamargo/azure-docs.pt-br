---
title: "Tutorial: integração do Azure Active Directory ao TargetProcess | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o TargetProcess."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 7cb91628-e758-480d-a233-7a3caaaff50d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a44967e57156a3cf2d002f03128f1828a20e111a


---
# <a name="tutorial-azure-active-directory-integration-with-targetprocess"></a>Tutorial: integração do Active Directory do Azure com o TargetProcess
O objetivo deste tutorial é mostrar como integrar o TargetProcess ao Azure AD (Azure Active Directory).

A integração do TargetProcess ao Azure AD oferece os seguintes benefícios: 

* Você pode controlar no Azure AD quem tem acesso ao TargetProcess 
* Você pode habilitar seus usuários a fazerem logon automaticamente no TargetProcess (logon único) com suas contas do Azure AD
* Você pode gerenciar suas contas em um local central, o portal clássico do Active Directory do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD com o TargetProcess, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura habilitada para logon único do TargetProcess

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

1. Adicionando TargetProcess da galeria 
2. configurar e testar o logon único do AD do Azure

## <a name="adding-targetprocess-from-the-gallery"></a>Adicionando TargetProcess da galeria
Para configurar a integração do TargetProcess ao Azure AD, você precisa adicionar o TargetProcess por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o TargetProcess por meio da galeria, realize as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **TargetProcess**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_01.png)
7. No painel de resultados, selecione **TargetProcess** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_10.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do Azure AD com o TargetProcess, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o AD do Azure precisa saber qual usuário do TargetPRocess é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do TargetProcess.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** ao Azure AD como sendo o valor de **Nome de usuário** no TargetProcess.

Para configurar e testar o logon único do AD do Azure com o TargetProcess, você precisa concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste TargetProcess](#creating-a-targetprocess-test-user)** : para ter um equivalente de Brenda Fernandes no TargetProcess que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do AD do Azure
O objetivo desta seção é habilitar o logon único do Azure AD no portal clássico do Azure e configurar o logon único em seu aplicativo do TargetProcess. Como parte desse procedimento, será necessário criar um arquivo de certificado codificado em base 64. Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

Para configurar o logon único para o TargetProcess, você precisa de um domínio registrado. Se não tiver um domínio registrado, entre em contato com a equipe de suporte do TargetProcess pelo email [support@flatterfiles.com](mailto:support@flatterfiles.com).  

**Para configurar o logon único do Azure AD com o TargetProcess, realize as seguintes etapas:**

1. No portal clássico do Azure AD, na página de integração de aplicativos do **TargetProcess**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
    ![Configurar o logon único][6]
2. Na página **Como você deseja que os usuários façam logon no TargetProcess**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_02.png) 
3. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_03.png) 

    a. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para entrar no aplicativo TargetProcess (por exemplo: *https://fabrikam.TargetProcess.com/*).

    b. Clique em **Avançar**.


1. Na página **Configurar logon único no TargetProcess** , realize as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_04.png) 
   
    a. Clique em **Baixar certificado**e salve o arquivo em seu computador.
   
    b. Clique em **Avançar**.
2. Entre no seu aplicativo TargetProcess como administrador.
3. No menu na parte superior, clique em **Configuração**.
   
    ![Configurar Logon Único](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_05.png)
4. Clique em **Configurações**.
   
    ![Configurar Logon Único](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_06.png) 
5. Clique em **Logon Único**.
   
    ![Configurar Logon Único](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_07.png) 
6. Na caixa de diálogo Configurações de Logon Único, execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_08.png) 
   
    a. Clique em **Habilitar Logon Único**.
   
    b. No portal clássico do Azure, na página **Configurar logon único no TargetProcess**, copie o valor da **URL do Serviço de Logon Único** e cole-o na caixa de texto **URL de Logon**.
   
    c. Abra seu certificado baixado no Bloco de Notas, copie o conteúdo e cole-o na caixa de texto **Certificado** .
   
    d. Clique em **Habilitar Provisionamento de JIT**.
7. No portal clássico do Azure, selecione a confirmação da configuração de logon único e, em seguida, clique em **Avançar**. 
   
    ![Logon Único do AD do Azure][10]
8. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal clássico do Azure chamado Brenda Fernandes.
Na lista de usuários, selecione **Brenda Fernandes**.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_09.png)  
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**. 
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_04.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas: 
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_05.png)  
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas: 
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_06.png) 
   
   a. Na caixa de texto **Nome**, digite **Brenda**.  
   
   b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
   c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   
   d. Na lista **Função**, selecione **Usuário**.
   e. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_08.png) 
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.   

### <a name="creating-a-targetprocess-test-user"></a>Criar um usuário de teste TargetProcess
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no TargetProcess.
O TargetProcess dá suporte ao provisionamento de usuário just-in-time. Você já habilitou isso em [Configurar o logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on).

Não há itens de ação para você nesta seção.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
O objetivo desta seção é habilitar Brenda Fernandes a usar o logon único do Azure, concedendo a ela acesso ao TargetProcess.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao TargetProcess, realize as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, selecione **TargetProcess**.
   
    ![Configurar Logon Único](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_09.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste do logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco TargetProcess no Painel de Acesso, você deve ser conectado automaticamente ao seu aplicativo TargetProcess.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_205.png









<!--HONumber=Nov16_HO3-->


