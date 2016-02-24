<properties
	pageTitle="Tutorial: Integração do Active Directory do Azure com o Halogen Software"
	description="Saiba como configurar o logon único entre o Active Directory do Azure e o Halogen Software."
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
	ms.date="02/01/2016"
	ms.author="jeedes"/>


# Tutorial: Integração do Active Directory do Azure com o Halogen Software

O objetivo deste tutorial é mostrar a você como integrar o Halogen Software com o Active Directory do Azure (Azure AD).<br>A integração do Halogen Software com o Azure AD oferece os seguintes benefícios:

- Você pode controlar, no Azure AD, quem tem acesso ao Halogen Software 
- Você pode habilitar seus usuários a fazerem logon automaticamente no Halogen Software (logon único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um local central – o Portal do Active Directory do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## Pré-requisitos 

Para configurar a integração do Azure AD com o Halogen Software, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Halogen Software com logon único habilitado


> [AZURE.NOTE] Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/). 

 
## Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste. <br> O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Halogen Software por meio da galeria 
2. Configurar e testar o logon único do Azure AD


## Adicionar o Halogen Software por meio da galeria
Para configurar a integração do Halogen Software com o Azure AD, você precisa adicionar o Halogen Software, por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Halogen Software por meio da galeria, execute as seguintes etapas:**

1. No **Portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Active Directory**. <br><br> ![Active Directory][1]

2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.<br><br> ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.<br><br> ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo por meio da galeria**.<br><br> ![Aplicativos][4]
6. Na caixa de pesquisa, digite **halogen software**.<br> ![Aplicativos][5]
7. No painel de resultados, selecione **Halogen Software** e clique em **Concluir** para adicionar o aplicativo.<br>



##  Configurar e testar o logon único do Azure AD
O objetivo desta seção é mostrar como configurar e testar logon único do Azure AD com o Halogen Software, com base em um usuário de teste chamado "Britta Simon".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Halogen Software é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Halogen Software.<br> Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD ao valor de **nome de usuário** no Halogen Software.
 
Para configurar e testar o logon único do Azure AD com o Halogen Software, você precisa concluir os seguintes blocos de construção:

1. **[Configuração do logon único do Azure AD](#configuring-azure-ad-single-single-sign-on)** - para habilitar os usuários a usarem esse recurso.
2. **[Criação de um usuário de teste do Azure AD](#creating-an-azure-ad-test-user)** - para testar logon único do Azure AD com Britta Simon.
4. **[Criação de um usuário de teste do Halogen Software](#creating-a-halogen-software-test-user)** - para ter um equivalente de Britta Simon no Halogen Software que esteja vinculado à representação dela no Azure AD.
5. **[Atribuir o usuário de teste do Azure AD](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do Azure AD.
5. **[Teste do logon único](#testing-single-sign-on)** - para verificar se a configuração funciona.

### Configuração do logon único do Azure AD

O objetivo desta seção é habilitar o logon único do Azure AD no portal do Azure AD e configurar o logon único em seu aplicativo do Halogen Software.<br>

**Para configurar o logon único do Azure AD com o Halogen Software, execute as seguintes etapas:**

1. No portal do Azure AD, na página de integração do aplicativo **Halogen Software**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar logon único**.<br><br>![Configurar o logon único][8]

2. Na página **Como você deseja que os usuários façam logon no Halogen Software**, selecione **Logon único do Azure AD** e clique em **Avançar**.<br><br>![Logon único do AD do Azure][9]

3. Na página da caixa de diálogo **Definir Configurações de Aplicativo**, execute as seguintes etapas:<br><br>![Definir configurações de aplicativo][10]
 
     a. Na caixa de texto **URL de Entrada**, digite a URL usada pelos usuários para fazer logon no aplicativo da Halogen Software, usando o seguinte padrão: **https://global.hgncloud.com/fabrikam/welcome.jsp*

     b. Clique em **Próximo**.
 
4. Na página **Configurar logon único no Halogen Software**, clique em **Baixar metadados** e salve o arquivo de metadados em seu computador.<br><br>![O que é o Azure AD Connect][11]

5. Em uma janela diferente do navegador, faça logon no aplicativo **Halogen Software** como administrador.
6. Clique na guia **Opções**. <br><br>![O que é o Azure AD Connect][12]
7. No painel de navegação esquerdo, clique em **Configuração do SAML**. <br><br>![O que é o Azure AD Connect][13]
8. Na página **Configuração do SAML**, realize as seguintes etapas: <br><br>![O que é o Azure AD Connect][14]

    a. Como **Identificador exclusivo**, selecione **NameID**.

    b. Em **Identificador exclusivo mapeia para**, selecione **Username**.

    c. Para carregar o arquivo de metadados baixado, clique em **Procurar** para selecionar o arquivo e clique em **Carregar arquivo**.

    d. Para testar a configuração, clique em **Executar Teste**.

    > [AZURE.NOTE] Você precisa esperar pela mensagem "*O teste de SAML foi concluído. Feche esta janela*". Feche a janela do navegador aberta. <br> A caixa de seleção **Habilitar SAML** só será habilitada se o teste for concluído.

    e. Selecione **Habilitar SAML**.
    
    f. Clique em **Salvar Alterações**.


9. No portal do Azure AD, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.<br><br>![O que é o Azure AD Connect][15]
10. Na página **Confirmação de logon único**, clique em **Concluir**.<br><br>![O que é o Azure AD Connect][16]




### Criação de um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no portal do Azure chamado Britta Simon.

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No **Portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Active Directory**. <br><br>![O que é o Azure AD Connect][100] 
2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**. <br><br>![O que é o Azure AD Connect][101] 
4. Para abrir a caixa de diálogo **Adicionar usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar usuário**.<br><br>![O que é o Azure AD Connect][102] 
5. Na página de diálogo **Conte-nos sobre este usuário**, execute as seguintes etapas: <br><br>![O que é o Azure AD Connect][103] 
 
    a. Em **Tipo de Usuário**, selecione **Novo usuário na organização**.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em Avançar.
6.  Na caixa de diálogo **Perfil do Usuário**, execute as seguintes etapas: <br><br>![O que é o Azure AD Connect][104] 

    a. Na caixa de texto **Nome**, digite **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Próximo**.
7. Na página de caixa de diálogo **Obter senha temporária**, clique em **criar**. <br><br>![O que é o Azure AD Connect][105]  
8. Na página de caixa de diálogo **Obter senha temporária** execute as seguintes etapas:<br><br>![O que é o Azure AD Connect][106]   

    a. Anote o valor da **Nova Senha**.
    b. Clique em **Concluído**.
  
 
### Criação de um usuário de teste do Halogen Software

O objetivo desta seção é criar um usuário chamado Britta Simon no Halogen Software.

**Para criar um usuário chamado Britta Simon no Halogen Software, execute as seguintes etapas:**

1. Faça logon no aplicativo **Halogen Software** como administrador.
2. Clique na guia **Central do Usuário** e clique em **Criar Usuário**. <br><br>![O que é o Azure AD Connect][300]  
3. Na página do diálogo **Novo Usuário**, execute as seguintes etapas: <br><br>![O que é o Azure AD Connect][301] 
  a. Na caixa de texto **Nome**, digite **Brenda**. 
  b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
  c. Na caixa de texto **Nome de Usuário**, digite o **Nome de usuário de Brenda Fernandes no portal do AD do Azure**. 
  d. Na caixa de texto **Senha**, digite uma senha para Brenda. 
  e. Clique em **Salvar**.


### Atribuição do usuário de teste do AD do Azure

O objetivo desta seção é habilitar que Britta Simon use o logon único do Azure, concedendo a ela acesso ao Halogen Software.<br><br>![O que é o Azure AD Connect][200]

**Para atribuir Britta Simon ao Halogen Software, execute as seguintes etapas:**

1. No Portal do Azure, para abrir a exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu principal.<br> <br><br>![O que é o Azure AD Connect][201]
2. Na lista de aplicativos, selecione **Halogen Software**. <br><br>![O que é o Azure AD Connect][202]
1. No menu na parte superior, clique em **Usuários**.<br> <br><br>![O que é o Azure AD Connect][203]
1. Na lista de Usuários, selecione **Britta Simon**. <br><br>![O que é o Azure AD Connect][204]
2. Na barra de ferramentas na parte inferior, clique em **Atribuir**. <br><br>![O que é o Azure AD Connect][205]



### Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.<br> Quando clica no bloco Halogen Software no Painel de Acesso, você deve fazer logon automaticamente no seu aplicativo Halogen Software.


## Recursos adicionais

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

<!---HONumber=AcomDC_0204_2016-->