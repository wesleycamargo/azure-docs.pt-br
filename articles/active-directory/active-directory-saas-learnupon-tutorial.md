<properties
	pageTitle="Tutorial: Integração do Active Directory do Azure com o Novatus | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Active Directory do Azure e o LearnUpon."
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
	ms.date="02/09/2016"
	ms.author="jeedes"/>


# Tutorial: Integração do Active Directory do Azure com o LearnUpon

O objetivo deste tutorial é mostrar como integrar o LearnUpon ao AD do Azure (Active Directory do Azure).<br>A integração do LearnUpon ao AD do Azure oferece os seguintes benefícios:

- No AD do Azure, você pode controlar quem tem acesso ao LearnUpon
- Você pode habilitar que usuários façam logon automaticamente no LearnUpon (logon único) com as respectivas contas do AD do Azure
- Você pode gerenciar suas contas em um local central, o portal clássico do Active Directory do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos

Para configurar a integração do AD do Azure ao LearnUpon, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do LearnUpon


> [AZURE.NOTE] Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste. <br> O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o LearnUpon da galeria
2. Configurar e testar o logon único do AD do Azure


## Adicionando o LearnUpon da galeria
Para configurar a integração do LearnUpon ao AD do Azure, você precisará adicionar o LearnUpon da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o LearnUpon da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. <br><br> ![Active Directory][1]<br>

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a exibição dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu superior.<br><br> ![Aplicativos][2]<br>
4. Clique em **Adicionar** na parte inferior da página.<br><br> ![Aplicativos][3]<br>
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.<br><br> ![Aplicativos][4]<br>
6. Na caixa de pesquisa, digite **LearnUpon**.<br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_01.png)<br>
7. No painel de resultados, selecione **LearnUpon** e clique em **Concluir** para adicionar o aplicativo. <br><br>

##  Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do AD do Azure com o LearnUpon, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o AD do Azure precisa saber qual usuário do LearnUpon é equivalente a um usuário do AD do Azure. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do AD do Azure e o usuário relacionado do LearnUpon.<br> Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no AD do Azure como o valor de **nome de usuário** no LearnUpon.

Para configurar e testar o logon único do AD do Azure com o LearnUpon, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)** - para permitir que seus usuários usem esse recurso.
2. **[Criar um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criando um usuário de teste do LearnUpon](#creating-a-learnupon-test-user)** - para ter um equivalente de Brenda Fernandes no LearnUpon que esteja vinculado à representação dela no AD do Azure.
5. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)**: para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure

O objetivo desta seção é habilitar o logon único do AD do Azure no portal do AD do Azure e configurar o logon único em seu aplicativo LearnUpon.



**Para configurar o logon único do Azure AD com o LearnUpon, execute as seguintes etapas:**

1. No portal do AD do Azure, na página de integração do aplicativo **LearnUpon**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**. <br><br> ![Configurar o logon único][6] <br>

2. na página **Como você deseja que os usuários façam logon no LearnUpon**, selecione **Logon Único do Azure AD** e clique em **Avançar**. <br><br> ![Configurar o logon único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_03.png) <br>

3. Na página da caixa de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas: <br><br>![Configurar o logon único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_04.png) <br>


    a. Na caixa de texto URL de Resposta, digite a URL do Serviço do Consumidor de Declaração usando o seguinte padrão: **"https://<nomedaempresa>.learnupon.com/saml/consumer"**.


4. Na página **Configurar logon único no LearnUpon**, execute as seguintes etapas: <br><br>![Configurar o logon único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_05.png) <br>

    a. Clique em **Baixar certificado** e salve o arquivo em seu computador. Precisaremos desse certificado e das URLs de metadados (ID da Entidade, URL de Entrada e URL de saída de SSO) para configurar o SSO no lado do LearnUpon.

    b. Clique em **Próximo**.


5. Abra outro logon da instância de navegador na instância do LearnUpon com o usuário de administrador para definir **SSO do SAML** no lado do LearnUpon. Quando estiver conectado ao LearnUpon, você verá uma tela semelhante à seguinte. <br><br>![Configurar o logon único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_06.png) <br>

	a. Clique na guia de **configurações** para abrir a janela de configurações.<br> b. Clique em **Logon Único - SAML**<br> c. Clique em **Configurações Gerais** para definir as configurações de SAML. <br><br>![Configurar o logon único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_07.png) <br> d. Preencha o formulário de **Configurações Gerais** da seguinte forma: <br><br>![Configurar o logon único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_08.png) <br> d1. Marque a caixa de seleção **Habilitado** para habilitar o SAML nesse portal<br> d2. Escolha a **versão 2.0**<br> d3. Escolha **condições de Não Ignorar**<br> d4. **Nome do parâmetro POST do Token SAML** é o nome do parâmetro POST da solicitação para a URL de consumidor SAML indicada acima, que contém a declaração SAML a ser verificada e autenticada. Por exemplo,**SAMLResponse** <br> d5. **Formato do Identificador de Nome** informa onde na sua declaração SAML reside o identificador de usuários (endereço de email), por exemplo, **urn:oasis:names:tc:SAML:1.1:nameid- format:emailAddress**.<br> d6. **Identificar Local do Provedor** é para onde enviaremos os usuários se eles clicarem em seu ícone carregado da tela de logon do portal.<br> d7. Copie a **URL do Serviço de Logon Único** na tela de configuração do Azure para **URL de Saída**. <br>![Configurar o logon único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_09.png) <br> d8. Clique no link **Gerenciar impressões digitais** da impressão digital de certificado para carregar a impressão digital do certificado. <br>![Configurar o logon único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_10.png) <br> d9. Clique no botão Salvar e. Clique em **Configurações de Usuário** para definir as configurações de usuário SAML. <br><br>![Configurar o logon único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_11.png) <br> e1. **Formato do Identificador de Nome** informa onde na sua declaração SAML reside o nome dos usuários, por exemplo, **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ givenname**. e2. **Formato do Identificador de Sobrenome** informa onde na sua declaração SAML reside o sobrenome dos usuários, por exemplo, **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ surname**


6. No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**. <br><br>![Logon único do AD do Azure][10]<br>

7. Na página **Confirmação de logon único**, clique em **Concluir**. <br>![Logon único do AD do Azure][11]




### Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal clássico do Azure chamado Brenda Fernandes.<br> Na lista Usuários, escolha **Brenda Fernandes**.<br><br>![Criar um usuário do AD do Azure][20]<br>

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-learnupon-tutorial/create_aaduser_09.png) <br>

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**. <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-learnupon-tutorial/create_aaduser_03.png) <br>

4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**. <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-learnupon-tutorial/create_aaduser_04.png) <br>

5. Na página do diálogo **Conte-nos sobre este usuário**, realize as seguintes etapas: <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-learnupon-tutorial/create_aaduser_05.png) <br>

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Próximo**.

6.  Na página da caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas: <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-learnupon-tutorial/create_aaduser_06.png) <br>

    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Próximo**.

7. Na página da caixa de diálogo **Obter senha temporária**, clique em **criar**. <br><br> ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-learnupon-tutorial/create_aaduser_07.png) <br>

8. Na página da caixa de diálogo **Obter senha temporária**, execute as seguintes etapas: <br><br>![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-learnupon-tutorial/create_aaduser_08.png) <br>

    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.



### Criar um usuário de teste do LearnUpon

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no LearnUpon. O LearnUpon dá suporte ao provisionamento just-in-time, que é habilitado por padrão.

Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o LearnUpon, caso ele ainda não exista. [Configuração do logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Se precisar criar um usuário manualmente, entre em contato com a equipe de suporte do LearnUpon.


### Atribuição do usuário de teste do AD do Azure

O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao LearnUpon. <br><br>![Atribuir usuário][200] <br>

**Para atribuir Brenda Fernandes ao LearnUpon, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior. <br><br>![Atribuir usuário][201] <br>

2. Na lista de aplicativos, selecione **LearnUpon**. <br><br>![Configurar o logon único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_50.png) <br>

1. No menu na parte superior, clique em **Usuários**. <br><br>![Atribuir usuário][203] <br>

1. Na lista Usuários, selecione **Brenda Fernandes**.

2. Na barra de ferramentas na parte inferior, clique em **Atribuir**. <br><br>![Atribuir usuário][205]



### Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.<br> Quando você clicar no bloco LearnUpon no Painel de Acesso, deverá ser automaticamente conectado ao seu aplicativo LearnUpon.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0211_2016-->