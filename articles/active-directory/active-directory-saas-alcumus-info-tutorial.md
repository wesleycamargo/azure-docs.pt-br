---
title: 'Tutorial: Integração do Active Directory do Azure ao Alcumus Info Exchange | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o Alcumus Info Exchange.
services: active-directory
documentationcenter: ''
author: jeevansd
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2016
ms.author: jeedes

---
# Tutorial: integração do Active Directory do Azure ao Alcumus Info Exchange
O objetivo desse tutorial é mostrar como integrar o Alcumus Info Exchange ao Azure AD (Azure Active Directory). A integração do Alcumus Info Exchange ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Alcumus Info Exchange
* Você pode habilitar seus usuários a fazerem logon automaticamente no Alcumus Info Exchange (logon único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos
Para configurar a integração do Azure AD com o Alcumus Info Exchange, você precisa dos seguintes itens:

* Uma assinatura do [Azure AD](https://azure.microsoft.com/)
* Uma assinatura do [Alcumus Info Exchange](http://www.alcumusgroup.com/) com logon único habilitado

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em três blocos de construção principais:

1. Adicionando o Alcumus Info Exchange a partir da galeria
2. Configurar e testar o logon único do AD do Azure

## Adicionando o Alcumus Info Exchange a partir da galeria
Para configurar a integração do Alcumus Info Exchange ao Azure AD, você precisa adicioná-lo a partir da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Alcumus Info Exchange a partir da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory][1]
2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Alcumus Info Exchange**.
   
    ![Aplicativos][5]
7. No painel de resultados, selecione **Alcumus Info Exchange** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Aplicativos][400]

## Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do Azure AD com o Alcumus Info Exchange baseado em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Alcumus Info Exchange é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Alcumus Info Exchange. Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD ao valor de **Nome de usuário** no Alcumus Info Exchange.

Para configurar e testar o logon único do Azure AD com o Alcumus Info Exchange, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do Azure AD](#configuring-azure-ad-single-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#creating-an-azure-ad-test-user)**: para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criando um usuário de teste do Alcumus Info Exchange](#creating-a-alcumus-info-exchange-test-user)**: para ter um equivalente da Brenda Fernandes no Alcumus Info Exchange que está vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do Azure AD](#assigning-the-azure-ad-test-user)**: habilitar Brenda Fernandes a usar o logon único do Azure AD.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure
O objetivo desta seção é habilitar o logon único do Azure AD no portal clássico do Azure e configurar o logon único em seu aplicativo do Alcumus Info Exchange.

**Para configurar o logon único do Azure AD com o Alcumus Info Exchange, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração do aplicativo **Alcumus Info Exchange**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar o logon único][6]
2. Na página **Como você deseja que os usuários façam logon no Alcumus Info Exchange**, selecione **Logon único do Azure AD** e clique em **Avançar**.
   
    ![Logon único do AD do Azure][7]
3. Na página de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas:
   
    ![Logon único do AD do Azure][8]
   
    a. Na caixa de texto **URL de resposta**, digite a URL do consumidor que foi configurada para você pela equipe de suporte do Alcumus Info Exchange.
   
   > [!NOTE]
   > Se você não souber qual é o valor correto, entre em contato com a equipe de suporte do Alcumus Info Exchange via [helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com).
   > 
   > 
   
    b. Clique em **Avançar**.
4. Na página **Configurar logon único no Alcumus Info Exchange**, clique em **Baixar metadados** e salve o arquivo de metadados em seu computador.
   
    ![O que é o Azure AD Connect][9]
5. Entre em contato com a equipe de suporte do Alcumus Info Exchange via [helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com), forneça o arquivo de metadados e avise que eles devem habilitar o SSO para você.
6. No portal clássico do Azure, selecione a confirmação de configuração de logon único e clique em **Avançar**.
   
    ![O que é o Azure AD Connect][10]
7. Na página **Confirmação de logon único**, clique em **Concluir**.
   
    ![O que é o Azure AD Connect][11]

### Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_02.png)
2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_03.png)
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_04.png)
5. Na página do diálogo **Conte-nos sobre este usuário**, execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_05.png)
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em Avançar.
6. Na página da caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_06.png)

    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Avançar**.


1. Na página de caixa de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_07.png)
2. Na página de caixa de diálogo **Obter senha temporária**, execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_08.png)
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.

### Criar um usuário de teste do Alcumus Info Exchange
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Alcumus Info Exchange.

**Para criar um usuário chamado Brenda Fernandes no Alcumus Info Exchange, execute as seguintes etapas:**

1. Entre em contato com a equipe de suporte do Alcumus Info Exchange via [helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com),

### Atribuição do usuário de teste do AD do Azure
O objetivo desta seção é habilitar que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao Alcumus Info Exchange.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao Alcumus Info Exchange, execute as seguintes etapas:**

1. No portal do Azure, para abrir a exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu principal.
   
    ![Atribuir usuário][201]
2. Na lista de aplicativos, selecione **Alcumus Info Exchange**.
   
    ![Atribuir usuário][202]
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203]
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### Teste do logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso. Ao clicar no bloco do Alcumus Info Exchange no painel de acesso, você deve fazer logon automaticamente no seu aplicativo Alcumus Info Exchange.

## Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_01.png
[6]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_02.png
[7]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_03.png
[8]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_04.png
[9]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_05.png
[10]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_06.png
[11]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_07.png
[20]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_08.png
[203]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_205.png
[400]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_402.png

<!---HONumber=AcomDC_0907_2016-->