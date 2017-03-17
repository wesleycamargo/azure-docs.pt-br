---
title: "Tutorial: Integração do Active Directory do Azure com o Halogen Software"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o Halogen Software."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 2ca2298d-9a0c-4f14-925c-fa23f2659d28
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 06d4b7495f4201387500944758ba2a0916b619d2
ms.openlocfilehash: cbe20975792ab0f5fe8fac64110ede3a964d443a
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-halogen-software"></a>Tutorial: Integração do Active Directory do Azure com o Halogen Software
O objetivo desse tutorial é mostrar como integrar o Halogen Software ao Azure AD (Azure Active Directory).

A integração do Halogen Software ao Azure AD oferece os seguintes benefícios: 

* Você pode controlar, no Azure AD, quem tem acesso ao Halogen Software 
* Você pode permitir que seus usuários façam logon automaticamente no Halogen Software usando SSO (logon único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD com o Halogen Software, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura do Halogen Software com SSO habilitado

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do Azure AD, você pode obter uma [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o SSO do Azure AD em um ambiente de teste. 

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Halogen Software por meio da galeria 
2. Configurar e testar o SSO do Azure AD

## <a name="adding-halogen-software-from-the-gallery"></a>Adicionar o Halogen Software por meio da galeria
Para configurar a integração do Halogen Software com o Azure AD, você precisa adicionar o Halogen Software, por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Halogen Software por meio da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.    
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **halogen software**.
   
    ![Aplicativos][5]
7. No painel de resultados, selecione **Halogen Software** e clique em **Concluir** para adicionar o aplicativo.

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar SSO do Azure AD
O objetivo desta seção é mostrar como configurar e testar SSO do Azure AD com o Halogen Software, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o SSO funcione, o Azure AD precisa saber qual usuário do Halogen Software é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Halogen Software.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD ao valor de **nome de usuário** no Halogen Software.

Para configurar e testar o SSO do Azure AD com o Halogen Software, você precisa concluir os seguintes blocos de construção:

1. **[Configuração do logon único do Azure AD](#configuring-azure-ad-single-single-sign-on)** - para permitir que os usuários usem esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do Halogen Software](#creating-a-halogen-software-test-user)** - para ter um equivalente de Britta Simon no Halogen Software que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD
O objetivo desta seção é habilitar o SSO do Azure AD no Portal Clássico do Azure e configurar o SSO em seu aplicativo Halogen Software.

**Para configurar o SSO do Azure AD com o Halogen Software, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração de aplicativos do **Halogen Software**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][8]
2. Na página **Como você deseja que os usuários façam logon no Halogen Software**, selecione **Logon único do Azure AD** e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][9]
3. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:

    ![Definir configurações de aplicativo][10]
   1. Na caixa de texto **URL de logon**, digite a URL usada pelos usuários para fazer logon no seu aplicativo do Halogen Software, usando o seguinte padrão: *https://global.hgncloud.com/fabrikam/welcome.jsp*
   2. Clique em **Próximo**.
4. Na página **Configurar logon único no Halogen Software**, clique em **Baixar metadados** e salve o arquivo de metadados em seu computador.
   
    ![O que é o Azure AD Connect][11]
5. Em uma janela diferente do navegador, faça logon no aplicativo **Halogen Software** como administrador.
6. Clique na guia **Opções** . 
   
    ![O que é o Azure AD Connect][12]
7. No painel de navegação esquerdo, clique em **Configuração do SAML**. 
   
    ![O que é o Azure AD Connect][13]
8. Na página **Configuração do SAML** , realize as seguintes etapas: 

    ![O que é o Azure AD Connect][14]
  1. Como **Identificador exclusivo**, selecione **NameID**.
  2. Em **Identificador exclusivo mapeia para**, selecione **Nome de usuário**.
  3. Para carregar o arquivo de metadados baixado, clique em **Procurar** para selecionar o arquivo e clique em **Carregar arquivo**.
  4. Para testar a configuração, clique em **Executar Teste**. 
    >[!NOTE]
    >Você precisa esperar pela mensagem "*O teste de SAML foi concluído. Feche esta janela*". Feche a janela do navegador aberta. A caixa de seleção **Habilitar SAML** só será habilitada se o teste for concluído. 
    >
  5. Selecione **Habilitar SAML**.
  6. Clique em **Salvar Alterações**. 
9. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**. 
   
    ![O que é o Azure AD Connect][15]
10. Na página **Confirmação de logon único**, clique em **Concluir**.  
    
    ![O que é o Azure AD Connect][16]

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![O que é o Azure AD Connect][100] 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![O que é o Azure AD Connect][101] 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**. 
   
    ![O que é o Azure AD Connect][102] 
5. Na página do diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas:
   
    ![O que é o Azure AD Connect][103] 
  1. Em **Tipo de Usuário**, selecione **Novo usuário na organização**.
  2. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
  3. Clique em Próximo.
6. Na página da caixa de diálogo **Perfil do Usuário** , execute as seguintes etapas: 
   
   ![O que é o Azure AD Connect][104] 
  1. Na caixa de texto **Nome**, digite **Brenda**.  
  2. Na caixa de texto **Sobrenome**, digite **Fernandes**.
  3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
  4. Na lista **Função**, selecione **Usuário**.
  5. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![O que é o Azure AD Connect][105]  
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![O que é o Azure AD Connect][106]   
  1. Anote o valor da **Nova Senha**.
  2. Clique em **Concluído**.   

### <a name="create-a-halogen-software-test-user"></a>Criar um usuário de teste do Halogen Software
O objetivo desta seção é criar um usuário chamado Britta Simon no Halogen Software.

**Para criar um usuário chamado Britta Simon no Halogen Software, execute as seguintes etapas:**

1. Faça logon no aplicativo **Halogen Software** como administrador.
2. Clique na guia **Central do Usuário** e clique em **Criar Usuário**.
   
    ![O que é o Azure AD Connect][300]  
3. Na página do diálogo **Novo Usuário** , realize as seguintes etapas:
   
    ![O que é o Azure AD Connect][301]
  1. Na caixa de texto **Nome**, digite **Brenda**. 
  2. Na caixa de texto **Sobrenome**, digite **Fernandes**. 
  3. Na caixa de texto **Nome de Usuário**, digite o **Nome de usuário de Brenda Fernandes no portal clássico do Azure**.
  4. Na caixa de texto **Senha** , digite uma senha para Britta.
  5. Clique em **Salvar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD
O objetivo desta seção é permitir que Brenda Fernandes use o SSO do Azure, concedendo a ela acesso ao Halogen Software.

![O que é o Azure AD Connect][200]

**Para atribuir Britta Simon ao Halogen Software, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![O que é o Azure AD Connect][201]
2. Na lista de aplicativos, selecione **Halogen Software**.
   
    ![O que é o Azure AD Connect][202]
3. No menu na parte superior, clique em **Usuários**.
   
    ![O que é o Azure AD Connect][203]
4. Na lista de usuários, selecione **Brenda Fernandes**.
   
    ![O que é o Azure AD Connect][204]
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![O que é o Azure AD Connect][205]

### <a name="test-single-sign-on"></a>Testar logon único
O objetivo desta seção é testar sua configuração de SSO do Azure AD usando o Painel de Acesso.

Quando clica no bloco Halogen Software no Painel de Acesso, você deve fazer logon automaticamente no seu aplicativo Halogen Software.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_01.png
[2]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_02.png
[3]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_03.png
[4]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_04.png
[5]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_05.png
[6]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_06.png
[7]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_07.png
[8]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_08.png
[9]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_09.png
[10]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_10.png
[11]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_11.png
[12]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_12.png
[13]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_13.png
[14]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_14.png
[15]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_15.png
[16]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_16.png
[100]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_100.png 
[101]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_101.png 
[102]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_102.png 
[103]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_103.png 
[104]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_104.png 
[105]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_105.png 
[106]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_106.png 
[200]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_200.png 
[201]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_201.png 
[202]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_202.png
[203]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_203.png
[204]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_204.png
[205]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_205.png
[300]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_300.png
[301]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_301.png

