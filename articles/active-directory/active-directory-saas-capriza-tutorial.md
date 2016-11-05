---
title: 'Tutorial: Integração do Active Directory do Azure ao Capriza | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o Capriza.
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
# Tutorial: Integração do Active Directory do Azure ao Capriza
O objetivo deste tutorial é mostrar como integrar o Capriza ao Azure AD (Azure Active Directory). A integração do Capriza ao Azure AD oferece os seguintes benefícios:

* no Azure AD, é possível controlar quem tem acesso ao Capriza
* você pode habilitar seus usuários a fazerem logon automaticamente no Capriza(Logon Único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos
Para configurar a integração do Azure AD ao Capriza, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura habilitada para logon único do Capriza

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Capriza da galeria
2. Configurar e testar o logon único do AD do Azure

## Adição do Capriza da galeria
Para configurar a integração do Capriza ao Azure AD, você precisa adicionar o Capriza à sua lista de aplicativos SaaS gerenciados a partir da galeria.

**Para adicionar o Capriza da galeria, execute as seguintes etapas:**

1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory][1]
2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Capriza**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-capriza-tutorial/tutorial_capriza_01.png)
7. No painel de resultados, selecione **Capriza** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-capriza-tutorial/tutorial_capriza_02.png)

## Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do Azure AD com o Capriza, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Capriza é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Capriza. Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** ao Azure AD como sendo o valor de **Nome de usuário** no Capriza.

Para configurar e testar o logon único do Azure AD com o Capriza, você precisa concluir os seguintes blocos de construção:

1. **[Configurando o Logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criando um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do Capriza](#creating-a-capriza-test-user)** – para ter um equivalente de Brenda Fernandes no Capriza que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - habilitar Simon Britta a usar o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure
O objetivo desta seção é habilitar o logon único do Azure AD no portal clássico do Azure e configurar o logon único em seu aplicativo do Capriza.

**Para configurar o logon único do Azure AD com o Capriza, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração de aplicativos do **Capriza**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
    ![Configurar o logon único][6]
2. Na página **Como você deseja que os usuários façam logon no Capriza**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar o logon único](./media/active-directory-saas-capriza-tutorial/tutorial_capriza_03.png)
3. Na página de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas:
   
    ![Configurar o logon único](./media/active-directory-saas-capriza-tutorial/tutorial_capriza_04.png)

    a. Na caixa de texto URL de Logon, digite a URL usada pelos usuários para fazer logon no seu aplicativo do Capriza usando o seguinte padrão: **“https://companyname.capriza.com/tenantid”**.

    b. Clique em **Avançar**.


1. Na página **Configurar logon único no Capriza**, execute as seguintes etapas:
   
    ![Configurar o logon único](./media/active-directory-saas-capriza-tutorial/tutorial_capriza_05.png)
   
    a. Clique em **Baixar certificado** e salve o arquivo em seu computador.
   
    b. Clique em **Próximo**.
2. Para obter o SSO configurado para o seu aplicativo, entre em contato com sua equipe de suporte do Capriza por meio do support@capriza.com e anexe o arquivo de certificado baixado ao email. Também forneça a URL de SSO de SAML, a URL de Saída e a URL do Emissor para que possam ser configuradas para integração com o SSO.
3. No portal clássico do Azure, selecione a confirmação da configuração de logon único e, em seguida, clique em **Avançar**.
   
    ![Logon único do AD do Azure][10]
4. Na página **Confirmação de logon único**, clique em **Concluir**.
   
    ![Logon único do AD do Azure][11]

### Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-capriza-tutorial/create_aaduser_09.png)
2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-capriza-tutorial/create_aaduser_03.png)
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-capriza-tutorial/create_aaduser_04.png)
5. Na página do diálogo **Conte-nos sobre este usuário**, execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-capriza-tutorial/create_aaduser_05.png)
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em **Avançar**.
6. Na página da caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-capriza-tutorial/create_aaduser_06.png)
   
   a. Na caixa de texto **Nome**, digite **Brenda**.
   
   b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
   c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.
   
   d. Na lista **Função**, selecione **Usuário**.
   
   e. Clique em **Avançar**.
7. Na página de caixa de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-capriza-tutorial/create_aaduser_07.png)
8. Na página de caixa de diálogo **Obter senha temporária**, execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-capriza-tutorial/create_aaduser_08.png)
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.

### Criação de um usuário de teste do Capriza
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Capriza. O Capriza dá suporte ao provisionamento just-in-time, que está habilitado por padrão. **Certifique-se de que seu nome de domínio esteja configurado no Capriza para o provisionamento do usuário. Depois disso, somente o provisionamento de usuário just-in-time funciona.**

Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o Capriza, caso ainda não exista. [Configurando o logon único do Azure AD](#configuring-azure-ad-single-single-sign-on).

> [!NOTE]
> Se precisar criar um usuário manualmente, entre em contato com a equipe de suporte do Capriza.
> 
> 

### Atribuição do usuário de teste do AD do Azure
O objetivo desta seção é habilitar Brenda Fernandes a usar o logon único do Azure, concedendo a ela acesso ao Capriza.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao Capriza, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201]
2. Na lista de aplicativos, selecione **Capriza**.
   
    ![Configurar o logon único](./media/active-directory-saas-capriza-tutorial/tutorial_capriza_50.png)
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203]
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### Teste do logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso. Ao clicar no bloco do Capriza no Painel de Acesso, você deverá ser conectado automaticamente a seu aplicativo do Capriza.

## Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-capriza-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-capriza-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-capriza-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-capriza-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-capriza-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-capriza-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-capriza-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-capriza-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-capriza-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-capriza-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-capriza-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-capriza-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-capriza-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0907_2016-->