---
title: "Tutorial: integração do Azure Active Directory com o Flatter Files | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o Flatter Files."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f86fe5e3-0e91-40d6-869c-3df6912d27ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 488a3c5f0aa05c5b71bf5d72539cbc4b7c6de1b5
ms.openlocfilehash: 062878ad877b501ce7f0d5c4f8ce9ca939ffe64d
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Tutorial: Integração do Active Directory do Azure ao Flatter Files
O objetivo deste tutorial é mostrar como integrar o Flatter Files ao Azure AD (Azure Active Directory).  

A integração do Flatter Files ao Azure AD oferece os seguintes benefícios: 

* No AD do Azure, você pode controlar quem tem acesso ao Flatter Files 
* Você pode permitir que seus usuários façam logon automaticamente no SSO (logon único) do Flatter Files com suas contas do Azure AD
* Você pode gerenciar suas contas em um local central, o portal clássico do Active Directory do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do AD do Azure ao Flatter Files, você precisará dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura do Flatter Files com logon único habilitado

>[!NOTE]
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção. 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste.  

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Flatter Files da galeria 
2. Configurar e testar o logon único do AD do Azure

## <a name="add-flatter-files-from-the-gallery"></a>Adicionar o Flatter Files da galeria
Para configurar a integração do Flatter Files ao AD do Azure, você precisará adicionar o Flatter Files da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Flatter Files da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Flatter Files**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_01.png)

7. No painel de resultados, selecione **Flatter Files**, em seguida, clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_500.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD
O objetivo desta seção é mostrar como configurar e testar logon único do AD do Azure com o Flatter Files, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o SSO funcione, o Azure AD precisa saber qual usuário do Flatter Files é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Flatter Files.  

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Username** em Flatter Files.

Para configurar e testar o logon único do AD do Azure com o Flatter Files, você precisará concluir os seguintes blocos de construção:

1. **[Configuração do logon único do Azure AD](#configuring-azure-ad-single-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do Flatter Files](#creating-a-halogen-software-test-user)** : para ter um equivalente de Brenda Fernandes no Flatter Files que esteja vinculado à representação dela no AD do Azure.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD
O objetivo desta seção é habilitar o logon único do AD do Azure no portal clássico do AD do Azure e configurar o logon único em seu aplicativo Flatter Files. Como parte deste procedimento, será necessário criar um arquivo de certificado codificado em base&64;. Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).
Para configurar o logon único para o Flatter Files, você precisará de um domínio registrado. Se não tiver um domínio registrado, entre em contato com a equipe de suporte do Flatter Files pelo email [support@flatterfiles.com](mailto:support@flatterfiles.com).  

**Para configurar o logon único do AD do Azure com o Flatter Files, execute as seguintes etapas:**

1. No portal clássico do Azure AD, na página de integração de aplicativos **Flatter Files**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
2. Na página **Como você gostaria que os usuários fizessem logon no Flatter Files**, selecione **Logon Único do Azure AD**, em seguida, clique em **Próximo**.
       ![Configurar Logon Único](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_02.png) 
3. Na caixa de diálogo **Definir Configurações do Aplicativo**, clique em **Próximo**.
   
    ![Configurar Logon Único](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_03.png) 
   >[!NOTE]
   >O Flatter Files usa a mesma URL de SSO para todos os clientes: [https://www.flatterfiles.com/site/login/sso/](https://www.flatterfiles.com/site/login/sso/).
   > 
   
4. Na página **Configurar logon único no Flatter Files** , execute as seguintes etapas:
   
    ![Configurar o logon único](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_04.png)  
    1. Clique em **Baixar certificado**e salve o arquivo em seu computador.
    2. Clique em **Próximo**.
5. Faça logon no aplicativo Flatter Files como administrador.
6. Clique em Painel. 
   
    ![Configurar Logon Único](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_05.png)  
7. Clique em **Configurações** e execute as etapas a seguir na guia **Empresa**: 
   
    ![Configurar Logon Único](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_06.png)  
    1. Selecione **Usar SAML 2.0 para Autenticação**.
    2. Clique em **Configurar SAML**.
8. No diálogo **Configuração de SAML** , execute as seguintes etapas: 
   
    ![Configurar o logon único](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_08.png)  
   1. Na caixa de texto Domínio, digite seu domínio registrado.
   
    >[!NOTE]
    >Se não tiver um domínio registrado, entre em contato com a equipe de suporte do Flatter Files via [support@flatterfiles.com] (mailto:support@flatterfiles.com). 
    >    
   2. No portal clássico do Azure, na caixa de diálogo Configurar logon único no Flatter Files, copie a URL do Serviço de Logon Único e cole-a na caixa de texto URL do Provedor de Identidade.
   3.  Crie um arquivo **codificado em base&64;** usando o certificado baixado.  
 
   >[!TIP]
   >Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).
   >  
   4.  Abra seu certificado codificado em Base&64; no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado do Provedor de Identidade do Flatter Files** .
   5. Clique em **Atualizar**.
9. No portal clássico do Azure AD, selecione a confirmação de configuração de logon único e clique em **Avançar**. 
   
    ![Logon Único do AD do Azure][10]
10. Na página **Confirmação de logon único**, clique em **Concluir**.  
    
     ![Logon Único do AD do Azure][11]

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

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
   1. Em Tipo de Usuário, selecione Novo usuário na organização.
   2. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   3. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas: 
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_06.png) 
   1. Na caixa de texto **Nome**, digite **Brenda**.  
   2. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   4. Na lista **Função**, selecione **Usuário**.
   5. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_08.png) 
   1. Anote o valor da **Nova Senha**.
   2. Clique em **Concluído**.   

### <a name="create-a-flatter-files-test-user"></a>Criar um usuário de teste do Flatter Files
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Flatter Files.

**Para criar um usuário chamado Brenda Fernandes no Flatter Files, execute as seguintes etapas:**

1. Faça logon no site da empresa **Flatter Files** como administrador.
2. No painel de navegação à esquerda, clique em **Configurações**, em seguida, clique na guia **Usuários**.
   
    ![Criar um Usuário do Flatter Files](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_09.png)
3. Clique em **Adicionar Usuário**. 
4. No diálogo **Adicionar Usuário** , realize as seguintes etapas:
   
    ![Criar um Usuário do Flatter Files](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_10.png)
   1. Na caixa de texto **Nome**, digite **Brenda**.
   2. Na caixa de texto **Sobrenome**, digite **Fernandes**. 
   3. Na caixa de texto **Endereço de Email** , digite o endereço de email de Brenda no portal clássico do Azure.
   4. Clique em **Enviar**.   

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD
O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao Flatter Files.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Flatter Files, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, escolha **Flatter Files**.
   
    ![Atribuir usuário](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_11.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="test-single-sign-on"></a>Testar logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.  

Quando você clica no bloco Flatter Files no Painel de Acesso, deve fazer logon automaticamente no seu aplicativo Flatter Files.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_205.png







