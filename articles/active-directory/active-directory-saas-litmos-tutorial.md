---
title: "Tutorial: Integração do Azure Active Directory ao Litmos | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o Litmos."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: cfaae4bb-e8e5-41d1-ac88-8cc369653036
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 2c967d759806b47862179ecf9e9a89801134192a
ms.contentlocale: pt-br
ms.lasthandoff: 05/26/2017


---
# <a name="tutorial-azure-active-directory-integration-with-litmos"></a>Tutorial: Integração do Active Directory do Azure ao Litmos
O objetivo deste tutorial é mostrar como integrar o Litmos ao Azure AD (Azure Active Directory).  

A integração do Litmos ao Azure AD proporciona os seguintes benefícios: 

* No AD do Azure, você pode controlar quem tem acesso ao Litmos 
* Você pode permitir que usuários façam logon automaticamente no SSO (logon único) do Litmos com as respectivas contas do Azure AD
* Você pode gerenciar suas contas em um único local: o Active Directory do Azure 

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do AD do Azure ao Litmos, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura habilitada para logon único do Litmos

>[!NOTE]
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste.  

O cenário descrito neste tutorial consiste em três blocos de construção principais:

1. Adicionando o Litmos da galeria 
2. Configurar e testar o logon único do AD do Azure

## <a name="add-litmos-from-the-gallery"></a>Adicionar o Litmos da galeria
Para configurar a integração do Litmos ao AD do Azure, você precisará adicionar o Litmos da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Litmos da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Litmos**.
   
    ![Aplicativos][5]
7. No painel de resultados, selecione **Litmos** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Aplicativos][500]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do AD do Azure com o Litmos, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o AD do Azure precisa saber qual usuário do Litmos é equivalente a um usuário do AD do Azure. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Litmos.  

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** no Litmos.

Para configurar e testar o logon único do AD do Azure com o Litmos, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configuring-azure-ad-single-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#creating-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Litmos](#creating-a-halogen-software-test-user)** – para ter um equivalente de Brenda Fernandes no Litmos que esteja vinculado à representação dela no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Testar o logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD
O objetivo desta seção é habilitar o logon único do Azure AD no portal clássico do Azure AD e configurar o logon único em seu aplicativo Litmos.  

Como parte deste procedimento, será necessário criar um arquivo de certificado codificado em base 64.  

Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

Como parte da configuração, você precisa personalizar os **Atributos de Token SAML** para seu aplicativo Litmos.  

![Logon único do AD do Azure][17] 

**Para configurar o logon único do AD do Azure com o Litmos, execute as seguintes etapas:**

1. No portal clássico do Azure AD, na página de integração do aplicativo **Litmos**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
2. Na página **Como você deseja que os usuários façam logon no Litmos**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][7] 
3. Logon no site da sua empresa Litmos (por exemplo: *https://azureapptest.litmos.com/account/Login*) como administrador.
   
    ![Logon Único do AD do Azure][21] 
4. Na barra de navegação à esquerda, clique em **Contas**.
   
    ![Logon único do AD do Azure][22] 
5. Clique na guia **Integrações** .
   
    ![Logon Único do AD do Azure][23] 
6. Na guia **Integrações**, role para baixo até **Integrações de Terceiros** e clique na guia **SAML 2.0**.
   
    ![Logon Único do AD do Azure][24] 
7. Copie o valor em **O ponto de extremidade SAML para litmos é:**.
   
    ![Logon único do AD do Azure][26] 
8. No portal clássico do Azure, na página de diálogo **Definir Configurações do Aplicativo** , execute as seguintes etapas:
   
    ![Logon Único do AD do Azure][8] 
   
    1. Na caixa de texto **Identificador**, digite a URL usada pelos usuários para fazer logon no aplicativo Litmos (por exemplo: *https://azureapptest.litmos.com/account/Login*).
   
    2. Na caixa de texto **URL de resposta** , cole o valor que você copiou do aplicativo Litmos na etapa anterior.
   
    3. Clique em **Avançar**.
9. Na página **Configurar logon único no Litmos** , execute as seguintes etapas:
   
    ![Logon Único do AD do Azure][2] 
   
    * Clique em Baixar certificado e salve o certificado localmente no computador.
10. No seu aplicativo **Litmos** , execute as seguintes etapas:
    
     ![Logon Único do AD do Azure][25] 
    
     1. Clique em **Habilitar SAML**.
    
     2. Crie um arquivo **codificado em base 64** usando o certificado baixado.  
    >[!TIP]
    >Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).
     >

     3. Abra seu certificado codificado em base 64 no bloco de notas, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **Certificado SAML X.509** .
    
     4. Clique em **Salvar Alterações**.
11. No portal clássico do Azure AD, selecione a confirmação de configuração do logon único e clique em **Avançar**. 
    
     ![Logon Único do AD do Azure][10]
12. Na página **Confirmação de logon único**, clique em **Concluir**.  
    
     ![Logon Único do AD do Azure][11]
13. Na parte superior do menu, clique em **Atributos** to open the **SAML Token Atributos** . 
    
    ![Configurar Logon Único][12]
14. No diálogo **Adicionar Atributo de Usuário** , execute as seguintes etapas: 
    
    ![Configurar Logon Único][14]
    
    | Nome do atributo | Valor do atributo |
    | --- | --- |
    | Email |user.mail |
    | Nome |user.givenname |
    | Sobrenome |user.surname |
    
    Para cada linha de dados na tabela acima, execute as seguintes etapas:
    
 1. Clique em **adicionar atributo de usuário**.    
   ![Configurar Logon Único][15]
 2. Na caixa de texto **Nome do Atributo**, digite o **Nome do Atributo** mostrado para essa linha.
 3. Selecione o **Valor do Atributo** mostrado para a linha.
 4. Clique em **Concluir** para fechar a caixa de diálogo **Adicionar Atributo de Usuário**.


1. Clique em **Aplicar alterações**. 
   
   ![Configurar Logon Único][16]

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.  

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação esquerdo, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-litmos-tutorial/create_aaduser_09.png)  
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-litmos-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**. 
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-litmos-tutorial/create_aaduser_04.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas: 
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-litmos-tutorial/create_aaduser_05.png)  
   
    1. Em **Tipo de Usuário**, selecione **Novo usuário na organização**.
   
    2. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    3. Clique em **Avançar**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas: 
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-litmos-tutorial/create_aaduser_06.png) 
   
   1. Na caixa de texto **Nome**, digite **Brenda**.  
   
   2. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
   3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   
   4. Na lista **Função**, selecione **Usuário**.
   5. Clique em **Avançar**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-litmos-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-litmos-tutorial/create_aaduser_08.png) 
   
    1. Anote o valor da **Nova Senha**.
   
    2. Clique em **Concluído**.   

### <a name="create-a-litmos-test-user"></a>Criar um usuário de teste do Litmos
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Litmos.  
O aplicativo Litmos oferece suporte ao provisionamento Just-in-Time. Isso significa que, se for necessário, uma conta de usuário será criada automaticamente durante uma tentativa de acessar o aplicativo usando o Painel de Acesso.

**Para criar um usuário chamado Brenda Fernandes no Litmos, execute as seguintes etapas:**

1. Logon no site da sua empresa Litmos (por exemplo: *https://azureapptest.litmos.com/account/Login*) como administrador.
   
    ![Logon Único do AD do Azure][21] 
2. Na barra de navegação à esquerda, clique em **Contas**.
   
    ![Logon único do AD do Azure][22] 
3. Clique na guia **Integrações** .
   
    ![Logon Único do AD do Azure][23] 
4. Na guia **Integrações**, role para baixo até **Integrações de Terceiros** e clique na guia **SAML 2.0**.
   
    ![Logon Único do AD do Azure][24] 
5. Escolha **Gerar Usuários Automaticamente:**.
   
    ![Logon Único do AD do Azure][27] 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD
O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao Litmos.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Litmos, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, escolha **Litmos**.
   
    ![Atribuir usuário][202] 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="test-single-sign-on"></a>Testar o logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.  

Ao clicar no bloco Litmos no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Litmos.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_01.png
[500]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_02.png

[6]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_03.png
[8]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_04.png
[9]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_05.png
[10]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_07.png
[12]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_80.png
[13]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[14]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_82.png
[15]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[16]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_19.png
[17]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_67.png


[20]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_66.png

[200]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_68.png
[203]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_400.png
[401]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_401.png
[402]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_402.png






