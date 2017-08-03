---
title: "Tutorial: Integração do Azure Active Directory com o Questetra BPM Suite | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o Questetra BPM Suite."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: fb6d5b73-e491-4dd2-92d6-94e5aba21465
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 6a01f53ce05aa8084f0a18e56714b1790cfce912
ms.openlocfilehash: 7ae75446c9d19ce15a82caa9604658a528ab9941
ms.contentlocale: pt-br
ms.lasthandoff: 02/15/2017


---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Tutorial: Integração do Active Directory do Azure com o Questetra BPM Suite
O objetivo desse tutorial é mostrar como integrar o Questetra BPM Suite ao Azure AD (Azure Active Directory).  
A integração do Questetra BPM Suite ao Azure AD oferece os seguintes benefícios: 

* Você pode controlar, no AD do Azure, quem tem acesso ao Questetra BPM Suite 
* Você pode habilitar seus usuários a fazerem logon automaticamente no Questetra BPM Suite (logon único) com suas contas do AD do Azure
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do AD do Azure com o Questetra BPM Suite, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura habilitada para logon único do [Questetra BPM Suite](https://senbon-imadegawa-988.questetra.net/)

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste.  
O cenário descrito neste tutorial consiste em três blocos de construção principais:

1. Adicionar um Questetra BPM Suite da galeria 
2. Configurar e testar o logon único do Azure AD

## <a name="adding-questetra-bpm-suite-from-the-gallery"></a>Adicionar um Questetra BPM Suite da galeria
Para configurar a integração do Questetra BPM Suite com o AD do Azure, você precisa adicionar o Questetra BPM Suite, por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Questetra BPM Suite por meio da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **Questetra BPM Suite**.
   
    ![Aplicativos][5]

7. No painel de resultados, selecione **Questetra BPM Suite** e clique em **Concluir** para adicionar o aplicativo.

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar logon único do AD do Azure com o Questetra BPM Suite, com base em um usuário de teste chamado "Britta Simon".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Questetra BPM Suite é equivalente a um usuário do AD do Azure. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do AD do Azure e o usuário relacionado do Questetra BPM Suite.  
Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** no Questetra BPM Suite.

Para configurar e testar o logon único do AD do Azure com o Questetra BPM Suite, você precisa concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)** - para habilitar seus usuários para usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste do Questetra BPM Suite](#creating-a-questetra-bpm-suite-test-user)** - para ter um equivalente de Britta Simon no Questetra BPM Suite que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do AD do Azure
O objetivo desta seção é habilitar o logon único do Azure AD no portal clássico do Azure e configurar o logon único em seu aplicativo do Questetra BPM Suite.

**Para configurar o logon único do AD do Azure com o Questetra BPM Suite, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração de aplicativos do **Questetra BPM Suite**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar o logon único][8]

2. Na página **Como você deseja que os usuários façam logon no Questetra BPM Suite**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][9]

3. Em outra janela do navegador da Web, faça logon em seu site de empresa **Questetra BPM Suite** como administrador.

4. No menu na parte superior, clique em **Configurações do Sistema**. 
   
    ![Logon Único do AD do Azure][10]

5. Para abrir a página **SingleSignOnSAML**, clique em **SSO (SAML)**. 
   
    ![Logon Único do AD do Azure][11]

6. No portal clássico do Azure, na página de diálogo **Definir Configurações de Aplicativo** , execute estas etapas: 
   
    ![Definir Configurações de Aplicativo][13]
   
    a. No site de empresa do **Questetra BPM Suite**, na seção Informações do SP, copie a **URL de ACS** e cole-a na caixa de texto **URL de Entrada**.
   
    b. No site de empresa do **Questetra BPM Suite**, na seção Informações do SP, copie a **ID de Entidade** e cole-a na caixa de texto **URL do Emissor**.
   
    c. No site de empresa do **Questetra BPM Suite**, na seção Informações do SP, copie a **URL de ACS** e cole-a na caixa de texto **URL de Resposta**.
   
    d. Clique em **Próximo**.

7. Na página **Configurar logon único no Questetra BPM Suite**, clique em **Baixar certificado** e salve o arquivo de certificado localmente no computador.
   
    ![Configurar o logon único][14]

8. No seu site da empresa **Questetra BPM Suite** , execute as seguintes etapas: 
   
    ![Configurar Logon Único][15]
   
    a. Selecione **Habilitar Logon Único**.
   
    b. No portal clássico do Azure, copie o valor da **URL do Emissor** e cole-o na caixa de texto **ID de Entidade**.
   
    c. No portal clássico do Azure, copie o valor da **URL do Serviço de Logon Único** e cole-o na caixa de texto **URL da página de conexão**.
   
    d. No portal clássico do Azure, copie o valor da **URL do Serviço de Saída Único** e cole-o na caixa de texto **URL da página de saída**.
   
    e. Na caixa de texto **Formato da NameID**, digite **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.

    f. Crie um arquivo codificado em base&64; usando o certificado baixado. 

    >[!TIP] 
    >Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

    g. Abra seu certificado codificado em base-64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado de validação** . 

    h. Clique em **Salvar**.

1. No portal clássico do Azure, selecione a confirmação de configuração de logon único e clique em **Avançar**. 
   
    ![O que é o Azure AD Connect][17]

2. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![O que é o Azure AD Connect][18]


### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criar um usuário de teste do AD do Azure][100] 

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criar um usuário de teste do AD do Azure][101] 

4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**. 
   
    ![Criar um usuário de teste do AD do Azure][102] 

5. Na página do diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas:
   
    ![Criar um usuário de teste do AD do Azure][103]
   
    a. Em **Tipo de Usuário**, selecione **Novo usuário na organização**.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em Avançar.

6. Na página da caixa de diálogo **Perfil do Usuário** , execute as seguintes etapas: 
   
    ![Criar um usuário de teste do AD do Azure][104] 
   
    a. Na caixa de texto **Nome**, digite **Brenda**. 
   
    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
    c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   
    d. Na lista **Função**, selecione **Usuário**.
   
    e. Clique em **Próximo**.

7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criar um usuário de teste do AD do Azure][105]  

8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criar um usuário de teste do AD do Azure][106]   
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.   

### <a name="creating-a-questetra-bpm-suite-test-user"></a>Criar um usuário de teste do Questetra BPM Suite
O objetivo desta seção é criar um usuário chamado Britta Simon no Questetra BPM Suite.

**Para criar um usuário chamado Brenda Fernandes no Questetra BPM Suite, execute as seguintes etapas:**

1. Faça logon no site da sua empresa do Questetra BPM Suite como um administrador.
2. Vá para **Configurações do Sistema > Lista de Usuários > Novo Usuário**. 
3. Na caixa de diálogo Novo Usuário, execute as seguintes etapas: 
   
    ![Criar um usuário de teste][300] 
   
    a. Na caixa de texto **Nome** , digite o nome de usuário Britta no Azure AD.
   
    b. Na caixa de texto **Email** , digite o nome de usuário Britta no Azure AD.
   
    c. Na caixa de texto **Senha** , digite uma senha.

4. Clique em **Adicionar novo usuário**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
O objetivo desta seção é habilitar Brenda Fernandes a usar o logon único do Azure, concedendo a ela acesso ao Questetra BPM Suite.

![O que é o Azure AD Connect][200]

**Para atribuir Britta Simon ao Questetra BPM Suite, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![O que é o Azure AD Connect][201]
2. Na lista de aplicativos, selecione **Questetra BPM Suite**.
   
    ![O que é o Azure AD Connect][205]
3. No menu na parte superior, clique em **Usuários**.
   
    ![O que é o Azure AD Connect][202]
4. Na lista de usuários, selecione **Brenda Fernandes**.
   
    ![O que é o Azure AD Connect][203]
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![O que é o Azure AD Connect][204]

### <a name="testing-single-sign-on"></a>Teste do logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.  
Quando clica no bloco Questetra BPM Suite no Painel de Acesso, você deve fazer logon automaticamente no seu aplicativo Questetra BPM Suite.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_01.png


[8]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_06.png
[9]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_02.png
[10]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[12]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_05.png
[13]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_06.png
[14]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_07.png
[15]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_08.png
[16]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_09.png
[17]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_10.png
[18]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_08.png


[100]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_09.png 
[101]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_10.png 
[102]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_11.png 
[103]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_12.png 
[104]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_13.png 
[105]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_14.png 
[106]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_15.png 


[200]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_16.png 
[201]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_17.png 
[202]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_18.png
[203]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_19.png
[204]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_20.png
[205]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_12.png

[300]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_11.png 

