<properties
	pageTitle="Tutorial: Integração do Active Directory do Azure com o Questetra BPM Suite | Microsoft Azure"
	description="Saiba como configurar o logon único entre o Active Directory do Azure e o Questetra BPM Suite."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="msStevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/02/2015"
	ms.author="markusvi"/>


# Tutorial: Integração do Active Directory do Azure com o Questetra BPM Suite

O objetivo deste tutorial é mostrar a você como integrar o Questetra BPM Suite com o Active Directory do Azure (AD do Azure).<br>A integração do Questetra BPM Suite com o AD do Azure oferece os seguintes benefícios:

- Você pode controlar, no AD do Azure, quem tem acesso ao Questetra BPM Suite 
- Você pode habilitar seus usuários a fazerem logon automaticamente no Questetra BPM Suite (logon único) com suas contas do AD do Azure
- Você pode gerenciar suas contas em um local central – o Portal do Active Directory do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos 

Para configurar a integração do AD do Azure com o Questetra BPM Suite, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do [Questetra BPM Suite](https://senbon-imadegawa-988.questetra.net/)


> [AZURE.NOTE]Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste. <br> O cenário descrito neste tutorial consiste em três blocos de construção principais:

1. Adicionar um Questetra BPM Suite da galeria 
2. Configurar e testar o logon único do Azure AD


## Adicionar um Questetra BPM Suite da galeria
Para configurar a integração do Questetra BPM Suite com o AD do Azure, você precisa adicionar o Questetra BPM Suite, por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Questetra BPM Suite por meio da galeria, execute as seguintes etapas:**

1. No **Portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Active Directory**. <br><br> ![Active Directory][1]

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu superior.<br><br> ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.<br><br> ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo por meio da galeria**.<br><br> ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Questetra BPM Suite**.<br> ![Aplicativos][5]
7. No painel de resultados, selecione **Questetra BPM Suite** e clique em **Concluir** para adicionar o aplicativo.<br>



##  Configurar e testar o logon único do Azure AD
O objetivo desta seção é mostrar como configurar e testar logon único do AD do Azure com o Questetra BPM Suite, com base em um usuário de teste chamado "Britta Simon".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Questetra BPM Suite é equivalente a um usuário do AD do Azure. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do AD do Azure e o usuário relacionado do Questetra BPM Suite.<br> Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no AD do Azure ao valor de **Nome de usuário** no Questetra BPM Suite.
 
Para configurar e testar o logon único do AD do Azure com o Questetra BPM Suite, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Logon único do Azure AD](#configuring-azure-ad-single-single-sign-on)** - para habilitar seus usuários para usar esse recurso.
2. **[Criação de um usuário de teste do Azure AD](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
4. **[Criação de um usuário de teste do Questetra BPM Suite](#creating-a-questetra-bpm-suite-test-user)** - para ter um equivalente de Britta Simon no Questetra BPM Suite que esteja vinculado à representação dela no Azure AD.
5. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do AD do Azure.
5. **[Teste do Logon Único](#testing-single-sign-on)** - para verificar se a configuração funciona.

### Configuração do logon único do AD do Azure

O objetivo desta seção é habilitar o logon único do AD do Azure no portal do AD do Azure e configurar o logon único em seu aplicativo do Questetra BPM Suite.<br>

**Para configurar o logon único do AD do Azure com o Questetra BPM Suite, execute as seguintes etapas:**

1. No portal do AD do Azure, na página de integração de aplicativos do **Questetra BPM Suite**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.<br><br> ![Configurar o logon único][8]

2. Na página **Como você deseja que os usuários façam logon no Questetra BPM Suite**, selecione **Logon único do AD do Azure** e clique em **Avançar**.<br><br> ![Logon único do AD do Azure][9]


3. Em outra janela do navegador da Web, faça logon em seu site de empresa **Questetra BPM Suite** como administrador.

4. No menu na parte superior, clique em **Configurações do Sistema**. <br><br> ![Logon único do AD do Azure][10]

5. Para abrir a página **SingleSignOnSAML**, clique em **SSO (SAML)**. <br><br> ![Logon único do AD do Azure][11]


6. No portal do Azure, na página da caixa de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas: <br><br>![Definir configurações de aplicativo][13]
 
    a. No seu site da empresa **Questetra BPM Suite**, na seção de Informações de SP, copie a **URL de ACS** e cole-a na caixa de texto **URL de Logon**.

    b. No seu site da empresa **Questetra BPM Suite**, na seção de Informações de SP, copie a **ID da Entidade** e cole-a na caixa de texto **URL do Emissor**.

    c. No seu site da empresa **Questetra BPM Suite**, na seção de Informações de SP, copie a **URL de ACS** e cole-a na caixa de texto **URL de Resposta**.

    d. Clique em **Próximo**.

 
7. Na página **Configurar logon único no Questetra BPM Suite**, clique em **Baixar certificado** e, em seguida, salve o arquivo de certificado localmente em seu computador.<br><br>![Configurar o logon único][14]


8. No seu site da empresa **Questetra BPM Suite**, execute as seguintes etapas: <br><br>![Configurar o logon único][15]

    a. Selecione **Habilitar Logon Único**.
     
    b. No portal do Azure, copie o valor de **URL do Emissor** e, em seguida, cole-o na caixa de texto **ID da Entidade**.

    c. No portal do Azure, copie o valor de **URL do Serviço de Logon Único** e, em seguida, cole-o na caixa de texto **URL da página de entrada**.

    d. No portal do Azure, copie o valor de **URL do Serviço de Logout Único** e, em seguida, cole-o na caixa de texto **URL da página de saída**.

    e. Na caixa de texto **Formato da NameID**, digite **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.


    f. Crie um arquivo codificado em base 64 usando o certificado baixado.

    >[AZURE.TIP]Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

    g. Abra seu certificado codificado em base-64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado de validação**.

    h. Clique em **Salvar**.


9. No portal do Azure AD, selecione a confirmação de configuração de logon único e, em seguida, clique em **Próximo**. <br><br>![O que é o Azure AD Connect][17]


10. Na página **Confirmação de logon único**, clique em **Concluir**. <br><br>![O que é o Azure AD Connect][18]




### Criação de um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no portal do Azure chamado Britta Simon.

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **Portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Active Directory**. <br><br>![Criar um usuário de teste do AD do Azure][100] 

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**. <br><br>![Criar um usuário de teste do AD do Azure][101]

4. Para abrir a caixa de diálogo **Adicionar usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar usuário**. <br><br>![Criar um usuário de teste do AD do Azure][102]

5. Na página de diálogo **Conte-nos sobre este usuário**, execute as seguintes etapas: <br><br>![Criar um usuário de teste do AD do Azure][103]
 
    a. Em **Tipo de Usuário**, selecione **Novo usuário na organização**.
  
    b. Na **caixa de texto** Nome de Usuário, digite **BrittaSimon**.

    c. Clique em Avançar.
6.  Na caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas: <br><br>![Criar um usuário de teste do AD do Azure][104] 
  
    a. Na caixa de texto **Nome**, digite **Britta**.
 
    b. Na caixa de texto **Sobrenome**, digite **Simon**.

    c. Na caixa de texto **Nome de Exibição**, digite **Britta Simon**.

    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Próximo**.

7. Na página de diálogo **Obter senha temporária**, clique em **criar**. <br><br>![Criar um usuário de teste do AD do Azure][105]

8. Na página de diálogo **Obter senha temporária**, execute as seguintes etapas: <br><br>![Criar um usuário de teste do AD do Azure][106]
  1. Anote o valor da **Nova senha**.
  2. Clique em **Concluído**.   
  
 
### Criar um usuário de teste do Questetra BPM Suite

O objetivo desta seção é criar um usuário chamado Britta Simon no Questetra BPM Suite.

**Para criar um usuário chamado Britta Simon no Questetra BPM Suite, execute as seguintes etapas:**

1.	Faça logon no site da sua empresa do Questetra BPM Suite como um administrador.
2.	Vá para **Configurações do Sistema > Lista de Usuários > Novo Usuário**. 
3.	Na caixa de diálogo Novo Usuário, execute as seguintes etapas: <br><br>![Criar um usuário de teste][300] 

    a. Na caixa de texto **Nome**, digite o nome de usuário Britta no Azure AD.

    b. Na caixa de texto **Email**, digite o nome de usuário Britta no Azure AD.

    c. Na caixa de texto **Senha**, digite uma senha.

4.	Clique em **Adicionar novo usuário**.



### Atribuição do usuário de teste do Azure AD

O objetivo desta seção é habilitar Britta Simon a usar o logon único do Azure, concedendo a ela acesso ao Questetra BPM Suite. <br><br>![O que é o Azure AD Connect][200]

**Para atribuir Britta Simon ao Questetra BPM Suite, execute as seguintes etapas:**

1. No portal do Azure, para abrir a exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior. <br><br>![O que é o Azure AD Connect][201]
2. Na lista de aplicativos, selecione **Questetra BPM Suite**. <br><br>![O que é o Azure AD Connect][205]
1. No menu na parte superior, clique em **Usuários**. <br><br>![O que é o Azure AD Connect][202]
1. Na lista de usuários, selecione **Britta Simon**. <br><br>![O que é o Azure AD Connect][203]
2. Na barra de ferramentas na parte inferior, clique em **Atribuir**. <br><br>![O que é o Azure AD Connect][204]



### Teste do logon único

O objetivo desta seção é testar a sua configuração de logon único do Azure AD usando o painel de acesso.<br> Quando clica no bloco Questetra BPM Suite no Painel de Acesso, você deve fazer logon automaticamente no seu aplicativo Questetra BPM Suite.


## Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_01.png
[2]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_02.png
[3]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_03.png
[4]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_04.png
[5]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_01.png


[8]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_06.png
[9]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_02.png
[10]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_03.png
[11]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_04.png
[12]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_05.png
[13]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_06.png
[14]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_07.png
[15]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_08.png
[16]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_09.png
[17]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_10.png
[18]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_08.png


[100]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_09.png
[101]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_10.png
[102]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_11.png
[103]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_12.png
[104]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_13.png
[105]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_14.png
[106]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_15.png


[200]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_16.png
[201]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_17.png
[202]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_18.png
[203]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_19.png
[204]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_20.png
[205]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_12.png

[300]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_11.png

<!---HONumber=Oct15_HO3-->