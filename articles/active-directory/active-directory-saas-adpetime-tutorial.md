---
title: 'Tutorial: integração do Azure Active Directory ao ADP eTime | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ADP eTime.
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
# Tutorial: integração do Azure Active Directory ao ADP eTime
O objetivo desse tutorial é mostrar como integrar o ADP eTime ao Azure AD (Azure Active Directory). A integração do ADP eTime ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao ADP eTime
* É possível permitir que seus usuários façam logon automaticamente no ADP eTime (logon único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos
Para configurar a integração do Azure AD com o ADP eTime, você precisará dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura do ADP eTime com logon único habilitado

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do ADP eTime a partir da galeria
2. Configurar e testar o logon único do AD do Azure

## Adição do ADP eTime a partir da galeria
Para configurar a integração do ADP eTime com o Azure AD, você precisa adicionar o ADP eTime à sua lista de aplicativos SaaS gerenciados a partir da galeria.

**Para adicionar o ADP eTime a partir da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory][1]
2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **ADP eTime**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_01.png)
7. No painel de resultados, escolha **ADP eTime** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_06.png)

## Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do Azure AD com o ADP eTime, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do ADP eTime é equivalente a um usuário do AD do Azure. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no ADP eTime. Essa relação de vinculação é estabelecida por meio da atribuição do valor de **nome de usuário** no Azure AD como o valor de **Nome de usuário** no ADP eTime.

Para configurar e testar o logon único do Azure AD com o ADP eTime, você precisará concluir os seguintes blocos de construção:

1. **[Configurando o Logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)**: para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do ADP eTime](#creating-a-adpetime-test-user)** –para ter um equivalente de Brenda Fernandes no ADP eTime que esteja vinculado à representação dela no Azure AD.
4. **[Atribuindo o usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure
O objetivo desta seção é habilitar o logon único do Azure AD no portal clássico do Azure e configurar o logon único em seu aplicativo do ADP eTime.

Seu aplicativo ADP eTime espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos de token SAML. A captura de tela a seguir mostra um exemplo disso. O nome da declaração sempre será **"PersonImmutableID"** e o valor que mapeamos para ExtensionAttribute2 que contém o EmployeeID do usuário. Aqui, o mapeamento do usuário do Azure AD para o ADP eTime será feito com o EmployeeID, mas isso pode ser mapeado para um valor diferente também baseado em suas configurações do aplicativo. Portanto, trabalhe com a equipe do eTime ADP primeiro para usar o identificador correto de um usuário e mapear esse valor com a declaração **"PersonImmutableID"**.

![Configurar o logon único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_02.png)

Antes de configurar a declaração SAML, você precisará entrar em contato com a equipe de suporte do ADP eTime e solicitar o valor do atributo de identificador exclusivo para seu locatário. Você precisa desse valor para configurar a declaração personalizada para seu aplicativo.

**Para configurar o logon único do Azure AD com o ADP eTime, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração do aplicativo **ADP eTime**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar o logon único][6]
2. Na página **Como você deseja que os usuários façam logon no ADP eTime**, escolha **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar o logon único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_03.png)
3. Na página do diálogo **Definir Configurações do Aplicativo**, realize as seguintes etapas:
   
    ![Configurar o logon único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_04.png)

    a. Na caixa de texto **URL de Resposta**, digite a URL usada pelos usuários para fazer logon em seu aplicativo do ADP eTime usando o seguinte padrão: `https://<server name>.adp.com/affwebservices/public/saml2assertionconsumer`.

    b. Clique em **Avançar**.

1. Na página **Configurar logon único no ADP eTime**, execute as seguintes etapas:
   
    ![Configurar o logon único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_05.png)
   
    a. Clique em **Baixar metadados** e salve o arquivo no computador.
   
    b. Clique em **Avançar**.
2. Para configurar o SSO em seu aplicativo, entre em contato com a equipe de suporte do ADP eTime e envie o arquivo de metadados baixado por email para que eles possam ser configurados para integração de SSO.
   
   > [!NOTE]
   > Depois que a equipe do **ADP eTime** configurá-lo, obtenha o valor **RelayState** e solicite ao Azure AD para configurá-lo para a instância do aplicativo. Você pode enviar esse valor, sua ID de locatário do Azure AD e a ID do aplicativo para a equipe do Azure AD no endereço [waadpartners@microsoft.com](mailTo:waadpartners@microsoft.com). Depois dessa configuração, teste a integração e ela funcionará. Portanto, observe que essa configuração é importante para que a integração com o aplicativo funcione.
   > 
   > 
3. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon único do AD do Azure][10]
4. Na página **Confirmação de logon único**, clique em **Concluir**.
   
    ![Logon único do AD do Azure][11]

### Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes. Na lista de usuários, selecione **Brenda Fernandes**.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-adpetime-tutorial/create_aaduser_09.png)
2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-adpetime-tutorial/create_aaduser_03.png)
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-adpetime-tutorial/create_aaduser_04.png)
5. Na página do diálogo **Conte-nos sobre este usuário**, execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-adpetime-tutorial/create_aaduser_05.png)
   
    a. Em **Tipo de Usuário**, selecione **Novo usuário na organização**.
   
    b. Na caixa de texto **Nome de Usuário**, digite **BrendaFernandes**.
   
    c. Clique em **Avançar**.
6. Na página da caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-adpetime-tutorial/create_aaduser_06.png)
   
   a. Na caixa de texto **Nome**, digite **Brenda**.
   
   b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
   c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.
   
   d. Na lista **Função**, selecione **Usuário**.
   
   e. Clique em **Avançar**.
7. Na página de caixa de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-adpetime-tutorial/create_aaduser_07.png)
8. Na página de caixa de diálogo **Obter senha temporária**, execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-adpetime-tutorial/create_aaduser_08.png)
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.

### Criar um usuário de teste do ADP eTime
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no ADP eTime. Trabalhe com a equipe de suporte do eTime ADP para adicionar os usuários na conta do eTime ADP.

> [!NOTE]
> Se precisar criar um usuário manualmente, entre em contato com a equipe de suporte do ADP eTime.
> 
> 

### Atribuição do usuário de teste do AD do Azure
O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao ADP eTime.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao ADP eTime, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201]
2. Na lista de aplicativos, escolha **ADP eTime**.
   
    ![Configurar o logon único](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_50.png)
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203]
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### Teste do logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso. Ao clicar no bloco do ADP eTime no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo do ADP eTime.

## Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0907_2016-->