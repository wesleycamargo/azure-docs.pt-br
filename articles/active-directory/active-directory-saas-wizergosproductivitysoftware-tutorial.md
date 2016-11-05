---
title: 'Tutorial: Integração do Azure Active Directory com o Wizergos Productivity Software | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Wizergos Productivity Software.
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
ms.date: 09/15/2016
ms.author: jeedes

---
# Tutorial: Integração do Azure Active Directory com o Wizergos Productivity Software
O objetivo desse tutorial é mostrar como integrar o Wizergos Productivity Software ao Azure AD (Azure Active Directory).

A integração do Wizergos Productivity Software ao Azure AD oferece os seguintes benefícios:

* No Azure AD, você pode controlar quem tem acesso ao Wizergos Productivity Software
* Você pode habilitar seus usuários a fazerem logon automaticamente no Wizergos Productivity Software (logon único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos
Para configurar a integração do Azure AD com o Wizergos Productivity Software, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura habilitada para logon único do Wizergos Productivity Software

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do software de produtividade Wizergos da galeria
2. Configurar e testar o logon único do AD do Azure

## Adição do software de produtividade Wizergos da galeria
Para configurar a integração do Wizergos Productivity Software com o Azure AD, você precisa adicionar o Wizergos Productivity Software, por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Wizergos Productivity Software da galeria, execute as seguintes etapas:**

1. No **Portal Clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory][1]
2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Wizergos Productivity Software**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_01.png)
7. No painel de resultados, selecione **Wizergos Productivity Software** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Seleção do aplicativo na galeria](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_001.png)

## Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar logon único do Azure AD com o Wizergos Productivity Software, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Wizergos Productivity Software é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Wizergos Productivity Software.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD ao valor de **Nome de usuário** no Wizergos Productivity Software.

Para configurar e testar o logon único do Azure AD com o BynWizergos Productivity Softwareder, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)**: para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do Wizergos Productivity Software](#creating-a-wizergos-productivity-software-test-user)** - para ter um equivalente de Brenda Fernandes no Wizergos Productivity Software que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)**: para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do Azure AD
Nesta seção, você habilitará o logon único do Azure AD no portal clássico e configurará o logon único em seu aplicativo Wizergos Productivity Software.

**Para configurar o logon único do Azure AD com o Wizergos Productivity Software, execute as seguintes etapas:**

1. No portal clássico, na página de integração de aplicativos do **Wizergos Productivity Software**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
    ![Configurar o logon único][6]
2. Na página **Como você deseja que os usuários façam logon no Wizergos Productivity Software**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar o logon único](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_03.png)
3. Na página da caixa de diálogo **Definir Configurações de Aplicativo**, se quiser configurar o aplicativo em **modo iniciado pelo IDP**, execute as seguintes etapas e clique em **Avançar**:
   
    ![Configurar o logon único](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_04.png)
   
    a. Na caixa de texto **Identificador**, digite: `https://www.wizergos.net`
   
    b. Na caixa de texto **URL de Resposta**, digite: `https://www.wizergos.net/register_users/saml2/`
   
    c. Clique em **Próximo**.
4. Se quiser configurar o aplicativo no **modo iniciado pelo SP**, na página de caixa de diálogo **Definir Configurações do Aplicativo**, clique em **"Mostrar configurações avançadas (opcional)"**, insira a **URL de Entrada** e clique em **Avançar**.
   
    ![Configurar o logon único](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_05.png)
   
    a. Na caixa de texto **URL de Entrada**, digite: `https://www.wizergos.net`
   
    b. Clique em **Próximo**.
5. Na página **Configurar logon único no Wizergos Productivity Software**, execute as seguintes etapas e clique em **Avançar**:
   
    ![Configurar o logon único](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_06.png)
   
    a. Clique em **Baixar certificado** e salve o arquivo em seu computador.
   
    b. Clique em **Avançar**.
6. Em uma janela diferente do navegador da Web, faça logon em seu locatário do Wizergos Productivity Software como um administrador.
7. No menu hambúrguer, selecione **Admin**.
   
    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_000.png)
8. Na página de administração no menu à esquerda, selecione **AUTENTICAÇÃO** e clique em **Azure AD**.
   
    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_002.png)
9. Execute as etapas a seguir na seção **AUTENTICAÇÃO**.
   
    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_003.png)
   
    a. Abra o certificado baixado do Azure AD e copie a impressão digital do certificado. Em seguida, cole-o na caixa de texto de **Impressão Digital do Certificado**.
   
    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_004.png)
   
    b. Na caixa de texto **URL do Emissor**, coloque o valor de **URL do Emissor** do assistente de configuração de aplicativo do Azure AD.
   
    c. Na caixa de texto **URL de Logon Único**, insira o valor da **URL de serviço de logon único** do assistente de configuração de aplicativo do Azure AD.
   
    d. Na caixa de texto **URL de Logon Único**, insira o valor da **URL de serviço de logoff único** do assistente de configuração de aplicativo do Azure AD.
   
    e. Clique no botão **Salvar**.
   
   > [!NOTE]
   > Observe que a impressão digital de certificado não deve conter espaços.
   > 
   > 
10. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
    
     ![Logon único do AD do Azure][10]
11. Na página **Confirmação de logon único**, clique em **Concluir**.
    
     ![Logon único do AD do Azure][11]

### Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal Clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_09.png)
2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_03.png)
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_04.png)
5. Na página do diálogo **Conte-nos sobre este usuário**, execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_05.png)
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em **Avançar**.
6. Na página da caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_06.png)
   
   a. Na caixa de texto **Nome**, digite **Brenda**.
   
   b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
   c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.
   
   d. Na lista **Função**, selecione **Usuário**.
   
   e. Clique em **Avançar**.
7. Na página de caixa de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_07.png)
8. Na página de caixa de diálogo **Obter senha temporária**, execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_08.png)
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.

### Criação de um usuário de teste do Wizergos Productivity Software
Nesta seção, você deve criar um usuário chamado Brenda Fernandes no Wizergos Productivity Software. Trabalhe com a equipe de suporte do Wizergos Productivity Software via [support@wizergos.com](emailTo:support@wizergos.com) para adicionar os usuários à plataforma do Wizergos Productivity Software.

### Atribuição do usuário de teste do AD do Azure
O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao Wizergos Productivity Software.

   ![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao Wizergos Productivity Software, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201]
2. Na lista de aplicativos, selecione **Wizergos Productivity Software**.
   
    ![Configurar o logon único](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_50.png)
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203]
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### Teste do logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando clica no bloco Wizergos Productivity Software no Painel de Acesso, você deve fazer logon automaticamente no seu aplicativo Wizergos Productivity Software.

## Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0921_2016-->