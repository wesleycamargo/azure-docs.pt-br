<properties
	pageTitle="Tutorial: Integração do Azure Active Directory ao Trakopolis | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Azure Active Directory e o Trakopolis."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/08/2016"
	ms.author="jeedes"/>


# Tutorial: Integração do Azure Active Directory ao Trakopolis

O objetivo deste tutorial é mostrar a você como integrar o Trakopolis ao Azure Active Directory (Azure AD).<br>A integração do Trakopolis ao Azure AD oferece os seguintes benefícios:

- No Azure AD, você pode controlar quem tem acesso ao Trakopolis
- Você pode permitir que os usuários façam logon automaticamente no Trakopolis (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um único local: o Active Directory do Azure 

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos

Para configurar a integração do Azure AD ao Trakopolis, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Trakopolis


> [AZURE.NOTE] Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste. <br> O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Trakopolis a partir da galeria
2. Configurar e testar o logon único do AD do Azure


## Adicionando o Trakopolis a partir da galeria
Para configurar a integração do Trakopolis ao Azure AD, você precisará adicionar o Trakopolis da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Trakopolis da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a exibição dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu superior.<br><br> ![Aplicativos][2]<br>
4. Clique em **Adicionar** na parte inferior da página.<br><br> ![Aplicativos][3]<br>
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.<br><br> ![Aplicativos][4]<br>
6. Na caixa de pesquisa, digite **Trakopolis**.<br><br> ![Aplicativos](./media/active-directory-saas-trakopolis-tutorial/tutorial_trakopolis_01.png)<br>
7. No painel de resultados, selecione **Trakopolis** e clique em **Concluir** para adicionar o aplicativo. <br><br>![Aplicativos](./media/active-directory-saas-trakopolis-tutorial/tutorial_trakopolis_02.png)<br>

##  Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar logon único do Azure AD com o Trakopolis com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Trakopolis é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Trakopolis.<br> Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Nome de Usuário** no Trakopolis.

Para configurar e testar o logon único do Azure AD com o Trakopolis, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)**: para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)**: para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criação de um usuário de teste do Trakopolis](#creating-a-trakopolis-test-user)**: para ter um equivalente de Brenda Fernandes no Trakopolis que esteja vinculado à representação dela no Azure AD.
5. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)**: para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure

O objetivo desta seção é habilitar o logon único do Azure AD no portal clássico do Azure e configurar o logon único em seu aplicativo Trakopolis.



**Para configurar o logon único do Azure AD com o Trakopolis, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração do aplicativo **Trakopolis**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**. <br><br> ![Configurar o logon único][6] <br>

2. Na página **Como você deseja que os usuários façam logon no Trakopolis**, selecione **Logon Único do Azure AD** e clique em **Avançar**. <br><br> ![Configurar o logon único](./media/active-directory-saas-trakopolis-tutorial/tutorial_trakopolis_03.png) <br>

3. Na página da caixa de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas: <br><br>![Configurar o logon único](./media/active-directory-saas-trakopolis-tutorial/tutorial_trakopolis_04.png) <br>


    a. Na caixa de texto da URL de Entrada, digite a URL usada pelos usuários para fazer logon no seu aplicativo Trakopolis usando o seguinte padrão: **“https://<nome da empresa>.trakopolis.com”**.

     b. Clique em **Próximo**.

4. Na página **Configurar logon único no Trakopolis**, execute as seguintes etapas: <br><br>![Configurar o logon único](./media/active-directory-saas-trakopolis-tutorial/tutorial_trakopolis_05.png) <br>

    a. Clique em **Baixar certificado** e salve o arquivo em seu computador.

    b. Clique em **Próximo**.


5. Para configurar o SSO para seu aplicativo, entre em contato com a equipe de suporte do Trakopolis pelo email [support@cantelematics.com](mailto:support@cantelematics.com), anexe o certificado baixado e forneça a eles a **URL do Emissor**, a **URL de SSO SAML** e a **URL de Saída**.


6. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**. <br><br>![Logon único do AD do Azure][10]<br>

7. Na página **Confirmação de logon único**, clique em **Concluir**. <br><br>![Logon único do AD do Azure][11]




### Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal clássico do Azure chamado Brenda Fernandes.<br> Na lista Usuários, escolha **Brenda Fernandes**.<br><br>![Criar um usuário do AD do Azure][20]<br>

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-trakopolis-tutorial/create_aaduser_09.png) <br>

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**. <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-trakopolis-tutorial/create_aaduser_03.png) <br>

4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**. <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-trakopolis-tutorial/create_aaduser_04.png) <br>

5. Na página do diálogo **Conte-nos sobre este usuário**, realize as seguintes etapas: <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-trakopolis-tutorial/create_aaduser_05.png) <br>

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Próximo**.

6.  Na página da caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas: <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-trakopolis-tutorial/create_aaduser_06.png) <br>

    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Próximo**.

7. Na página da caixa de diálogo **Obter senha temporária**, clique em **criar**. <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-trakopolis-tutorial/create_aaduser_07.png) <br>

8. Na página da caixa de diálogo **Obter senha temporária**, execute as seguintes etapas: <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-trakopolis-tutorial/create_aaduser_08.png) <br>

    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.



### Como criar um Usuário de teste do Trakopolis

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Trakopolis.<br> Trabalhe com a equipe de suporte do Trakopolis para adicionar os usuários ao Trakopolis.



### Atribuição do usuário de teste do AD do Azure

O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Trakopolis. <br><br>![Atribuir usuário][200] <br>

**Para atribuir Brenda Fernandes ao Trakopolis, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior. <br><br>![Atribuir usuário][201] <br>

2. Na lista de aplicativos, selecione **Trakopolis**. <br><br>![Configurar o logon único](./media/active-directory-saas-trakopolis-tutorial/tutorial_trakopolis_50.png) <br>

1. No menu na parte superior, clique em **Usuários**. <br><br>![Atribuir usuário][203] <br>

1. Na lista Usuários, selecione **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**. <br><br>![Atribuir usuário][205]



### Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.<br> Quando você clicar no bloco Trakopolis no Painel de Acesso, deverá fazer logon automaticamente no seu aplicativo Trakopolis.


## Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-trakopolis-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-trakopolis-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-trakopolis-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-trakopolis-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-trakopolis-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-trakopolis-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-trakopolis-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-trakopolis-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-trakopolis-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-trakopolis-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-trakopolis-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-trakopolis-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-trakopolis-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0309_2016-->